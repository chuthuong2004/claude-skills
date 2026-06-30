# Verify gates — the adversarial cross-check

The gates are what make this a *verified* loop rather than a relay race. Each gate is run by an agent that **did not produce** the artifact under review. A gate emits an explicit verdict and, on failure, **routes the defect back to the role that owns it**.

## The who-verifies-whom matrix

| Gate | Produced by | Verified by | Question the verifier answers | Fail routes to |
|------|-------------|-------------|-------------------------------|----------------|
| **G1** Spec | PM | Architect | Is the spec complete, internally consistent, and buildable? Are the acceptance criteria testable? | PM |
| **G2** Design | Architect | Reviewer **or** a 2nd Architect-critic | Is this the optimal approach? Can a simpler / more efficient / more robust architecture do the same job? | Architect |
| **G3** Approach | FE / BE / specialist | Architect | Does each approach match the contract and avoid obvious waste/rework? | the engineer |
| **G4** Code | FE / BE / specialist | Reviewer | Correct? Safe? Maintainable? Convention-compliant? | the engineer who wrote the flagged code |
| **G5** Tests | FE / BE / specialist | Tester | Do static/build/unit/integration pass with evidence? | the engineer |
| **G6** Acceptance | the whole build | QC | Is every acceptance criterion individually satisfied, with evidence? | the role owning the failed criterion |
| **Final** | the loop | CEO → human | Should this ship? | back to the failing stage |

The invariant: **trace any green checkmark to an agent other than the one being checked.** If you can't, it isn't verified.

## G2 and G3 — the heart of the harness

These two gates implement the user-facing promise: *"one agent finishes, another verifies whether the approach is sound and proposes the ideal architecture — before code is written."*

### G2 — design critique (adversarial)

Spawn the critic with an **adversarial prompt**, not a rubber-stamp one. The critic's job is to *try to beat the design*:

> You are reviewing a proposed architecture for `<feature>`. Assume it can be improved. Read `02-design.md` and the spec. Answer:
> 1. What's the simplest design that satisfies the spec? Is the proposed one more complex than it needs to be?
> 2. Where will this design hurt at 10× scale / under failure / for the next engineer?
> 3. Is there a more efficient data flow, a reusable existing seam, or a pattern already in the codebase that this ignores?
> 4. Propose a concrete **alternative architecture** if you have one — with the trade-off vs. the original.
> Verdict: **Approve** (the design is at or near optimal) or **Propose-better** (attach the alternative).

On **Propose-better**, the Architect revises (inner loop, cap 2). If they still disagree after 2 rounds, present *both* designs to the human with the trade-offs — don't let two agents deadlock.

### G3 — approach verification (cheap-defect catch)

Before an engineer writes bulk code, they emit a 5–10 line **approach note**: the files they'll touch, the shape of the change, the libraries they'll use, and how it meets the contract. The Architect verifies:

> Read this approach note against `02-design.md`'s interface contract. Answer:
> 1. Does it honor the contract (types, endpoints, events)? Any drift?
> 2. Is it the least-effort path that's still correct, or is it about to reinvent something the codebase already has?
> 3. Any foreseeable rework (wrong layer, missed edge case, perf cliff)?
> Verdict: **Proceed** or **Revise** (with the specific fix).

Catching a bad approach here costs one paragraph. Catching it at G4 costs a rewrite. That's the entire economic argument for this gate.

## Running a gate (autonomous mode)

1. **Gather inputs** — the artifact under review plus its upstream context (read the `.claude/outputs/team/` files).
2. **Spawn the verifier subagent** with the adversarial framing above and the artifact path. For G2/G4 you may spawn **multiple independent verifiers** and require a majority for high-stakes changes (perspective-diverse verify: e.g. one critic on simplicity, one on failure modes, one on security).
3. **Collect the verdict.** Pass requires explicit, evidenced approval. Absence of findings ≠ pass.
4. **On fail:** append the findings to the relevant artifact, route back per the matrix, increment that gate's counter. At 3, escalate to the human.
5. **On pass:** advance to the next stage.

## Anti-patterns the gates exist to prevent

- **Self-approval.** The producer says "looks good to me." Never counts.
- **Verdict-by-silence.** "No comments" treated as approval. A pass must be a positive, evidenced statement.
- **Late verification.** Reviewing the approach only after 400 lines are written. G3 moves it earlier.
- **Infinite polish.** Looping a gate forever chasing diminishing returns. The cap forces a human decision at round 3.
- **Routing to the wrong role.** A failing integration test caused by the BE bounced to the PM. Route to the owner of the defect, not the start of the line.
