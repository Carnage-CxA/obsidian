# Blackstart audit — what Obsidian keeps, rebuilds or kills

Date: 2026-11-10 · Phase 0 deliverable (ROADMAP: "Blackstart audit") · Prompt: `docs/prompts/blackstart-audit.md`
Status: **accepted by Edward 2026-11-13** — ledger (§8) accepted as written; the calls are logged in `DECISIONS.md`.

## Verdict in one paragraph

Blackstart's stack was not what failed. Its data was. It has one good idea for each Obsidian must-have: a directed edge table for topology, and a verbatim-tag, lazy-loaded registry browser. Both sit on a data plane Obsidian cannot use. The client's tag is the primary key. About 40% of the registry was fabricated by cloning a template building. 63 of 106 migrations are hand-run data surgery, and 44 migrations carry real client tags. At least 16 database objects and 5 columns the app depends on exist only in the live database. The topology has no device state, and its one "simulation" is physically wrong for a redundant plant. There is no floor or room, no mechanical topology, no IST/FPT step model and not a single automated test. About 10.3 MB of real client data is on GitHub. **Port patterns and a sanitized fixture, never data, migrations or engine code.**

---

## 0. Scope and method

**Which copy was audited (Step 0).** Neither copy is complete. GitHub `Carnage-CxA/blackstart` `origin/main` (@`1c8130a`, 2026-07-13, 271 files, 106 migrations) and the local working copy `C:\Users\EdwardRivera\projects\blackstart` (branch `power-path` @`a117613`, 2026-07-22) have **diverged**: local is 7 commits ahead of and 44 behind `origin/main`, plus 49 uncommitted changes. `origin/main` has `/review /completion /mops /plan /priorities` and the WebGL login. Local alone has `/planner`, three importer/auditor scripts and 31 untracked probe scripts. The audit covers **both**. Every finding is tagged **MAIN**, **LOCAL** or **BOTH**. `X:\03 PROJECTS\BLACKSTART` was not the GitHub match.

**How.** The audit covered 13 dimensions. Each was surveyed by one agent and then re-checked by a second, hostile agent that opened every cited file, read the line ranges and grepped the quotes. Result: **415 findings survived, 1 was cut, and roughly 1 in 8 was corrected** (usually the wrong tree, an overstated severity, or a redaction). An `adversarial-critic` pass then spot-checked 24 of this draft's claims directly against both trees. Its corrections are applied, and the dispositions are in §10.

**Limits — read these before trusting a "working".**
- **No runtime check against the live database.** The sandbox blocked production reads. "Working" means *statically traced*: every table, column and function the code touches exists in `supabase/schema.sql` + `supabase/migrations/*.sql`. That matters more than usual here, because the live database holds objects the repo cannot create (§2.4).
- Nothing was built, run or screenshotted. Blackstart was read only and not modified. Local HEAD and the 49-change count were checked before and after.
- **Redaction.** Real client tags, site/client names, MOP numbers and third-party emails are described by shape, never reproduced. Tags from `seed/*.csv` (building "X", `MV-SWBD-A`, `GEN-1`) are sanitized and quoted freely.

---

## 1. Inventory

Status key: **working** = statically traced end to end · **partly** = runs, but a material part is stubbed, wrong or dead-ends · **broken** = must throw or fail on the normal path, whatever the database holds · **unreproducible** = reads objects that no migration in this tree creates; it likely works against the shared live database, but that can't be verified or rebuilt · **dead** = unreachable or superseded.

### 1.1 Screens

| Route | Entry file | Tree | Status | Notes |
|---|---|---|---|---|
| `/` home hub | `app/page.tsx` | BOTH (diverged) | partly | KPI-tile hub. The unresolved metric can be silently truncated (MAIN). The uncommitted LOCAL edit keeps the older, inflated count. |
| `/login` | `app/login/page.tsx`, `LivingLogin.tsx`, `shaders/*.ts` | MAIN WebGL / LOCAL plain | working | MAIN: 40k-particle WebGL "organism". LOCAL: plain magic-link form that ignores `?error=callback`. |
| `/auth/callback` | `app/auth/callback/route.ts` | BOTH | working | Correct PKCE exchange with an open-redirect-safe `next`. |
| `/access-denied` | `app/access-denied/page.tsx` | BOTH | working | Public, no redirect loop. |
| `/assets` registry | `app/assets/page.tsx`, `registry-ui.tsx`, `actions.ts` | BOTH (byte-identical) | partly | Building → Wing → flat table, lazy-loaded. **Building/Wing filters do not filter the tree.** No tag search. The Status column reads a field the main importer never writes. |
| `/assets/[id]` detail | `app/assets/[id]/page.tsx`, `impact-tree.tsx` | BOTH | partly | Upstream/downstream impact tree. Path-blind: duplicates subtrees on every dual-fed path. RPC errors are swallowed. "Citations" is a hardcoded stub. |
| `/power-path` one-line | `app/power-path/page.tsx`, `power-path-view.tsx` (827 lines MAIN / 617 LOCAL), `actions.ts` | BOTH (diverged) | partly | Tier-column SVG graph viewer. MAIN adds flow animation, glow and fault sim, which LOCAL lacks. Intra-wing only. "Coming soon" stub in the drawer. |
| `/readiness` | `app/readiness/page.tsx`, `sections.tsx`, `discipline.ts` | **different page per tree** | MAIN partly / LOCAL unreproducible | MAIN turned it into an Operations Calendar. LOCAL calls `l4_readiness_summary()`, which LOCAL's migrations never define (MAIN's 083 creates it on the shared database). |
| `/planner` | `app/planner/page.tsx` (390 lines) | LOCAL only, never pushed | partly | Near-verbatim copy of MAIN `/readiness` plus a hardcoded building filter. Links to `/completion`, which LOCAL lacks. |
| `/completion` | `app/completion/page.tsx`, `table.tsx` | MAIN only | unreproducible | Reads a view no migration creates. Its count and its "pending units" list use different definitions. |
| `/priorities` | `app/priorities/page.tsx` | MAIN only | unreproducible | Catch-up curve + overdue + pending + FPT backlog on undefined tables, using a stale pending source. |
| `/plan` | `app/plan/page.tsx`, `actions.ts` | MAIN only | working | Daily crew roster. Write-only island, destructive delete-then-insert, no auth gate. |
| `/mops` | `app/mops/page.tsx`, `actions.ts` | MAIN only | working | Keeps a private copy of ACC issues in `mop_issues`. |
| `/ingest` | `app/ingest/page.tsx`, `actions.ts`, `lib/sitrep-*.ts` | BOTH | working (LOCAL throws on bad input) | Paste one SitRep's text. Delete-then-insert per (team, date). Non-transactional. |
| `/schedule/upload` | `app/schedule/upload/*`, `lib/schedule/*.mjs` | BOTH | working | **Best importer in the repo** (§3). Superuser pg pool. LOCAL has no auth guard on it. |
| `/schedule/resolve` | `app/schedule/resolve/*` | BOTH | working | Human picks assets per (team, signature). Rejects unknown IDs. |
| `/changes` | `app/changes/page.tsx`, `change-card.tsx`, `actions.ts` | BOTH | partly | Flat proposed/applied/rejected inbox. **Nothing in the app ever proposes a change**: only a test seed script feeds it. Accepting a DELETE hard-deletes. LOCAL writes any payload to any table. |
| `/review` self-audit | `app/review/page.tsx`, `actions.ts`, `unverified-edges.tsx` | MAIN only | unreproducible | Reads 5 views/tables and 5 edge columns that exist in no migration. Errors render as an empty "all clear". |

