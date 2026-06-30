# Team Verify (`/team-verify`)

Run a single **independent verify gate** on the latest team-loop artifact — the headline move of the harness: *one agent finishes, a different agent checks whether the approach/output is sound and proposes a better one.* Use this stand-alone to critique a design, an approach, or a diff without running the whole loop.

## Execution mode: single gate

### Procedure

1. **Pick the target.** From `$ARGUMENTS` or the newest file in `.claude/outputs/team/`, determine what to verify:
   - a design (`02-design.md`) → **G2 critique**
   - an approach note (`03-impl-*.md` header) → **G3 approach check**
   - a diff / implementation → **G4 code review**

2. **Spawn an independent verifier** — an agent that did **not** produce the artifact. Never let the producer self-approve.
   - G2 → `loop-reviewer` or a fresh `loop-architect` instance, prompted adversarially: *assume it can be improved — what's the simplest viable design? where does it hurt at scale/under failure? propose a concrete better architecture with the trade-off.*
   - G3 → `loop-architect`: *does the approach honor the contract and avoid rework? PROCEED or REVISE with the fix.*
   - G4 → `loop-reviewer`: correctness + security + maintainability, findings + verdict.

3. **Emit a verdict line** and, on failure, a routing block naming the owning role. Append both to the artifact.

4. **On "propose-better"**, hand the alternative back to the producing role (cap: 2 design iterations) or, if they deadlock, surface both options to the user.

## When to use vs. `/3-review`

- `/3-review` reviews the working git diff in the lifecycle pipeline.
- `/team-verify` verifies a team-loop **artifact** (design / approach / diff) with the adversarial framing, and can run before any code exists.

---

Verifying `$ARGUMENTS`. Loading the `ai-team-loop` `verify-gates.md` framing and spawning an independent verifier.
