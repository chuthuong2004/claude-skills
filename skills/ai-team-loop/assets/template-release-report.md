# Release Report — <feature title>

> Owner: Release / DevOps · Reads: approved diff, `06-qc.md`, `00-brief.md`

- **Preconditions:** all gates green (G4/G5/G6 PASS) · CI/CD enabled · clean tree — <confirmed?>
- **Target:** <env> · **Strategy:** <rolling / blue-green / single>

## Deploy steps (executed)
1. `<cmd>` — <result>
2. `<cmd>` — <result>

## Post-deploy health checks
| Check | Command/URL | Result |
|-------|-------------|--------|
| Service up | `<healthcheck>` | PASS/FAIL |
| Critical path | <smoke> | PASS/FAIL |
| Error rate | <dashboard> | <baseline?> |

## Rollback plan
- Trigger: <condition> · Command: `<rollback cmd>` · Data: <reversible? / backfill notes>

---
> VERDICT: DEPLOYED | by: release-engineer | post-deploy: PASS | next: retro (ceo)
> (on fail) VERDICT: ROLLED-BACK | by: release-engineer | reason: <…> | escalate: human
