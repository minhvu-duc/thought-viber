---
feature: <slug>
path: direct
status: proposed        # proposed | hardening | durable
owner: <product owner>
created: YYYY-MM-DD
confirmed:              # set when the owner confirms this brief
---

# Direct: <feature name>

## Outcome
<what is true for users when this is done>

## Why no beta
- Reason: <foundation | obligation | safety floor>
- <one or two sentences: who depends on it, or what requires it>

## What would stop us
<the honest answer to "is there a result that would make us not build this?". If the answer is about whether users will use it, this is a probe, not a direct feature.>

## Behaviors (in scope)
<!-- Short, testable from outside (HTTP, UI, CLI). Each becomes acceptance tests in a spec;
     harden assigns each to exactly one intent. -->
- B1:
- B2:

## Out of scope
<!-- With no usage data to trim the build, this list keeps it small.
     Anything not under Behaviors is out; name the tempting additions. -->
-

## Users and systems affected
-

## Constraints
<compliance, deadlines, compatibility, performance>

## Interfaces for betas
<durable interfaces this feature provides that future betas may call (they go into config `public_interfaces`), or "none">

## Safety floor
<floor items from config it touches, or "none". Each spec flags the matching policy owners.>

## Changes after confirmation
<!-- YYYY-MM-DD — change — reason — approved by. Never edit Behaviors or Out of scope above after confirmation. -->
