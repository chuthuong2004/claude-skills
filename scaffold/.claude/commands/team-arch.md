# Team · Architect (`/team-arch`)

Run the **Architect** stage: design the architecture, **or** verify upstream work (the harness's core move). Stages: G1 spec check, DESIGN, G3 approach check.

### Modes
- `/team-arch` — **design**: write `.claude/outputs/team/02-design.md` (template `ai-team-loop/assets/template-architecture-design.md`): components, the exact **FE↔BE interface contract**, schema, risks, "alternatives considered", and the **task split → assigned specialist** (`react-developer`/`vue-developer`/`react-native-developer`/`flutter-developer`/`nodejs-developer`/`python-developer`/`golang-developer`/generalist) per detected stack.
- `/team-arch --verify` — **G1**: is `01-spec.md` complete, consistent, buildable, with testable ACs? Route gaps to PM.
- `/team-arch --approach` — **G3**: read each engineer's approach note vs. the contract; `PROCEED` or `REVISE` (with the fix) before bulk code.

### Procedure
1. Read `.claude/config.md`, `docs/architecture/`, the relevant `.claude/outputs/team/` artifacts, and 2–3 sibling modules. Adopt `.claude/agents/team-loop/loop-architect.md`.
2. Do the mode's work; append a verdict line to the artifact under review.

### Next
- After DESIGN → `/team-verify` (G2 independent critique) → `/team-fe` + `/team-be`.

---
Architect stage for `$ARGUMENTS`. Reading the design inputs and `loop-architect.md`.