Nav (`components/nav.tsx`): MAIN has 12 entries, all real. LOCAL has 8, including `/planner`, and still shows on `/login`. The trees disagree.

### 1.2 Libraries and data layer

| Module | Entry | Status | Notes |
|---|---|---|---|
| Supabase clients + proxy gate | `lib/supabase/{client,server,middleware}.ts`, `proxy.ts` | working | Server-side `getUser()`. Cookie-less requests go to `/login`. Correct, but it is the *only* authorization (§5). |
| Allowlist | `lib/auth/allowlist.ts`, `lib/auth/guard.ts` (MAIN) | working, weak | 4 hardcoded emails (3 in LOCAL). `requireAllowed()` is used in only 3 of 9 server-action files. |
| Zoom maths | `lib/zoom.ts` | working | Pure log-scale zoom helpers. |
| Schedule parser | `lib/schedule/{parse,diff,db,write}.mjs` | working | Shared by the UI and the CLI. |
| SitRep parser | `lib/sitrep-parser.ts` + `lib/sitrep-corpus.mjs` | partly | Two copies that have **already drifted** (21 vs 25 family rules). |
| Traversal | `downstream_of` / `upstream_of` (SQL), power-path BFS ×2, drawer walk | partly | **Four separate traversal implementations, none tested.** LOCAL's SQL still walks excluded edges (migration 084 never reached it). |
| Schema | `supabase/schema.sql` + 106 migrations | broken as a source of truth | 28 tables, 7 views, 9 functions, 3 triggers *as written*, but 21 identifiers the code uses (16 objects + 5 columns) have no DDL (§2.4). |

### 1.3 Scripts (`scripts/`): 37 code scripts + 10 SQL dumps (MAIN), 3 LOCAL-only, 31 untracked probes (LOCAL)

| Group | Members | Status |
|---|---|---|
| Importers | `import-building.mjs` (ACC asset CSV), `import-acc-status.mjs`, `import-schedule.mjs`, `ingest-corpus.mjs`, `ingest-test-events.mjs`, `load-daily-*.m*s` | working |
| Importers, broken | `import.mjs` (its `ON CONFLICT` target matches no index after 087, so it must throw), `import-loops.mjs` on MAIN (calls `fn_register_revision`, which has no DDL) | broken |
| SQL-dump loaders (MAIN) | `load-*.mjs` + 10 `load_*.sql`: frozen dumps of real client data; they write tables no migration creates | broken + **leak** |
| Auditors | `master.mjs`, `mech_forms_audit.mjs`, `ats_fpt_audit.mjs` (LOCAL) | working, hardwired to a OneDrive folder |
| Resolution/triage | `propose-resolutions`, `triage-unresolved`, `resolve-final`, `finalize-triage` | working, one-shot |
| Data repair | `reconcile-b*.mjs` (fabricates a building by cloning another), ungated | working, **harmful** |
| Seed/fixture | `generate-seed.mjs` ✓, `seed-sitreps.mts`, `seed-proposed-changes.mjs` (plants test rows in prod), `seed-and-test.mjs` (broken under RLS) | mixed |
| "Tests" | `apply-and-test.mjs` (replays all migrations on prod, always prints "passed"), `verify-rls.mjs` (asserts exactly 5 assets), `test-schedule-diff.mjs` (live DB), `test-sitrep-parser.mts` (needs gitignored corpus) | broken / live-only |
| Probes (LOCAL, untracked) | 31 `scripts/_*.mjs` + `_audit_out/`, `_audit_scratch/` | dead; several write to the DB, none gated |

### 1.4 Specs vs reality

Built: the PRODUCT_SPEC MVP (registry, detail, impact tree), MULTI_BUILDING import, the dark theme. Half-built: SCHEDULE_DRIFT (snapshots only), DOC_REVIEW (no attempt log), SITREP (parser and ingest, but **not the conflict detector the spec calls "the feature that justifies BLACKSTART's existence"**). Never built: Claude PDF ingestion, the citation/change_log invariant, FORMS_ACCOUNTABILITY. Six shipped pages (`/review /readiness /completion /priorities /mops /plan`) appear in no spec or backlog item. ROADMAP, BACKLOG, PRODUCT_SPEC and README were last touched 2026-05-19 to 06-12. **122 commits followed the last BACKLOG commit (`4f0c862`), and 81 migration files were added or changed after it. Neither BACKLOG nor ROADMAP was updated to match.** BACKLOG.md: 28 threads opened, 4 closed, ~6 partly done, 18 still open.

---

## 2. Architecture

