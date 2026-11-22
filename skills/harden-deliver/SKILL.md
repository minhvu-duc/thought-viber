---
name: harden-deliver
description: Deliver one defined thought-viber harden intent — build it from its spec against locked acceptance tests, verify it, delete the beta slice it replaces (if any), and take it through code review. Use when harden's index shows an intent at build, test or review, or when someone asks to build or review a hardened or direct intent.
---

# harden-deliver

The engineering side of one intent: turn an approved spec into reviewed, merged durable code, and delete the beta code it replaces in the same change. The agent never approves its own work.

**Direct feature** (started with `direct`): there is no beta, so skip everything about rerouting, deleting beta code and migrating beta data. The rest is unchanged.

**Input:** the intent's accepted `intent.md` and `spec.md` (every flag resolved). If either is missing or unapproved, stop and send the user to `harden-define`.
**Output:** a merged PR, the beta slice deleted, and the intent marked `done` in `index.md`.

Pick up where the intent stands in `index.md`: `build`, `test` or `review`.

## 1. Build

- **First commit:** bring this intent's acceptance tests from `acceptance_dir` into the durable test suite (direct feature: write the tests `spec.md` defines, straight into the durable suite), pointed at the durable implementation. They should fail.
- **These tests are locked.** The agent may not edit, skip or delete them; if the agent supports hooks or file-protection rules, recommend one that blocks edits to them. If a test is genuinely wrong, stop and ask the product owner, and record the change in `spec.md`.
- Implement what `spec.md` says and only that, following the project's agent instruction file (`AGENTS.md`, `CLAUDE.md`, `GEMINI.md` or equivalent). When the agent makes the same mistake twice, add a line to it.
- If the implementation needs to differ from `spec.md`, stop and ask the product owner, and record the change in `spec.md` before going on.
- Bug found? Failing test first, commit, then fix.
- Direct feature: if this intent builds an interface listed under "Interfaces for betas" in `brief.md`, put it at a path that follows config's `public_interface_convention` and add that path to `public_interfaces`. If it builds code listed under "Safety floor", add those paths to `floor_paths`. Both in the same change.
- **Same change set:** reroute this slice from beta to durable and delete the beta code it replaces, including its marked wiring lines. Beta and durable code coexist only between slices, never for the same behavior.
- **Data intent:** in the same change set, migrate the beta data and switch the beta code still running to the durable interface, per the Cutover section of `spec.md`. Prove it: the acceptance tests of slices not yet hardened still pass against the migrated data, and nothing writes to `beta_<feature>_*` after the cutover.

Set status `test`.

## 2. Test

"Done" is verifiable: all locked acceptance tests, the full suite and the boundary check are green. For UI, compare screenshots against the spec over 2–3 rounds.

Then run a verifier in a fresh context (a subagent if your agent supports them, otherwise a new session). It checks the work against `intent.md` and `spec.md` and reports; it does not fix. Fix what it finds, rerun, and set status `review`.

## 3. Review

Open a PR (or prepare one) linking intent and spec. Run the review passes — bugs, security, compliance with spec — against the repo's `REVIEW.md` (create it from `templates/REVIEW.md` if missing; the tech lead owns it). Rank findings by severity. Findings do not approve or block on their own.

**Gate:** a code owner approves and can explain every changed file. After merge, mark the intent `done` in `index.md` and tell the user to run `harden` for what comes next.
