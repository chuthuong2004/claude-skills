# Design: <feature title>

> Owner: Architect · Reads: `01-spec.md`, `00-brief.md`

## Approach summary
<2–3 sentences: the shape of the solution and why this over the alternatives.>

## Components touched
| Component | Change | New/modify |
|-----------|--------|------------|
| <module> | <what> | modify |

## Interface contract (FE ↔ BE)
The single source of truth that lets FE and BE build in parallel. Be exact.

- **Endpoint(s):** `<METHOD> <path>` → request `<shape>`, response `<shape>`, errors `<codes>`
- **Types / DTOs:** `<name> { field: type }`
- **Events / messages (if any):** `<topic> { payload }`

## Data / schema changes
- Migration needed: yes/no · Models: `<list>` · Backfill: <yes & how / no>

## Task split (lanes + assigned specialists)
- **FE lane →** `<react-developer | react-native-developer | flutter-developer | frontend-engineer>` — <why this specialist; key libs>
- **BE lane →** `<backend-engineer | …>` — <scope>

## Dependencies
- New deps: <none / name + justification + alternative considered>

## Risks
- **R-1** — <risk> → <mitigation>

## Alternatives considered (for the G2 critic)
- <Option B and why it was not chosen — gives the critic something to push on.>

---
> VERDICT: READY | gate: design | by: architect | next: G2 (independent critique)
