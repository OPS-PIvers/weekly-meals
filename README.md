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
references/preferences.md         constraints + family profile (writable via PR)
references/meal-history.md        rolling log of approved weeks (appended via PR)
references/meal-plan-app-spec.md  app spec + data schema (v1 ROUGH; v2 hardening deferred)
app/index.html                    Pages app shell, built once (provisional pending spec v2)
app/data/week.json                the week's meals + grocery list (the only weekly change)
```

## App / Pages

- Single static `index.html` + `data/week.json`, no build step.
- Pages: **deploy from a branch**, `main` at **root** — the app is served at `…/weekly-meals/app/` and reads `…/weekly-meals/app/data/week.json`.
- The app is currently **provisional pending spec v2**; the shell meets the acceptance checklist in the spec but its layout/scheduler math aren't hardened yet.

## Deferred (separate pass)

- Harden `references/meal-plan-app-spec.md` to v2 (pin layout geometry, scheduler math, component states, data contract) so two builds come out identical.
- Then polish `app/index.html` against the hardened spec and flip the app from "offer" to auto-build.
