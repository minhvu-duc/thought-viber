---
name: vibe
description: Build a thought-viber beta fast inside the beta boundary. Use after a probe is confirmed, to vibe-code the feature behind a flag with telemetry and black-box acceptance tests, without anyone needing to read the code.
---

# vibe

Speed is the goal. Nobody will read this code, so the boundary — not the code — is what keeps it safe and disposable.

## Before building

1. Read `probe.md` (status must be `building`) and `.thought-viber/config.yaml`.
2. Make sure the boundary is enforced mechanically. If the repo has no check yet, add one and wire it into CI and the normal test command:
   - durable code must not import anything under `<beta_root>/`;
   - beta code may import durable code only from `public_interfaces`.

   Use the stack's native tool (ESLint `no-restricted-imports` or dependency-cruiser for JS/TS, import-linter for Python, ArchUnit for the JVM, a CI script for others). A grep-based CI check is fine when nothing fits.
3. Do the durable prep listed in probe.md (below) before writing beta code that depends on it.

## Durable prep

Sometimes the beta needs something durable code doesn't offer yet: a facade over logic that has no public interface, or an extension point where the feature changes existing behavior. Don't reach into internals and don't edit durable behavior from the beta. Instead:

1. Propose the smallest durable change:
   - **Facade:** a thin public function or endpoint over existing logic, with no new behavior.
   - **Extension point:** a slot, strategy or hook in durable code. Its default is exactly today's behavior; the beta registers an implementation that is used only when `beta.<feature>` is on.
2. It lands as its own normal change (PR), separate from the beta, reviewed and approved by a human like any durable code. Nothing beta-specific goes in it.
3. Add its path to `public_interfaces` in config and mark it done in probe.md → Durable prep.
4. Then build the beta against it.

The prep stays durable after the beta is reaped; `reap` lists it so the owner can keep or remove it.

## Boundary rules (non-negotiable)

- All feature code lives in `<beta_root>/<feature>/`. Wiring into the app (route registration, menu entry, DI binding, registering an extension) is the only allowed change outside it. Keep it to the fewest lines possible and mark each with a `thought-viber:beta:<feature>` comment so `reap` and `harden` can find it.
- Durable code never depends on beta code.
- Beta calls durable code only through `public_interfaces`. If one is missing, stop and do durable prep; never reach into internals.
- Beta data goes in its own namespace: `beta_<feature>_*` tables, collections or keys. If it must sit on existing core data, add new nullable `beta_<feature>_*` columns (or fields) and mark their migration `thought-viber:beta:<feature>`. Never alter, rename or drop an existing column. If neither works, stop and discuss with the user.
- Nothing in config's `safety_floor` list or under `floor_paths`.
- Everything behind flag `beta.<feature>`, default off. Turning the flag on is what makes deployed code live; turning it off is the kill switch and must hide the feature from every user path (instantly with a runtime `flag_system`, on the next deploy with a config one); for an extension point, flag off means the durable default runs. The code stays until `reap` deletes it or `harden` replaces it.
- A visible "Beta" label wherever users meet the feature.
- New dependencies only when necessary; prefer what the repo already uses.

## Build

1. Implement the smallest scope in probe.md. Cut anything that doesn't help answer the hypothesis.
2. Emit every telemetry event named in probe.md, with its listed properties.
3. Write black-box acceptance tests in `acceptance_dir` (from config, with `<feature>` filled in). They drive the feature only through its external surface (HTTP, UI, CLI, public API) and never import beta internals. One test per user-visible behavior, named for the behavior. These tests outlive the beta code: they become the spec the durable rebuild must pass.
4. **Register them with the project's test runner** so the normal test command and CI run them (for example a jest `projects`/`roots` entry, a pytest `testpaths` entry, a Playwright project, a Gradle source set). Confirm by running the normal test command and checking the acceptance tests appear in its output. Mark the registration line `thought-viber:beta:<feature>` like any other wiring.
5. Keep a feedback loop: run the tests and the build, take screenshots for UI, and iterate until green.
6. Also prove: flag off ⇒ feature gone (or durable default restored); every event fires.
7. Write `<artifacts_root>/<feature>/build-notes.md`: files touched outside `<beta_root>` (with their markers), durable prep used, how the acceptance tests are registered, the data namespace, events, and known shortcuts. This is for `reap` and `harden`, not for review.
8. Hand off to `gate`. Never turn the flag on or mark the beta live yourself; that is `launch`.
