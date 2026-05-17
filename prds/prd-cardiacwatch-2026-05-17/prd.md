# CardiacWatch — Research Initiative PRD

**Document type:** Research initiative brief / protocol summary
**Principal Investigator:** [PI, MD/PhD], Division of Cardiac Electrophysiology
**Co-Investigators:** [Co-I, MD] (cardiology), [Co-I, PhD] (biomedical informatics), [Co-I, MS] (biostatistics)
**Institution:** [Academic Medical Center], Department of Medicine
**Prepared for:** Institutional Research Review Committee
**Date:** 2026-05-17
**Status:** Draft for committee review — Phase 1 IRB approval pending (anticipated Q3 2026)

---

## 1. Executive Summary

CardiacWatch is a two-phase research program investigating whether passive, continuously collected smartphone-and-wearable signals can prospectively identify atrial fibrillation (AFib) episodes 24 hours before clinical onset. Phase 1 is an IRB-approved 200-participant prospective observational study at a single academic medical center. Phase 2, contingent on Phase 1 reaching its pre-specified statistical threshold, will develop a participant-facing notification application and pursue an FDA De Novo clearance pathway.

This document summarizes the hypothesis, prior art, research questions, study design, analytic plan, regulatory posture, system capabilities required, and pre-specified success criteria. It is not a product specification; it is a research protocol summary written in PRD form to support institutional review and infrastructure planning.

---

## 2. Hypothesis

**H1 (primary, falsifiable):** In adults with a prior documented history of paroxysmal AFib, a composite signal derived from (a) Apple Watch resting heart rate trajectories, (b) overnight heart rate variability (HRV) measures, and (c) accelerometer-derived micro-tremor features during sedentary windows, can predict an AFib episode within the subsequent 24-hour window with sensitivity ≥ 0.75 and specificity ≥ 0.85.

**H0 (null):** The composite signal performs no better than the participant-specific resting heart rate baseline alone (AUROC delta ≤ 0.05).

The hypothesis is falsifiable on a pre-specified cohort, pre-registered statistical test (see §7), and pre-specified threshold. A failure to reject H0 will be reported as a negative finding and Phase 2 will not proceed.

---

## 3. Background and Prior Art

Atrial fibrillation is the most common sustained cardiac arrhythmia, with substantial associated morbidity from stroke and heart failure. Episode onset is frequently asymptomatic and intermittent, making both diagnosis and longitudinal monitoring difficult.

Relevant prior work informing this protocol:

- **Apple Heart Study (Perez et al., NEJM 2019).** Large-scale (~419,000 participants) consumer-grade study using irregular pulse notifications. Established feasibility of photoplethysmography (PPG) screening at scale but was not designed to predict future episodes — only to detect current irregularity.
- **Huawei Heart Study (Guo et al., JACC 2019).** Demonstrated PPG-based AFib screening feasibility in a non-US cohort with linkage to clinical follow-up.
- **mAFA-II trial (Guo et al., 2020).** Integrated mobile health intervention for known-AFib management; established that wearable-driven care pathways are tolerable to patients.
- **Fitbit Heart Study (Lubitz et al., 2022).** Algorithm-based AFib detection using consumer wearable PPG; reinforced that PPG-derived irregularity is detectable but did not address pre-episode prediction.
- **Tison et al. (JAMA Cardiology, 2018).** Deep learning on Apple Watch heart rate data to passively identify AFib; pioneered the methodological approach this study extends from detection to forecasting.

To our knowledge, no prospective study has tested whether multimodal passive signals — heart rate trajectory, sleep-window HRV, and accelerometer micro-tremor — can predict AFib onset on a 24-hour horizon in a population with established paroxysmal disease. CardiacWatch addresses that gap.

---

## 4. Research Questions

