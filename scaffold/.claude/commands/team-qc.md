# Team · QC (`/team-qc`) — Gate 6

Run the **QC** stage: verify each acceptance criterion against the running app, with evidence.

### Procedure
1. Read `.claude/outputs/team/00-brief.md` (the `AC-n` checklist), `01-spec.md`, the impl reports, `.claude/config.md` (E2E tool, entry URLs, critical_paths, auth), `.claude/shared/procedures.md`. Adopt `.claude/agents/team-loop/loop-qc.md`.
2. For each `AC-n`, run the proving scenario and capture evidence under `.claude/outputs/team/evidence/<slug>/`. Mark ✔ only with evidence; ✘ → bug report `BR-0n` (repro, expected/actual, evidence, route).
3. Critical-path smoke E2E; record console errors. Write `.claude/outputs/team/06-qc.md` (template `ai-team-loop/assets/template-qc-report.md`) with the AC table fully marked.
4. **Gating rule:** if the app/E2E tool can't run, do **not** PASS — warn and escalate.

### Next
- PASS (all ACs green) → `/team-release` (after CEO ship approval). · FAIL → route to the owning role.

---
QC for `$ARGUMENTS`. Reading the acceptance criteria in `00-brief.md`.
