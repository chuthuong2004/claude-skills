# Team · CEO intake (`/team-ceo`)

Run the **CEO** stage alone: frame a goal and write the brief with testable acceptance criteria. Stage 1 of the team loop; the human reviews the brief before `/team-pm`.

### Procedure
1. Read `.claude/config.md`. Adopt `.claude/agents/team-loop/loop-ceo.md`.
2. Frame `$ARGUMENTS` in one sentence. If ambiguous, use `AskUserQuestion` first.
3. Write `.claude/outputs/team/00-brief.md` (skill template `ai-team-loop/assets/template-ceo-brief.md`): goal, `AC-1…` (testable), non-goals, constraints, run mode (full/lite).
4. Stop for human review. End with `> VERDICT: READY | gate: intake | next: /team-pm`.

### Next
- Approve the brief → `/team-pm` (or `/team-loop` to run the rest autonomously).

---
Framing the goal for `$ARGUMENTS`. Reading `.claude/config.md` and `loop-ceo.md`.
