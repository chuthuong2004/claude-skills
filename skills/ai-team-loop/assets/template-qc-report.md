# QC Report — <feature title>

> Owner: QC · Reads: `00-brief.md` (criteria), `01-spec.md`, running app

- **Environment:** <dev base URLs> · **E2E tool:** Connected / Not connected
- **Overall:** PASS / FAIL

## Acceptance criteria verification
Each AC from the brief, marked with evidence. No checkmark without evidence.

| AC | Statement | Result | Evidence |
|----|-----------|--------|----------|
| AC-1 | <…> | ✔ / ✘ | `evidence/<slug>/ac1.png` / response log |
| AC-2 | <…> | ✔ / ✘ | … |

## Critical-path smoke
| Path | Result | Notes |
|------|--------|-------|
| <flow> | PASS/FAIL | console errors: <0/N> |

## Bug reports
### BR-01 — <title>
- Severity: Critical/Major/Minor · Found in: AC-<n>
- Repro: <exact steps> · Expected: <…> · Actual: <…>
- Evidence: <path/log> · Affected: `<file:line>` · **route:** <role>

---
> VERDICT: PASS | gate: G6 | by: qc | criteria: N/N green | next: ship-decision (ceo)
> (on fail) VERDICT: FAIL | gate: G6 | by: qc | failed: AC-3, AC-5 | route: <roles>
