# Team · Release (`/team-release`)

Run the **Release / DevOps** stage: build, deploy, verify post-deploy health, own the rollback. Runs only after the CEO approves and every gate is green.

### Procedure
1. Read `.claude/outputs/team/06-qc.md` (must be PASS), confirm `04-review.md`/`05-test.md` PASS. Read `.claude/config.md` → CI/CD + Infrastructure. Adopt `.claude/agents/team-loop/loop-release-engineer.md`.
2. **Preconditions (refuse otherwise):** all gates green · CI/CD `enabled: true` · clean tree · a rollback path exists.
3. Build → deploy via the project's mechanism → post-deploy health checks (service-up, critical-path smoke, error rate). Any fail → **rollback** + escalate.
4. Write `.claude/outputs/team/07-release.md` (template `ai-team-loop/assets/template-release-report.md`) including the rollback plan.

### Next
- Deployed + healthy → CEO writes `08-retro.md`.

---
Release for `$ARGUMENTS`. Verifying gates are green and reading `loop-release-engineer.md`.
