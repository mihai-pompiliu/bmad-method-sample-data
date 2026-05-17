# LeakMate Firmware PRD

- Product: LeakMate residential water leak detector
- SKUs: LM-LR (LoRa, STM32WL55) and LM-CL (LTE-M, nRF9151)
- Owner: Firmware + Hardware (co-authored)
- Status: Draft, v0.3
- Date: 2026-05-17
- Target tape-out / FW freeze: Q4 2026

## Problem

Residential basement water damage averages $11k per claim and is the single largest non-weather homeowner insurance loss category. Existing leak detectors either (a) require WiFi, which drains AA cells in months and dies silently before the leak event, or (b) ship as part of a $200+ hub ecosystem the buyer never installs. LeakMate is a single-purpose, 2xAA, five-year shelf-and-service device that screams locally and notifies remotely, with a BOM under $14 so it fits a 4-pack retail SKU under $79.

## Hardware Target

LoRa SKU (LM-LR):
- MCU: ST STM32WL55JC. Cortex-M4 + sub-GHz radio in one package. 256KB flash, 64KB SRAM.
- Radio: integrated SX126x-class, 14dBm max in US915, 16dBm in EU868.

Cellular SKU (LM-CL):
- MCU/Modem: Nordic nRF9151 SiP. Cortex-M33, LTE-M / NB-IoT, integrated PMIC.
- Application code budget is shared with the modem firmware partition. Treat application flash budget as the same 192KB target to keep one codebase.

Common:
- Power: 2x AA alkaline in series. 3.0V nominal, 2.7V cutoff, 2.4V brownout-safe floor.
- Regulator: TI TPS62840 buck-boost, 1.8V rail to MCU. Iq = 60nA typical, which matters.
- Alarm: Murata PKM13EPYH4000 piezo, driven by complementary PWM at 4kHz resonance.
- Probes: two stainless steel pads on the FR4 board edge, HASL finish, 5mm spacing, conformally coated everywhere except the pads.
- Optional: TMP102 on I2C for freeze-watch (DNP on v1 SKUs, footprint reserved).
- Optional: SPDT tamper switch on lid, GPIO with internal pull-up, wake source.
- Debug: SWD header, depopulated on retail units. UART TX/RX on test pads for MFG.

## Power Budget

This is the load-bearing constraint. Every other decision flows from here.

Energy available: 2x AA alkaline at 2500 mAh usable down to 2.7V = ~5000 mAh at the pack level. Over 5 years (43,800 hours) the allowed average current is:

    5000 mAh / 43,800 h = 114 µA average

Allocation:
- MCU deep sleep (Stop2 on WL55, System OFF + RTC on nRF9151): < 2 µA
- Regulator Iq: < 0.1 µA
- Probe bias leakage (probes floating, weak pull): < 0.5 µA
- Wake-on-touch comparator (LPCOMP, always-on): < 1 µA
- TMP102 in shutdown when unused: 0.5 µA

Sleep floor target: < 4 µA. That leaves ~110 µA of average budget for active work.

Active events:
- Heartbeat: 1 LoRa TX/day at 14dBm, ~50ms airtime, ~30 mA peak. Energy per heartbeat: ~1.5 mC. Over 5 years: 1825 heartbeats = 2.74 C = 0.76 mAh. Negligible.
- Alarm event budget: 4 events/yr * 5 yr = 20 events. Each event: piezo on duty-cycled for 2h worst case, plus 6 radio retransmits. Piezo at 4kHz drawing ~3 mA RMS at 50% duty = ~1.5 mA average over 2h = 3 mAh per event. 20 events = 60 mAh lifetime. Acceptable.
- Self-test (probe impedance sweep): weekly, < 0.1 mAh per. Negligible.
- LTE-M attach (LM-CL only): this is the budget killer. Cold attach can pull 200 mA for 5+ seconds. Use PSM with TAU = 24h. Budgeted as one attach per heartbeat. If carrier won't honor PSM, the LM-CL SKU is 3-year battery life, not 5. Open question, see below.

Headroom: LM-LR has ~30% headroom on the 5-year number. LM-CL is tight, see open questions.

## Functional Requirements

FR-1. Detect water contact by measuring resistance between probes < 500 kΩ for > 200ms. Threshold tunable in flash config.

FR-2. On detection, fire piezo alarm. First 5 minutes continuous. After that, 60s on / 60s off duty until probes dry or battery low.

FR-3. Transmit alarm event over radio within 12 seconds of confirmed detection. LoRa SKU: confirmed uplink on FPort 10, retry 6x over 2 min if no ack. LTE-M SKU: UDP packet to fixed endpoint, 6 retries with backoff.

