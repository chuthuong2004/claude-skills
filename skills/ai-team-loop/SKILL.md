---
name: ai-team-loop
description: Run a full software team as a closed build→verify→refine loop — a CEO orchestrates specialist agents (Product Manager, Architect, Frontend, Backend, Reviewer, Tester, QC, Release) to ship a feature end to end, where every producer's work is checked by an independent verifier before it moves forward. Use whenever the user wants to "ship a feature end to end", "run the AI team", "chạy quy trình team", "build feature với cả team", "harness nhiều agent", "let agents verify each other", "have one agent check another's approach", "đề xuất kiến trúc tối ưu cho feature", or wants an autonomous multi-role agent loop that plans, builds, reviews, tests, QCs and releases with adversarial cross-checking. Prefer this skill over ad-hoc single-agent work for any non-trivial feature that should go through plan → implement → review → test → QC → ship.
---

# AI Team Loop

A reusable **multi-agent harness** that runs a software-delivery team as a **closed loop**. One orchestrator (the **CEO**) takes a goal, delegates to nine specialist roles, and drives a `build → verify → refine` cycle until the feature meets its acceptance criteria — or escalates to the human when it can't.

The defining rule of this harness: **no role grades its own homework.** Every artifact a producer emits (a spec, a design, a diff, a test suite) is checked by an *independent* verifier before the loop advances. When an engineer proposes *how* they'll build something, an Architect verifies the approach is sound and proposes a better one *before* code gets written — not after.

Output language: **English** for all artifacts and code. If the user writes in Vietnamese, mirror their language in conversation but keep the artifacts (specs, reports, code) in technical English unless they ask otherwise.

---

## When to use

Trigger this skill when the user wants to take a feature or change **from intent to shipped** with a team of agents, e.g.:

- "Build feature X end to end" / "ship this with the full team"
- "Run the AI team on this" / "chạy quy trình team cho tính năng này"
- "Have one agent verify another's work" / "agent này làm xong thì agent khác vào review cách làm"
- "Propose the best architecture for this feature, then implement it" / "đề xuất kiến trúc tối ưu rồi triển khai"
- Any non-trivial change that deserves plan → build → review → test → QC → release rather than a one-shot edit.

**Don't** use it for trivial one-line fixes, pure questions, or work the user explicitly wants done inline. The loop has real overhead (multiple subagent spawns); reserve it for changes where independent verification pays for itself.

---

## The roles

**Core roles** (always present in the loop):

| # | Role | Produces | Verified by |
|---|------|----------|-------------|
| 1 | **CEO** (orchestrator) | Brief, acceptance criteria, ship/iterate decisions | the human (final gate) |
| 2 | **Product Manager / BA** | Spec + user stories + testable acceptance criteria | Architect (Gate 1) |
| 3 | **Architect / Tech Lead** | Architecture design, FE/BE task split, interface contract | Reviewer or 2nd Architect-critic (Gate 2) |
| 4 | **Frontend Engineer** | FE approach note → FE implementation | Architect (approach) + Reviewer (code) |
| 5 | **Backend Engineer** | BE approach note → BE implementation | Architect (approach) + Reviewer (code) |
| 6 | **Reviewer** | Code review report (correctness + maintainability) | — (is itself a verifier) |
| 7 | **Tester** | Static/build/unit/integration results | — (is itself a verifier) |
| 8 | **QC** | Acceptance + E2E verification vs. the criteria | — (is itself a verifier) |
| 9 | **Release / DevOps** | Build, deploy, rollback, post-deploy health | QC smoke on prod (Gate 6) |

**Implementation specialists** (pluggable — the Architect routes the FE/BE lane to whichever matches the detected stack):

| Specialist | Lane | Use when the stack is |
|------------|------|-----------------------|
| **react-developer** | FE | React web (Vite / CRA / Next.js SPA / React Router) |
| **vue-developer** | FE | Vue 3 / Nuxt / Vite |
| **react-native-developer** | FE/mobile | React Native / Expo |
| **flutter-developer** | FE/mobile | Flutter / Dart |
| **nodejs-developer** | BE | Node.js (Express / NestJS / Fastify / Hono) |
| **python-developer** | BE | Python (FastAPI / Django / Flask) |
| **golang-developer** | BE | Go (net/http / gin / echo / chi) |
| *(extend)* | FE or BE | add `nextjs-developer`, `svelte-developer`, `rails-developer`, `dotnet-developer`, … |