### 2.1 Stack
Next 16.2.6 · React 19.2.4 · TypeScript (strict) · Tailwind 4 · Supabase (Postgres + Auth via `@supabase/ssr`) · `pg` for direct connections · `exceljs` **and** `xlsx` (SheetJS, from a CDN tarball: two libraries for one job) · `adm-zip`, `utif`, `mammoth` · Playwright (installed, unused) · six 3D/animation packages for the login (two never imported). `package.json` scripts: `dev / build / start / lint`. **No `test`.** No `.github/`, no CI. Outside the app (6.7k lines) sit ~5.5k lines of untyped `.mjs` scripts, ~8.1k lines of schema/migration SQL and ~15k lines of committed SQL data dumps.

### 2.2 State management
Server components + `force-dynamic` + per-route `revalidatePath`. No client state library, no cache. PostgREST shaped the code: silent 1000-row truncation, a 50k global cap, paging loops, **no transactions** across calls, and TS2589 type-depth casts.

### 2.3 Build / deploy
Vercel from git push with no config. An empty commit served as the release trigger. Env vars live only in the dashboard. The **real production is `next dev` bound to `0.0.0.0` on Edward's machine**, launched by an untracked `.bat` that force-deletes git's index lock. One Supabase database serves dev, tests, loaders, agents and the live app.

### 2.4 Data model and where asset data lives
- **The client's tag is the primary key** (`assets.id text`). Every FK is `ON UPDATE NO ACTION`, so correcting a tag means editing many tables. The PK column also holds aliases, case variants and junk prefixes. Nothing constrains tag format.
- **How many copies of the same data:** asset identity sits in `assets.id` **plus 20 other columns across 16 tables, only 8 of them FK-enforced**. Building is stored in **13 columns in 3 formats** (`'N.0'`, `'BN'`, `'N'`), wing in 11, type in 11. L4 status is picked from 4–5 competing sources. Schedule, MOP, crew, SitRep-activity and ACC-issue tables carry **no asset key at all**. The same ACC export is parsed in **~11 separate places**, with **4 divergent ID-normalization rule sets**. Two asset importers write `assets` with contradictory ownership rules.
- **Topology** = one `relationships` edge table (parent → child, free-text `relationship_type`, `source_position`, `breaker_feeder`, `confidence`, later `edge_kind`, `normally_open` (034), `excluded` (053), `breaker_id` (057/072)). Ring topology is stored a second time in `loops`/`loop_members`, and nothing reads it. The breaker model (12 migrations, ~2,500 rows) is **write-only**.
- **Migrations are a data-surgery log, not a schema history:** 63 DATA-FIX, 20 FUNCTION/VIEW, 16 SCHEMA, 4 INDEX, 3 RLS. 69 of 106 change rows. Duplicate numbers (two 095s, two 103s; one 103 was rewritten after it was applied). The runner keeps **no ledger** and re-runs every file. **The chain cannot replay onto an empty database**, and **21 identifiers the pages, scripts and later migrations use (16 objects + 5 `relationships` columns, including `v_sitrep_completion` and `fn_register_revision`) exist only in production.** Neither tree can rebuild production.
- **Soft-delete** uses five overlapping mechanisms (`excluded`, `is_physical`, `superseded`, `sanitized_from`, status values), applied inconsistently.

---

## 3. The good: worth porting (as patterns, with the file)

| What | Where | Why it's good |
|---|---|---|
| Verbatim tag rendering | `app/assets/registry-ui.tsx`, `app/assets/[id]/page.tsx`: prints `{asset.id}` with no transform, in mono | Exactly Obsidian's rule. Keep the rendering contract. |
| Lazy per-group pagination | `app/assets/actions.ts`: 1000-row `range()` loop ordered by id | Immune to the PostgREST row cap. |
| Whole-table aggregate RPCs | migrations 009/011: `asset_filter_options()`, `asset_group_counts()` → JSONB in one round trip | Right way to feed filters and counts. |
| **Schedule snapshot → diff → approve** | `lib/schedule/*.mjs`, `app/schedule/upload/*`, `scripts/import-schedule.mjs` | One parser shared by UI and CLI. Preview never writes. Approve runs in one transaction. Snapshots are immutable. **The best importer pattern in the repo.** |
| 3-pass alias matcher with collision refusal | `scripts/import-acc-status.mjs`, `import-building.mjs` | Keeps the source name verbatim, records `match_method`, refuses ambiguous matches and queues unmatched ones. The seed of Phase 1's "tags that don't resolve" report. |
| Never-guess ID-resolution ladder | `scripts/import-loops.mjs` | exact → synonym fold → append unit → unique → flag and hold. Validates before it writes. |
| Preview → exact-count guards → ROLLBACK → typed keyword → COMMIT | migrations 032–082 (39 of 106 `raise exception`); LOCAL `scripts/_b21_preview/_apply`; `import-acc-forms.mjs` | The real verification layer Blackstart had. The late batch (099–101) dropped it. |
| Edge-table shape | `supabase/schema.sql` L44–58 + 034/053/057/072 | Directed edge, no-self-loop CHECK, unique directed edge, normal-state flag, soft-exclude, breaker link. The right *shape*, re-keyed on surrogate ids. |
| Change card with citation diff | `app/changes/change-card.tsx` | Payload vs `previous_state` diff with source doc, section, excerpt and URL. |
| Column-allowlist on change apply | MAIN `app/changes/actions.ts` L36–49 | A small, correct guard. |
| Auth plumbing | `lib/supabase/*`, `app/auth/callback/route.ts` | `getUser()` verification, cookie-carrying redirects, open-redirect-safe callback. The runtime never holds the service-role key. |
| SQL function hygiene | migration 092 | No `SECURITY DEFINER` anywhere, `search_path` pinned on every function, new views use `security_invoker`. |
| Input safety | across `app/` | Parameterized SQL, no raw HTML sinks, patched SheetJS. |
| **Secrets** | whole history | **No match on any ref for JWT, `sbp_`, `sk-ant-`, `ghp_`, AKIA, PEM or password-bearing postgres-URL patterns.** |
| Sanitized fixture | `seed/asset_registry.csv`, `seed/backend_relationships.csv`, `scripts/generate-seed.mjs` | Deterministic, sanitized single-building Tier III fixture. **Obsidian Phase 1's starting point.** |
| Smoke fixture | `supabase/fixtures/mvp_smoke_test.sql` | Sanitized 5-asset utility → CRAH chain with documented expected traversal. It has no assertions, so it is the expected-results half of a test. |
| Interaction scaffolding | `power-path-view.tsx` pan/zoom, click-vs-drag, trace, `lib/zoom.ts`; MAIN's >600-edge flow disable and reduced-motion fallback | Solid, cheap and correct. |

