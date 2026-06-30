# Team · Test (`/team-test`) — Gate 5

Run the **Tester** stage: static + build + unit + integration, with evidence.

### Procedure
1. Read `.claude/outputs/team/03-impl-*.md`, `01-spec.md`, `.claude/config.md` (`typecheck_cmd`, `lint_cmd`, `build_cmd`, `test_cmd`). Adopt `.claude/agents/team-loop/loop-tester.md`.
2. Run typecheck, lint, build, unit, integration — capture real output. Write missing tests (`T-0n`) for the new surface asserting the spec's ACs.
3. Write `.claude/outputs/team/05-test.md` (template `ai-team-loop/assets/template-test-report.md`) with commands + results; paste verbatim failing output and route it.
4. Verdict: `PASS` only with shown command output; never PASS if build/typecheck failed.

### Next
- PASS → `/team-qc`. · FAIL → back to `/team-fe` or `/team-be`.

---
Running tests for `$ARGUMENTS`. Reading `.claude/config.md` and `loop-tester.md`.
