# Team · Frontend build (`/team-fe`)

Run the **FE lane**: approach note → (G3) → build the client slice to the contract. Routes to the specialist the Architect assigned.

### Procedure
1. Read `.claude/config.md`, `.claude/outputs/team/02-design.md` (binding contract) and `01-spec.md`.
2. Adopt the assigned agent: a specialist (`react-developer`/`vue-developer`/`react-native-developer`/`flutter-developer`) if installed, else `.claude/agents/team-loop/loop-frontend-engineer.md`.
3. **Approach note first** in `03-impl-fe.md` header. Stop until the Architect's `PROCEED` (`/team-arch --approach`).
4. **Build** to the contract: loading/empty/error states, validation, accessibility, cache invalidation. Self-check; run typecheck/lint.
5. End with `> VERDICT: READY | gate: build-fe | contract-complete: yes/no | next: G4 (/team-review)`.

### Next
- Both lanes built → `/team-review`.

---
FE lane for `$ARGUMENTS`. Reading the interface contract in `02-design.md`.
