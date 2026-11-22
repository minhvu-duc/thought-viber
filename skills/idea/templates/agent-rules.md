## Beta code (thought-viber)

Code under `beta/` and code marked with a `thought-viber:beta:<feature>` comment was written for a beta. The beta may be reaped, and that code deleted with it.

- Only the same beta may import, call or extend its code.
- If a task needs beta code, stop and tell the user which beta it belongs to and where it stands (`.thought-viber/betas/<feature>.md`), then suggest one of:
  1. **Write your own.** Reimplement or copy the small piece you need into permanent code. Usually the cheapest.
  2. **Wait for the verdict**, if it's close.
  3. **Promote that piece now.** Move it into permanent code, remove its marker, and have the beta use the permanent version.
  4. **Use it anyway**, if the user accepts the risk: mark the using code `thought-viber:uses-beta:<feature>` and add it under "Depended on by" in the beta's file, so reaping or transitioning the beta deals with it.
- Beta code written outside `beta/<feature>/` gets a marker comment on the line above it, or `begin`/`end` markers around a block:
  `thought-viber:beta:<feature> — written for the <feature> beta; may be removed. Don't build on it.`
