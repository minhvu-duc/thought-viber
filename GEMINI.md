# thought-viber

Skills for trying features as throwaway betas, watching how much people use them, and either reaping them or rebuilding them as permanent code.

When a request matches one of these skills, read its `SKILL.md` and follow it. Templates a skill mentions are in that skill's `templates/` folder.

| Skill | Use when | File |
|---|---|---|
| idea | Someone wants to try a feature idea as a beta | `skills/idea/SKILL.md` |
| vibe | A beta's idea is written and it needs building and shipping | `skills/vibe/SKILL.md` |
| watch | Someone asks how a beta is being used, or what betas exist and what's next | `skills/watch/SKILL.md` |
| verdict | A beta's watch window ends, or the numbers already make the call | `skills/verdict/SKILL.md` |
| reap | A beta was killed or abandoned and needs deleting | `skills/reap/SKILL.md` |
| transition | A beta was kept and needs a plan to become permanent code: intents, kept black-box tests, removal first | `skills/transition/SKILL.md` |
| plan | A feature will be built regardless of usage (sign-in, core schema, a requirement) | `skills/plan/SKILL.md` |
| design | A plan's next intent needs its spec | `skills/design/SKILL.md` |
| build | A specced intent needs building (or, for a kept beta, intent 00 removes the beta) | `skills/build/SKILL.md` |
| test | A built intent needs verifying and a PR | `skills/test/SKILL.md` |

Code under `beta/` or marked `thought-viber:beta:<feature>` belongs to a beta and may be removed. Don't build on it: warn the user and suggest a way around it (see `skills/idea/templates/agent-rules.md`).
