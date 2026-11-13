# Design — Obsidian must not look AI-made

Status: **v1, locked 2026-11-13.** Edward chose direction A (Lineup) with B's oversized tag and path-to-utility panel. Reference mockup: the "Chosen" artboard on the canvas "Obsidian — Two Directions". Changes to anything below go through visual-director and Edward.

## Direction
Black volcanic glass with one red. An instrument, not a website. The reference is the gear itself: a switchgear lineup with engraved nameplates and indicating lights.

- **The room is a lineup.** Assets are drawn as sections standing side by side, like facing the gear. Location is the point of the Site Map; never reduce a room to a plain list.
- **Every asset wears a nameplate.** The tag sits on a black plate with a thin white edge, the way an engraved nameplate looks.
- **Tags are the hero.** Monospace, verbatim, never truncated. If a tag doesn't fit, it gets smaller or wraps; it is never cut.
- **Density over air.** Tight grids, aligned columns, real data.
- **Hard edges.** Radius 0 everywhere.

## The two color rules
1. **Red means energized. Nothing else.** Same convention as the red indicating light on the gear. Not selection, not buttons, not decoration, not errors.
2. **White means "you are here."** Selection inverts to the paper color (white plate, black text) plus a 2px white ring.

De-energized is a hollow square, so state never depends on hue alone. Faults, flags and warnings (Phase 4) get their own treatment by a logged decision; do not reach for a new color without one.

## Tokens
These names are the contract. Values live in one token file in code; components never hard-code them.

### Color
| Token | Value | Use |
|---|---|---|
| `ink-0` | `#0A0A0B` | Page background |
| `ink-1` | `#101012` | Panels: top bar, room list, detail panel |
| `ink-2` | `#141417` | Section (asset) body |
| `ink-well` | `#050506` | Nameplates, inputs |
| `rule` | `#2B2B30` | Hairlines, section borders |
| `rule-strong` | `#55555C` | Input and secondary-button borders, de-energized links |
| `paper` | `#EDEDEF` | Primary text, selection fill, primary button |
| `text-2` | `#A6A6AD` | Secondary text. **The dimmest text allowed.** |
| `live` | `#D8140C` | Energized: lamp, path node, path link. White text on it passes contrast. |
| `live-text` | `#FF5347` | Only if red must be text on black |

### Type
| Role | Face | Notes |
|---|---|---|
| Headings, wordmark | Barlow Condensed 600 / 700 | Room title 40px, panel titles 24px |
| Interface text | Barlow Semi Condensed 400 / 500 / 600 | Base 16px |
| Tags and every data value | Martian Mono, width 75% to 87.5%, weights 500 / 700 / 800 | Counts, sources, paths, tags |

- Scale (px): 12, 13, 14, 15, 16, 17, 18, 24, 40. Hero tag: up to 64, sized to fit.
- Minimum text size 12px.
- Sentence case for labels and headings. Tags and the wordmark are the only all-caps text.
- **Fonts are bundled in the app** (both are open-licensed). No font CDN: Obsidian must work offline on site.

### Space, shape, motion
- Spacing steps (px): 2, 4, 6, 8, 10, 12, 16, 20, 24, 28.
- Lineup gap: 2px, so sections read as one lineup.
- Radius: 0. (Only exception: the two screw dots on a nameplate, if drawn.)
- Borders: 1px `rule`. Selection ring: 2px inset `paper`.
- Shadows, gradients, blur: none.
- Touch target: 44px minimum. It gets used on a tablet in the field.
- Motion: none by default. State changes (a lamp, a path link) may take 120ms linear and must respect reduced-motion. Energize / transfer animation is defined in Phase 3.

## Components (v1)
- **Nameplate:** `ink-well` plate, 1px `paper` edge, tag centered in Martian Mono 700 at 75% width. Selected: `paper` plate, `ink-0` text.
- **Section:** one asset in the lineup. Nameplate, type, lamp + state word, upstream tag(s). `ink-2` body, 1px `rule`, min height 172px, min width 152px.
- **Lamp:** 12px square. Energized: solid `live`. De-energized: hollow, 2px `text-2` edge. Always paired with the state in words.
- **Hero tag:** the selected asset's tag in the detail panel. Martian Mono 800 at 75% width, as large as fits, up to 64px.
- **Path to utility:** vertical chain from source to the selected asset. Lamp-style node per asset, 2px link between nodes, `live` where energized, `rule-strong` where not. The selected asset is bold.
- **Room list:** rows 44px, name left, asset count right in mono. Current room inverts to `paper`.
- **Buttons:** primary is `paper` fill with `ink-0` text; secondary is transparent with a `rule-strong` border. Never red.
- **Input:** `ink-well`, 1px `rule-strong`, mono text, 44px tall, with a visible label.
- **Wordmark (provisional):** OBSIDIAN in Barlow Condensed 700, letter-spacing 0.16em, with an 8×24px `live` bar before it. Final brand work is separate.

## Banned (the tells of AI-generated UI)
- Fonts: Inter, Roboto, Geist, Poppins, Montserrat, Space Grotesk, JetBrains Mono, system-ui as the brand face
- Purple/blue gradients, gradient text, glows behind cards
- Glassmorphism / frosted blur cards
- Cream / beige / off-white "paper" backgrounds
- Large rounded cards with soft drop shadows as the default container
- Hero + three feature cards; centered everything
- Small uppercase letter-spaced "eyebrow" labels over every heading
- Rows of identical KPI tiles as the default dashboard
- Emoji as icons; sparkle / "AI" badges; a default icon set dropped in uncurated
- Filler copy: seamlessly, unlock, empower, elevate, supercharge, effortless
- Toasts for everything; skeleton shimmer on every load
- A colored left border as the "selected" or "status" marker
- Truncating a tag with an ellipsis

## Process
1. Every UI change is checked against this file and its banned list before handoff.
2. Screenshot at desktop and phone width; approved looks are saved to `X:\03 PROJECTS\OBSIDIAN\05 BRAND`.
3. Mockups use fixture tags only (Site X, `UPS-A1`, `MSB-A1`). Never a real client tag.
