# MGT Conditions (built on Mast for Astro)

This project is **MGT Conditions**: runner-reported conditions for Toronto's
Martin Goodman Trail. Read `docs/PROJECT.md` first — it holds the agreed
design, features, build phases and the Stacki editing rules.
`reference/prototype.html` is the working prototype to match.

The site is built on Mast, a minimal, component-first CSS framework (Astro
edition). The Mast style-guide and blog demo pages were removed. Guiding principle:
**as little abstraction as possible** — prefer deleting a layer over
adding one.

## Commands

- `npm run dev` / `build` / `preview`
- `npm run check` — typecheck .astro files (must stay at 0 errors)
- `npm run format` — prettier

## Architecture

- `src/styles/` — the framework CSS in cascade layers
  (tokens → base → layout → typography → components → utilities).
  `tokens.css` is the theme: colors, fonts, and fluid min/max pairs at
  the top are the editable surface; the generated clamp() formulas at the
  bottom are never edited by hand.
- `src/components/` — typed .astro components. Interactive components
  carry their own `<script>` import (Astro dedupes per page and only
  ships JS for components actually used).
- `src/scripts/` — self-initializing vanilla TS modules. Each exits
  early when its elements aren't on the page. Attribute APIs
  (`data-accordion`, `data-tabs-*`, `data-slider`, `data-video`,
  `data-theme-toggle`) match the upstream Webflow Mast scripts in
  `nocodesupplyco/mast`. The marquee (`data-marquee-*`) is CSS-only —
  no script.
- `reference/prototype.html` — the single-file prototype (design + logic
  reference only; not served, not part of the build).
- `docs/PROJECT.md` — project brief, phases, Stacki rules.

## Conventions

- **Classes are the styling API; components exist for structure and
  behavior.** Structural components (`Section`, `Row`, `Col`,
  `ContentWrap`, `Spacer`, `Divider`) emit minimal DOM with the Mast
  classes; text is plain HTML (`<h2 class="h1">`, `<div class="rich-text">`,
  `<div class="eyebrow cc-rule">`). There are intentionally NO wrapper
  components for headings, rich text, or eyebrows.
- Variant classes are `cc-*`; utilities are `u-*` and always win (last
  layer). No `!important` outside of documented exceptions.
- Theming is `light-dark()` + `color-scheme`. Never branch on a theme
  class in CSS — add a token in tokens.css instead.
- Icons are Phosphor SVGs inlined at build time via `<Icon name="…">`
  (`@phosphor-icons/core`); there is no icon font.
- When changing framework CSS, check the rendered result yourself:
  build, screenshot, compare (light, dark, and 390px mobile).
  `playwright-core` is in devDependencies for exactly this — drive the
  preview server headlessly and screenshot/measure pages. The original
  Webflow export is no longer vendored here; compare against the live
  Mast style guide, or retrieve the export from git history
  (`git show d563d79:reference/...`).

## Agent skills

Skills live in `.agents/skills/` (the cross-agent convention), with
`.claude/skills/<name>` symlinked to each so Claude Code finds them too —
one source of truth, two discovery paths. `AGENTS.md` is likewise a
symlink to this file. See `.agents/skills/README.md`.

- `mast-build` — the rules where Mast diverges from ordinary web practice,
  pointing at `docs/` for depth. Read it before writing markup or CSS.
- `mast-migrate` — converting a Mast for Webflow site or export into this
  project.
