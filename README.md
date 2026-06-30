# claude-skills

A curated collection of [Claude Code](https://docs.claude.com/en/docs/claude-code) **skills** and **subagents** — also installable into **Cursor** via `AGENTS.md`. Plus a one-command **project bootstrap** that scaffolds a full Claude-Code-ready repo tree (README, CLAUDE.md, AGENTS.md, docs/, .claude/agents+commands+shared+templates).

One URL. Pick AI, pick scope, optionally bootstrap, pick items. Nothing is auto-installed.

```bash
curl -fsSL https://raw.githubusercontent.com/chuthuong2004/claude-skills/main/install.sh | bash
```

You'll get three interactive prompts:

```
1) Pick AI tool          →  Claude Code  /  Cursor
2) Pick scope            →  user (~/.claude/)  /  project ($PWD/.claude/)   ← Claude only
3) Pick item(s)          →  multi-select checklist: tick the project scaffold
                            and/or any skills/agents. Space toggles, a = all, Enter confirms.
```

The **project scaffold** (README/CLAUDE.md/AGENTS.md/docs/.claude/) is the first tickable row in the item checklist — tick it to bootstrap the tree alongside whatever skills/agents you select.

---

## What's in the repo

### Skills (`skills/<name>/`)

Workflow recipes — auto-trigger on matching prompts, enforce a strict output structure.

| Skill | What it does |
|---|---|
| [`architecture-doc-writer`](skills/architecture-doc-writer/) | Full backend HLDs, migration plans, component deep-dives — Mermaid diagrams, state machines, queue topology, SQL schema, phased rollouts, risk registers, SLOs. Triggers on *"viết tài liệu kiến trúc"*, *"architecture doc"*, *"HLD"*, *"migration plan"*, *"RFC"*. |
| [`ai-team-loop`](skills/ai-team-loop/) | Run a full software team as a closed **build → verify → refine** loop. A **CEO** orchestrates PM, Architect, FE/BE (+ platform specialists), Reviewer, Tester, QC and Release, where **every producer's work is checked by an independent verifier** and an engineer's *approach* is verified before code is written. Triggers on *"ship a feature end to end"*, *"run the AI team"*, *"chạy quy trình team"*, *"agent này làm xong thì agent khác verify"*, *"đề xuất kiến trúc tối ưu rồi triển khai"*. Ships the 12 agents below + `/team-loop` + `/team-verify`. |

### Subagents (`agents/<name>.md`)

Specialist personas with their own context window. Used via the `Task` tool or proactively by Claude.

| Agent | What it does |
|---|---|
| [`seo-expert`](agents/seo-expert.md) | Technical SEO for **Next.js App Router** — metadata API, JSON-LD, sitemaps, robots, canonical/hreflang, image SEO, Core Web Vitals, i18n. Audits new pages and PRs. Model: `sonnet`. |

#### AI Team Loop roles

The nine **core roles** + seven **platform specialists** that the [`ai-team-loop`](skills/ai-team-loop/) skill orchestrates. Each is installable on its own; the skill ties them into the loop.

| Agent | Role | Model |
|---|---|---|
| [`ceo`](agents/ceo.md) | Orchestrator — frames the goal, sets acceptance criteria, runs the verify gates, makes the ship call. | `opus` |
| [`product-manager`](agents/product-manager.md) | PM / BA — turns the brief into a spec with testable acceptance criteria. | `sonnet` |
| [`architect`](agents/architect.md) | Architect / Tech Lead — designs the architecture **and** verifies approach (G1/G2/G3); proposes the optimal design before code. | `opus` |
| [`frontend-engineer`](agents/frontend-engineer.md) | Generalist FE — implements the client slice when no specialist fits. | `sonnet` |
| [`backend-engineer`](agents/backend-engineer.md) | BE — endpoints, data, migrations, authorization. | `sonnet` |
| [`reviewer`](agents/reviewer.md) | Independent code review (G4) + optional design critic (G2). Findings only. | `opus` |
| [`tester`](agents/tester.md) | Static/build/unit/integration (G5) with evidence; writes missing tests. | `sonnet` |
| [`qc`](agents/qc.md) | Acceptance verification (G6) vs. the criteria, E2E/smoke, evidence-backed. | `sonnet` |
| [`release-engineer`](agents/release-engineer.md) | Build, deploy, post-deploy verify, rollback. Refuses red gates. | `sonnet` |
| [`react-developer`](agents/react-developer.md) | FE specialist — React web (Vite/CRA/Next SPA). | `sonnet` |
| [`vue-developer`](agents/vue-developer.md) | FE specialist — Vue 3 / Nuxt / Vite. | `sonnet` |
| [`react-native-developer`](agents/react-native-developer.md) | FE specialist — React Native / Expo mobile. | `sonnet` |
| [`flutter-developer`](agents/flutter-developer.md) | FE specialist — Flutter / Dart mobile. | `sonnet` |
| [`nodejs-developer`](agents/nodejs-developer.md) | BE specialist — Node.js (Express/NestJS/Fastify). | `sonnet` |
| [`python-developer`](agents/python-developer.md) | BE specialist — Python (FastAPI/Django/Flask). | `sonnet` |
| [`golang-developer`](agents/golang-developer.md) | BE specialist — Go (net/http/gin/echo/chi). | `sonnet` |

> Add another specialist (e.g. `nextjs-developer`, `svelte-developer`, `dotnet-developer`) by copying the closest one — see [`skills/ai-team-loop/references/roles.md`](skills/ai-team-loop/references/roles.md) → *Adding a new specialist*.

> More items will be added without breaking existing installs. The installer only touches what you select.

---

## Install — interactive (single URL)

```bash
curl -fsSL https://raw.githubusercontent.com/chuthuong2004/claude-skills/main/install.sh | bash
```

The picker reads from `/dev/tty` so it works under `curl | bash`.

### Step 1 — Pick AI tool

```
Pick AI tool (↑/↓ + Enter, q to cancel):
> Claude Code   →  .claude/skills + .claude/agents
  Cursor        →  AGENTS.md at project root
```

### Step 2 — (Claude only) Pick scope

```
Install destination:
  user    → ~/.claude/{skills,agents}
  project → $PWD/.claude/{skills,agents}

Pick scope (↑/↓ + Enter, q to cancel):
> user      (global — available in every project)
  project   (scoped to current directory)
```

Cursor target always writes to `$PWD/AGENTS.md` (override with `CURSOR_AGENTS_FILE=...`).

### Step 3 — Pick items (multi-select)

The project scaffold and every skill/agent appear as tickable rows in one checklist:

```
Pick item(s) — tick the project and/or any skills/agents (↑/↓ move, Space toggle, a all, Enter confirm, q cancel):
> [ ] Project scaffold         (bootstrap README/CLAUDE.md/AGENTS.md/docs/.claude/ into $PWD)
  [x] architecture-doc-writer  (skill)
  [x] ai-team-loop             (skill)
  [x] ceo                      (agent)
  [x] architect                (agent)
  [ ] react-developer          (agent)
  …  (seo-expert + the rest of the AI Team Loop roles)
```

Toggle with `Space`, toggle all with `a`, confirm with `Enter`. Nothing happens until you confirm. If you tick **Project scaffold**, it bootstraps first, then the selected skills/agents install into the fresh tree.

---

## Bootstrap a new project (`--init`)

If you're starting a fresh repo and want the same Claude-Code conventions used in the source projects this collection grew out of, pass `--init`:

```bash
# Bootstrap only (no items)
./install.sh --claude --project --init

# Bootstrap + install items
./install.sh --claude --project --init seo-expert architecture-doc-writer

# Overwrite existing files (default is skip)
./install.sh --claude --project --init --force
```

What `--init` writes to `$PWD`:

```
README.md                                    project README placeholder
CLAUDE.md                                    Claude Code overlay → AGENTS.md
AGENTS.md                                    vendor-neutral agent contract
docs/
  architecture/README.md                     how to organize architecture docs
  changelogs/README.md                       per-domain changelog index
  plans/README.md                            feature-plan index
.claude/
  config.md                                  single source of truth (ports/paths/cmds)
  agents/{planner,implementer,code-reviewer,
          cto,tester,qa-engineer,verifier,
          deployer,devops}.md                9 lifecycle subagents
  agents/team-loop/loop-{ceo,product-manager,
          architect,frontend-engineer,
          backend-engineer,reviewer,tester,
          qc,release-engineer}.md            9 org-chart "team loop" subagents
  commands/{0-run,1-plan,2-implement,
            3-review,4-test,5-deploy,
            6-verify}.md                     6-stage lifecycle slash commands
  commands/{team-loop,team-verify}.md        autonomous team loop + verify gate
  commands/team-{ceo,pm,arch,fe,be,review,
            test,qc,release}.md              per-role team-loop stage commands
  shared/{principles,procedures,templates}.md
                                             agent-shared resources
  outputs/                                   stage handoff artifacts (with history/)
  skills/                                    drop installed skills here
  templates/docs/changelogs/CHANGELOG_TEMPLATE.md
```

Existing files are **skipped** by default — re-running `--init` is safe. Use `--force` to overwrite.

After `--init`, edit `.claude/config.md` to replace `<placeholder>` values with your project's specifics. Then `grep -RIn '<PROJECT_NAME>' .` to find the rest.

---

## Install — non-interactive (skip prompts)

```bash
# Claude Code, user-level, one agent
./install.sh --claude --user seo-expert

# Claude Code, project-level, multiple items
./install.sh --claude --project seo-expert architecture-doc-writer

# Cursor (always project root)
./install.sh --cursor seo-expert architecture-doc-writer

# Symlink instead of copy (Claude only, good for live-edit)
./install.sh --claude --link --user seo-expert

# Bulk
./install.sh --claude --user all-skills
./install.sh --claude --user all-agents

# Uninstall
./install.sh --uninstall --claude --user seo-expert
./install.sh --uninstall --cursor seo-expert    # removes that section from AGENTS.md
```

> `all` by itself is **not supported** — use `all-skills` or `all-agents`.

### Env overrides

```bash
CLAUDE_SKILLS_DIR=/path     # override Claude skills destination
CLAUDE_AGENTS_DIR=/path     # override Claude agents destination
CURSOR_AGENTS_FILE=/path    # override Cursor AGENTS.md path
```

---

## How each target stores items

### Claude Code

| Item | Destination (user scope) |
|---|---|
| Skill | `~/.claude/skills/<name>/` (full directory) |
| Agent | `~/.claude/agents/<name>.md` (single file) |

Swap `~/.claude/` for `$PWD/.claude/` for project scope.

### Cursor

Everything goes into a single `AGENTS.md` at the project root. Each item becomes a section delimited by marker comments:

```markdown
<!-- claude-skills:start seo-expert -->
## Agent: seo-expert

(full agent prompt)
<!-- claude-skills:end seo-expert -->
```

- **Skills** are flattened: `## Skill: <name>` + the `SKILL.md` body, followed by `### References` and `### Assets` sub-sections containing each referenced file inline.
- **Agents** are stripped of YAML frontmatter and inserted under `## Agent: <name>`.
- Re-installing the same item replaces its block in place — **idempotent**, your manual edits outside the markers are preserved.

---

## Manual install (if you don't want to run the script)

**Claude — skill:**

```bash
mkdir -p ~/.claude/skills
cp -R skills/architecture-doc-writer ~/.claude/skills/
```

**Claude — agent:**

```bash
mkdir -p ~/.claude/agents
cp agents/seo-expert.md ~/.claude/agents/
```

**Cursor:** the script's `AGENTS.md` flattening logic is non-trivial — running `./install.sh --cursor <name>` is strongly recommended over hand-writing.

> After install, **restart Claude Code / Cursor** so items are picked up.

---

## Verify

**Claude:**

```bash
ls ~/.claude/skills/architecture-doc-writer
ls ~/.claude/agents/seo-expert.md
```

Then in Claude Code: type `/architecture-doc-writer`, or ask naturally (*"viết tài liệu kiến trúc cho hệ thống X"*). For the agent: *"use the seo-expert agent to audit `app/blog/[slug]/page.tsx`"*.

**Cursor:**

```bash
grep '<!-- claude-skills:start' AGENTS.md
```

Cursor reads `AGENTS.md` automatically when opened in the project.

---

## Updating

```bash
# Re-run the installer for the items you care about
curl -fsSL https://raw.githubusercontent.com/chuthuong2004/claude-skills/main/install.sh | bash

# Or if cloned:
git pull && ./install.sh --claude --user all-skills all-agents
```

> If you installed Claude items with `--link`, `git pull` alone refreshes them.

---

## Uninstalling

```bash
./install.sh --uninstall --claude --user seo-expert
./install.sh --uninstall --cursor seo-expert     # removes the AGENTS.md section
```

Or by hand:

```bash
rm ~/.claude/agents/seo-expert.md
rm -rf ~/.claude/skills/architecture-doc-writer
# For Cursor: delete the section between the matching <!-- claude-skills:start/end --> markers.
```

---

## Repo layout

```
.
├── README.md
├── LICENSE
├── install.sh
├── agents/                        # single-file subagent personas
│   ├── seo-expert.md              # → ~/.claude/agents/seo-expert.md  (Claude)
│   ├── ceo.md  product-manager.md  architect.md
│   ├── frontend-engineer.md  backend-engineer.md
│   ├── reviewer.md  tester.md  qc.md  release-engineer.md
│   └── react-developer.md  react-native-developer.md  flutter-developer.md
│                                  #   (the 12 ai-team-loop roles)
├── scaffold/                      # → starter project tree used by --init
│   ├── README.md                  #   (copied to $PWD/README.md)
│   ├── CLAUDE.md
│   ├── AGENTS.md
│   ├── docs/{architecture,changelogs,plans}/
│   └── .claude/{config.md, agents/, commands/, shared/, outputs/, skills/, templates/}
└── skills/
    ├── architecture-doc-writer/   # → ~/.claude/skills/architecture-doc-writer/ (Claude)
    │   ├── SKILL.md               # → ## Skill: architecture-doc-writer in AGENTS.md (Cursor)
    │   ├── references/            # → ### References / #### <file> sub-sections
    │   └── assets/                # → ### Assets / #### <file> sub-sections
    └── ai-team-loop/              # → ~/.claude/skills/ai-team-loop/ (Claude)
        ├── SKILL.md               #   orchestrator playbook (the CEO loop)
        ├── references/            #   loop-protocol, roles, verify-gates, handoff-artifacts
        └── assets/                #   8 handoff-artifact templates (brief → release)
```

---

## Contributing

**Adding a skill:**
1. New dir under `skills/<your-skill-name>/`.
2. `SKILL.md` with YAML frontmatter (`name`, `description`).
3. Optional: `references/` (loaded on demand by Claude), `assets/` (templates).
4. Add a row to the **Skills** table above.

**Adding a subagent:**
1. New file at `agents/<your-agent-name>.md`.
2. Frontmatter: `name`, `description`, optional `model` (`sonnet` / `opus` / `haiku`).
3. Body = the system prompt — be specific about when to use it, what to inspect, what conventions to follow.
4. Add a row to the **Subagents** table above.

**Changing the project scaffold:**
1. Edit files under `scaffold/`. The directory mirrors the destination layout — a file at `scaffold/foo/bar.md` is copied to `$PWD/foo/bar.md` when a user runs `--init`.
2. Empty directories should keep a `.gitkeep` so they're preserved when scaffolded.
3. Add or update placeholders (`<PROJECT_NAME>`, `<API_PORT>`, etc.) consistently — they're the contract users find/replace after `--init`.
4. Test locally: `cd /tmp && mkdir t && cd t && /path/to/install.sh --claude --project --init` then inspect the tree.

See the [skills docs](https://docs.claude.com/en/docs/claude-code/skills) and [subagents docs](https://docs.claude.com/en/docs/claude-code/sub-agents).

---

## License

MIT — see [LICENSE](LICENSE).
