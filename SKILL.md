---
name: weekly-meal-plan
description: Plans the week's family dinners for Paul — proposes a 3-meal slate, then (only after approval) delivers recipes, a consolidated grocery list, an optional mobile app, and version-controlled state. Use whenever Paul wants to plan meals, figure out what to cook this week, build a dinner lineup, generate a weekly grocery list, or pick recipes for the household — even if he doesn't say the word "meal plan." Trigger on phrasings like "plan this week's dinners," "what should we eat," "help me figure out meals," "grocery list for the week," or "let's do the meal thing."
---

# Weekly Meal-Plan Skill

This skill plans **three family dinners for the coming week**, then produces recipes, a grocery list, a mobile app view, and updated state files — *in that order, behind two gates*. It runs as a Claude Code cloud session, driven from the Claude mobile app, backed by this GitHub repo.

The whole design exists to fix three failures of the earlier version:
1. **It skipped the workflow** — pattern-matched to "make meals" and blew past intake and approval. → Two explicit gates below, the second a hard stop.
2. **The app drifted** — rebuilt from fuzzy memory each week. → The app is built once from a spec; only `app/data/week.json` changes weekly.
3. **The meals were samey, too much work, and re-suggested things already vetoed.** → Hard rules on effort balance, intra-week distinctness, and the OUT lists, all loaded from a writable file.

## Before anything else: load state

At the **start of every run**, read both of these. They are the ground truth for constraints and what's already been cooked — never plan from memory:

- `references/preferences.md` — the family profile, hard constraints (allergies, OUT lists), effort tolerance, and confirmed likes/dislikes.
- `references/meal-history.md` — the rolling log of approved weeks. Use it to avoid repeating recent proteins/methods/dishes. **Do not ask Paul what he had recently — derive it from here.**

If either file is missing or looks empty, say so and ask Paul how he'd like to seed it, rather than inventing constraints.

## The workflow

### Gate 1 — Intake (light, skippable)

Ask only what actually changes the plan, and make it effortless to skip. Default to one short question:

> "Anything on hand you want to use up this week — a protein or some veg? (Or just say *you pick* and I'll run with it.) Any cravings?"

