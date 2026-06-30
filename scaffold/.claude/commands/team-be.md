# Team · Backend build (`/team-be`)

Run the **BE lane**: approach note → (G3) → build the server slice to the contract. Routes to the specialist the Architect assigned.

### Procedure
1. Read `.claude/config.md` (migrate_cmd, schema_file), `.claude/outputs/team/02-design.md` (contract + schema binding) and `01-spec.md`.
2. Adopt the assigned agent: a specialist (`nodejs-developer`/`python-developer`/`golang-developer`) if installed, else `.claude/agents/team-loop/loop-backend-engineer.md`.
3. **Approach note first** in `03-impl-be.md` header. Stop until the Architect's `PROCEED` (`/team-arch --approach`).
4. **Build** to the contract: typed request/response, validation, authz guards, error envelope, audit rows; edge cases. Migrations verified non-destructive. Self-check; run typecheck/lint.
5. End with `> VERDICT: READY | gate: build-be | contract-complete: yes/no | next: G4 (/team-review)`.

### Next
- Both lanes built → `/team-review`.

---
BE lane for `$ARGUMENTS`. Reading the interface contract in `02-design.md`.