1. **RQ1 (primary).** Can a composite passive-signal model predict AFib onset in the subsequent 24 hours with the pre-specified sensitivity/specificity thresholds (§2)?
2. **RQ2.** Which individual signal stream (resting HR, HRV, micro-tremor) contributes the most predictive value, and is the composite meaningfully better than the best single stream?
3. **RQ3.** Does predictive performance differ by age band, sex, BMI category, or CHA₂DS₂-VASc score?
4. **RQ4.** What is the false-alarm rate experienced by participants over the 12-month observation window, and is it within a tolerance plausibly acceptable for a future notification product?
5. **RQ5.** What proportion of predicted episodes are subsequently confirmed by participant-worn ambulatory ECG patch (gold standard) within the 24-hour window?

---

## 5. Phase 1 — Observational Study

### 5.1 Design

Single-site, prospective, observational cohort study. 12-month per-participant observation window. No intervention is delivered. Participants continue standard cardiologist-directed care; the study layers passive data collection and intermittent ambulatory ECG patch confirmation on top.

### 5.2 Participants

Target enrollment: **n = 200**, with anticipated 15% attrition yielding ≥ 170 evaluable participants. Enrollment window: 6 months from IRB approval.

**Inclusion criteria:**
- Adults aged 22 to 80 years.
- Documented history of paroxysmal AFib (≥ 2 episodes in the prior 24 months, confirmed by 12-lead ECG or ambulatory monitor).
- Active Apple Watch user (Series 6 or later) OR willing to receive a study-provided device.
- Daily smartphone use; ability to comply with data-sync requirements.
- Capable of providing informed consent in English.

**Exclusion criteria:**
- Persistent or permanent AFib (per current ACC/AHA classification).
- Implanted cardiac rhythm device (pacemaker, ICD, CRT) — confounds passive signal.
- Active pregnancy or planned pregnancy during study window.
- Age < 22 years (pediatric and young-adult populations excluded; see §10).
- Severe tremor disorder (essential tremor, Parkinson disease) — confounds accelerometer micro-tremor channel.
- Cognitive impairment precluding consent or compliance.

### 5.3 Data Collection Plan

Per-participant, continuous for 12 months:

