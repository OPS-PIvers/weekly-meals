# weekly-meals

A reusable **weekly meal-planning system** for Paul's household, run as a Claude Code cloud session from the Claude mobile app. The behavior lives in a skill; the state lives in version-controlled files; the weekly plan surfaces as a mobile web app on GitHub Pages.

## How it works

1. Paul asks Claude to plan the week.
2. The skill (`SKILL.md`) reads the current state, asks one light intake question, then **proposes a 3-meal slate** with effort labels.
3. **Nothing else is produced until Paul approves the slate** (hard stop — the fix for the old version skipping ahead).
4. After approval: precise recipes, a consolidated grocery list, the app's `week.json`, and updated history/preferences.
5. Claude commits to a `claude/…` branch and opens a **PR**. Paul reviews and merges on mobile.
6. Merging to `main` makes the change permanent and **deploys the app** via GitHub Pages.

The PR review is the anti-drift gate: a preference or history change is only permanent once merged.

## Layout

```
SKILL.md                          behavior / workflow engine (the core)
PRODUCT.md                        product strategy: users, purpose, principles (design source of truth)
DESIGN.md                         "Mise en place" visual system: OKLCH tokens, type, components, motion
references/preferences.md         constraints + family profile (writable via PR)
references/meal-history.md        rolling log of approved weeks (appended via PR)
references/meal-plan-app-spec.md  original v1 app spec (SUPERSEDED by PRODUCT.md + DESIGN.md)
app/index.html                    production Pages app — renders week.json, built once
app/data/week.json                the week's meals + grocery list, schemaVersion 2 (the only weekly change)
```

## App / Pages

- Single static `index.html` + `data/week.json`, no build step. Vanilla HTML/CSS/JS.
- Pages: **deploy from a branch**, `main` at **root** — the app is served at `…/weekly-meals/app/` and reads `…/weekly-meals/app/data/week.json`.
- **Design lives in `DESIGN.md` (visual) and `PRODUCT.md` (strategy)**, produced with the [`impeccable`](https://impeccable.style) skill. The app shell is built once against those; only `week.json` changes weekly.
- Mobile-first (`max-width 560px`), light + dark themes (AA contrast verified), `prefers-reduced-motion` honored. Shopping state, serve times, and toggles persist in `localStorage`, keyed by `weekOf`.

## What the app does

- **This Week** — the three nights as an editorial list (no cards), with cross-meal hand-off tags and an unmissable apple-allergy strip.
- **Recipe** — stats strip, a serve-time scheduler that back-calculates "begin your first step at …", prep-then-cook step rows each showing their clock time, and carryover notes.
- **Cook-along** — full-screen, one step at a time, progress bar, and countdown timers on wait steps that beep + vibrate at zero.
- **Shopping** — grouped by store then section, with running subtotals/grand total, check vs. "have it", a pantry-staples master toggle, and store reassignment.
