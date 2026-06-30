# Test Report — <feature title>

> Owner: Tester · Reads: the diff, `01-spec.md`

## Commands run (with real output)
| Step | Command | Result |
|------|---------|--------|
| Typecheck | `<cmd>` | PASS / FAIL |
| Lint | `<cmd>` | PASS / FAIL |
| Build | `<cmd>` | PASS / FAIL |
| Unit | `<cmd>` | <N passed / M failed> |
| Integration | `<cmd>` | <…> |

## Tests added/updated
- **T-01** — `<file>` — asserts AC-<n> behavior — <pass/fail>
- **T-02** — …

## Failures
- **<test name>** — expected `<x>`, got `<y>` — likely cause `<file:line>` — **route:** <engineer>

```
<verbatim failing output>
```

---
> VERDICT: PASS | gate: G5 | by: tester | unit: N/N | next: G6
> (on fail) VERDICT: FAIL | gate: G5 | by: tester | route: <engineer> | failed: T-03
