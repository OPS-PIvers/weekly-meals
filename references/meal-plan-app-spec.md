# Meal-Plan App Spec — v1 (ROUGH)

> **Status: ROUGH / provisional.** This is the documented intent for the Pages app, captured so the shell stops being rebuilt from memory each week (the old drift bug). **Hardening to v2 is deferred** — pinning exact layout geometry, the scheduler back-calculation math, and full component states is a separate pass. Until then, `app/index.html` is labeled "provisional pending spec v2" and should not be polished mid-plan.
>
> What *is* stable enough to rely on now: the **data schema** below (so `week.json` is consistent week to week) and the **acceptance checklist** (hard gates the shell must pass).

## How the app is fed

- Single static `app/index.html` (no build step) reads `app/data/week.json` at load.
- Deployed via **GitHub Pages, deploy-from-branch, `main` at root**, so the app is served at `…/weekly-meals/app/` and reads `…/weekly-meals/app/data/week.json`.
- **Only `week.json` changes weekly.** The shell is built once.

## Acceptance checklist (hard gates)

The shell must satisfy all of these before it's considered done (v2):

- [ ] **Dark-mode readable** — legible contrast on a phone in a kitchen.
- [ ] **No card-stack / swipe-deck** — meals are visible together, scannable, not buried behind swipes.
- [ ] **No emoji.**
- [ ] **Hand-off / unattended steps shown inline** in the recipe flow (e.g., "smoke 5 hr unattended"), not hidden.
- [ ] **Timers beep and vibrate** when they finish.
- [ ] **Shopping list is store-reassignable** — Paul can move an item to a different store, and the grouping updates.
- [ ] **Working serving-time → start-time scheduler** — Paul sets when he wants to eat; the app back-calculates when to start each step. **Each step's `min` is wall-clock time** for that step.

## Data schema

`app/data/week.json`:

```json
{
  "weekOf": "YYYY-MM-DD",
  "provisional": true,
  "meals": [
    {
      "id": "kebab-case-id",
      "name": "Display Name",
      "effort": "Low | Med | High",
      "protein": "Chicken | Beef | Pork | ...",
      "method": "Smoker | Sear | Sheet-pan | ...",
      "flavor": "short flavor-profile label",
      "servings": 6,
      "serveTime": "18:00",
      "leftoversNote": "optional string",
      "steps": [
        {
          "label": "What to do",
          "min": 20,
          "active": true,
          "handoff": false
        }
      ],
      "ingredients": [
        { "name": "Pork ribs", "qty": "2 racks (~5 lb)" }
      ]
    }
  ],
  "grocery": [
    {
      "name": "Pork ribs",
      "qty": "2 racks (~5 lb)",
      "section": "Meat",
      "store": "Costco",
      "onHand": false
    }
  ]
}
```

Field notes:

- **`meals[].steps[].min`** — wall-clock minutes for that step. The scheduler sums forward from the start to `serveTime`; to get start time it back-calculates `serveTime − Σ min`. (Exact handling of parallel/overlapping steps is a v2 detail.)
- **`active`** — true if the cook is hands-on; false = unattended (drives the "hand-off shown inline" gate).
- **`handoff`** — explicit flag for a long unattended stretch worth calling out.
- **`grocery[].section`** — store-section grouping ("Meat", "Produce", "Pantry", "Dairy", …).
- **`grocery[].store`** — default store; the app lets Paul reassign (store-reassignable gate).
- **`grocery[].onHand`** — true = already have it; render flagged/checked so he doesn't re-buy.
- **`provisional`** — surfaced in the UI as the "provisional pending spec v2" label until v2 lands.

## Deferred for v2 (do not block on these)

- Pin layout geometry (spacing, type scale, section order) so two builds come out identical.
- Pin the scheduler math for overlapping/parallel steps and per-meal vs whole-evening scheduling.
- Enumerate component states (timer running/paused/done, item checked/reassigned, empty/missing-data).
- Lock the `week.json` / `MEALS[]` / `ITEMS[]` contract precisely.