---

## 4. The bad: structural problems

1. **No single source of truth for an asset** (§2.4). This is precisely the failure Obsidian's CLAUDE.md exists to prevent.
2. **Tags are rewritten.** Tags were renamed in four incompatible ways (041/042/043/062/082a–c), "canonicalized" to a house style chosen by rulings: 65 renames, 42 source spellings hidden. Type is derived from tag substrings (014), and SQL regexes parse meaning out of the tag (lineup role, reserve side, related board). **This directly contradicts "tags displayed exactly as the source writes them."**
3. **Fabricated registry rows.** Migrations 015/016/018 and `scripts/reconcile-b*.mjs` clone whole buildings and swap the tag's first digit: **8,799 of ~21,849 rows (~40%) carry tags no source document ever wrote.** `sanitized_from` was reused to hold the template tag. Migration 100 creates 43 physical reserve boards with **no upstream source**. Topology for five buildings is "derived from the template pattern" by nothing in either tree.
4. **No device state.** Only static connectivity plus one `normally_open` boolean. That flag is set only on reserve feeds: generator feeds, ATS inputs and MV-ring N/O points are all stored as *normally closed*. No traversal or UI code reads it, and the UI and the DB disagree about what is standby.
5. **Physically wrong hierarchy baked into the data.** The tiers are right in outline (MV-ATS → PDC → TX → MSB → SWBD/ATS → loads), but:
   - UPS is a leaf, with critical RPPs hung directly off the MSB. Blackstart's own TOPOLOGY_NOTES calls that WRONG, and 052–055 put it back.
   - MV rings are a one-way TX→TX chain with no N/O point.
   - There is no bus or tie node type. 086 retired the one invented bus type (`MSB_OUTPUT_BUS`, 60 tombstones; its header calls it "a node type that does not exist in the field"), so ties and bus sections cannot be represented.
   - MSB "loops" feed switchboards in series.
   - LV loads hang directly off MV switchgear through "pattern-inferred" groups.
   - The graph stops at the campus MV-ATS: utility and turbine sources are not connected.
6. **Topology history is reversals, not evolution** (027→028, 045–049→052–055, 033→085, 099 contradicting 085). Hypothesis edges are stamped `confidence='confirmed'`. Copied breakers are stamped as read from elevation drawings.
7. **Four traversal implementations, zero tests.** The SQL CTEs terminate (cycle guard + depth 20), but they enumerate paths rather than nodes, ignore `normally_open` and `edge_kind`, and stop silently at depth 20. Two client BFS loops and a 16-round-trip drawer walk re-implement them.
8. **Features bolted on without sequence.** **At least 12 competing definitions of "complete/ready"** across 6 screens and ~12 migrations. "Authoritative L4" was redefined **three times in ~10 hours on 2026-06-23** (tracker 07:57 → ACC stage 17:25 → forms 17:32). Only `/completion` moved to the last one, so `/completion`, `/priorities` and the readiness "remaining" box show different pending numbers for the same family. `/readiness` is a different page in each tree. `/planner` is a copy.
9. **Provenance promised, never enforced.** The schema has `change_log` + `citations`, but all 63 data-fix migrations bypass them. Citations are never required, `previous_state` is never written, and apply is not atomic. The "revision engine" (`source_doc`, `source_rev`, `verified`, `fn_register_revision`, `document_revisions`, `v_graph_health`) **exists only in production.**
10. **No tests, no CI, no automated gate** (§2.1). What looks like tests are ~7 hand-run scripts against live production. The smoke "test" **leaked a 5-asset fixture into production and corrupted 55 real records**, and re-running either seed path would undo its quarantine.
11. **Engineering hygiene.** `power-path-view.tsx` is an 827-line client component that mixes graph model, layout, traversal, fault sim and fetching. Large pages do query, business rules and markup in one file. Supabase errors are silently dropped across many pages, so failures render as "no data". MAIN has no error boundary, loading UI or 404 (LOCAL's were never committed). The `.env.local` parser is copy-pasted into 31 of 48 scripts in two incompatible variants.
12. **Process memory failed.** The two-session planner/executor protocol worked as a *write brake* (~100 guarded, individually committed migrations). It failed as *shared memory*: by 2026-09-17 its own state files disagreed with each other, pointed at a stale clone and could not reach the database.

---

## 5. The dirty: security, client data, things that would embarrass us

**Client data on GitHub (critical).** Both GitHub branches carry **~10.3 MB in 36 paths** of real client data plus the scripts that embed it:
- `scripts/thermo_probe/` (25 files, 8.95 MB; 11 of them are extraction scripts and logs that embed tags), added inside commit `e84b4a8`, the one titled "gate destructive scripts". It holds:
  - real thermography results for ~975 pieces of equipment, with ACC deep links and FLIR tag dumps;
  - a raw thermal `.bin`;
  - **an iPhone photo taken inside the client facility, with GPS coordinates in its EXIF** (a UPS front panel and arc-flash label showing a real tag);
  - a live test-instrument screenshot.
- `scripts/load_*.sql` (10 files, MAIN): **~15k rows** of ACC issues, forms, status and the electrical tracker, full issue narratives, **52 third-party names** and ~4,500 distinct real tag-shaped tokens.
- `_audit_write_probe.txt` at the repo root, which shows `e84b4a8` was a blind snapshot of a dirty working tree.

Beyond those 36 paths, real tags, room numbers and project codenames run through **44 migrations** (hundreds of tag literals, 550–700 depending on the pattern used), the docs and CLAUDE.md, back to the first commit. Real crew names, tags and the employer's name are hardcoded in UI strings. A third-party contractor name is in UI copy. **Remediation cost:** a targeted `git filter-repo` purge rewrites **44 commits**, force-pushes 2 branches and re-syncs both clones. That covers only the bulk files: the tags inside migrations would need a full content rewrite. Make the GitHub repo private (if it isn't already) and archive it rather than scrub it. Obsidian must import none of its history.

