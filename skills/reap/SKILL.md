---
name: reap
description: Delete thought-viber betas that were killed, pivoted away from, or expired without a verdict, and enforce the live-beta limit. Use on a schedule or after a kill or pivot verdict; removes beta code, wiring, flags and data, and keeps the probe as a recorded lesson.
---

# reap

The default outcome for any beta is deletion. `reap` makes deletion cheap and routine.

## 1. Find candidates

Scan `<artifacts_root>/*/probe.md`:

- `decided` with kill or pivot → reap.
- `live` and past `expires` with no verdict → run `verdict` first. If there is still no decision after `reap_grace_days`, recommend kill to the sponsor.
- `hardening` past `hardening_due` → report it to the sponsor. Don't reap it; the decision to keep was made.
- Report the live count (`building` + `live` + `hardening`) against `max_live_betas`.

## 2. Remove (per feature)

1. Confirm the flag `beta.<feature>` is off.
2. **Data:** list everything in the `beta_<feature>_*` namespace with row counts: tables, collections, keys, and namespaced columns on core tables (with how many rows hold a value). Ask the sponsor whether to export it, hand it to a pivot probe, or drop it. Never drop user data without explicit confirmation; exports go where the sponsor says.
3. Delete `<beta_root>/<feature>/`, a migration that drops the namespaced tables and columns (once the sponsor has decided on the data), every `thought-viber:beta:<feature>` wiring line (use `build-notes.md` and a repo-wide search), the flag definition, the telemetry event definitions, and beta-only dependencies nothing else uses.
4. Keep the acceptance tests in `acceptance_dir` as records, but remove their registration from the test runner (the marked line noted in `build-notes.md`). Confirm the normal test command no longer runs them.
5. Run the boundary check, the build and the full test suite; all must be green. If durable code referenced the removed code, the boundary leaked: stop and report it.
6. List any durable prep from probe.md (facades, extension points). It is durable code and stays by default; ask its owner whether to keep it for future use or remove it in a separate change.
7. Prepare one commit or PR titled `reap: <feature>`.

## 3. Record the lesson

- Add a closing section to `verdict.md`: final numbers, what was learned, date.
- Set probe status to `reaped`.
- Append one line to `.thought-viber/LEARNINGS.md`: `YYYY-MM-DD | <feature> | <outcome> | <one-sentence lesson>`.

Kept probes are how the team avoids re-testing ideas that already failed: `probe` should check LEARNINGS.md before a new bet.
