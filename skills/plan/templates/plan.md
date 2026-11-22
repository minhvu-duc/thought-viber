---
feature: <slug>
from: plan              # plan | transition (a kept beta)
approved_by:
approved:               # YYYY-MM-DD
---

# Plan: <feature name>

## Outcome
<what's true for users when this is done>

## In scope
<!-- Short, testable from the outside. Each one belongs to exactly one intent. -->
- B1:
- B2:

## Out of scope
-

## Constraints
<compliance, deadlines, compatibility, performance>

## From the beta
<!-- transition only; leave out otherwise. -->
- Beta file: `.thought-viber/betas/<slug>.md`
- During the rebuild: <gap | held> — <why>
- Data move: <beta_<slug>_* → permanent store, by intent NN>

| Dropped behavior | Share of usage |
|---|---|

| Kept black-box test | Behavior | Owned by intent |
|---|---|---|

| Depends on the beta | How it works until rebuilt | Rebuilt by intent |
|---|---|---|

## Intents
<!-- One user-visible outcome each, small enough for one spec and one PR. -->
| # | Intent | Outcome | Covers | Depends on | Status |
|---|---|---|---|---|---|
| 00 | remove-beta | Beta code gone; kept tests and beta data stay (transition only) | — | — | todo |
| 01 | | | B1 | | todo |

Status: todo → design → build → test → done

## Changes
<!-- YYYY-MM-DD — change (including to a kept black-box test) — why — approved by -->
