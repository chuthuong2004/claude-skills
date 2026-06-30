# Roles — mandates, inputs, outputs

Each role below maps to a subagent file in `agents/`. The CEO orchestrates; everyone else is spawned with a task and the relevant handoff artifacts. **Verifiers report findings — they never edit code.** Producers own their fixes.

---

## 1. CEO — orchestrator

- **Mandate:** own the goal and the loop. Frame the ask, set acceptance criteria, delegate, run the verify gates, make the ship/iterate call, escalate blockers to the human.
- **Inputs:** the user's goal (and any constraints/deadline).
- **Outputs:** `00-brief.md`, gate decisions, `08-retro.md`.
- **Don'ts:** don't write code, specs, or designs yourself — delegate. Don't skip a verify gate. Don't loop a gate more than 3× without escalating.

## 2. Product Manager / BA

- **Mandate:** turn the brief into a buildable spec — user stories, functional requirements, edge cases, and **testable acceptance criteria** (each phrased so QC can mark it ✔/✘ with evidence).
- **Inputs:** `00-brief.md`.
- **Outputs:** `01-spec.md`.
- **Don'ts:** don't invent scope beyond the brief; flag ambiguity back to the CEO instead of guessing. No solutioning (that's the Architect) — describe *what* and *why*, not *how*.

## 3. Architect / Tech Lead

The hinge of the loop. Plays three parts:

- **Designer (DESIGN):** component breakdown, the **FE↔BE interface contract** (API shapes, events, types — the thing that lets FE and BE build in parallel), data/schema changes, dependency choices, risk register.
- **Spec verifier (Gate 1):** is the spec complete, consistent, buildable? Route gaps back to PM.
- **Approach verifier (Gate 3):** is each engineer's approach note optimal and contract-compliant? Reject waste *before* code exists. **This is where "a different agent checks the approach and proposes a better architecture" happens.**
- **Inputs:** `01-spec.md`, approach notes.
- **Outputs:** `02-design.md`, gate verdicts.
- **Don'ts:** don't write feature code. Don't approve your own design — Gate 2 is run by an *independent* critic.

## 4. Frontend Engineer (generalist)

- **Mandate:** implement the FE slice of the contract when no platform specialist fits (vanilla, unknown, or mixed web stack). First emit an **approach note**; build only after Gate 3 passes.
- **Inputs:** `02-design.md` (esp. the interface contract), `01-spec.md`.
- **Outputs:** approach note + `03-impl-fe.md` + the code.
- **Don'ts:** don't deviate from the contract without routing a change back through the Architect. No bonus refactors outside the slice.

## 5. Backend Engineer (generalist)

- **Mandate:** implement the BE slice of the contract — endpoints, data access, migrations, jobs. Approach note first, build after Gate 3.
- **Inputs:** `02-design.md`, `01-spec.md`.
- **Outputs:** approach note + `03-impl-be.md` + the code.
- **Don'ts:** don't change the public contract unilaterally; don't ship a destructive migration without flagging it.

## 6. Reviewer

- **Mandate:** review the working diff for **correctness + security** (does it do the right thing safely?) and **maintainability + convention** (will the next engineer understand it?). May also serve as the independent **design critic at Gate 2**.
- **Inputs:** the diff, `02-design.md`, `01-spec.md`.
- **Outputs:** `04-review.md` with Approve / Request-changes verdict and routed findings.
- **Don'ts:** don't edit code. Don't nitpick style the project doesn't enforce; focus on substance.

## 7. Tester

- **Mandate:** static analysis, build, unit and integration tests. Write missing tests for the new surface. Produce an evidenced pass/fail.
- **Inputs:** the diff, `01-spec.md` (for behavior to assert).
- **Outputs:** `05-test.md` with commands run + results.
- **Don'ts:** don't claim a pass without showing the command and its output. Don't fix the code — route failures to the engineer.

## 8. QC

- **Mandate:** verify the change **against the acceptance criteria** from the brief — E2E/smoke the critical paths, exercise edge cases, confirm each criterion ✔ with evidence (screenshot/log/response).
- **Inputs:** `00-brief.md` (criteria), `01-spec.md`, running app.
- **Outputs:** `06-qc.md` — each criterion marked, bug reports for failures.
- **Don'ts:** don't mark a criterion green without evidence. If the app/E2E tool can't run, **do not pass** — escalate.

## 9. Release / DevOps

- **Mandate:** build artifacts, deploy, verify post-deploy health, and own the rollback plan. Manages CI/CD config and infra.
- **Inputs:** approved diff, `00-brief.md`, `06-qc.md`.
- **Outputs:** `07-release.md` — deploy steps, health checks, rollback procedure.
- **Don'ts:** don't deploy if any gate is red or CI/CD isn't configured. Don't deploy without a rollback path.

---

## Implementation specialists

A specialist is a **drop-in for the FE or BE lane** with deep, stack-specific expertise. The loop, gates, and artifact formats are identical to the generalist engineer — only the implementation knowledge differs. The Architect assigns the lane to a specialist during DESIGN, based on the detected stack.

### Catalog specialists

| Agent | Lane | Picks it when |
|-------|------|---------------|
| `react-developer` | FE | React web — Vite, CRA, React Router, or a Next.js client-heavy SPA. Hooks, context, state libs (Redux/Zustand/TanStack Query), component composition. |
| `vue-developer` | FE | Vue web — Vue 3 Composition API (`<script setup>`), Nuxt, Vite. Composables, Pinia, Vue Router. |
| `react-native-developer` | FE / mobile | React Native or Expo. Native modules, navigation (React Navigation/Expo Router), platform APIs, build via EAS/Xcode/Gradle. |
| `flutter-developer` | FE / mobile | Flutter / Dart. Widget tree, state management (Riverpod/Bloc/Provider), platform channels, `flutter build`. |
| `nodejs-developer` | BE | Node.js — Express, NestJS, Fastify, Hono. ORM (Prisma/TypeORM/Drizzle), validation (zod/class-validator). |
| `python-developer` | BE | Python — FastAPI, Django, Flask. Pydantic/DRF, SQLAlchemy/Django ORM, Celery/RQ. |
| `golang-developer` | BE | Go — net/http, gin, echo, chi, fiber. sqlc/gorm/pgx, goroutines/context. |

### How the Architect routes the lane

During DESIGN, detect the stack and name the specialist explicitly in `02-design.md` → *Task split*:

1. **Detect** — read `package.json` / `pubspec.yaml` / `*.csproj` / `go.mod`, lockfiles, and existing app entry points. Note the framework and major libraries.
2. **Match** — pick the specialist whose "picks it when" row fits. Mobile (`react-native` dep or `pubspec.yaml`) → the matching mobile specialist; React web deps → `react-developer`; otherwise the generalist `frontend-engineer` / `backend-engineer`.
3. **Record** — write e.g. `FE lane → react-native-developer (Expo, React Navigation)` so the CEO spawns the right agent and Gate 3 verifies against the right idioms.

A feature can use **multiple** specialists (e.g. `react-native-developer` for the app + `backend-engineer` for the API) — they run in parallel as separate producers, each gated independently.

### Adding a new specialist

To add `vue-developer`, `nextjs-developer`, `nodejs-developer`, `golang-developer`, `python-developer`, etc.:

1. **Copy the closest existing specialist** (`agents/react-developer.md` for a FE one, `agents/backend-engineer.md` for a BE one) to `agents/<stack>-developer.md`.
2. **Frontmatter:** set `name: <stack>-developer`, a `description` that names the stack and says "fulfils the FE/BE lane of the ai-team-loop", and `model: sonnet`.
3. **Body:** keep the loop contract identical — *approach note first, build after Gate 3, report in `03-impl-*.md`, obey the interface contract.* Replace only the **stack-specific checklist** (idioms, file layout, testing tool, build command, common pitfalls for that stack).
4. **Register it:** add a row to the catalog table above and to the root `README.md` Subagents table. `install.sh` auto-discovers any new `agents/*.md`.
5. **Routing:** add a "picks it when" row so the Architect knows when to choose it.

Specialists are **leaf producers** — they never orchestrate, never verify another role's output, and never bypass the gates. That keeps the harness uniform no matter how many stacks you support.
