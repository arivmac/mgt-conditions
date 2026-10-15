# MGT Conditions — project brief

A dressmyrun-style site where runners report conditions on Toronto's Martin
Goodman Trail (MGT), so the recurring r/RunTO "is the MGT icy?" posts get a
live answer. Built with Mast for Astro, edited visually in Stacki, deployed to
Cloudflare.

`reference/prototype.html` is the agreed design and behaviour (a single-file
working prototype). Match it; don't redesign it.

## Design (agreed)

- Single dark theme, pure black, white text at 95% / 70% / 48% opacity.
  Inspired by marathon-weekend.com. Inter (Google Fonts).
- Colour is reserved for trail conditions only:
  clear `#3DD68C`, wet `#5AB8F0`, slush `#F5B83D`, packed snow `#B7BEC8`,
  icy `#7C8CFF`, no report `rgba(255,255,255,.22)`.
- One scrolling page, no tabs or pop-over panels:
  1. Header: wordmark, "Toronto · Martin Goodman Trail" pill, white
     "Report conditions" pill button.
  2. Verdict (left) + today's hourly forecast strip (right).
  3. Slim full-width trail map strip with landmarks.
  4. Sections list (left) + report form and email signup (right, sticky).

## Features (all in the prototype)

- **Verdict headline** in plain trail language, never gear advice:
  "Slippery until ~12pm", "Slippery in spots", "Likely slippery…",
  "Slushy in spots", "Wet but runnable", "Good to go". Plus a
  "Best bet:" line (where/when to run).
- **Hourly strip**: temp, ice-risk dot, wind arrow + km/h.
  Ice risk: ≤ −2°C high, < 1°C moderate, else low.
- **Wind + lake spray**: spray icing likely when ≤ 0°C, wind ≥ 20 km/h,
  from 45°–200° (off the lake). Exposed sections: Humber Bay, Beaches.
  "Head east/west first" tip.
- **Six sections**, grouped West end / Downtown / East end: Humber Bay Shores,
  Ontario Place / Coronation Park, Harbourfront / Queens Quay,
  Cherry St / Port Lands, Leslie St to Ashbridges Bay, Beaches Boardwalk.
  Long-term: define sections by landmarks at each end.
- **Reports**: section + surface (clear/wet/slush/packed snow/icy) + optional
  note (200 chars) + photo. Fade with age, hidden after 12 h.
- **Confirmations**: "Still accurate" / "It's changed" (changed jumps to the
  form with the section preselected). More "changed" than "accurate" →
  "May have changed". 3+ reports by one person → "Regular reporter".
- **Hazards**: flooding, closed or detour, construction, debris or branch,
  waves over path, other. Shown on map (white diamond), row and verdict.
  Live 72 h or until someone taps "It's cleared".
- **Moderation**: anyone can flag; 3 flags hides an item for everyone but the
  owner. Owner: Keep visible / Delete (two-tap confirm).
- **Map**: static SVG, no Mapbox (the trail never moves). Hover a section →
  highlight, dim others, card with status/time/confirmations/note/hazards;
  click → scroll to its row (or to the form if no report). Hovering a row
  lights the map. Keyboard: Tab / Enter / Escape. 12 landmarks on the lake
  side (Humber Bay Park → Balmy Beach); hidden on phones.
- **Morning email** (5:45am): daily or "only when icy". Double opt-in,
  one-click unsubscribe (CASL).
- **Rate limit**: one report per section per 10 min (server-side in the real
  build; consider "your repeat report replaces your earlier one").

## Stack and build phases

| Phase | Work |
| --- | --- |
| 1 | Static UI from components with sample data (`src/data/sample.ts`) |
| 2 | Real trail geometry: Toronto Open Data cycling network GeoJSON → simplify → project → one `<path>` per section in `TrailMap.astro` |
| 3 | `@astrojs/cloudflare` adapter, D1 (reports, votes, hazards, flags, subscribers), Astro Actions for forms, Turnstile + rate limiting, R2 for photos, Open-Meteo forecast (cached) |
| 4 | Workers cron at 5:45am → Resend digest; Cloudflare Access on `/admin` for moderation |

## Stacki rules (keep pages visually editable)

Stacki edits a page visually when it is a layout wrapper plus a flat list of
self-closing component instances with simple props (string / number /
boolean / literal unions). So:

- `src/pages/index.astro` = `<BaseLayout>` + one self-closing component per
  page block (`<Verdict />`, `<HourStrip />`, `<TrailMap />`,
  `<SectionList />`, `<ReportForm />`, `<EmailSignup />`).
- Logic and data live inside components (or `src/lib/`), not in the page.
- Component props: few, enumerated, typed (Mast convention anyway).

Mast conventions apply throughout — read `CLAUDE.md` and
`.agents/skills/mast-build/SKILL.md` before writing markup or CSS. The theme
goes in `src/styles/tokens.css`; custom classes in `components.css`.
