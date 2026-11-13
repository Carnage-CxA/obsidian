# Prompt: C: vs X: comparison (read-only)

Goal: confirm whether everything from the tablet's internal drive (C:) is already on the Extreme drive (X:), before any data gets moved.

**Read-only. Do not copy, move, rename, delete or edit anything on C: or X:, except writing the one report file named below.**

1. Read `X:\AI-Memory\START_HERE.md` and `X:\AI-Memory\LOCATIONS.md` for where projects currently live on X:.
2. Compare `C:\Users\EdwardRivera\projects\blackstart` against Blackstart's location on X: (expected `X:\03 PROJECTS\BLACKSTART`; use LOCATIONS.md if it says otherwise). Ignore `node_modules` and `.next`. A dry-run listing such as `robocopy <C path> <X path> /E /L /XD node_modules .next` is a good way to do it without copying.
   Report:
   - files only on C:
   - files only on X:
   - files on both that differ in size or modified date
   - for each copy: git branch, HEAD commit, number of uncommitted and untracked files
3. Check whether `C:\Users\EdwardRivera\Downloads\claude-agents` has a copy on X:, and whether the files match.
4. List any other folders under `C:\Users\EdwardRivera\projects` and say whether each has a counterpart on X:.
5. Do not print real client tags, client names or file contents in the report. File paths, counts and sizes are enough.

Write the result to `X:\03 PROJECTS\OBSIDIAN\06 AUDITS\2026-11-13 C-vs-X comparison.md` (this folder is outside the git repo; do not commit it).

Finish with a summary of at most 10 lines: does X: have everything, yes or no; if no, exactly what is missing or newer on C:, with total size. Then give Edward a SWITCH block sending him back to the "Obsidian Workup" chat with that summary.
