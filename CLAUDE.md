# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

A single-page calculator for sample sizes needed to achieve confidence intervals for proportions, implementing three statistical methods: Wald, Wilson, and Agresti-Coull. The UI is in Russian.

The entire application lives in `index.html` — no build step, no dependencies to install, no framework. Open the file directly in a browser to run it.

## Architecture

Everything is self-contained in `index.html`:

- **CSS** (lines 7–52): inline styles only, two responsive card layouts
- **HTML** (lines 54–134): two cards — the interactive calculator and an explanation of the numerical search method
- **JS** (lines 136–302): pure vanilla JS with no modules

### Statistical logic (lines 137–165)

- `waldN(p, E, z)` — closed-form formula: `n = ceil(z²·p·(1−p) / E²)`
- `wilsonN(p, E, z)` — linear search: increments `n` until `wilsonHalfWidth ≤ E`
- `agrestiN(p, E, z)` — linear search: increments `n` until `agrestiHalfWidth ≤ E`

The Wilson and Agresti-Coull half-width formulas are the authoritative implementations; the CI bounds functions (`waldCI`, `wilsonCI`, `agrestiCI`) derive bounds from those same half-widths.

### Chart

Chart.js 4.4.1 loaded from CDN (`cdnjs.cloudflare.com`). A custom plugin `vline` draws a vertical red dashed line at the current `p` value. The chart re-renders on every slider/select change via `chart.update('none')` (no animation for responsiveness).

### Controls

Three inputs drive everything through a single `update()` function wired to `input`/`change` events:
- `#p` range slider — true proportion (0–1)
- `#e` range slider — half-width target E (0.01–0.20)
- `#conf` select — confidence level, value is the corresponding z-score (1.645 / 1.960 / 2.576)
