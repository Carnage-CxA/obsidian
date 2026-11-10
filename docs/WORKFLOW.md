# How Obsidian gets built

## Two rooms, one repo

| | War room | Factory |
|---|---|---|
| Where | Claude chat (claude.ai / desktop app), chat "Obsidian Workup" | Claude Code in the terminal, inside `~/projects/obsidian` |
| For | Strategy, architecture, decisions, audits, research, design direction, reviewing results | Writing code, running tests, building screens, migrations, deploys |
| Writes to repo | `docs/` (decisions, roadmap, design, prompts) | Code, tests, and `docs/` status updates |

Both read the same repo, so nothing gets relayed by hand. The chat pushes decisions to GitHub; the terminal pulls them (`git pull`) and builds. The terminal pushes code; the chat reads it from GitHub.

**Rule:** every session in either room starts with `git pull` and ends with a commit + push. If it isn't pushed, the other room can't see it.

## SWITCH blocks (handoff notifications)

Whenever Edward needs to do something, or a task would be done better in a different place, Claude stops and shows this, before continuing:

```
⚡ SWITCH → <where: Terminal / Chat / Browser / Desktop app / GitHub>
Why:     <one line>
Do this: 1. <exact step or command>
         2. <...>
Effort:  <Low / Medium / High / Max / Ultracode>   Mode: <normal / plan>
Then:    <what to say or paste back, and where>
```

Triggers — always give a SWITCH when:
- the work is hands-on building/testing and we're in chat → go to Terminal
- the work is a decision, review, or strategy question and we're in Terminal → go to Chat
- Edward must click/approve something (GitHub repo, connector, folder access, login, payment, deploy approval)
- the task needs a different effort level or plan mode than the current one
- a specific agent should run the task (name it)
- a step can't be done from the current surface (no access, blocked permission)

## Effort guide

| Task | Effort |
|---|---|
| Questions, renames, copy edits, tiny fixes | Low / Medium |
| One screen, one function, normal feature work | High (default) |
| Schema, architecture, nasty bugs, topology engine logic | Max |
| Whole-module builds, codebase audits, large refactors | Ultracode (`/effort ultracode` in terminal) |

Start a new module in **plan mode** (Shift+Tab in the terminal) so the plan is approved before code is written.

## Agents

Live in `.claude/agents/`. Claude Code loads them automatically; call one by name ("have qa-engineer verify this") or let chief-operator route.
