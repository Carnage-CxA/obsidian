# Decisions

Newest first. Each entry: date — decision — why. Open questions at the bottom.

- **2026-11-13 — Audit ledger accepted as written** (`docs/audits/blackstart-audit.md` §8: 6 KEEP, 18 REBUILD, 18 KILL). Obsidian ports patterns and the sanitized fixture only: no Blackstart data, migrations, history or engine code. Blackstart itself keeps running unchanged. Any later move of an item to KEEP needs its own entry here.
- **2026-11-13 — v1 is local-first.** Obsidian runs on Edward's machine, bound to localhost, with one database per client project stored under `X:\03 PROJECTS\OBSIDIAN\02 PROJECT DATA`. Works offline on site and keeps client data off any cloud. Hosting comes later, as a new setup with its own security review, when sharing is a real requirement. This closes the open questions "desktop vs. web" and "stack".
- **2026-11-13 — Stack (audit §9).** Next 16 + React 19 + TypeScript strict + Tailwind 4 for the app. Engine, importer, fixture generator and validators live in plain TypeScript packages with no Next/React/database imports. Postgres dialect through PGlite, one in-repo migration tool, surrogate ids (the client's tag is data, never the key). **Postgres stores, never computes**: no graph logic in SQL. ELK.js for one-line layout. Vitest + fast-check + a fresh in-memory database per test file from day one. One XLSX library. *To verify at Phase 1 start (research-scout):* PGlite on this ARM64 Windows machine and its single-process limits; fallback is `node:sqlite`, cheap because no logic lives in SQL.
- **2026-11-13 — Blackstart GitHub repo: back up, then remove the client data from GitHub.** Order is fixed: (1) full mirror of both branches onto X:, verified; (2) Edward chooses purge-the-files vs. delete-the-GitHub-copy and gives an explicit go at that moment. Not started. Irreversible, so never done on a general instruction.
- **2026-11-13 — Blackstart local folder.** Add the missing ignore rules so client files cannot be committed by accident; copy the client data from C: to X: (copy, never delete; Edward decides what happens to the C: copies). Approved, not started.
- **2026-11-10 — Name: OBSIDIAN.** Fits the Blackstart / Umbra family, reads premium in a boardroom, red-on-black brand. Note: Obsidian (notes app) and Obsidian Entertainment exist; trademark/domain check needed before branding spend (consider a qualifier such as "Obsidian Cx").
- **2026-11-10 — One repo is the source of truth.** Chat (war room) and Claude Code terminal (factory) both read and write this repo; nothing is relayed by hand. See WORKFLOW.md.
- **2026-11-10 — Two must-haves define v1:** Site Map and Sequence Simulator, both on one shared asset registry + topology model. See CLAUDE.md.
- **2026-11-10 — Strict phase gating.** No feature outside the current ROADMAP phase; new ideas go to IDEAS.md.
- **2026-11-10 — No real client data in the repo.** Fixtures are sanitized/synthetic.

## Open
- How project data arrives: CxAlloy/Procore API vs. exported files. No connector exists for either today; start with exports.
- Design system v1: which of visual-director's two directions (Phase 0).
- Trademark/domain check on the name before branding spend.
