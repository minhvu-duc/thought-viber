---
name: gate
description: Check a thought-viber beta is safe to release before its flag turns on. Use after vibe finishes or after any change to beta code; runs deterministic boundary and safety checks plus an independent AI review and writes a pass/fail report.
---

# gate

Best effort, with a hard floor. Gate reports; it never fixes. Fixes go back through `vibe`, then gate runs again.

## 1. Deterministic checks — any failure is FAIL

- Boundary check passes: no durable → beta imports; beta imports only `public_interfaces`.
- Changes outside `<beta_root>/<feature>/` are only wiring lines marked `thought-viber:beta:<feature>`. Durable prep listed in probe.md is not part of the beta diff: it must already be merged through its own reviewed change.
- No changes to `floor_paths`, the core schema, or migrations outside the `beta_<feature>_*` namespace.
- No secrets in the diff (gitleaks or trufflehog if available, otherwise a pattern scan).
- New dependencies are listed and audited with the ecosystem's tool (npm audit, pip-audit, etc.); any high or critical = FAIL.
- Flag `beta.<feature>` exists and defaults off; the flag-off test passes.
- The telemetry test passes for every event in probe.md.
- The project's normal test command runs the acceptance tests in `acceptance_dir` (they appear in its output, count > 0), and they and the existing suite pass, with nothing skipped or deleted.

## 2. Independent review

Run the review in a fresh context that has not seen the build: a subagent if your agent supports them, otherwise a new session. Give it only the diff, probe.md and this checklist. It reports findings ranked by severity and does not edit code.

- **Authorization:** every new endpoint or action reuses existing auth; nothing bypasses it.
- **Input handling:** validation, injection (SQL, command, template), XSS, SSRF, path traversal.
- **Data exposure:** the beta reads and returns only what the feature needs; no cross-user or cross-tenant leakage.
- **Resource safety:** no unbounded queries or loops, pagination and timeouts present, nothing that can load durable systems heavily.
- **Failure isolation:** if the beta throws, the rest of the app keeps working.

Any critical or high finding = FAIL.

## 3. Report

Write `<artifacts_root>/<feature>/gate.md` from `templates/gate.md`: date, commit, PASS/FAIL, each check's result, and findings by severity.

- Never mark PASS with a critical or high finding open.
- A human may accept a medium or low finding; record who and why.

On PASS, tell the user the beta is ready and hand off to `launch`. Any later change to beta code needs a new gate before launch, or while live.
