# shhistory

Lint your shell history for leaked secrets before someone else does.

## Why

I keep pasting API keys into my terminal. Not on purpose, obviously. It happens
when I am debugging a curl command, or when a script prints an env var, or when
I copy a connection string from a teammate's Slack and run it raw. Then it sits
in my `.zsh_history` forever, syncing to every new machine I set up, waiting to
get screenshared during a pairing session. I want something that just looks at
my history files, flags the obvious stuff, and offers to scrub it without me
having to think.

## What it does

- Scans `.bash_history`, `.zsh_history`, and any `.env` files in your home
  directory for things that look like secrets.
- Recognizes the common shapes: AWS keys, GitHub tokens, OpenAI keys, Stripe
  keys, JWTs, generic high-entropy strings near the words "key", "token",
  "secret", or "password".
- Shows you each hit in context so you can decide whether it is actually a
  secret or just a false positive (looking at you, base64-encoded image data).
- Offers to redact in place, replacing the secret with a placeholder and
  keeping a timestamped backup of the original file.
- Can run in a read-only mode that just prints findings and exits, for the
  paranoid (correct) crowd.
- Optionally checks whether any flagged token is still live by hitting a
  known validation endpoint for that provider, so you know what to rotate
  first.
- Remembers what you have already triaged so re-running it does not re-prompt
  on stuff you already said was fine.

## Install

Coming soon. Probably a single binary, probably installable with one line.
I have not picked a package manager yet because I have not written any of it.

## Status

Pre-alpha. Not built yet. This is just a README to see if I want to live with
this idea for a week before I start typing. If I still want it on Friday, I
will start. If I have forgotten about it by then, that is also a useful
signal.

## Open questions I am still chewing on

- Does this need to be a long-running daemon that watches history in real time,
  or is a manual run plus a weekly cron enough? Leaning toward the second one.
  Daemons are a commitment.
- How aggressive should the default redaction be? Replace with a fixed string,
  or keep the first and last few characters so you can still recognize which
  secret it was when grepping later?
- Should it touch `.env` files at all, or is that scope creep? Shell history
  is the original itch. The `.env` thing is a bonus I keep talking myself
  into.
- Is there a friendly way to handle the case where the secret is part of a
  command someone might want to re-run? Probably not. If it is in your
  history and it is a secret, you have already lost. The tool just helps you
  lose less.

## Non-goals

- Not a full secret scanner for repos. `gitleaks` and `trufflehog` already
  exist and are good. This is the boring local-machine cousin nobody wrote.
- Not a password manager. Not a vault. Not trying to replace anything.
- Not going to phone home, ever. The whole point is that your history is
  embarrassing and private and should stay that way.
