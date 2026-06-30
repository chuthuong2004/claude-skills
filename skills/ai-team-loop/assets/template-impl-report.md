# Implementation: <FE | BE | mobile> lane — <feature title>

> Owner: <agent name, e.g. react-developer> · Reads: `02-design.md`, `01-spec.md`

## Approach note (write BEFORE coding — gated at G3)
- **Files to touch:** `<path>` — <why>
- **Shape of change:** <2–3 lines>
- **Libraries / patterns:** <existing seam reused / new lib + reason>
- **Contract items satisfied:** <which endpoints/types/events from the design>
- **Foreseen edge cases:** <list>

> G3 VERDICT (filled by architect): PROCEED | REVISE — <note>

---
## What was built (fill AFTER G3 passes)

### Files changed
- `<path>` — <one-line summary> (covers AC-<n>, contract item <x>)

### Files created
- `<path>` — <purpose>

### Migration (if any)
- File: `<path>` · Effect: <columns/indexes/backfill>

### Self-check
- [ ] Honors the interface contract exactly
- [ ] Matches surrounding conventions (imports, naming, error handling)
- [ ] Edge cases from the spec handled
- [ ] No out-of-scope changes
- [ ] Typecheck/lint pass locally
- [ ] No debug noise / commented-out blocks

### Deferred (not done here)
- <follow-ups worth a later plan>

---
> VERDICT: READY | gate: build-<fe|be> | by: <agent> | contract-complete: yes/no | next: G4 (review)
