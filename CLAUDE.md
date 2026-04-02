# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Single-page leaderboard for an amateur karting championship ("Karting Cuecen"). It's a plain HTML/CSS/JS site with no build step, deployed via GitHub Pages.

- **Live site**: https://mundurragac.github.io/karting-cuecen/
- **Data source**: Google Sheets (fetched client-side via Google Visualization API JSONP)
- **Sheet ID**: `1s0-Zzr0Nbnz6XiKwKvOVgJjVnm_ydG1gYPM-mabAZ9A`

## Architecture

Everything lives in `index.html` — no bundler, no framework, no dependencies.

### Data Flow
1. Four Google Sheets tabs are fetched via JSONP (`/gviz/tq?tqx=out:json;responseHandler:...`):
   - `drivers` — driver names and team assignments (14 drivers, 7 teams)
   - `teams` — team colors (hex bg + font color)
   - `races` — results per race date (position 1-14 + fastest lap)
   - `points` — scoring system (25-20-16-13-11-10-9-8-7-6-5-4-3-2 + 1 for FL)
2. Google Sheets returns headers as the first data row (`parsedNumHeaders: 0`), so the fetch function treats row 0 as headers.
3. Points are calculated client-side from race positions.

### UI Structure
Four tabs rendered with vanilla JS:
- **Pilotos** — driver standings table with per-race points
- **Equipos** — team standings (sum of both drivers)
- **Carreras** — individual race results (newest first)
- **Gráfico** — canvas-based position chart showing championship position evolution across races. Deferred rendering (drawn on first tab visit since canvas needs visible dimensions). Legend items are clickable to toggle drivers.

### Key Design Decisions
- JSONP instead of fetch to avoid CORS issues (Google Sheets blocks cross-origin fetch but allows script tags)
- Chart uses raw Canvas 2D — no charting library
- Teammates differentiated by solid vs dashed lines using team color
- Responsive via media queries at 768px and 480px breakpoints, tables wrapped in `.table-wrapper` for horizontal scroll

## Development

No build step. To preview locally:
```
npx http-server . -p 8765
```
Opening `index.html` directly as `file://` will fail due to JSONP script injection (works only when served over HTTP or on GitHub Pages).

## Deployment

Push to `main` triggers GitHub Pages legacy build automatically. No workflow file needed.
