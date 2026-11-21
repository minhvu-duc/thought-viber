---
name: verdict
description: Decide whether a thought-viber beta is kept, killed, pivoted or extended by comparing its usage to the probe's pre-set threshold. Use when a beta's window ends, when it expires, or for an early read; on keep, builds the evidence pack harden needs.
---

# verdict

## 1. Read the probe

Load probe.md. The threshold, kill criteria and minimum sample are the ones confirmed there, plus recorded amendments. Do not reinterpret them.

## 2. Get the numbers

Pull data from `telemetry.source` in config with whatever tool is connected (analytics connector, SQL, logs). If nothing is reachable, ask the user for the exact figures and record where they came from. Save the raw queries and results to `evidence/usage-raw.md`. Compute in code, not in prose.

## 3. Recommend

| Outcome | When |
|---|---|
| keep | primary metric ≥ threshold and sample ≥ minimum |
| kill | below threshold at window end, or kill criteria hit early |
| pivot | usage clusters on something other than the hypothesis (pivot signals) |
| extend | sample below minimum at window end and `extensions < max_extensions` |

A beta that expired with no verdict defaults to **kill**.

Write the recommendation and its evidence to `verdict.md` from `templates/verdict.md`. The sponsor decides; record their decision, name and date, and set probe status to `decided`.

## 4. Follow through

- **kill** → hand off to `reap`.
- **pivot** → start a new `probe` that links this one, then `reap` this beta (keep its data only if the new probe needs it).
- **extend** → update `expires`, increment `extensions`, and record an amendment.
- **keep** → build the evidence pack below, then hand off to `harden`.

## Evidence pack (keep only)

In `<artifacts_root>/<feature>/evidence/`:

- **`behavior-spec.md`** — every user-visible behavior, each linked to its acceptance test. Review the acceptance tests now: add tests for behaviors users actually rely on that aren't covered (use telemetry to find them), and mark tests for behaviors nobody used as `drop`.
- **`usage-map.md`** — each behavior or entry point with its share of usage. Anything below the sponsor's cut (default 5%) is a candidate to drop from the rebuild.
- **`data-shape.md`** — beta tables, keys and namespaced columns on core tables, their schema, row counts, and what must migrate into durable storage.
- **`feedback.md`** — incidents, bugs, support tickets and user comments from the window.

The sponsor reviews the pack. It is the input to `harden`; the beta code is only a reference.
