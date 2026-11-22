---
name: probe
description: Start a thought-viber beta. Use when a dev wants to try a new feature idea fast before building it properly; writes the hypothesis, success metric, threshold, trial length and end date, and checks the feature is allowed to be a beta.
---

# probe

A beta is a probe, not a prototype: it exists to answer one question — does this feature deserve to exist? `probe` writes that question and its pass mark down before any code exists, so the answer can't be bent later.

If no usage result would stop the team building it (auth, the user model, a contractual or compliance requirement), there is no question to answer. Suggest `direct` instead.

## Steps

1. **Load config.** Read `.thought-viber/config.yaml`. If it's missing, create it from `templates/config.yaml`: detect the stack, propose `beta_root`, `public_interface_convention`, `public_interfaces`, `floor_paths`, `flag_system` and the telemetry source, show the default `safety_floor` list, and confirm all of them with the user. The user may add, remove or reword safety floor items. For `flag_system`, explain the trade-off and let the user choose: a runtime flag system switches the feature on and off instantly; flags in code or config only switch on the next deploy.

2. **Check capacity.** Count features whose probe status is `building`, `live` or `hardening` (beta code is still in the repo for all three). At `max_live_betas`, stop, list them, and suggest running `verdict` or `reap` on one first.

3. **Check past lessons.** Read `.thought-viber/LEARNINGS.md` if it exists. If a similar bet was already reaped, show the user that line before going further.

4. **Check eligibility (safety floor).** If the feature must change anything in config's `safety_floor` list or under its `floor_paths`, it is not beta-eligible. Say so and offer either a narrower version that stays off the floor, or building it as durable code with `direct` (no beta, no trial).

5. **Sharpen the hypothesis with the user.** Ask until each of these is concrete:
   - **Exposure:** which users see it (segment, % rollout).
   - **Hypothesis:** "If we ship X, then Y users will Z."
   - **Primary metric:** one number, and the telemetry event(s) that produce it.
   - **Keep threshold**, **kill-early criteria**, and **pivot signals** (a usage pattern that suggests a different need).
   - **Minimum sample** for the result to count.
   - **Trial length** in days: how long users get to try it (default from config).
   - **Sponsor:** the human who owns the decision (not the code).

   Steer away from vanity metrics: prefer repeat use, task completion and retention over clicks and views. A "Beta" label attracts curiosity clicks, so by default count a user only after their second meaningful use.

6. **Find durable prep.** Look at where the feature meets durable code:
   - **Additive feature** (new page, endpoint, panel): it only needs to call durable code. List any call that has no public interface yet.
   - **Changes existing behavior** (a different checkout step, ranking, form): the beta can't live in `<beta_root>` unless durable code offers a seam. List the extension point it needs: a slot, strategy or hook in durable code whose default is today's behavior and which the beta fills when its flag is on.

   Record each under Durable prep in probe.md. These are small durable changes, done once, before `vibe` (see vibe → Durable prep).

7. **Write** `<artifacts_root>/<feature>/probe.md` from `templates/probe.md` with `status: proposed`. Keep scope to the smallest thing that answers the question.

8. **Gate.** The sponsor confirms probe.md. On confirmation set `status: building` and hand off to `vibe`.

## Rules

- Thresholds are fixed once confirmed. Later changes go in the Amendments section with date and reason, never as silent edits.
- One probe tests one bet. If the idea is two bets, write two probes.
- `live_since` and `expires` are set by `launch` when the flag first turns on, not now.
