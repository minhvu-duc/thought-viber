---
name: status
description: Show every thought-viber feature at a glance — betas and direct features, with status, sponsor or owner, days left, gate result and what needs doing next. Use when someone asks what betas are live, what's expiring, what's overdue, or what to do next. Read-only; changes nothing.
---

# status

A read-only overview. It reports and suggests; it never edits files, flags or data.

## 1. Gather

Read `.thought-viber/config.yaml`, then every `<artifacts_root>/*/probe.md` (betas) and `<artifacts_root>/*/brief.md` (direct features), plus `gate.md`, `verdict.md` and `harden/index.md` where they exist. Use today's date for every "days left".

## 2. Table

One row per feature that is not `durable` or `reaped`, sorted by what needs attention first:

| Feature | Path | Status | Sponsor / owner | Clock | Gate | Next |
|---|---|---|---|---|---|---|

- **Path:** `beta` or `direct`.
- **Clock:** `live` → days until `expires` (negative if past); `hardening` → days until `hardening_due` (betas only) and intents done / total from `index.md`; otherwise blank.
- **Gate:** PASS / FAIL / none, and **stale** if beta code, marked wiring or acceptance tests changed after the gate's commit. Blank for direct features.
- **Next:** the one skill to run and why, from the attention list below.

Below the table: live count against `max_live_betas` (betas only: `building` + `live` + `hardening`; direct features don't count), and the number of `durable` and `reaped` features with a pointer to `LEARNINGS.md`.

## 3. Needs attention

Flag each of these, most urgent first:

- Over capacity.
- `live` past `expires` with no verdict → `verdict`; past `expires` + `reap_grace_days` → recommend kill to the sponsor.
- `live` within 3 days of `expires` → schedule `verdict`.
- `decided` kill or pivot not yet reaped → `reap`.
- `decided` keep not yet hardening → `harden`.
- `hardening` beta past `hardening_due` → tell the sponsor; the beta code is still in the repo.
- `hardening` otherwise (beta or direct) → the next intent from `index.md` and its skill: `harden-define` (todo, intent, spec) or `harden-deliver` (plan, build, test, review).
- direct `proposed` with `confirmed` set but no `harden/index.md` → `harden`.
- direct `proposed` not yet confirmed by the owner.
- `building` with a gate FAIL, or a stale gate → `vibe` fixes, then `gate`.
- `building` with gate PASS but not launched → `launch`.
- `proposed` not yet confirmed by the sponsor.

If nothing needs attention, say so in one line.
