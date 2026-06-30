# Handoff artifacts — the loop's shared memory

Every stage reads the prior artifacts and writes its own. A freshly-spawned subagent gets full context by **reading the folder**, not by being re-briefed in the prompt. This keeps each spawn cheap and each role stateless.

## Folder layout

All artifacts live under `.claude/outputs/team/` (create it on INTAKE):

```
.claude/outputs/team/
  00-brief.md        CEO    — goal, acceptance criteria, non-goals, constraints
  01-spec.md         PM     — user stories, functional spec, testable criteria
  02-design.md       Arch   — components, FE↔BE contract, schema, risks, task split + chosen specialists
  03-impl-fe.md      FE     — approach note (header) + what was built (FE / mobile lane)
  03-impl-be.md      BE     — approach note (header) + what was built (BE lane)
  04-review.md       Rev    — correctness/maintainability findings + verdict
  05-test.md         Test   — commands run, pass/fail, new tests added
  06-qc.md           QC     — per-criterion verdicts + bug reports + evidence
  07-release.md      Rel    — deploy steps, health checks, rollback plan
  08-retro.md        CEO    — what shipped, which gate caught what, learnings
  evidence/<slug>/   QC     — screenshots, recordings, logs
  history/<date>_<slug>/    — archived prior runs (see below)
```

For a feature with multiple producers in one lane (e.g. a web app + a mobile app), suffix the file: `03-impl-fe-web.md`, `03-impl-fe-mobile.md`.

## Naming & archiving

- One **run** = one feature through the loop. Before starting a new run, archive the previous one: move `0*-*.md` into `history/<YYYY-MM-DD>_<feature-slug>/` so the working folder always reflects the current run.
- The **slug** comes from the brief title (kebab-case, ≤40 chars). Fall back to `unknown`.

## Cross-role reference conventions

So a downstream role can act on an upstream finding without re-reading everything:

- **Stable IDs.** Acceptance criteria are `AC-1, AC-2, …` (set in the brief, never renumbered). Review findings are `R-01, …`; bug reports `BR-01, …`; test cases `T-01, …`. Downstream artifacts reference these IDs.
- **Verdict line.** Every gate artifact starts with a one-line machine-readable verdict so the CEO can route without parsing prose:
  ```
  > VERDICT: PASS | gate: G4 | by: reviewer | findings: 0 blocking, 2 minor | next: G5
  > VERDICT: FAIL | gate: G6 | by: qc | failed: AC-3, AC-5 | route: backend-engineer
  ```
- **Routing block.** On a failure, the verifier ends with an explicit routing block:
  ```
  ## Routing
  - AC-3 failed → backend-engineer (endpoint returns 500 on empty cart) — see BR-02
  - AC-5 failed → react-developer (filter state not persisted) — see BR-04
  ```
- **Traceability.** An implementation report lists which AC / contract item each change satisfies. QC maps each AC back to the evidence that proves it. This two-way link is what lets the CEO assert "done" with confidence.

## What the CEO reads to make the ship call

The CEO doesn't re-derive anything — it reads the four verdict lines from `04`, `05`, `06`, and the acceptance checklist in `00`, confirms every `AC-n` is ✔ in `06-qc.md`, and decides. If any verdict is FAIL or any AC is unproven, the loop is not done.

## Keeping artifacts honest

- Artifacts record **what actually happened**, not what was intended. A test report says which commands ran and their real output. A QC report attaches real evidence. A release report says whether post-deploy checks actually passed.
- If a step was skipped, the artifact says so and why — silent gaps read as "covered" when they weren't.