A specialist **slots into the FE or BE lane** — it obeys the same approach-note → Gate 3 → build → Gate 4 flow as the generalist engineer; only its stack expertise differs. If no specialist matches, the generalist `frontend-engineer` / `backend-engineer` handles the lane. How the Architect picks, and how to add a new specialist, is in [`references/roles.md`](references/roles.md) → *Implementation specialists*.

Full responsibilities, inputs, and outputs for each role: [`references/roles.md`](references/roles.md).

These map 1:1 to installable subagents in this catalog (`agents/ceo.md`, `agents/architect.md`, `agents/react-developer.md`, …). The CEO spawns them via the **Agent / Task tool**.

---

## How the loop runs (orchestrator playbook)

You — the session acting as **CEO** — drive this. Read [`references/loop-protocol.md`](references/loop-protocol.md) for the full state machine; the short version:

```
GOAL ─▶ ① CEO frames it ─▶ ② PM writes spec ─▶ ◇G1 Architect verifies spec
      ─▶ ③ Architect designs ─▶ ◇G2 design critique ─▶ ④/⑤ FE+BE propose approach
      ─▶ ◇G3 Architect verifies approach ─▶ build ─▶ ◇G4 Reviewer reviews diff
      ─▶ ◇G5 Tester ─▶ ◇G6 QC vs. acceptance ─▶ CEO ship-decision
      ─▶ ⑨ Release ─▶ post-deploy verify ─▶ CEO retrospective
```

