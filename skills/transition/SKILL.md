---
name: transition
description: Plan how a kept thought-viber beta becomes permanent code. Use after verdict says keep; breaks the feature into small, clear intents, keeps the beta's black-box tests as the bar the permanent version must pass, and writes the plan the SDLC flow follows — first an intent that removes the beta code completely, then one per intent to build the full feature.
---

# transition

The beta proved people want the feature. Its code was written to be thrown away, and swapping it out piece by piece means beta and permanent code living side by side, which is hard to get right. So the beta is removed in one clean step, and the permanent version is built fresh, intent by intent. The beta's black-box tests tie the two together: the permanent version has to pass them, so users get the same feature.

`transition` is the `plan` step of the SDLC flow for a feature that started as a beta. Every intent in its plan, the removal first, goes `design` → `build` → `test`.

## 1. Gather

- The beta file: idea, footprint, watch log, verdict, "Depended on by".
- The beta code and its black-box tests. They show what the feature does; they aren't a starting point for the permanent code.
- Usage by part, from the watch log. If it isn't broken down by part, run `watch` first.

## 2. Break it into intents

- **Keep and drop.** List what the beta does, with each part's share of usage. Parts nobody used are dropped and die with the beta. The kept parts become the plan's in-scope behaviors.
- **One intent, one outcome.** Each intent is one thing a user can do or see when it's done, small and clear enough for one spec and one PR. If you can't say it in one sentence, split it.
- **Data first.** The first build intent is the permanent data model and moving the `beta_<feature>_*` data into it.
- Every kept behavior belongs to exactly one intent.

## 3. Keep the black-box tests

- For each kept behavior, keep the beta's black-box tests that cover it. If a behavior people rely on has none (check the watch log), write one now against the beta while it still runs. Tests for dropped behaviors go with the beta.
- Assign each kept test to the intent that owns its behavior.
- These tests are the bar: the permanent version passes them unchanged, so users see the same feature. Changing one needs the owner's OK, recorded in the plan.

## 4. Plan the removal

Intent 00 removes the beta completely, before anything permanent is built. It:

- moves the kept tests out of the beta folder into the permanent test suite, skipped with a note naming the intent that will turn each one back on;
- deletes the beta folder, every `thought-viber:beta:<feature>` marker and marked block (restoring edits to existing code from Footprint), the flag, the event definitions and beta-only dependencies;
- leaves the beta's data untouched, for the data intent to move;
- handles each entry under "Depended on by": how it works without the beta until the intent that rebuilds what it needs lands.

Ask the owner how users get through the rebuild, and record it:

- **gap:** the removal ships on its own and the feature is gone until the intents ship. Simplest; fine for a small feature or few users.
- **held:** the removal and the intents build up on one branch and merge together, so users never lose the feature.

## 5. Write and approve

Write `.thought-viber/features/<feature>/plan.md` from the `plan` skill's template (`plan/templates/plan.md`), with `from: transition`, "From the beta" filled in, intent 00 for the removal and the build intents after it.

The owner approves the plan. Then `design` intent 00. When the last intent is done, `test` sets the beta's status to `permanent`.
