# Roadmap

One phase at a time. A phase closes only when every exit criterion has evidence and qa-engineer returns PASS.

**Current phase: 0 — Foundation**

---

## Phase 0 — Foundation
Goal: know exactly what to keep from Blackstart, lock the look, and pick the stack before writing app code.
- [x] Repo, CLAUDE.md, agents, workflow, roadmap (2026-11-10)
- [ ] Blackstart audit → `docs/audits/blackstart-audit.md` (prompt: `docs/prompts/blackstart-audit.md`)
- [ ] Stack decision recorded in DECISIONS.md, informed by the audit
- [ ] Design system v1: palette, type, spacing, components, banned list confirmed by Edward (`docs/DESIGN.md`)

Exit: audit accepted by Edward; stack + design decisions logged.

## Phase 1 — Data spine
Goal: one asset registry and one topology model everything else reads.
- Hierarchy: site → building → floor → room → system → asset
- Asset record: tag (verbatim), type, discipline, room, system, source document + revision
- Connections: feeds / fed-by (electrical), supply / return and valves (mechanical), with normal state
- Importer for CxAlloy / Procore exports (CSV/XLSX) into the registry; report of tags that don't resolve
- Fixture: one sanitized building that exercises every asset type

Exit: fixture imports cleanly; every connection resolves to two real assets; tests green.

## Phase 2 — Site Map (must-have #1)
Goal: click building → room → asset, tags always visible, learnable.
- Building and room navigation from the registry (no hard-coded layouts)
- Room view: every asset in the room, grouped by discipline, tags verbatim
- Search by tag jumps to its room
- Study mode (hide tags, guess, reveal) to memorize locations

Exit: Edward can find any fixture asset in ≤3 clicks or one search.

## Phase 3 — Topology + state engine
Goal: the model knows what is energized / flowing given device states.
- Electrical first: utility/MV sources, transformers, switchgear, breakers, ATS/ATC, generators, UPS, PDUs
- State propagation: breaker open/close → which buses are live, from which source
- Mechanical second: chillers, pumps, valves, CRAH/FWU/CDU flow paths
- Rendering on a one-line/P&ID view

Exit: engine tests cover normal, single-source loss, both-source loss, generator transfer, return-to-normal.

## Phase 4 — Sequence Simulator (must-have #2)
Goal: prove an IST/FPT script is physically executable.
- Script model: ordered steps, each an action on a tagged device + expected result
- Step through, watch the model change, see expected vs. modeled result
- Validator flags: unknown tag, impossible action (operating a device with no source, closing into an interlock), out-of-order dependency, expected result the model contradicts
- Report export listing every flag with step number and source reference

Exit: a sanitized copy of a real IST script runs end to end and every planted error is caught.

## Phase 5+ — Gated
Only items Edward promotes from `docs/IDEAS.md`.
