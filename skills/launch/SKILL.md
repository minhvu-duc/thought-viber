---
name: launch
description: Turn a gated thought-viber beta on for its probe's exposure and start its clock. Use after gate passes, when the team is ready to let users try the beta; checks the gate is current, turns the flag on (or has a human do it), confirms telemetry arrives, and sets live_since, expires and status live.
---

# launch

The beta's window starts the moment real users can reach it. `launch` makes that moment explicit, so `expires` and the verdict are measured from the right day.

## 1. Check it's ready

- probe.md has `status: building` and was confirmed by the sponsor.
- `gate.md` says PASS, and no file under `<beta_root>/<feature>/`, no marked wiring line and no acceptance test has changed since the gate's commit. If anything changed, stop and run `gate` again.
- Capacity: count `building`, `live` and `hardening` probes (this one included). Over `max_live_betas`, stop and say which ones to `verdict` or `reap` first.
- Telemetry: `telemetry.source` is set in config, or the user has said how they will report the numbers.

## 2. Confirm the exposure

Show the sponsor the Exposure section of probe.md (segment, % rollout) and the flag name. Turning a flag on in production is outward-facing: get an explicit yes from the sponsor before going further.

## 3. Turn the flag on

Set `beta.<feature>` to the confirmed exposure, and nothing wider, using the project's flag system:

- If a flag tool or CLI is connected and the user lets you use it, do it there.
- Otherwise give the user the exact change (the flag, environment, targeting rule) and wait for them to confirm it's done.

## 4. Smoke check

- Load the feature as a user inside the exposure (or ask the user to) and as one outside it: inside sees it with its Beta label, outside doesn't.
- Confirm at least one `beta.<feature>.*` event has arrived in `telemetry.source`.

If either fails, turn the flag off, leave status `building`, and report what failed.

## 5. Start the clock

In probe.md set:

- `status: live`
- `live_since:` today
- `expires:` `live_since` + `window_days`

Add a line to Amendments only if the exposure differs from what was confirmed: date, actual exposure, reason, approved by.

Tell the user the expiry date and that `verdict` runs then (or earlier for a kill-early read).

Turning the flag off early is always allowed. Record the date and reason in probe.md.
