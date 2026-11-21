---
name: harden
description: Rebuild a proven thought-viber beta as durable, human-owned code through an AI-native SDLC. Use after verdict decides keep; splits the feature into small intents, runs each through intent, spec, plan, build, test and review with human gates, and deletes the beta slice by slice.
---

# harden

The beta proved the feature deserves to exist. Now rebuild it so a human owns it: every decision written down, every file explainable. AI drafts and does the work; humans approve at gates. The agent never approves its own work.

The loop follows Anthropic's AI-Native SDLC Playbook: each stage commits an artifact that the next stage reads, and the commit history records who asked for what, what the agent produced, and who approved it.

**Input:** `verdict.md` (decision: keep) and the evidence pack.
**Output:** durable code, the beta deleted, and an artifact trail per intent.

**Rebuild or adopt is the team's choice.** Per intent, the team may rebuild from the evidence pack or adopt beta code as the starting point. Either way the code moves out of `<beta_root>`, goes through the same gates, and a human owns it. Record the choice in `plan.md`.

When you start, set probe status to `hardening`, `hardening_since` to today and `hardening_due` to today + `max_hardening_days`.

```
<artifacts_root>/<feature>/harden/
  index.md                     # intents, order, dependencies, status
  01-<slug>/intent.md spec.md plan.md
  02-<slug>/...
```

## Phase 1 — Decompose into intents

Read the evidence pack and the beta code, then propose intents:

- **One intent = one user-visible outcome**, shippable on its own, owning specific acceptance tests from `behavior-spec.md`.
- **Only what was used.** Behaviors marked `drop` get no intent; they die with the beta.
- **Data and contract first.** Intent 01 is usually the durable data model, the migration from the `beta_<feature>_*` namespace, and the public interface. Other intents depend on it.
- **Small enough for one `plan.md` and one reviewable PR.** If not, split again.
- **Last intent closes out:** removes the flag, the Beta label and anything left in `<beta_root>/<feature>/`.

Write `harden/index.md` from `templates/index.md`. Every acceptance test not marked `drop` belongs to exactly one intent.

**Gate:** the product owner (usually the sponsor) approves the decomposition.

## Phase 2 — Per-intent loop

Run intents in dependency order. Intents with no dependency between them and no shared files can run in parallel, each in its own git worktree and branch. Start with 2–3 parallel sessions and add more only while reviewers keep up.

### 2.1 Intent — `intent.md`
Draft from `templates/intent.md`: problem, outcome, affected users and systems, constraints, acceptance tests owned, out of scope, open questions.
**Gate:** product owner accepts or edits it; commit it.

### 2.2 Spec — `spec.md`
From the accepted intent, in one session: behaviors in detail, interfaces and contracts, data, error cases, non-functional needs. Apply the org's policy skills present in the repo (security, compliance, UX, brand) and list every concern under Flags with an owner.
**Gate:** product owner and the named policy owners resolve every flag before build starts.

### 2.3 Plan — `plan.md`
Plan without editing anything (use the agent's plan or read-only mode if it has one): read the codebase only. The plan must be implementable by someone who never saw the conversation: files that change, order of work, risks, the tests that prove it, the exact "done" commands, and how this slice's beta code is removed.
**Gate:** an engineer approves. If implementation diverges later, update `plan.md` in the same commit.

### 2.4 Build
- **First commit:** bring this intent's acceptance tests from `acceptance_dir` into the durable test suite, pointed at the durable implementation. They should fail (if adopting beta code, they may pass once it moves; that's fine).
- **These tests are locked.** The agent may not edit, skip or delete them; if the agent supports hooks or file-protection rules, recommend one that blocks edits to them during harden. If a test is genuinely wrong, stop and ask the product owner, and record the change in `spec.md`.
- Implement per `plan.md`, following the project's agent instruction file (`AGENTS.md`, `CLAUDE.md`, `GEMINI.md` or equivalent). When the agent makes the same mistake twice, add a line to it.
- Bug found? Failing test first, commit, then fix.
- **Same change set:** reroute this slice from beta to durable and remove the beta version it replaces (rewritten or moved), including its marked wiring lines. Beta and durable code coexist only between slices, never for the same behavior.

### 2.5 Test
"Done" is verifiable: run the exact commands from `plan.md` and check for the expected healthy output. All locked acceptance tests, the full suite and the boundary check are green. For UI, compare screenshots against the spec over 2–3 rounds.

Then run a verifier in a fresh context (a subagent if your agent supports them, otherwise a new session). It checks the work against `intent.md`, `spec.md` and `plan.md` and reports; it does not fix.

### 2.6 Review
Open a PR (or prepare one) linking intent, spec and plan. Run the review passes — bugs, security, compliance with spec and plan — against the repo's `REVIEW.md` (create it from `templates/REVIEW.md` if missing; the tech lead owns it). Rank findings by severity. Findings do not approve or block on their own.
**Gate:** a code owner approves and can explain every changed file. Mark the intent done in `index.md`.

## Phase 3 — Close

When the last intent merges, confirm:

- nothing left in `<beta_root>/<feature>/`, no `thought-viber:beta:<feature>` markers (including the acceptance-test registration), no `beta.<feature>` flag;
- beta data migrated, and the `beta_<feature>_*` namespace dropped only after the owner confirms the migration;
- every incident in `feedback.md` has a regression test or eval;
- probe status set to `durable`.

Report: intents merged, what was dropped from the beta and why, and who owns each part.

## Gates

| Stage | Artifact | Approver |
|---|---|---|
| Decompose | `index.md` | Product owner |
| Intent | `intent.md` | Product owner |
| Spec | `spec.md` with flags resolved | Product owner + policy owners |
| Plan | `plan.md` | Engineer |
| Test | Verifier report | Automated |
| Review | PR | Code owner |
