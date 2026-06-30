# Loop Protocol — the state machine

This is the authoritative control flow for the AI Team Loop. The CEO (orchestrator) follows it; the slash commands implement it stage-by-stage.

## States and transitions

```
                    ┌─────────────────────────────────────────────────────┐
                    │                                                       │
 GOAL ─▶ [INTAKE] ─▶ [SPEC] ─▶◇G1─▶ [DESIGN] ─▶◇G2─▶ [APPROACH] ─▶◇G3─▶ [BUILD]
   (CEO)    │          (PM)    (Arch)  (Arch)  (critic)  (FE+BE)   (Arch)   (FE+BE)
            │                    │               │                   │         │
            │                    ▼ fail           ▼ fail              ▼ fail    ▼
            │                  back to PM      back to Arch       back to eng   │
            │                                                                   ▼
            └──────────────────────────────────────────────────────────▶ ◇G4 [REVIEW] (Reviewer)
                                                                              │
                                          ┌───────────────────────────────────┤
                                          ▼ pass                               ▼ fail → back to eng
                                      ◇G5 [TEST] (Tester) ── fail → back to eng
                                          │ pass
                                          ▼
                                      ◇G6 [QC] (QC vs. acceptance) ── fail → back to owning role
                                          │ pass
                                          ▼
                                      [SHIP?] (CEO) ── no → re-loop to failing stage
                                          │ yes
                                          ▼
                                      [RELEASE] (Release/DevOps) ─▶ post-deploy ◇ ─▶ [RETRO] (CEO) ─▶ DONE
```

## Stage contracts

Each stage has: **entry condition**, **actor**, **action**, **artifact**, **exit gate**.

| Stage | Entry | Actor | Action | Artifact | Exit |
|-------|-------|-------|--------|----------|------|
| INTAKE | a user goal exists | CEO | frame goal, set acceptance criteria + non-goals; clarify with `AskUserQuestion` if ambiguous | `00-brief.md` | always → SPEC |
| SPEC | brief exists | PM | user stories + functional spec + **testable** acceptance criteria | `01-spec.md` | → G1 |
| **G1** | spec exists | Architect (verify) | is the spec complete, consistent, buildable? | (verdict appended) | pass → DESIGN / fail → SPEC |
| DESIGN | G1 passed | Architect | components, **FE↔BE interface contract**, data/schema, risks | `02-design.md` | → G2 |
| **G2** | design exists | Reviewer **or** fresh Architect-critic | refute the design; propose a more efficient/robust architecture | (critique appended) | pass → APPROACH / better-design → DESIGN |
| APPROACH | G2 passed | FE + BE (parallel) | each writes a short approach note vs. the contract (plan, not code) | inline in `03-impl-*.md` header | → G3 |
| **G3** | approach notes exist | Architect (verify) | is each approach optimal and contract-compliant? | (verdict appended) | pass → BUILD / fail → APPROACH |
| BUILD | G3 passed | FE + BE (parallel) | implement to the contract | `03-impl-fe.md`, `03-impl-be.md` | → G4 |
| **G4** | code written | Reviewer | correctness + maintainability + convention review of the diff | `04-review.md` | pass → G5 / changes → BUILD |
| **G5** | review passed | Tester | static + build + unit + integration | `05-test.md` | pass → G6 / fail → BUILD |
| **G6** | tests passed | QC | each acceptance criterion + E2E/smoke critical paths | `06-qc.md` | pass → SHIP? / fail → owning role |
| SHIP? | G6 passed | CEO | all criteria green? approve or re-loop | (decision in `08-retro.md` draft) | yes → RELEASE / no → failing stage |
| RELEASE | ship approved | Release/DevOps | build, deploy, post-deploy verify, rollback plan | `07-release.md` | post-deploy ✔ → RETRO / ✘ → rollback + escalate |
| RETRO | released (or aborted) | CEO | capture learnings; update changelog/memory | `08-retro.md` | DONE |

## Iteration & escalation rules

- **Per-gate cap = 3.** Count round-trips through each gate. On the 3rd failure of the *same* gate, **stop and escalate to the human** — present the verifier's findings and the producer's latest attempt, and ask how to proceed. Do not auto-loop a 4th time.
- **Inner design loop cap = 2.** G2 may bounce the design back to the Architect at most twice. After that, surface *both* the Architect's design and the critic's alternative to the human and let them pick.
- **Blocker = immediate escalate.** Missing credentials, an ambiguous requirement that needs a product call, an external dependency that's down, or a destructive migration the engineer is unsure about → stop the loop and ask the human. Never guess past a blocker.
- **Parallel barrier.** APPROACH, BUILD run FE and BE concurrently; G3, G4, G5, G6 are barriers — wait for both producers before running the gate.
- **Re-entry is targeted.** When a gate fails, loop back only to the stage that owns the defect (e.g. a failing test caused by the BE goes to BE, not to the PM). The routing rules live in `verify-gates.md`.

## What "pass" means at each gate

A gate passes only on an **explicit, evidenced** verdict from the verifier — never on absence of comment. A Tester saying "I didn't see failures" is not a pass; "ran `<cmd>`, 142/142 green, output attached" is. A QC pass requires every acceptance criterion individually marked ✔ with evidence.

## Minimal vs. full runs

- **Full run:** all 14 stages. Use for a real feature.
- **Lite run** (CEO may choose for a small change): INTAKE → DESIGN(+G2) → BUILD → G4 → G5 → G6 → SHIP. Skips a formal PM spec (CEO writes acceptance criteria directly) and the approach gate. Never skip G4/G5/G6 — independent verification is the whole point.