- **Heart rate:** Apple Watch native HR sampling, including resting HR (sampled per Apple's algorithm, typically every 2 to 10 minutes at rest, more frequent during activity).
- **Heart rate variability (HRV):** SDNN computed from overnight windows (00:00 to 06:00 local time); RMSSD as secondary.
- **Accelerometer:** Raw 3-axis accelerometer streams during sedentary windows (defined as ≥ 10 minutes stationary on-wrist), at 50 Hz. Used to derive micro-tremor spectral features.
- **Sleep stage and duration:** Apple Watch sleep classification.
- **Self-reported symptoms:** Brief in-app daily check-in (≤ 30 seconds), optional.
- **Confirmatory ECG:** Two 14-day ambulatory ECG patches per participant (months 3 and 9), plus event-triggered patches when feasible. Patch readings serve as gold-standard ground truth.
- **Clinical context:** Demographics, comorbidities, medications (extracted from EHR under HIPAA authorization).

Data sync target: at least once per 24 hours. Participants are not asked to interpret or act on any signal during Phase 1.

### 5.4 Endpoints

**Primary endpoint:** Sensitivity and specificity of the composite predictive model for AFib episode onset within 24 hours, with ECG-patch-confirmed episodes as ground truth.

**Secondary endpoints:**
- Discrimination (AUROC) of composite model vs. baseline-HR-only model.
- Per-signal-stream feature importance.
- Calibration (Brier score, calibration slope) on held-out folds.
- Median time-to-prediction before clinically-recorded onset.
- Subgroup performance (RQ3).

**Exploratory endpoints:** Participant-reported burden, data completeness rate, signal artifact rate per device generation.

### 5.5 Analysis Plan (plain English)

We will partition the cohort into a development set (70%) and a held-out test set (30%), stratified by AFib episode frequency. On the development set we will train a per-participant baseline (using only that participant's resting heart rate distribution) and a composite gradient-boosted model that ingests features from all three signal streams over rolling 72-hour windows. Hyperparameters are tuned by cross-validation within the development set only.

The composite model is then evaluated **once** on the held-out test set. Sensitivity and specificity are computed against the pre-specified thresholds (§2). A bootstrap procedure (n=10,000) yields 95% confidence intervals. The primary statistical test is whether the lower bound of the bootstrap 95% CI for sensitivity exceeds 0.75 and for specificity exceeds 0.85, with a pre-registered alpha of 0.01.

No model selection or threshold tuning is permitted on the test set. The pre-registered analysis plan will be filed with the IRB before any test-set evaluation.

---

## 6. Phase 2 — Notification App (Conditional)

**Trigger condition:** Phase 1 meets pre-specified primary endpoint thresholds.

Phase 2 develops a participant-facing notification application that surfaces a 24-hour-ahead probabilistic alert when the validated model indicates elevated AFib risk. The application is paired with a clinician dashboard for the participant's existing cardiologist. Phase 2 also initiates a confirmatory multi-site prospective study sized for FDA De Novo submission and engages contracted regulatory and quality-system support.

Phase 2 is not scoped or funded by this PRD beyond planning. A separate protocol, IRB submission, and budget will be authored if Phase 1 succeeds.

---

## 7. Regulatory Considerations

- **IRB.** Phase 1 protocol submitted to the institutional IRB; approval anticipated Q3 2026. The protocol is observational and non-interventional. No predictions are returned to participants in Phase 1.
- **HIPAA.** All identifiable data are stored within the institution's HIPAA-compliant research enclave. A Business Associate Agreement is in place with the cloud infrastructure provider. Apple HealthKit data flow under participant-granted authorization.
- **Informed consent.** Written informed consent covers passive data collection, ECG patch wear, EHR linkage, secondary use within the institution, and the right to withdraw without affecting clinical care.
- **Pre-registration.** The study will be registered on ClinicalTrials.gov prior to enrollment. The statistical analysis plan will be filed and version-locked before any test-set evaluation.
- **FDA.** A pre-submission (Q-Sub) conversation with FDA CDRH has been initiated regarding the eventual De Novo pathway for the Phase 2 device-software-function. FDA has been clear that Phase 1, as designed, is research and not a device study. Phase 2 will require predicate analysis, software-as-a-medical-device classification (likely Class II under De Novo), and a quality management system aligned with 21 CFR 820.
- **Data sharing.** A de-identified dataset will be prepared for controlled-access sharing post-publication, consistent with NIH data-sharing expectations.

---

## 8. System Capabilities

The research infrastructure must support eight capabilities. Each is stated as an intent plus a verifiable success criterion.

1. **Ingest Apple Watch data continuously.**
   *Intent:* Pull HR, HRV, accelerometer, and sleep streams from participant devices via HealthKit-authorized sync.
   *Success criterion:* ≥ 95% of enrolled participants have ≥ 80% expected daily data volume across the observation window.

2. **Store data securely under HIPAA.**
   *Intent:* Persist raw and derived signals in the institutional research enclave with full audit logging.
   *Success criterion:* Zero data-residency exceptions in quarterly compliance review; all access events traceable to a named investigator.

3. **Link to EHR clinical context.**
   *Intent:* Associate each participant's passive data with their authorized EHR slice (demographics, problem list, meds, prior rhythm studies).
   *Success criterion:* ≥ 98% of enrolled participants have a successfully reconciled EHR linkage at enrollment and at month 6.

4. **Support investigator queries over the cohort.**
   *Intent:* Provide a query and analysis surface (SQL plus notebook environment) for the research team, scoped to de-identified data by default.
   *Success criterion:* Three named co-investigators independently execute a representative cohort query in under 10 minutes during UAT.

5. **Support participant withdrawal.**
   *Intent:* Honor a withdrawal request by halting data collection, optionally purging identifiable records, and recording the withdrawal in the audit log.
   *Success criterion:* Withdrawal completes end-to-end in under one business day in 100% of test cases; identifiable-data purge verified.

6. **Confirmatory ECG patch ingestion and adjudication workflow.**
   *Intent:* Receive ambulatory ECG patch reports, surface them in a clinician-adjudication queue, and bind adjudicated episodes to the passive-signal timeline as ground truth.
   *Success criterion:* ≥ 95% of returned patches adjudicated within 14 days of return; binding to passive-signal timeline verified on a 10% audit sample.

7. **Pre-registered analysis lock.**
   *Intent:* Cryptographically version and freeze the analysis plan and model code prior to any test-set evaluation, with a tamper-evident record.
   *Success criterion:* The frozen analysis-plan hash matches the published pre-registration record at the time of primary analysis.

8. **Adverse event and safety reporting.**
   *Intent:* Provide a clear channel for participants and clinicians to report device-related skin reactions, anxiety, or any unanticipated problems; route to the PI and IRB per institutional SOP.
   *Success criterion:* All reportable events logged within 24 hours; IRB reporting timelines met in 100% of applicable cases.

---

## 9. Constraints

- **IRB approval timeline.** Full IRB approval is anticipated Q3 2026; enrollment cannot begin until approval is granted in writing. Any protocol amendment requires re-review.
- **FDA dialogue.** A Q-Sub conversation has been opened with FDA CDRH regarding the Phase 2 device pathway. Phase 1 is explicitly not a device study; we will not represent it as such in communications.
- **Funding.** $1.2M grant funding awarded for Phase 1 (NIH R01 supplement, with institutional cost-share). This budget covers personnel, ECG patches, study-provided wearables for ≤ 20% of participants, infrastructure, and analysis. Phase 2 is unfunded at the time of this writing.
- **Phase 1 window.** Total Phase 1 duration is 18 months: 3 months infrastructure build-out, 6 months enrollment, 12 months observation overlapping enrollment tail, and 3 months analysis and write-up. Per-participant observation is 12 months.
- **Single site.** Phase 1 is single-site; external validity is acknowledged as a limitation and addressed in Phase 2 planning.
- **Device dependency.** Apple Watch hardware and HealthKit API stability are external dependencies; mitigation includes documenting OS/firmware versions and reserving budget for one Series upgrade cycle.

---

## 10. Non-Goals

- **Not a consumer product.** No predictions, alerts, or interpretations are returned to participants during Phase 1. The mobile companion is a data-collection client only.
- **Not a replacement for diagnostic ECG.** Ambulatory ECG patch remains the in-study gold standard; clinical care continues to rely on standard-of-care ECG and clinician judgment.
- **Not enrolling pediatric populations.** Participants under 22 are excluded.
- **Not enrolling pregnant participants.** Pregnancy is an exclusion criterion for Phase 1.
- **Not a multi-arrhythmia detector.** Scope is limited to AFib. Detection of other arrhythmias (atrial flutter, SVT, VT) is out of scope and any incidental findings flow through standard clinical channels.
- **Not a stroke-prevention claim.** No claim of downstream stroke or mortality benefit is made or implied by this protocol.

---

## 11. Success Signal

The hypothesis-resolution moment for this initiative is reached when, on the locked held-out test set from the Phase 1 cohort, the following is true:

> We can predict an AFib episode 24 hours in advance with sensitivity ≥ 0.75 and specificity ≥ 0.85, in a 200-participant cohort, with statistical significance p < 0.01.

If met, Phase 2 is authorized to proceed. If not met, the negative result is published, the dataset is prepared for controlled-access sharing, and the program closes out without a Phase 2.

---

## 12. References (selected)

- Perez MV, et al. *Large-Scale Assessment of a Smartwatch to Identify Atrial Fibrillation* (Apple Heart Study). NEJM. 2019.
- Guo Y, et al. *Mobile Photoplethysmographic Technology to Detect Atrial Fibrillation* (Huawei Heart Study). JACC. 2019.
- Guo Y, et al. *Mobile Health Technology for Atrial Fibrillation Management* (mAFA-II). 2020.
- Lubitz SA, et al. *Detection of Atrial Fibrillation in a Large Population Using Wearable Devices* (Fitbit Heart Study). 2022.
- Tison GH, et al. *Passive Detection of Atrial Fibrillation Using a Commercially Available Smartwatch.* JAMA Cardiology. 2018.

---

*End of document.*