**Client data in the local working directory.** `C:\Users\EdwardRivera\projects\blackstart` holds **171.5 MiB of client data inside the repo folder**: `corpus/` (585 files), `scripts/_audit_scratch/` (57 MB of FPT PDFs), `Claude outputs/` (client MOP/RCA deliverables whose filenames carry the MOP number, client code, building and wing), `seed-private/`, `scripts/_audit_out/` and `docs/Asset_detail_B<n>.csv`. **78.8 MiB (82.6 MB) / 87 files of it is not gitignored, so one `git add -A` away from repeating `e84b4a8`.** LOCAL's `.gitignore` lacks MAIN's `scripts/_*` rule, and nothing covers `Claude outputs/`. It also breaks Obsidian's STORAGE.md: data on C:, inputs from OneDrive.

**Auth is a UI fence.**
- The email allowlist exists only in the Next proxy (`lib/supabase/middleware.ts` L62–70).
- **All 23 RLS policies (across 11 migrations, 003–102) are `for all to authenticated using (true) with check (true)`.** `003_auth_policies.sql` L9 states the intent: "Single-user mode: any logged-in user gets full access".
- Login calls `signInWithOtp` without `shouldCreateUser:false`, and the anon key is public. So any email that can obtain a Supabase session could read and write every table through PostgREST, never touching the proxy. Whether sign-ups are enabled could not be checked (no `config.toml` committed).
- No migration enables RLS on `breakers` (057). Migration 098 L1 reports only one advisor security ERROR on 2026-06-23 (the backup table), so RLS on `breakers` was probably set out-of-band. That is the drift problem again, not necessarily an open table.
- A real-data backup table sat open to anon for ~21 hours.
- `requireAllowed()` guards only 3 of 9 server-action files. 6 (MAIN) / 7 (LOCAL) mutating actions skip it. LOCAL `acceptChange` writes any payload to any table named in a `change_log` row.
- `/schedule/upload` writes through a **postgres-role pool that bypasses RLS, over TLS with certificate checks off**, and on LOCAL with no auth gate.

**The live machine is behind GitHub on security.** LOCAL (which runs the live `:3000` server) is missing three security commits that exist only on GitHub, including the destructive-script gate (`e84b4a8` is not an ancestor of LOCAL HEAD). Even on MAIN that gate is a bare `--apply` argv check covering 9 scripts, and at least 14 live writers remain ungated.

**Scripts.**
- 30+ scripts read postgres/service-role credentials. 28 tracked ones disable TLS certificate checks. None checks which database it is pointed at.
- Tracked scripts hardcode Edward's user profile and an **employer-tenant OneDrive** folder as the client-data root.
- An untracked LOCAL script ran a live UPDATE on `relationships`, so the change exists in production but in no migration.
- `_auth_gate_verify` creates and deletes real users in the production auth tenant.

**Allowlist PII.** `lib/auth/allowlist.ts` commits 4 addresses (3 on LOCAL), including work emails at third-party domains. Its own comment still says "three".

---

## 6. The sexy: ideas Obsidian should keep even though the code dies

| Idea (stripped of implementation) | Where it showed up | Obsidian home |
|---|---|---|
| **"What does this take down / what feeds this back to utility": one click from any asset** | `/assets/[id]` impact tree | Phase 3 (engine query), surfaced in Phase 2 |
| **Fault shockwave**: de-energize in hop order, with a count banner and one-key restore | MAIN `/power-path` fault sim | Phase 3 rendering, but driven by a *real* engine. Blackstart's version is physically wrong |
| **Redundancy rules as an executable test spec** | `docs/TOPOLOGY_NOTES.md` L38–61, L171–177 (sanitize first) | Phase 3 test cases (cx-authority signs off) |
| **"Testing this electrical board de-energizes these mechanical units"** cross-discipline watch | the readiness views; logic lives only in a production-only view | Phase 4: the one Phase-4-shaped idea Blackstart had |
| **Proposed-change review queue**: before/after diff with citation, accept/reject | `/changes` + `change-card.tsx` | Phase 1 importer review step |
| **Unresolved-token triage loop that remembers the answer** | `/schedule/resolve` | Phase 1 "tags that don't resolve" report |
| **Verify-to-earn-trust on edges** (unverified → verified with source) | MAIN `/review` (broken) | Phase 1 provenance |
| **Live graph-health self-audit**: violation count per check (orphans, ID collisions, sourceless boards) | MAIN `/review` (broken) | Phase 1 importer QA report |
| **Preview → exact-count guard → ROLLBACK → typed keyword → COMMIT** | migrations, `_b21_*`, `import-acc-forms.mjs` | Every Obsidian write path |
| **Modelling rule: telemetry is never topology; normal state is separate from live state; breakers are switching devices on edges** | TOPOLOGY_NOTES + 057 | Phase 1 schema, Phase 3 engine |
| **"L4 scheduled before L3 complete" flag**: a sequencing check | readiness | Phase 4 validator rule (out-of-order dependency) |
| **Single unconditional dark theme** (no light mode, `prefers-color-scheme` removed on purpose) | `app/globals.css` | Phase 0 design (the decision, not the palette) |
| Study / offline RPP maps | exist only as hand-made client xlsx deliverables, no code | Phase 2 study mode (already on the ROADMAP) |

Not sexy, despite appearances: the always-on "live current" dash animation (decorative, not state) and the WebGL login (§7).

---

## 7. Look audit against `docs/DESIGN.md`

Blackstart is a stock 2025 Next.js/Tailwind dark dashboard. It is static reading only; nothing was rendered.

