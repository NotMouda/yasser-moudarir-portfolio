# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is
Personal portfolio for **Yasser Moudarir** (Ingénieur Électromécanique · Performance Énergétique). Single-page, single-file `index.html` at the project root, data-driven via `data.json`. No build step, no npm dependencies. Tailwind via CDN.

## Always do first
Invoke the `frontend-design` skill before writing any frontend code in this repo. The aesthetic is already committed (see "Design system" below) — don't redesign without instruction.

## Commands
```bash
node serve.mjs      # static server on http://localhost:3000 (Range-aware for video)
```
- `serve.mjs` is a zero-dependency Node 24+ static server. If it's already running, don't start a second instance.
- **Do not take screenshots to verify visual changes.** Reason: screenshot capture + PNG reads burn tokens for little gain. Reason about layout from CSS/HTML directly; ask the user to verify visually if uncertain.

## Architecture

**Single-file site, runtime data binding.** `index.html` contains all CSS and JS inline. On load, `init()` (`<script>` block at the bottom of `index.html`) does `fetch('data.json')` and renders every section by mutating empty containers (`#trackSoftware`, `#expGrid`, `#projectsGrid`, `#timeline`, `#certGrid`, etc.). Editing the portfolio's *content* almost always means editing `data.json`, not the HTML.

**Hero video sequencing** (`heroVideo` IIFE in `index.html`). Two `<video>` elements are layered absolutely; `1.mp4` autoplays opaque on top, `loop.mp4` is preloaded underneath at `opacity:0`. On `1.mp4` `ended`, opacity swaps (~100ms), `revealHero()` adds `is-on` classes that trigger: lightning SVG draw (`stroke-dashoffset` 600ms), per-letter `letter-charge` keyframe (staggered via `animationDelay`), then subtitle/tagline/cta fade-up. There's a 12s failsafe in case `ended` never fires, and an autoplay-blocked fallback that skips straight to loop. **Touch this sequencing carefully** — flash-free transition + lightning timing + nav reveal are all coupled.

**Title splitting preserves word boundaries.** Each word is wrapped in `<span style="display:inline-block; white-space:nowrap">`, individual letters inside it. This stops mid-word breaks on narrow viewports — required because each letter is `inline-block` for the per-letter animation.

**Marquees use 4× duplication, not 2×.** With six items per row, 2× isn't wide enough to fill a 1440px viewport during a `translateX(-50%)` cycle, leaving a gap. Keep 4× when adding/removing skill items.

**Logo cells use light card backgrounds.** Many skill logos are JPGs with white backgrounds; `filter: brightness(0) invert(1)` would turn them into solid white squares. The cell background is a light gradient and the filter is `saturate(.7) contrast(1.02)` so colored logos read naturally as "badges". Don't switch to white-silhouette filters without re-checking every JPG.

**Asset paths with spaces/special chars must be `encodeURI`'d** before use as `<img src>`. Real filenames in this repo include `brand_assets/photo yasser (2).png`, `brand_assets/skills/software/Caneco  BT.jpg` (two spaces), `cv/CV YASSER FIN.pdf`, and `certifications professionnelles/.../Capture d'écran ....png`. The render code uses a `resolveImage()` helper for project/cert images that also intercepts `placehold.co` URLs and replaces them with inline SVG data URIs to avoid network flakiness.

**Plan files** live at `C:\Users\Admin\.claude\plans\` (outside the repo). The current plan is `prompt-portfolio-rippling-fog.md` — read it before substantial structural changes.

## Design system (committed — don't redesign on a whim)
- **Palette:** `--bg #070b14`, `--bg-2 #0a0a0f`, `--surface #0f1420`, `--cyan #00d9ff` (single accent), `--text #e8eef5`, `--line #1a1f2e`. No default Tailwind colors anywhere.
- **Type:** Space Grotesk 500/600/700 (titles, eyebrows, buttons), Inter 400/500 (body). Tracking `-0.03em` to `-0.04em` on large headings, `line-height: 1.7` on body.
- **Shadows:** multi-layer with cyan tint (e.g. `0 0 0 1px rgba(0,217,255,.08), 0 8px 32px -12px rgba(0,217,255,.18), 0 24px 64px -24px rgba(0,0,0,.7)`). Never `shadow-md`.
- **Animation:** only `transform` and `opacity`. Easing `cubic-bezier(0.16, 1, 0.3, 1)`. Never `transition-all`.
- **Reduced motion:** `prefers-reduced-motion: reduce` zeroes animations, hides hero videos, hides cursor canvas, and forces all stagger-revealed elements to `opacity:1`.
- **Mobile-first responsive.** Hero collapses to single column at ≤768px with a stronger overlay so text reads against the video. Timeline switches from centered to left-rail at ≤900px.

## Iteration discipline
1. Make the change, reason about the result from the CSS/HTML diff.
2. Critique with specifics ("ATLYKAS logo box is 64px but BOUYGUES is 72px because…") based on the code, not screenshots.
3. Verify mobile breakpoints by reading the relevant `@media` rules. Ask the user to confirm visually when in doubt.

## Hard rules
- No `transition-all`. No default Tailwind blue/indigo as primary color. No flat `shadow-md`.
- Don't add sections or features that aren't in `data.json` or the plan file. The site's content shape is fixed.
- Don't reintroduce the heavy `grayscale(1) brightness(.92)` logo filter — it makes red logos (AutoCAD) disappear on dark backgrounds.
- Don't auto-scroll into a section as a side-effect of `init()`. Only scroll on explicit user click (the `fromClick` flag in `activateExp`).
- Animated PCB traces, lightning, and timeline pulse use `stroke-dasharray`/`dashoffset` — keep that approach; don't switch to JS-driven path animation.
- **No screenshots.** Don't run `screenshot.mjs`, don't write to `./temporary screenshots/`, don't read PNGs there to verify your work. Read the code instead.
- **No `data-glow` (hue-shifting border) on certification cards.** Cert cards keep tilt + local cyan spotlight, but never the viewport-cursor hue-shifting border. Enforced in `setupTilt()` via `GLOW_SELECTOR` (excludes `.ym-cert-card`).
