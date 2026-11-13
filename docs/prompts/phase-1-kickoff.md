# Prompt: Phase 1 kickoff — plan the data spine (no app code yet)

Run in the terminal after double-clicking START-OBSIDIAN. Use **plan mode** (Shift+Tab) and `/effort max`.

---

Phase 0 is closed. Plan Phase 1 (Data spine) from `docs/ROADMAP.md`. Follow chief-operator's protocol. **This run produces a plan document and one verification, nothing else: no schema, no packages, no app code.**

Read first: `CLAUDE.md`, `docs/ROADMAP.md` (Phase 1), `docs/DECISIONS.md`, `docs/STORAGE.md`, and `docs/audits/blackstart-audit.md` §3, §8 and §9 (salvage path).

Blackstart reference copy (read-only, never modify, never copy data out of it):
`X:\Preserved\Additional Extreme Files\Source Trees\Backup\projects\blackstart`
Only the items the audit ledger marks KEEP or REBUILD may inform the plan. Before proposing to reuse any Blackstart file (the seed CSVs especially), confirm it contains no real client tags.

1. **research-scout:** verify PGlite on this machine (ARM64 Windows). In a scratch folder outside the repo: install it, create a file-backed database, write and read a row, close and reopen it, and confirm the data persisted. Report version, what worked, and the single-process limits. If it fails, test `node:sqlite` the same way. Record the outcome as a dated entry in `docs/DECISIONS.md`.
2. **data-architect:** write `docs/plans/phase-1-data-spine.md` covering:
   - Package layout: plain TypeScript packages with no Next/React/database imports for registry types, importer, fixture generator and validators; where the database adapter sits.
   - Schema: site → building → floor → room → system → asset; asset (surrogate id, tag verbatim, type, discipline, room, system); aliases; connections (from/to, ports, medium: electrical or fluid, switching device, normal state); source document + revision on every fact. Show the query each table or column serves; no speculative columns.
   - Migration tool choice, and the rule that the chain must replay onto an empty database in a test.
   - Fixture spec: one sanitized site ("Site X") that exercises every asset type, with floors and rooms, dual-fed paths, a generator and transfer switch, a UPS with bypass, and one chilled-water loop with valves.
   - Importer: file formats for v1 (CSV first), the never-guess alias matcher, the unresolved-tags report, and the preview → exact-count guard → rollback → confirm → commit write path.
   - Test list in build order (importer, schema replay, tag byte-for-byte round trip, duplicate tag fails loudly, every connection resolves to two assets).
   - Build sequence as small steps, each with done-criteria qa-engineer can check.
   - Open questions for Edward, each with a recommended answer.
3. **cx-authority:** review the fixture's electrical and mechanical topology for physical plausibility (sources, transfer, UPS, bypass, normal states). Verdict in the plan.
4. **adversarial-critic:** review the plan. Cut anything not needed for the Phase 1 exit criteria. Verdict in the plan.

Commit the plan and the DECISIONS entry, and push. Then give Edward a summary of at most 10 lines and a SWITCH block sending him to the "Obsidian Workup" chat to approve the plan before any building starts.