`◇` is a **verify gate**. A gate either **passes** (advance) or **fails** (loop back to the role that owns the defect, with the verifier's findings attached). The complete gate definitions and the *who-verifies-whom* matrix are in [`references/verify-gates.md`](references/verify-gates.md).

### Step-by-step

1. **Intake (CEO).** Restate the goal in one sentence. Define **acceptance criteria** as a checklist of *testable* statements and the **non-goals**. If the goal is ambiguous, use `AskUserQuestion` before spawning anyone. Write the brief using [`assets/template-ceo-brief.md`](assets/template-ceo-brief.md) to `.claude/outputs/team/00-brief.md`.

2. **Spec (PM).** Spawn `product-manager` with the brief. It returns a spec with user stories and acceptance criteria. → `01-spec.md`.

3. **Gate 1 — spec verification (Architect).** Spawn `architect` *in verify mode*: is the spec complete, internally consistent, and buildable? Missing/contradictory → loop back to PM. Pass → continue.

4. **Design (Architect).** Same Architect now *designs*: component breakdown, the **interface contract** between FE and BE (this contract is what lets them build in parallel), data/schema changes, risks. → `02-design.md`.

5. **Gate 2 — design critique (independent).** Spawn a *second* verifier (`reviewer`, or a fresh `architect` instance prompted to **refute**) to answer: *is this the optimal approach? Propose a more efficient / simpler / more robust architecture.* If it proposes a materially better design, loop back to the Architect to revise (cap: 2 inner iterations, then surface both options to the human). This is the gate that delivers the user's core ask — **a different agent checks the approach and proposes the ideal architecture before any code is written.**

6. **Approach notes (FE + BE).** Spawn `frontend-engineer` and `backend-engineer` *in parallel*. Each first emits a **short approach note** (the plan, not the code) against the interface contract.

7. **Gate 3 — approach verification (Architect).** The Architect verifies each approach note *before bulk coding*. Reject a wasteful or contract-violating approach now, when it costs one paragraph — not after 400 lines. Pass → engineers implement.

8. **Implement (FE + BE, parallel).** Each writes code to the contract and emits an implementation report. → `03-impl-fe.md`, `03-impl-be.md`. (Run with `isolation: worktree` if they touch overlapping files.)

9. **Gate 4 — code review (Reviewer).** Spawn `reviewer` on the combined diff. Request-changes → loop back to the responsible engineer. → `04-review.md`.

10. **Gate 5 — tests (Tester).** Spawn `tester`: static + build + unit + integration. Fail → loop back to engineer. → `05-test.md`.

11. **Gate 6 — QC vs. acceptance (QC).** Spawn `qc`: walk each acceptance-criterion from the brief, E2E/smoke the critical paths. Any criterion red → loop back to the owning role. → `06-qc.md`.

12. **Ship decision (CEO).** All gates green? Decide ship. Otherwise re-loop to the failing stage. Surface the decision to the human.

13. **Release (Release/DevOps).** On approval, spawn `release-engineer`: build, deploy, post-deploy verify, rollback plan. → `07-release.md`.

14. **Retrospective (CEO).** Capture what the loop learned (which gate caught what, where it iterated) in `08-retro.md`. If a learning is durable, write it to the repo's changelog or a memory.

### Termination (avoid infinite loops)

- **Gate iteration cap:** default **3** round-trips per gate. On the 3rd failure, **stop and escalate to the human** with the verifier's findings — don't grind.
- **Done:** every acceptance criterion is green at Gate 6 and the CEO has approved.
- **Hard stop:** any role reports a blocker it cannot resolve (missing credential, ambiguous requirement, external dependency down) → escalate immediately, don't loop.

---

## Two ways to run it

This harness ships in **two interchangeable forms** so you can run it autonomously or step-by-step:

1. **Autonomous (skill-driven).** This skill *is* the playbook. When triggered, the session adopts the CEO role and runs the full loop above, spawning role subagents via the Task tool and parking handoff artifacts under `.claude/outputs/team/`. Best when the user says "just build it."

2. **Manual (slash-command-driven).** In a project bootstrapped with this repo's scaffold (`install.sh --init`), two slash commands drive it with human checkpoints: `/team-loop` runs the whole loop, and `/team-verify` runs a single independent verify gate (G2 design critique / G3 approach check / G4 review) on the latest artifact. To run one role by hand, spawn its agent (`loop-architect`, `loop-reviewer`, … or the catalog `architect`, `reviewer`, …) directly via the Task tool. Best when the user wants to inspect and approve each gate.

Both forms read the **same** role definitions and the **same** artifact formats — only the driver differs.

---

## Handoff artifacts

Every stage reads the previous artifact and writes its own, all under `.claude/outputs/team/`. This is the loop's shared memory — a fresh subagent gets full context by reading the folder, not by re-deriving it.

| File | Owner | Template |
|------|-------|----------|
| `00-brief.md` | CEO | [`assets/template-ceo-brief.md`](assets/template-ceo-brief.md) |
| `01-spec.md` | PM | [`assets/template-spec.md`](assets/template-spec.md) |
| `02-design.md` | Architect | [`assets/template-architecture-design.md`](assets/template-architecture-design.md) |
| `03-impl-*.md` | FE / BE | [`assets/template-impl-report.md`](assets/template-impl-report.md) |
| `04-review.md` | Reviewer | [`assets/template-review-report.md`](assets/template-review-report.md) |
| `05-test.md` | Tester | [`assets/template-test-report.md`](assets/template-test-report.md) |
| `06-qc.md` | QC | [`assets/template-qc-report.md`](assets/template-qc-report.md) |
| `07-release.md` | Release | [`assets/template-release-report.md`](assets/template-release-report.md) |

Conventions for cross-role handoffs (how to reference findings, how to route a failure back): [`references/handoff-artifacts.md`](references/handoff-artifacts.md).

---

## Operating principles

- **Independent verification is the point.** Never let the agent that produced an artifact be the one that signs off on it. If you're tempted to skip a gate "because it's obviously fine," that's exactly the case the gate exists for.
- **Verify the *approach* before the *output*.** The cheapest defect to fix is a bad plan. Gates 2 and 3 exist so a flawed design or wasteful implementation strategy is caught as prose, before it becomes code.
- **Findings, not rewrites, from verifiers.** Reviewer/Architect-critic/QC report problems and route them; they do **not** edit code. Fixes go back to the producing engineer so ownership stays clean.
- **Parallelize producers, serialize gates.** FE and BE build concurrently; verify gates are barriers that need the combined result.
- **Cap the loop.** Three round-trips per gate, then a human decides. Diminishing returns are real.
- **One source of truth per fact.** Acceptance criteria live in the brief; the interface contract lives in the design. Downstream roles read them, they don't redefine them.

---

## Reference index

- [`references/loop-protocol.md`](references/loop-protocol.md) — the full state machine, gate transitions, iteration caps, escalation rules.
- [`references/roles.md`](references/roles.md) — every role's mandate, inputs, outputs, and hard "don'ts".
- [`references/verify-gates.md`](references/verify-gates.md) — the six gates, the who-verifies-whom matrix, and the adversarial-critique prompts.
- [`references/handoff-artifacts.md`](references/handoff-artifacts.md) — artifact folder layout, naming, and failure-routing conventions.
