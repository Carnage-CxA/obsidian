# Storage — where everything lives

## The rule
Everything for Obsidian lives on the **Extreme external drive (X:)**. Never OneDrive. Never the tablet's Documents/Desktop/Downloads (Windows can silently sync those to OneDrive). If a tool tries to save somewhere else, stop and move it.

GitHub (`Carnage-CxA/obsidian`, private) holds the code and docs only, so the chat and the terminal share one copy. Client files never go to GitHub.

## Layout

```
X:\03 PROJECTS\OBSIDIAN\
├── obsidian\                  ← the git repo: code + docs/ (this folder)
├── 01 REFERENCE\              ← non-client material: standards, vendor manuals, design inspiration
├── 02 PROJECT DATA\           ← real client source docs, one folder per project, never committed
│   ├── TX3 Vantage\
│   │   ├── One-Lines\
│   │   ├── Sequences of Operation\
│   │   ├── Equipment Lists\
│   │   └── Drawings\
│   └── Project Plaid\
├── 03 SCRIPTS UNDER TEST\     ← IST / FPT scripts to run through the simulator
│   └── <Script name>\
│       └── Supporting Documents\
├── 04 EXPORTS\                ← raw CxAlloy / Procore exports, dated folders (YYYY-MM-DD)
├── 05 BRAND\                  ← logo, mood board, fonts, screenshots of approved looks
├── 06 AUDITS\                 ← copies of audit reports and their evidence
└── 90 ARCHIVE\                ← superseded material; nothing is deleted, it moves here
```

## Naming
- Dates ISO: `2026-11-10`.
- No `final`, `v2_REAL`, `new`. Revisions say `Rev 1.0`, matching the source document.
- Asset tags in filenames exactly as the drawings write them.

## Every session
- Files Claude creates go straight into the matching folder above, never "Claude outputs" or Downloads.
- Anything that lands in the wrong place gets moved in the same session, with a one-line note of what moved where.