Keep it to tappable/short answers. Do **not** interrogate. Do **not** ask about recent repeats (that's what `meal-history.md` is for). Cravings are optional. If Paul says "you pick," move straight to proposing.

### Propose the 3-meal slate

Build three dinners that obey the hard rules below, then present them as a compact, scannable list. **Label each with its effort (Low / Med / High)** so the balance is visible before Paul commits.

Hard rules for the slate:

- **Effort balance.** At most **one High-effort** meal; at least **one Low-effort** meal; the third is flexible. Capping active prep time is *not* the same as capping total burden — judge the whole experience (active prep + babysitting + cleanup + coordination). The single High slot is where the weekly "try something new" / beginner-smoker cook lives. (See effort definitions in `references/preferences.md`.)
- **Intra-week distinctness.** The three meals must differ in **protein, cooking method, and flavor profile**. Not three takes on one theme (e.g., not chicken-three-ways, not three things on the smoker). Variety *within* the week matters as much as across weeks.
- **No recent repeats.** Cross-check `meal-history.md`; don't repeat a protein/method/dish from the last couple of weeks.
- **Use-it-up & leftovers.** Plan around full use of perishable ingredients (no half-bunches orphaned). Target the household's usual serving count so there are intentional leftovers for the next day. (Specifics in `references/preferences.md`.)
- **Never propose anything on an OUT list.** Obey silently — don't surface the list or explain the omission.

Present like this (adapt naturally, keep it tight):

> **Proposed dinners for the week of <date>:**
> 1. **<Meal>** — *<effort>* · <protein> · <method> · <flavor>
> 2. **<Meal>** — *<effort>* · <protein> · <method> · <flavor>
> 3. **<Meal>** — *<effort>* · <protein> · <method> · <flavor>
>
> Want to lock these in, or swap any?

### Novel / undecided items — ask first, then persist

If an idea is **on neither the IN nor the OUT list** — genuinely undecided — *ask before building it in*: "You haven't done <X> before — open to it this week?" Don't silently bake an unknown into the plan, and don't avoid it out of caution either. When Paul answers, that yes/no becomes a confirmed preference: write it into `references/preferences.md` as part of the PR (see Persist below).

This is how the OUT/IN lists grow over time and how the slate stops re-litigating the same questions.

### Gate 2 — Approval (HARD STOP)

**Do not produce recipes, the grocery list, `week.json`, or the app until Paul explicitly approves the slate.** This is the single most important rule in the skill — it's the fix for the old version blowing through the workflow.

Approval means a clear yes to the three meals (after any swaps). "Looks good," "lock it in," "yep" all count. Questions, hesitation, or silence do **not** count — keep refining. If Paul changes a meal, re-show the updated slate and re-confirm. Make the ask unmissable; never assume.

### After approval — deliver

Once approved, produce all of the following:

1. **Recipes** — precise, with weights/volumes and step-by-step instructions. Each step that consumes wall-clock time gets a duration so the app's scheduler works (see schema). Flag any hands-off/hand-off step inline (e.g., "smoke 5 hr unattended").
2. **Consolidated grocery list** — combined across the three meals, organized **by store section**, with quantities, and **on-hand items flagged** (don't make him re-buy staples).
3. **Write `app/data/week.json`** — the only weekly-changing app file. Match the schema in `references/meal-plan-app-spec.md`.
4. **Append the week to `references/meal-history.md`** — date + the three meals with their protein/method/flavor/effort.
5. **Apply any newly confirmed preferences to `references/preferences.md`** — the yes/no answers from "novel items" above.

Then commit and open the PR (see Persist).

### Offer the app (don't auto-dump it)

After delivering the recipes and list, **offer** the app rather than forcing it:

> "Want the phone view for this week? One tap and it's live."

If yes, ensure `app/data/week.json` is written (it drives the app) and point Paul at the Pages URL. The app shell itself is built once and lives in `app/index.html` — **don't rebuild it weekly.** It's currently labeled **"provisional pending spec v2"**; until the spec is hardened (deferred work — see `references/meal-plan-app-spec.md`), treat the shell as provisional and don't invest in polishing it mid-plan.

## Persist — commit + PR (the anti-drift gate)

State changes only become permanent when Paul merges them. The loop:

1. Make sure all edits are on the working branch (a `claude/…` branch — cloud sessions can't push to `main` directly).
2. Commit the changed state files (`week.json`, `meal-history.md`, `preferences.md`) with a clear message, e.g. `Plan week of 2026-06-06`.
3. Open a **pull request** summarizing the week's slate and any preference changes.
4. Paul reviews on GitHub mobile and merges. The merge to `main` is what makes it real and (for `week.json`) what deploys the app via GitHub Pages.

The PR review *is* the approval-for-permanence. Surface preference changes clearly in the PR body so a glance is enough to approve.

## Constraints that always apply

- **Apple allergy (hard, silent).** No apple in any form — no apple, apple juice/cider, or apple cider vinegar — and check BBQ sauces and marinades for hidden apple (it hides in commercial sauces). Obey this absolutely. But **do not pin it at the top, banner it, or re-announce it each week** — Paul knows. The old prominence just added noise. Honor it silently; only mention it if a specific ingredient choice forces a callout.
- **OUT lists are never proposed**, silently.
- **Recipes are precise**; grocery lists are consolidated and sectioned.

## Working with Paul (cognitive load)

Paul has ADHD-inattentive type — planning and working memory are the load-bearing walls. Design every interaction to reduce that load:

- Prefer **tappable / short-answer choices** on mobile over open-ended prompts.
- **One question at a time.** No enumeration walls, no multi-part questionnaires.
- Keep the slate **scannable** (the compact list above), not a wall of prose.
- **Surface the app** so it doesn't get forgotten — it's the lowest-load way for him to cook from the plan.
- Don't make him track state in his head — that's what the repo files are for.

## File map

| File | Role | Changes |
|------|------|---------|
| `references/preferences.md` | Family profile, hard constraints, IN/OUT lists, effort tolerance | Occasionally, via PR, when a preference is confirmed |
| `references/meal-history.md` | Rolling log of approved weeks | Every approved week, via PR |
| `references/meal-plan-app-spec.md` | The app spec + data schema (currently ROUGH / v1) | Rarely; hardening to v2 is deferred |
| `app/index.html` | The Pages app shell, built once | Rarely; provisional pending spec v2 |
| `app/data/week.json` | The week's meals + grocery list | Every week — the only routine app change |
