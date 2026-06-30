# Team · Review (`/team-review`) — Gate 4

Run the **Reviewer** stage: independent review of the working diff. The reviewer did not write the code.

### Procedure
1. Read `.claude/outputs/team/03-impl-*.md`, `02-design.md` (contract), `01-spec.md`, `.claude/shared/principles.md`, `.claude/config.md`. Read 2–3 sibling files before judging convention. Adopt `.claude/agents/team-loop/loop-reviewer.md`.
2. Review full changed files on two lenses — correctness/security and maintainability/convention. Each finding: `R-0n — file:line — defect — severity — route: <engineer> — fix`.
3. Write `.claude/outputs/team/04-review.md` (template `ai-team-loop/assets/template-review-report.md`).
4. Verdict: `PASS` (positive, evidenced) or `FAIL` with a routing block. A pass is never the absence of comments.

### Next
- PASS → `/team-test`. · Request-changes → back to `/team-fe` or `/team-be`.

---
Reviewing the diff for `$ARGUMENTS`. Reading the impl reports and `loop-reviewer.md`.
