---
name: harden-define
description: Define one thought-viber harden intent — write its intent.md and spec.md and take each through its human approval. Use when harden's index shows an intent at todo, intent or spec, or when someone asks to write the intent or spec for a hardened or direct feature.
---

# harden-define

The product side of one intent: what the user gets (`intent.md`) and exactly how it behaves (`spec.md`). Both are approved by humans before any plan or code. The agent never approves its own work.

**Input:** `harden/index.md` (the intent's row: outcome, acceptance tests owned, dependencies), the evidence pack, and probe.md.
**Output:** an accepted `intent.md` and a `spec.md` with every flag resolved, in `harden/NN-<slug>/`. The intent's status in `index.md` becomes `plan`.

**Direct feature** (started with `direct`, no beta): the inputs are the intent's row in `index.md` and `brief.md`, in place of the evidence pack and probe.md. The intent owns behaviors from the brief rather than existing acceptance tests, so the spec defines the acceptance tests, one or more per behavior. Constraints and out of scope come from the brief; the intent may narrow them, never widen them. There's no `beta_<feature>_*` data to migrate and no cutover.

Pick up where the intent stands: no `intent.md` → start at 1; `intent.md` accepted → start at 2.

## 1. Intent — `intent.md`

Draft from `templates/intent.md`: problem, outcome, affected users and systems, constraints, acceptance tests owned, out of scope, open questions. Take the acceptance tests owned from the intent's row in `index.md`; use `usage-map.md` and `feedback.md` for the problem and constraints.

**Gate:** the product owner accepts or edits it. Record who and when, commit it, and set the intent's status to `spec`.

## 2. Spec — `spec.md`

From the accepted intent, in one session, draft from `templates/spec.md`: behaviors in detail (each linked to its acceptance test), interfaces and contracts, data (including migration from `beta_<feature>_*` and, for the data intent, the cutover: how the remaining beta code moves to the durable store so there is one source of truth), error cases, non-functional needs.

Apply the org's policy skills present in the repo (security, compliance, UX, brand). List every concern under Flags with the owner who must resolve it.

**Gate:** the product owner and each named policy owner resolve every flag. Record resolutions in the Flags table, commit it, and set the intent's status to `plan`. Hand off to `harden-deliver`.

## Rules

- Don't start the spec until the intent is accepted, and don't hand off until every flag is resolved.
- If the spec shows the intent is too big for one plan and one PR, stop and go back to `harden` to split it.
- Changing a locked acceptance test needs the product owner's approval, recorded under "Changes to locked acceptance tests" in `spec.md`.
