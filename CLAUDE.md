# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a single-file personal travel journal web app for a 6-day Hokkaido trip (Sapporo, Asahikawa, Otaru). Everything lives in `index.html` — no build step, no dependencies, no server required. Open the file directly in a browser.

## Architecture

`index.html` is a self-contained HTML/CSS/JS file structured as follows:

**Sections (in DOM order):**
1. Hero — full-viewport with canvas snow/dandelion animation and season toggle (winter ❄ / summer ☀)
2. Cities — static info cards for Sapporo, Asahikawa, Otaru
3. Journal — 6 tabbed day panels, each with journal text, food log, expense log, photo upload
4. Expense Overview — CSS bar chart + grand total
5. Gallery — all uploaded photos aggregated across all 6 days
6. Lightbox — fullscreen photo viewer

**Data persistence:** All user data is stored in `localStorage`. No backend.

| Key pattern | Content |
|---|---|
| `hk-j-{day}` | Journal HTML (innerHTML) |
| `hk-foods-{day}` | JSON array of `{name, rest, stars}` |
| `hk-exp-{day}` | JSON array of `{cat, name, amt}` |
| `hk-photos-{day}` | JSON array of base64 JPEG strings |
| `hk-date-{day}` | Date string for that day |
| `hk-wx-{day}` | Weather emoji selection |
| `hk-season` | `'winter'` or `'summer'` |
| `hk-hero-dates` | Editable hero date string |

**Photo storage caveat:** Photos are compressed via canvas to max 800px / JPEG 0.65 quality before storing as base64. localStorage has a ~5MB limit — around 30–40 photos before hitting the ceiling.

**Key JS functions:**
- `setSeason(s)` — toggles winter/summer hero visuals and canvas particles
- `buildPanels()` — dynamically generates all 6 day panel DOMs from `DAYS` config array
- `updateChart()` — recalculates and redraws the expense bar chart; called after every expense change
- `renderGallery()` — rebuilds the full gallery from all 6 days' photos; called after every photo change
- `compressImage(file, maxSize, quality)` — returns a Promise resolving to a base64 JPEG

## Development

No build tools. Edit `index.html` and reload in browser. To preview:

```
open index.html
```

## Extending Day Config

To change city assignments or day labels, edit the `DAYS` array at the top of the `<script>` block:

```js
const DAYS = [
  {day:1, title:'第一天', sub:'出発・札幌', city:'札幌', cls:'sapporo', seal:'旅', ph:'placeholder text'},
  ...
];
```

`cls` must be one of `sapporo`, `asahikawa`, or `otaru` — these map to CSS color themes and chart bar colors.
