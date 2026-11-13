# OBSIDIAN — Operating Rules

Obsidian is a commissioning platform for mission-critical buildings (data centers first). It replaces Blackstart. Blackstart failed because features were added in no order, with no single source of truth. Everything in this file exists to stop that from happening again.

Owner: Edward Rivera (CxA, electrical). He decides scope, sequence and look. You build.

## The two must-haves (everything serves these)
1. **Site Map** — click through site → building → floor → room and see every asset inside, with its exact asset tag (nomenclature) visible. Mechanical, electrical, controls: anything in Cx scope.
2. **Sequence Simulator** — an interactive model of the real systems (one-lines, P&IDs) built from the project's sources of truth (CxAlloy / Procore documentation). Load an IST or FPT script, step through it, and the model shows what each step does to the system and flags steps that are impossible, out of order, or conflicting.

Both sit on the same thing: **one asset registry + one topology model.** No feature gets its own private copy of asset data. Ever.

## Build order is law
Work follows `docs/ROADMAP.md`. Rules:
- Only the **current phase** gets built. Check ROADMAP before starting anything.
- A phase is done only when every exit criterion has evidence and `qa-engineer` returns PASS. Non-trivial phases also get an `adversarial-critic` ACCEPT.
- New ideas, however good, go to `docs/IDEAS.md` with one line. They do not get built until Edward promotes them into a phase.
- If a request would jump the sequence, say so in one line and ask Edward before doing it.

## Where decisions live
- `docs/DECISIONS.md` — every architecture/stack/design decision, dated, with the why. Read before proposing anything that contradicts one.
- `docs/DESIGN.md` — the look. Mandatory for any UI work.
- `docs/WORKFLOW.md` — how chat, Claude Code and the agents split the work.
- If it isn't in the repo, it isn't decided.

## Design (summary — full rules in docs/DESIGN.md)
Obsidian must not look AI-generated. Before writing any UI: read `docs/DESIGN.md`, use only its tokens, and check the banned list. `visual-director` owns the look; nobody else changes tokens.

## Storage — non-negotiable
Everything lives on the Extreme drive under `X:\03 PROJECTS\OBSIDIAN\`. **Never OneDrive**, never the tablet's Documents/Desktop/Downloads. Every file you create goes into its folder per `docs/STORAGE.md`. If something is in the wrong place, move it and say so.

## Data rules
- Real client project data (drawings, one-lines, tag lists, scripts from Vantage, Project Plaid, etc.) is **never committed** to this repo. Use `fixtures/` with sanitized or synthetic data. Real data loads at runtime from the user's own storage.
- Asset tags are displayed exactly as the source document writes them. Never "clean up" nomenclature.
- Every fact the simulator relies on carries its source (document, sheet, revision).

## Stack — decided (details in docs/DECISIONS.md)
- v1 is local-first: runs on localhost, one database per client project on X:. No hosted database, no deploys, until Edward says so.
- Engine, importer, fixtures and validators are plain TypeScript packages: no Next, React or database imports inside them.
- **Postgres stores, never computes.** No graph or traversal logic in SQL functions, views or triggers.
- Surrogate ids everywhere. The client's tag is data, never a key, and is never rewritten.
- Nothing is ported from Blackstart except what the audit ledger marks KEEP or REBUILD. Never its data, migrations or git history.

## Engineering standards
- Simplest thing that meets the done-criteria. No speculative abstraction, no placeholder screens, no fake data dressed as real.
- Run what you build. "Should work" is not done.
- Small commits, one concern each, message says what and why.
- Tests for the topology/state engine are mandatory — it is the part that must never be wrong.

## Agents (.claude/agents/)
chief-operator (orchestrates, never builds) · builder · qa-engineer · adversarial-critic · system-fixer · eval-designer · improvement-analyst · context-librarian · research-scout · visual-director · data-architect (registry, topology, schema, importers) · cx-authority (is the engineering physically right?).
For any multi-step task, follow chief-operator's protocol.
- Any change to stored data, schema or importers → data-architect designs or reviews it.
- Any topology rule, state-engine behavior or simulator verdict → cx-authority must return CORRECT before qa-engineer can PASS it.
- New agents are added only when a phase needs one (see `docs/AGENTS.md`), never speculatively.

## Handoffs to Edward — required
When a step needs Edward to act, or would go better in another tool or surface, stop and give a SWITCH block before continuing. Format and triggers are in `docs/WORKFLOW.md`. Never leave him to guess what to do next.
