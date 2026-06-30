# Team Loop (`/team-loop`)

Run a feature through the full **AI Team Loop** — a CEO orchestrates specialist roles in a `build → verify → refine` cycle where every producer's work is checked by an independent verifier before it advances. This is the autonomous driver; for a single gate use `/team-verify`, for the lifecycle pipeline use `/1-plan`…`/6-verify`.

## Execution mode: orchestrated

The current session acts as **CEO** and spawns role subagents via the Task tool. Handoff artifacts live under `.claude/outputs/team/`.

### Procedure

0. **Preflight.** Read `.claude/config.md` (paths, commands, stack) so spawned roles inherit correct values. If the `ai-team-loop` skill is installed, read its `references/loop-protocol.md` and `references/verify-gates.md` — they are the authoritative playbook. If not, install it:
   ```
   curl -fsSL https://raw.githubusercontent.com/chuthuong2004/claude-skills/main/install.sh | bash
   ```

1. **Intake (CEO).** Frame `$ARGUMENTS` in one sentence; write `.claude/outputs/team/00-brief.md` with testable acceptance criteria (`AC-1…`) and non-goals. Clarify ambiguity with `AskUserQuestion` first. Pick **full** or **lite** run.

2. **Run the loop** (spawn the `loop-*` agents in `.claude/agents/team-loop/`, or the catalog agents `ceo`/`architect`/… if installed):
   `SPEC (loop-product-manager) → G1 → DESIGN (loop-architect) → G2 critique → APPROACH (loop-frontend-engineer/loop-backend-engineer or a specialist) → G3 → BUILD → G4 (loop-reviewer) → G5 (loop-tester) → G6 (loop-qc) → ship decision → RELEASE (loop-release-engineer)`.

3. **Every gate is run by an agent that did NOT produce the artifact.** Pass = an explicit, evidenced verdict; fail = route to the owning role with findings. Cap each gate at **3** round-trips, then escalate to the user.

4. **Ship decision (CEO).** Confirm every `AC-n` is ✔ in `06-qc.md`. Approve → run release. Otherwise re-loop to the failing stage.

5. **Retro.** Write `08-retro.md` — what shipped, which gate caught what, learnings.

## Specialist routing

The Architect detects the stack and routes the FE/BE lane to a specialist. If the matching specialist agent (`react-developer`, `react-native-developer`, `flutter-developer`, …) isn't installed, install it from the catalog or fall back to `loop-frontend-engineer` / `loop-backend-engineer`.

## Error recovery

| Situation | Recovery |
|---|---|
| Goal ambiguous | `AskUserQuestion` before writing the brief. |
| A gate fails 3× | Stop; present the verifier's findings + latest attempt; ask the user. |
| Blocker (creds, product call, dep down, risky migration) | Stop and escalate immediately — don't loop. |
| Agents/skill not installed | Install via the curl one-liner above, then re-run. |

---

Starting the team loop for `$ARGUMENTS`. Reading `.claude/config.md` and the `ai-team-loop` playbook first.
