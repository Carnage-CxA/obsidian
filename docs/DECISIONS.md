# Decisions

Newest first. Each entry: date — decision — why. Open questions at the bottom.

- **2026-11-10 — Name: OBSIDIAN.** Fits the Blackstart / Umbra family, reads premium in a boardroom, red-on-black brand. Note: Obsidian (notes app) and Obsidian Entertainment exist; trademark/domain check needed before branding spend (consider a qualifier such as "Obsidian Cx").
- **2026-11-10 — One repo is the source of truth.** Chat (war room) and Claude Code terminal (factory) both read and write this repo; nothing is relayed by hand. See WORKFLOW.md.
- **2026-11-10 — Two must-haves define v1:** Site Map and Sequence Simulator, both on one shared asset registry + topology model. See CLAUDE.md.
- **2026-11-10 — Strict phase gating.** No feature outside the current ROADMAP phase; new ideas go to IDEAS.md.
- **2026-11-10 — No real client data in the repo.** Fixtures are sanitized/synthetic.

## Open
- Stack (frontend, database, hosting). Decide after the Blackstart audit. Supabase and Vercel are already connected to Edward's Claude account, which makes them the default candidates.
- How project data arrives: CxAlloy/Procore API vs. exported files. No connector exists for either today; start with exports.
- Desktop vs. web vs. both; offline use on site.