FR-4. Heartbeat every 24h ± 1h jitter. Payload: battery mV, last RSSI, temp (if present), uptime, alarm-event-count.

FR-5. Low battery: assert low-batt flag in heartbeat when Vbat < 2.85V under no load. Below 2.75V, switch to alarm-only mode (no more heartbeats, save energy for the one moment that matters).

FR-6. MFG test mode: enter on boot when TEST jumper pulled low at reset. Cycles piezo, radio TX at low power, probe self-test, temp sensor read, reports pass/fail over UART at 115200 8N1.

FR-7. OTA: LoRa SKU uses Class C receive window opened on demand, opt-in via paired bridge command. LTE-M SKU uses LwM2M firmware object. Both require dual-bank flash and signed images.

FR-8. Pairing state (bridge address, AppKey/DevEUI, network keys) persisted in flash and survives battery swap.

## Architecture

State machine, single super-loop in main, ISRs do the minimum:

States:
- BOOT: clocks, peripherals, restore pairing, enter SLEEP.
- SLEEP: Stop2 / System OFF. Wake sources: RTC alarm (heartbeat), LPCOMP (probe), GPIO (tamper, MFG jumper).
- SAMPLE: probe ADC + debounce. Either back to SLEEP or to ALARM.
- ALARM: piezo on, radio TX with retries, duty-cycle thereafter.
- HEARTBEAT: build packet, radio TX, back to SLEEP.
- OTA: only entered on explicit command, exclusive of other states.
- SELFTEST: weekly probe impedance check + RAM scrub.
- FAULT: watchdog reset path, latch reason in backup register, attempt one recovery before hard reset.

ISR vs main:
- ISRs: RTC, LPCOMP wake, radio DIO, UART RX (MFG only). All ISRs set flags or push to a 16-deep event queue. No work in ISR context beyond clearing the source.
- Main loop: drains the event queue, advances the state machine, pets watchdog at exactly one point (top of main loop).

Peripherals:
- LPCOMP (or COMP1 on WL55) on probe input, internal Vref/4 threshold, wake from Stop2.
- ADC for confirmed probe measurement and battery voltage divider.
- TIM with complementary PWM on piezo pin.
- LPUART for MFG.
- I2C1 for TMP102 (when populated).
- Radio peripheral: SUBGHZSPI internal on WL55, modem AT/IPC on nRF9151.

## Firmware Footprint Targets

Flash, 256KB total:
- Bootloader (MCUboot or vendor equivalent): 32KB
- Slot A (running app): 96KB target, 112KB hard ceiling
- Slot B (OTA staging): 96KB target, must match Slot A size
- Config + pairing (flash KV, wear-leveled): 16KB
- Reserve: 16KB

Application max: 96KB. Today's prototype is 71KB with LoRa stack. Headroom 25KB.

RAM, 64KB total:
- Stack: 8KB
- LoRaMAC / modem buffers: 12KB
- App state, event queue, KV cache: 8KB
- Radio packet buffers: 4KB
- Heap: forbidden. No malloc after init. Static allocation only.

## Boot and OTA Strategy

Bootloader: MCUboot in swap-move mode. Image headers signed with ed25519. Public key fused at MFG.

Boot flow: ROM -> MCUboot -> verify Slot A -> jump. If verify fails or boot-fail counter > 3, revert to last-known-good and latch fault.

OTA flow (LoRa): paired bridge sends OTA_START on FPort 200. Device opens Class C window for N minutes, receives image fragments over FUOTA (LoRaWAN FUOTA spec where supported, custom chunked protocol otherwise), writes to Slot B. On complete and hash match, sets pending-swap, requests confirmed reboot from bridge, swaps on next boot.

OTA flow (LTE-M): standard LwM2M /5 firmware object, push from server, same swap mechanic.

Rollback: if post-swap boot fails to mark "confirmed" within 60s of running new image, MCUboot reverts on next reset.

## Failure Modes and Watchdog Strategy

IWDG: 4-second timeout. Petted exactly once per main loop iteration, at the top. Never petted inside ALARM TX retry loop, which must complete within 3 seconds per attempt or be killed.

Failure modes considered:

1. Probe corrosion. Mitigation: stainless HASL pads, weekly impedance self-test, if baseline impedance drifts more than 4x from factory value, set degraded-sensor flag in heartbeat. Probes are not user-serviceable, this is a diagnostic for warranty.

2. Battery brownout during alarm TX. Mitigation: bulk cap (220µF tantalum) on regulator output sized for one TX burst. Detect VBAT sag mid-TX, abort, retry on backoff.

