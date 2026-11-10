# Prompt: Blackstart audit

Run in the terminal from `X:\03 PROJECTS\OBSIDIAN\obsidian`, with Blackstart attached:

```
cd "X:\03 PROJECTS\OBSIDIAN\obsidian"
claude --add-dir "C:\Users\EdwardRivera\projects\blackstart"
/effort ultracode
```

Blackstart exists in two places (`C:\Users\EdwardRivera\projects\blackstart` and `X:\03 PROJECTS\BLACKSTART`). Step 0 of the audit: confirm which copy matches GitHub `Carnage-CxA/blackstart` and audit that one.

Then paste everything below the line.

---

Audit the Blackstart codebase (the added Blackstart directory) to decide what Obsidian keeps, rebuilds or kills. Read `CLAUDE.md` and `docs/ROADMAP.md` in this repo first: Obsidian's two must-haves are the Site Map and the Sequence Simulator, both on one asset registry + topology model.

Do not modify anything in Blackstart. Read only.

Cover:
1. **Inventory** — every feature/module/screen that exists, one line each, with its entry file. Mark each as working, partly working, broken or dead code (verify by reading/running, not by its name).
2. **Architecture** — stack, data model, where asset/project data lives, how many copies of the same data exist, state management, build/deploy.
3. **The good** — what is genuinely well done and worth porting (cite files).
4. **The bad** — structural problems: duplicated data, tangled dependencies, features bolted on without sequence, missing tests.
5. **The dirty** — security, secrets in code, real client data committed, hard-coded paths, things that would embarrass us in front of a client.
6. **The sexy** — ideas or UX moments that worked and Obsidian should keep, even if the code doesn't survive.
7. **Look audit** — which parts of the UI read as AI-generated, measured against `docs/DESIGN.md`'s banned list.
8. **Ledger** — a table: item | KEEP (port as is) / REBUILD (keep idea, rewrite) / KILL | reason | Obsidian phase it maps to.
9. **Stack recommendation** for Obsidian, with tradeoffs, based on what you found.

Have `adversarial-critic` review the draft before finishing; findings it can't back with a file reference get cut.

Write the result to `docs/audits/blackstart-audit.md`, commit, and push. Then give Edward a 10-line summary and a SWITCH block sending him back to the "Obsidian Workup" chat to make the keep/kill calls.
