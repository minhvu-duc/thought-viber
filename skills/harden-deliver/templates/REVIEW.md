# REVIEW.md

Owned by: <tech lead>. Review policy for every PR, human or agent.

## Passes (every PR)
1. **Bugs** — logic errors, edge cases, error handling, concurrency.
2. **Security** — authz on every entry point, input validation, injection, secrets, data exposure.
3. **Compliance with spec** — the change does what `spec.md` says and only that; any divergence is recorded in `spec.md` with the product owner's approval.

## Severity
- **Critical** — data loss, security hole, broken core flow.
- **High** — wrong behavior users will hit.
- **Medium** — maintainability or edge-case risk.
- **Low / nit** — style; cap at 5 per PR.

## Rules
- Findings inform; they do not approve or block. A code owner approves.
- Locked acceptance tests must not change without a recorded approval in `spec.md`.
- The approver must be able to explain every changed file.

## Higher-risk changes (tech lead approval)
- Data migrations
- Public interface changes
- Anything on the safety floor
