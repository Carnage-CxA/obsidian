# Agent roster

Agents live in `.claude/agents/`. Claude Code loads them automatically. An agent is added only when a phase needs it, and removed if it stops earning its keep (adversarial-critic's call, Edward approves).

## Active

| Agent | Job | Model |
|---|---|---|
| chief-operator | Breaks work down, delegates, makes the go/no-go call. Never builds. | opus |
| builder | Implements: code, scripts, files. | sonnet |
| qa-engineer | Independent verification, binary PASS/FAIL with evidence. | sonnet |
| adversarial-critic | Hunts fake progress, bloat, weak handoffs. | opus |
| data-architect | Single source of truth: registry, topology, schema, importers. | opus |
| cx-authority | Is the engineering physically and procedurally correct? | opus |
| visual-director | The look; enforces DESIGN.md and the banned list. | opus |
| research-scout | Current, sourced facts (versions, vendor docs, standards). | haiku |
| system-fixer | Surgical repairs to agents, hooks, config. | sonnet |
| eval-designer | Turns a repeated failure into a permanent check. | sonnet |
| improvement-analyst | Root-cause across repeated failures. | opus |
| context-librarian | Keeps docs, decisions and indexes accurate. | sonnet |

## Planned (created when their phase starts)

| Agent | Phase | Why it will be needed |
|---|---|---|
| topology-engineer | 3 | Dedicated owner of the state-propagation engine and its test suite, the part that must never be wrong. |
| script-parser | 4 | Turns Excel/PDF IST and FPT scripts into structured steps the simulator can run. |
| security-reviewer | Before first deploy outside Edward's machine | Auth, data isolation per client project, secrets, before anyone else sees Obsidian. |

## Routing cheat sheet
- "Build X" → chief-operator → builder → qa-engineer (+ critic if non-trivial)
- Touches data → data-architect first
- Touches engineering logic → cx-authority before QA
- Touches the look → visual-director
- Same bug twice → eval-designer