| DESIGN.md banned item | Blackstart | Evidence |
|---|---|---|
| Banned fonts (Geist) | **Commits it.** Geist / Geist Mono are the wired faces, Arial is the effective body face, and `ui-monospace` is used in the graph: 3–4 faces at once | `app/layout.tsx`; `app/globals.css` `--font-sans: var(--font-geist-sans)`, `body { font-family: Arial, Helvetica, sans-serif }` |
| One red accent | **Fails.** Accent is blue (`--primary: #2563eb`, `--link: #60a5fa`); red is one of six severity hues | `app/globals.css` |
| Purple/blue gradients, gradient text, glows | **Commits all three** on the MAIN login. Glow on traced power-path nodes (CSS `drop-shadow`). Glows on ops screens | `app/login/LivingLogin.tsx`, `power-path-view.tsx` |
| Glassmorphism / frosted blur | **Commits.** Frosted rounded-2xl login card; frosted overlays on the power-path canvas | `LivingLogin.tsx`, `power-path-view.tsx` |
| Large rounded cards with soft shadows as default | **Commits.** `--shadow-card` rounded card is the default container (35 uses in MAIN). Radii run 4/6/8/16px and full pills; **no corner is 0–2px** | `app/globals.css`, throughout |
| Hero + feature cards | **Commits.** The home hub is a hero header plus a grid of identical description/metric cards | `app/page.tsx` |
| Uppercase letter-spaced eyebrow labels | **Commits.** 41 hits in 17 files | throughout |
| Rows of identical KPI tiles | **Commits** on home, `/priorities`, `/review` | those pages |
| Emoji as icons | Partly: Unicode glyphs plus one emoji warning | ops screens |
| Skeleton shimmer | LOCAL's untracked `loading.tsx`: six pulsing rounded shadow cards | LOCAL `app/loading.tsx` |
| Cream surfaces, centered everything, toasts, filler copy, AI badges | **Clean.** Not committed | grep across both trees |
| Tags as hero, monospace | **Partly.** Mono almost everywhere, but hero-styled only on the asset-detail `h1`. Elsewhere tags are link-blue cells, 10px chips, or SVG labels **truncated at 16–17 characters**, which breaks "verbatim" | `registry-ui.tsx`, `power-path-view.tsx` |
| Tokens in one file | **Partly.** Colour tokens only, with no radius, spacing, type or motion tokens. Power-path keeps a private hard-coded hex palette | `app/globals.css`, `power-path-view.tsx` |

**Matches Obsidian's direction (keep as ideas):** the single unconditional dark theme, the 3-step surface elevation scale with two border strengths held as tokens, the semantic severity token set behind one `StatusPill` (its values are stock Tailwind 950/300 pairs and must be re-picked), and genuinely dense completion/readiness tables.

**WebGL login: KILL.** A teal-to-violet 40k-particle "organism" with bloom, heartbeat, breathing, depth of field and chromatic aberration, plus gradient text, a violet-glow gradient button and a frosted card. It is real craft, and it is exactly the decoration DESIGN.md bans: motion that represents no state. It also pulls in six 3D/animation packages.

---

## 8. Ledger

KEEP = port as is (shape/contract) · REBUILD = keep the idea, rewrite it · KILL = don't carry it over. Phases: 0 Foundation · 1 Data spine · 2 Site Map · 3 Topology + state engine · 4 Sequence Simulator · — = none.

| Item | Call | Reason | Phase |
|---|---|---|---|
| `seed/*.csv` + `generate-seed.mjs` fixture | **KEEP** (shape) | Only sanitized, deterministic data in the repo. Port to TS and extend with floor/room/discipline/system/source+rev/normal state/a CHW loop | 1 |
| Verbatim tag rendering (`{asset.id}`, no transform) | **REBUILD** | The contract is already Obsidian's rule, but tags must become hero type and never truncate (`power-path-view.tsx` slices at 16) | 2 |
| Lazy per-group loading | **REBUILD** | Keep lazy loading per group. Drop the PostgREST paging and SQL aggregate RPCs, which existed only to work around the 1000-row cap and conflict with "Postgres stores, never computes" | 2 |
| Schedule snapshot → diff → approve pattern | **KEEP** (pattern) | Best importer in the repo | 1 |
| Preview / exact-count guard / ROLLBACK / typed-keyword discipline | **KEEP** | The one verification layer that worked | 1 (all writes) |
| Auth plumbing (`lib/supabase/*`, callback) | **KEEP** (reference) | Correct, if Supabase Auth is used once hosted | later |
| `lib/zoom.ts`, pan/zoom/trace interaction, reduced-motion fallback | **KEEP** | Small, pure, correct | 3 |
| Function hygiene (no SECURITY DEFINER, pinned `search_path`, `security_invoker`) | **KEEP** | Correct defaults | 1 |
| Edge-table design | **REBUILD** | Right shape. Re-key on surrogate ids; add ports, medium, switching device, normal state everywhere, source doc + revision | 1 |
| 3-pass alias matcher + never-guess resolution ladder | **REBUILD** | Becomes the `asset_aliases` resolver and the unresolved-tags report, matching spellings without renaming tags | 1 |
| ACC/CSV importer (`import.mjs`, `import-building.mjs` parsers) | **REBUILD** | Port only the structure (transactional upsert, FK pre-flight, skip report). `import.mjs` is currently broken after 087. Parse *every* location segment: today room is never parsed, and the raw location survives only in free-text notes. CxAlloy/Procore formats appear nowhere in Blackstart | 1 |
| Provenance (`change_log` + citations) | **REBUILD** | Right idea, never enforced. Make source+revision required on every fact | 1 |
| Change review queue + citation diff card | **REBUILD** | Good UX. Needs real proposers, supersession and no hard-deletes | 1 |
| Graph-health / ID-collision self-audit | **REBUILD** | Good checks, broken implementation | 1 |
| Registry browse (Building → Wing) | **REBUILD** | Precursor only. Needs site → building → floor → room, tag search, study mode | 2 |
| Impact tree ("what does this take down") | **REBUILD** | Great question. Path-blind implementation duplicates subtrees on 2N | 3 |
| TOPOLOGY_NOTES redundancy/impact rules | **REBUILD** as tests | A ready-made Phase 3 test spec, once sanitized | 3 |
| Breaker records with source/verified flags | **REBUILD** | Right idea (derived kept apart from drawn), but write-only and mis-linked | 3 |
| `/power-path` one-line viewer | **REBUILD** | Hardcoded tier columns, not a one-line. Re-do as ELK layout + engine-driven state | 3 |
| Fault shockwave UX | **REBUILD** | Keep the interaction. The physics must come from the engine | 3 |
| Cross-discipline de-energization watch | **REBUILD** | The one Phase-4-shaped idea | 4 |
| "L4 before L3" sequencing check | **REBUILD** | Valid validator rule, wrongly placed on schedule data | 4 |
| Single dark theme + surface scale + severity token set | **REBUILD** | Keep the decisions; re-pick every value against DESIGN.md | 0 |
| LOCAL `ats_fpt_audit.mjs` / FPT form parsers | **REBUILD** (reference) | Closest thing to a script parser (numbered-section split) | 4 |
| Live Blackstart database (any row) | **KILL** | Mixes real, renamed and fabricated rows; schema not reproducible | — |
| The 106 migrations | **KILL** | Data surgery with hundreds of real tag literals in 44 files; cannot replay | — |
| `scripts/load_*.sql` + `thermo_probe/` | **KILL** + purge | Client-data leak | — |
| Tag-as-primary-key + tag rewriting/"canonicalization" | **KILL** | Contradicts the verbatim rule | — |
| Template-cloned buildings / derived topology | **KILL** | 40% of rows trace to no source | — |
| SQL recursive-CTE traversal + 3 JS traversals | **KILL** | Untested, state-blind, path-enumerating. Replace with one typed engine | — |
| Fault-sim implementation | **KILL** | Plain reachability; contradicts Blackstart's own impact rules | — |
| SitRep parser/resolver (`resolve_test_event`, 5 rewrites) | **KILL** | One client's tag grammar; guesses and silently drops | — |
| `/readiness /planner /completion /priorities /plan /mops` + their views | **KILL** | 12 definitions of "complete"; none is in ROADMAP phases 0–4 | — |
| `/ingest`, corpus tables, `ingestion_jobs` | **KILL** | Out of scope / dead schema | — |
| `loops`/`loop_members`, `loop` loader | **KILL** | Second copy of topology, destructive loader | — |
| Email allowlist in source + `using (true)` RLS | **KILL** | Authorization must live in the database (if hosted) | — |
| Superuser pg pool in the web app | **KILL** | Bypasses RLS over unverified TLS | — |
| `apply-and-test`, `seed-and-test`, `verify-rls`, probe pile | **KILL** | Run against prod, broken, ungated | — |
| WebGL login + its 6 packages | **KILL** | Banned decoration | — |
| Home KPI hub, nav | **KILL** | Tied to Blackstart's feature list; banned layout | — |
| Planner/executor hand-maintained state files | **KILL** (keep the write brake) | Failed as memory; derive state from git + migrations | — |
| Playwright-installed-unused, dual XLSX libs, CDN tarball | **KILL** | Supply-chain/reproducibility smell; pick one library | — |

