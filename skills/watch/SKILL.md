---
name: watch
description: Catch up on how much users interact with a thought-viber beta, or see every beta and feature at a glance. Use any time while a beta is live, or when someone asks what betas exist, what's due or what to do next; pulls usage from telemetry, logs a dated snapshot, and says when a beta is due for verdict.
---

# watch

Looks and writes down what it saw. It never changes code, flags or data.

## One beta

1. Read the beta file and config. If `live_since` is empty, ask the user when the flag turned on, and set it and `watch_until`.
2. Pull usage from `telemetry.source` with whatever is connected: an analytics tool, SQL, logs. If nothing is reachable, ask the user for the figures and note where they came from. Compute in code, not by eye.
3. Report, since `live_since`:
   - people who saw it (if known), people who used it, people who came back and used it again;
   - uses per week, and whether that's rising or falling;
   - which parts get used and which don't (transition uses this to decide what to drop);
   - anything odd: errors, sudden spikes, use from outside the audience;
   - how it reads against "Keep if". A reading, not a decision.
4. Append a dated snapshot to "Watch log", with the queries used so the next run can repeat them.
5. Say what's next: keep watching (days left), or `verdict` if `watch_until` has passed or the numbers already make the call obvious.

## Everything

With no feature named, read every `.thought-viber/betas/*.md` and `.thought-viber/features/*/plan.md` and show one line each: name, status, owner, days left or next intent, latest usage, and what to do next.

Point out first:

- live betas past `watch_until` with no verdict → `verdict`, and recommend kill if it's been a while;
- killed betas not yet reaped → `reap`;
- kept betas with no plan yet → `transition`;
- betas with entries under "Depended on by", since reaping them will need care;
- plans with an intent waiting on someone: `design`, `build` or `test` for the next one.

If nothing needs attention, say so in one line.
