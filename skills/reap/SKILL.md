---
name: reap
description: Delete a thought-viber beta — its folder, every marked line outside it, its flag, events and data — and record what was learned. Use after a kill verdict or whenever a beta is abandoned. Warns about anything that came to depend on the beta before deleting it.
---

# reap

Everything in `vibe` exists so this step is quick.

1. **Flag off.** Confirm `beta.<feature>` is off for everyone, or have the user switch it off.
2. **Find everything.** The beta folder; every `thought-viber:beta:<feature>` marker in the repo (search, then cross-check Footprint in the beta file); the flag; the event definitions; the data under `beta_<feature>_*` and anything else Footprint lists; dependencies only this beta used.
3. **Check what depends on it.** Search for `thought-viber:uses-beta:<feature>`, read "Depended on by", and look for imports of the beta folder from outside it. For each one, stop and tell the user what uses it and suggest a fix: rewrite that code so it doesn't need the beta, or promote the piece it needs into permanent code first. Don't delete code something else still uses.
4. **Data.** List what the beta stored, with row counts. Ask the owner whether to export it, keep it for a new idea, or drop it. Never drop user data without a yes.
5. **Delete.** The folder, every marked line and block, edits to existing code (restore them using the undo notes in Footprint), the flag, the events, unused dependencies, the beta's tests, and a migration that drops the beta data once the owner has said so.
6. **Prove it's gone.** No `thought-viber:beta:<feature>` marker left, and the build and full test suite are green.
7. **One commit or PR:** `reap: <feature>`.
8. **Record the lesson.** Set `status: reaped`, add what was learned under "Verdict", and append one line to `.thought-viber/LEARNINGS.md`:
   `YYYY-MM-DD | <feature> | <one-sentence lesson>`

   `idea` reads this file, so the team doesn't re-run a bet that already lost.
