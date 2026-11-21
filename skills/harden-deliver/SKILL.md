---
name: harden-deliver
description: Deliver one defined thought-viber harden intent — plan it, build it against locked acceptance tests, verify it, delete the beta slice it replaces, and take it through code review. Use when harden's index shows an intent at plan, build, test or review, or when someone asks to plan, build or review a hardened intent.
---

# harden-deliver

The engineering side of one intent: turn an approved spec into reviewed, merged durable code, and delete the beta code it replaces in the same change. The agent never approves its own work.

**Input:** the intent's accepted `intent.md` and `spec.md` (every flag resolved). If either is missing or unapproved, stop and send the user to `harden-define`.
**Output:** a merged PR, the beta slice deleted, and the intent marked `done` in `index.md`.

Pick up where the intent stands in `index.md`: `plan`, `build`, `test` or `review`.

## 1. Plan — `plan.md`

Plan without editing anything (use the agent's plan or read-only mode if it has one): read the codebase only. Draft from `templates/plan.md`. The plan must be implementable by someone who never saw the conversation: files that change, order of work, risks, the tests that prove it, the exact "done" commands, and how this slice's beta code is removed.

**Gate:** an engineer approves. Commit it and set status `build`. If implementation diverges later, update `plan.md` in the same commit.

## 2. Build

- **First commit:** bring this intent's acceptance tests from `acceptance_dir` into the durable test suite (without a beta: write the tests `spec.md` defines), pointed at the durable implementation. They should fail.
- **These tests are locked.** The agent may not edit, skip or delete them; if the agent supports hooks or file-protection rules, recommend one that blocks edits to them. If a test is genuinely wrong, stop and ask the product owner, and record the change in `spec.md`.
- Implement per `plan.md`, following the project's agent instruction file (`AGENTS.md`, `CLAUDE.md`, `GEMINI.md` or equivalent). When the agent makes the same mistake twice, add a line to it.
- Bug found? Failing test first, commit, then fix.
- **Same change set:** reroute this slice from beta to durable and delete the beta code it replaces, including its marked wiring lines. Beta and durable code coexist only between slices, never for the same behavior.
- **Data intent:** in the same change set, migrate the beta data and switch the beta code still running to the durable interface, per the Cutover section of `spec.md`. Prove it: the acceptance tests of slices not yet hardened still pass against the migrated data, and nothing writes to `beta_<feature>_*` after the cutover.

Set status `test`.

## 3. Test

"Done" is verifiable: run the exact commands from `plan.md` and check for the expected healthy output. All locked acceptance tests, the full suite and the boundary check are green. For UI, compare screenshots against the spec over 2–3 rounds.

Then run a verifier in a fresh context (a subagent if your agent supports them, otherwise a new session). It checks the work against `intent.md`, `spec.md` and `plan.md` and reports; it does not fix. Fix what it finds, rerun, and set status `review`.

## 4. Review

Open a PR (or prepare one) linking intent, spec and plan. Run the review passes — bugs, security, compliance with spec and plan — against the repo's `REVIEW.md` (create it from `templates/REVIEW.md` if missing; the tech lead owns it). Rank findings by severity. Findings do not approve or block on their own.

**Gate:** a code owner approves and can explain every changed file. After merge, mark the intent `done` in `index.md` and tell the user to run `harden` for what comes next.
