---
feature: <slug>
status: proposed        # proposed | building | live | decided | hardening | durable | reaped
sponsor: <name>
created: YYYY-MM-DD
live_since:             # set by launch when the flag first turns on
expires:                # live_since + trial_days
trial_days: 21
extensions: 0
hardening_since:        # set by harden on start
hardening_due:          # hardening_since + max_hardening_days
flag: beta.<slug>
---

# Probe: <feature name>

## Problem
<the user pain or opportunity this tests>

## Hypothesis
If we ship <X>, then <Y users> will <Z>.

## Exposure
<segment / % rollout / who sees it>

## Metric
- Primary: <name> = <exact definition>
- Events: `beta.<slug>.<event>` — <when it fires, properties>
- Keep threshold: <value over the trial>
- Kill early if: <value>
- Pivot signals: <pattern>
- Minimum sample: <n users / sessions>

## Scope (smallest thing that answers the question)
- In:
- Out:

## Durable prep
<!-- Facades or extension points the beta needs that don't exist yet, or "none".
     Each lands as its own reviewed PR before vibe uses it. -->
| Need | Kind (facade / extension point) | Default behavior when flag is off | Status |
|---|---|---|---|

## Data
<what the beta stores: new `beta_<slug>_*` tables/collections/keys, or new `beta_<slug>_*` columns on existing core tables (which ones). Whether it must survive a rebuild.>

## Safety floor check
<checked against config `safety_floor` and `floor_paths`: how the feature stays off the floor, or what was cut to make it so>

## Amendments
<!-- YYYY-MM-DD — change — reason — approved by. Never edit thresholds above after confirmation. -->
