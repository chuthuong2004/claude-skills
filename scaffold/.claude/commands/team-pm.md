# Team · PM spec (`/team-pm`)

Run the **Product Manager** stage alone: turn the brief into a buildable spec. Stage 2; gated by `/team-arch` (G1) next.

### Procedure
1. Read `.claude/config.md` and `.claude/outputs/team/00-brief.md`. Adopt `.claude/agents/team-loop/loop-product-manager.md`.
2. Write `.claude/outputs/team/01-spec.md` (template `ai-team-loop/assets/template-spec.md`): user stories (`US-n`), functional requirements, edge cases, refined testable acceptance criteria (`AC-n` mapped to the brief), open questions.
3. Describe **what/why**, never **how**. Flag ambiguity instead of guessing.
4. End with `> VERDICT: READY | gate: spec | open-questions: <N> | next: G1 (/team-arch --verify)`.

### Next
- `/team-arch --verify` (G1 spec check) → then `/team-arch` (design).

---
Writing the spec from the brief. Reading `00-brief.md` and `loop-product-manager.md`.
