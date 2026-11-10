# Design — Obsidian must not look AI-made

Status: **draft v0** — direction set, tokens to be locked by visual-director in Phase 0 and approved by Edward.

## Direction
Black volcanic glass with one red. Feels like an instrument, not a website: a switchgear lineup, an F1 pit-wall telemetry screen, an aircraft glass cockpit, a SCADA HMI built by someone with taste.

- **Surfaces:** near-black layers (not pure #000), hard edges.
- **One accent:** red. Used for state and action only (energized, selected, fault, primary action) — never decoration.
- **Asset tags are typography.** Tags like `MV-9`, `REC-18CD`, `CX-17D` are set in a monospace face and treated as the hero element, not small grey metadata.
- **Density over air.** Engineers want information per screen. Tight grids, aligned columns, real data.
- **Motion means state change.** Animation only when something energizes, flows, transfers or fails. Nothing floats, bounces or fades in for show.
- **Corners:** 0–2px. No pill-shaped everything.

## Banned (the tells of AI-generated UI)
- Fonts: Inter, Roboto, Geist, Poppins, Montserrat, Space Grotesk, system-ui as the brand face
- Purple/blue gradients, gradient text, glows behind cards
- Glassmorphism / frosted blur cards
- Cream / beige / off-white "paper" backgrounds
- Large rounded cards with soft drop shadows as the default container
- Hero + three feature cards; centered everything
- Small uppercase letter-spaced "eyebrow" labels over every heading
- Rows of identical KPI tiles as the default dashboard
- Emoji as icons; sparkle ✨ / "AI" badges; a default icon set dropped in uncurated
- Filler copy: seamlessly, unlock, empower, elevate, supercharge, effortless
- Toasts for everything; skeleton shimmer on every load

## Process
1. visual-director builds a mood board from non-software references (gear, cockpits, motorsport, industrial design) and proposes 2 directions.
2. Edward picks one. Tokens go in one file (colors, type scale, spacing, radii, motion) — no one-off values in components.
3. Every UI change is checked against this file and screenshotted at desktop and phone width before handoff.