---

## 9. Stack recommendation for Obsidian

**Bottom line:** keep the web stack, change where the logic and data live.

| Layer | Recommendation | Tradeoff / why |
|---|---|---|
| **Framework** | **Stay on Next 16 + React 19 + TypeScript (strict) + Tailwind 4.** Engine, importer, fixture generator and validators live in **plain TS packages with no Next/React/Supabase imports.** | Kept mainly for continuity: Edward's agents and Blackstart's correct auth/proxy code already target it, and no failure in either tree traces to Next (sequencing and data hygiene caused them). The real alternative for a single-user localhost app is a **Vite SPA + Node CLI**: simpler, offline-native, no server-component layer. It loses RSC for the read-heavy Site Map and the ready-made auth path if Obsidian is ever hosted. Against Next: version 16 post-dates most model training (hence the Next-16 guard in *Blackstart's* AGENTS.md), and its server features add nothing offline. Either way, framework-free packages run unchanged in the browser, a Node CLI, Vitest and any future desktop shell. |
| **Topology / state engine** | **A typed TypeScript engine; Postgres stores, never computes.** A pure `energize(topology, deviceStates)` returns each node's live/dead state, its feeding source(s) and validation flags. **No graph semantics in SQL functions or triggers.** | In-SQL traversal keeps logic next to data, but in Blackstart it went silently wrong for 31 migrations (`excluded` added in 053, traversal fixed only in 084), cannot express state, enumerates paths, and got re-implemented three more times anyway. Scale is not an argument: Blackstart's whole registry is ~21.8k rows (including the ~40% cloned), and its UI describes one building's topology as ~1.3k edges, so in-memory BFS is trivial. Switching devices become first-class (normal state + simulated position); ATS, UPS and ties are modelled with ports. |
| **Database** | **Postgres dialect, one schema owned by one in-repo migration tool (e.g. drizzle-kit). Local-first by default: PGlite (Postgres in WASM), file-backed, one database per client project under `X:\03 PROJECTS\OBSIDIAN\02 PROJECT DATA`.** Hosted Supabase later, when sharing is a real requirement, and then a **new** project with migrations applied only by CI. | DECISIONS.md names Supabase and Vercel as default *candidates* because they are connected. **The audit disagrees with letting that decide where client registries live** before the offline and confidentiality questions are answered. Blackstart's hosted instance holds real exports, drifted out-of-band, and PostgREST brought row caps and no transactions. PGlite keeps the same dialect and gives an ephemeral DB per test. *External claims, not audit findings; verify before the DECISIONS entry:* PGlite needs no native build on this ARM64 box, and it is single-process (so use a `globalThis` singleton in dev and run imports through the app). Fallback: `node:sqlite`, which is cheap to switch to because no logic lives in SQL. |
| **Auth / isolation** | **v1 local: bind to localhost, one DB per project, nothing to log into.** When hosted: keep the `@supabase/ssr` proxy pattern, but put authorization **in Postgres** (`project_members(user, project, role)` + RLS on every row), disable sign-ups, `shouldCreateUser:false`, allowlist from env, no superuser pool in the app, and a test that fails on any table without RLS or with `using (true)`. | Physical isolation beats policy for confidential client data. Blackstart is the cautionary tale: 23/23 `using (true)` policies. |
| **One-line rendering** | **ELK.js layered layout with ports, plus saved manual position overrides, drawn as token-styled SVG symbols driven by engine state.** | Blackstart's hand-rolled tier-column SVG can't draw buses, switches, ports or transfers, has no edge routing, and its picture changes whenever the sort order changes. ELK handles orthogonal routing with ports; overrides let Edward match the drawing. |
| **Parsing** | **CSV first. Exactly one XLSX library, from the npm registry (vendored if SheetJS). PDFs through a JS library, not an external binary.** | Blackstart pulls SheetJS from a CDN tarball *and* ships ExcelJS for the same job. CxAlloy/Procore formats appear nowhere in Blackstart, so only the patterns port, not the column maps. |
| **Testing** | **Vitest + fast-check (property tests) + a fresh in-memory PGlite per test file, from day one.** Playwright later, for two click-path checks ("any asset in ≤3 clicks", "step a script end to end"). | First coverage, in order: (1) importer: fixture imports cleanly, every connection resolves to two assets, unresolved tags are *reported not dropped*, tags round-trip byte-for-byte, a duplicate tag fails loudly; (2) schema: fresh-from-migrations equals the committed snapshot; (3) engine: normal / single-source loss / both-source loss / generator transfer / return-to-normal, plus TOPOLOGY_NOTES' rules on the fixture. Properties: nothing is energized without a closed path to an available source; every energized node names its source; closing a tie between two independent live sources is flagged. |
| **Hosting** | **Conditional on the open DECISIONS question "desktop vs. web vs. both; offline use on site".** If v1 is single-user on Edward's machine: `next build && next start` bound to localhost, data on X:, which works offline with no extra work. Vercel only for a fixture-only demo deployed from CI. | Blackstart's real production was `next dev` on `0.0.0.0`. If Edward decides on multi-user sharing or on-site use from a device without the X: drive, that answer moves this to hosted Supabase (new project) plus a PWA or desktop shell. Decide that first; this row follows from it. |

**Salvage path: what Phase 1 actually starts from, in order:**
1. `seed/asset_registry.csv`, `seed/backend_relationships.csv`, `scripts/generate-seed.mjs` → a TS generator producing site "X" with floors, rooms, discipline, system, source document + revision, normal states and a chilled-water loop with valves. Keep the column naming so one importer reads both.
2. The `relationships` shape (`schema.sql` L44–58 + 034/053/057/072) → the connection table, re-keyed on surrogate ids and extended.
3. The structure of `scripts/import.mjs` (transactional upsert, FK pre-flight, skip report; the file itself is broken after 087) and the location/category parsers in `import-building.mjs`, extended to parse every location segment → the Phase 1 importer + unresolved-tags report.
4. The collision-refusing matcher in `scripts/import-acc-status.mjs` → the alias resolver.
5. `docs/TOPOLOGY_NOTES.md` L38–61, L171–177, restated generically → the first engine test cases for cx-authority.

**Never import:** the live database, its migrations, `scripts/*.sql`, any row with `sanitized_from` set, or anything in LOCAL's `corpus/`, `seed-private/`, `_audit_*`. If a real bootstrap is ever wanted, re-run Obsidian's importer against the original exports.

---

## 10. Adversarial review

`adversarial-critic` reviewed the draft before commit, under the rule that findings that can't be backed by a file reference get cut. **Verdict: ACCEPT-WITH-CUTS.** It spot-checked 24 claims directly in both trees.

**Backed as stated:** 106 migrations; 63 data-fix; 12 definitions of complete; 23 `using (true)` policies; the ~21 h open backup table; 10.3 MB / 36 paths / 44 commits to purge; the GPS-tagged photo; the 2026-06-23 triple redefinition (`a3f1bde` 07:57 → `083b81b` 17:25 → `9007395` 17:32); the 827/617/390-line files; 4 traversal implementations; no test script or CI; `requireAllowed` in 3 of 9 action files; TLS-off scripts; ~975 thermography rows; 43 sourceless boards (100).

**Corrected in this version:**
- "20 objects missing DDL" → 21 identifiers (16 objects + 5 columns).
- "63 migrations with real tags" → 44 tagged.
- `breakers` "never gets RLS" → probably set out-of-band.
- RLS citation spread over 11 migrations.
- "20k lines of scripts" → 5.5k scripts + 15k SQL dumps.
- "84 commits after the freeze" → 122 after the last BACKLOG commit.
- ~14,150 → ~15k rows.
- MB → MiB where measured in MiB.
- 086 "bans bus nodes" → retired one invented bus type.
- New status *unreproducible* replaces "broken" for pages that read production-only objects.
- Two ledger KEEPs (verbatim rendering, pagination/RPC) downgraded to REBUILD.
- `import.mjs` flagged as broken.
- DECISIONS quoted correctly ("candidates").
- Hosting made conditional on the open offline/desktop question.
- PGlite platform claims labelled external.
- Vite SPA alternative weighed.
- A real building-code format redacted.
- Probe count fixed at 31.
- Secrets claim narrowed to the patterns actually searched.

**Cut:** nothing material. The rest were restatements, not removals.

**Measured from Blackstart's own docs, not the database:** the 8,799 / ~21,849 (~40%) fabricated-row figure (sum of 015 + 016 + 018; migration 076 cites 22,403 total). Treat it as approximate.

## 11. Decisions this audit puts in front of Edward

1. Accept or amend the ledger (§8). Anything moved to KEEP needs a reason recorded in DECISIONS.md.
2. **Stack:** first answer the open DECISIONS question (desktop vs. web, offline on site). Then confirm local-first PGlite + TS engine, or overrule in favour of the connected candidates, hosted Supabase + Vercel (§9 argues against making them the default for client data).
3. **Blackstart GitHub repo:** make it private (if it isn't), and decide archive-as-is vs `filter-repo` purge (44 commits, 2 branches).
4. **Local Blackstart folder:** move the 171.5 MiB of client data off C: to X: per STORAGE.md, and add the missing `.gitignore` rules before anything else gets committed there.
5. Promote nothing from §6 into a phase without saying so. The non-phase ideas go to IDEAS.md.