3. Battery hot-swap. Pairing keys live in flash, not RAM. RTC state is lost and that is fine. On first boot after a swap, send a synthetic boot event so the bridge knows.

4. Stuck-on alarm (probe shorted by debris). After 4 hours of continuous detection, drop to one-alarm-burst-per-hour and one TX every 6 hours until clear, to preserve battery for the next real event.

5. Radio lockup. If radio peripheral doesn't return to idle within timeout, full radio peripheral reset, then full MCU reset on second occurrence within an hour.

6. Flash bit rot in KV region. Wear-leveled KV with CRC per record. Boot scrubs and rewrites any record failing CRC from its mirror.

## Manufacturing Test Mode

Entry: TEST pad held low at reset (jumpered on test fixture, never present on retail unit).

Sequence, reported over UART:
1. Vbat read, must be 2.95V to 3.20V on fresh cells.
2. Probe impedance with test fixture shorting bar: pass if < 1 kΩ.
3. Probe impedance open: pass if > 5 MΩ.
4. Piezo: 200ms tone at 4kHz, sound level mic on fixture confirms > 85dB at 10cm.
5. Radio TX at minimum power, fixture receiver confirms RSSI within window.
6. TMP102 read (if populated), must be within 5°C of fixture ambient.
7. Write serial number, region config, DevEUI, AppKey to flash KV.
8. Print PASS or FAIL <reason> and halt. Operator power-cycles to exit.

## Constraints

Regulatory:
- FCC Part 15 unintentional radiator (both SKUs).
- FCC Part 15.247 for the LoRa SKU radio.
- FCC Part 22/24 for the LTE-M SKU (carrier-driven, modem is pre-certified, device-level still required).
- CE / RED for EU. EN 300 220 (LoRa) or EN 301 908 (LTE-M).
- UL 916 (energy management equipment, water leak detector category).
- IC certification for Canadian market.

Environmental:
- Operating: -10°C to 50°C.
- Storage: -20°C to 60°C.
- Housing: IP67 for the electronics module. Probes are intentionally exposed (they have to be).

Firmware:
- < 192KB flash usable for the application image. Bootloader, second slot, config, and reserve consume the rest.
- < 56KB RAM usable for the application. No heap.
- No GPL'd code in shipping firmware. Permissive licenses only (MIT, BSD, Apache 2.0).
- All ISRs documented with worst-case execution time. Sum of WCET must not exceed 200µs.

## Non-Goals

- Not WiFi. Power budget makes this a non-starter on AA alkaline.
- Not BLE-primary. Range through basement walls is poor and the link is too session-oriented for an event device. BLE is provisioning-only on the LTE-M SKU and absent on the LoRa SKU.
- Not water flow measurement. Different sensor, different product, different cert path.
- Not freeze detection in v1. TMP102 footprint is on the board, firmware path is a stub returning fixed values. Light up in v1.1.
- Not user-replaceable probes. Replacement is whole-device. Warranty replacement at year 5.
- Not "smart." No ML, no anomaly detection, no learned baselines. A wet probe is a wet probe. The day this product gets clever is the day it misses a leak.

## Success Signal

- 5-year field battery life P50 on the LoRa SKU, validated by accelerated discharge + 12-month field beta.
- < 12s end-to-end alarm-to-notification latency, P95, on the LoRa SKU in a real basement (one wall to bridge).
- Zero brick rate in OTA, validated by 1000-device staged rollout before broad release.
- BOM at $13.20 or below at 100k volume.
- UL 916 and FCC certs in hand 90 days before retail launch.

## Open Questions

1. LTE-M PSM behavior across carriers. T-Mobile and Verizon honor PSM differently. If we can't get a 24h TAU on both, LM-CL is a 3-year device, not 5. Need carrier conformance test before committing to the spec on the box.
2. STM32WL55 vs WLE5: WLE5 is cheaper but smaller flash. Re-evaluate after Slot A footprint stabilizes.
3. Piezo sound-level requirement for UL 916: confirm 85dB at 10cm meets the audible alarm clause, or whether we need 75dB at 3m, which is a different driver circuit.
4. OTA over LoRa FUOTA: standardize on the LoRa Alliance FUOTA spec or ship a custom protocol. Spec is heavier in flash, custom is faster but is a maintenance liability.
5. Probe self-test cadence: weekly is the current plan. Need to confirm with corrosion accelerated-life data, may need daily.
6. Tamper switch: ship or DNP. Adds $0.18 BOM. Insurance-channel customers want it, retail does not care.
