# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Single-file vanilla HTML/CSS/JS app for production flow management at Mischa's Bakery. No build step, no dependencies, no server — open `index.html` directly in a browser.

## Architecture

Everything lives in `index.html` in three sections:

**`<style>`** — All CSS using CSS custom properties defined in `:root`. Colors follow a semantic naming convention (`--amber`, `--green`, `--blue`, `--purple`) that maps directly to the three production lanes and step types.

**HTML** — Three tab panels (`#tab-fluxo`, `#tab-dashboard`, `#tab-config`). The Fluxo tab has empty `#flow-prod`, `#flow-pos`, `#flow-pre` containers that are entirely populated by JavaScript at runtime.

**`<script>`** — Three key data/logic layers:

- **`STEPS`** object — the single source of truth for all process steps. Each step has flags that control rendering and calculation: `type` (manual/auto), `once`, `perCiclo`, `parallel`, `ddlOnly`, `fixedLabel`/`fixed` (hardcoded times), `defaultVal` (pre-filled but overridable). Steps can be grouped via `isGroup:true` with a nested `steps` array (used for "Produção de Massas").

- **`S` state object** — persisted to `localStorage` under key `mischa_v3`. Contains `flavor` (trad/ddl/mix), `times` (map of step id → minutes entered by user), `formas` (forms per day), `defaultFormas`, and `ppl` (people count per area).

- **`calc()`** — derives all dashboard metrics from `S` and `STEPS`. Key business logic: 5 formas per ciclo, 30 min per oven cycle (22 min baking + 8 min handling), bottleneck detection compares manual prep time per cycle against the 30-min oven window. `wallClock` accounts for overflow when manual prep exceeds oven time.

## Key behaviors to understand

- **DDL flavor** shows/hides steps with `ddlOnly:true` (e.g., "Topping DDL", "Encher sacos de confeitar"). The `showDDL()` helper checks `S.flavor`.
- **Pós-Produção steps** use `defaultVal` as a fallback when no time is stored — these pre-populate visually and are used in calculations without requiring user input.
- **Group bottleneck indicator** in `buildGroup()` sums only non-auto, non-parallel steps within the group and compares against `OVEN_WINDOW = 30`.
- The Dashboard Gantt positions Theo's pré-produção bar starting at `turnoPos` (after pós-produção ends), assuming sequential execution.
- Rendering is fully imperative: `renderFluxo()`, `renderDash()`, `renderConfig()` rebuild their entire DOM on each call.
