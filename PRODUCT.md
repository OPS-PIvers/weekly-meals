# Product

## Register

product

## Users

Paul — a teacher, ADHD–Inattentive type, cooking dinner for a household of three (two adults, one kid). He is the sole planner and cook. Executive function — planning, prioritizing, working memory — is the weak spot, so the app is used in two distinct contexts:

- **At the counter, mid-week, deciding what's next** — low energy, needs one obvious next action, not a wall of options.
- **At the store and at the stove** — hands busy, glancing at a phone, sometimes under time pressure or kitchen glare. Steps must be legible at arm's length and survive a wet-thumb tap.

The job to be done: *remove the thinking from the week's dinners.* The plan is already decided; the app's only job is to tell Paul the single next thing to do — buy this, start this step now, this finishes at 6:00 — and never make him hold anything in his head.

## Product Purpose

A personal, phone-first web app that surfaces one approved week of three dinners and one consolidated grocery run. The menu is regenerated weekly via a reviewed PR; the app is a fixed renderer. Success is measured in cognitive load avoided: Paul opens it, sees exactly one next action, acts, and closes it without ever planning, comparing, or remembering. A life-threatening apple allergy means safety information must be unmissable and never buried.

## Brand Personality

Calm, focused, quietly crafted. Three words: **composed, legible, kind.** It should feel like a well-kept kitchen at mise en place — everything prepped and in its place, nothing competing for attention, the one thing you're about to do sitting in clear light. Warm enough to feel personal (this is *Paul's* app, not enterprise software), precise enough to trust with a timer and a shopping total. It speaks plainly: short lead line, then specifics. Never chatty, never cute.

## Anti-references

- **Cutesy recipe blogs** — no food emoji, no handwritten or rounded "friendly" fonts, no warm cream/beige "cozy kitchen" palette, no stock hero photography.
- **Generic SaaS card-stacks** — no vertical stack of rounded, shadowed boxes; no identical card grids; no dashboard-metric clichés.
- **Busy / cluttered UI** — no walls of text, no dense control panels, nothing where everything competes at once. One clear next action per screen.
- **Corporate / sterile enterprise UI** — not cold, gray, and lifeless; it has a point of view and a living accent.

## Design Principles

1. **One next action.** Every screen resolves to a single obvious thing to do. Hierarchy does the prioritizing so Paul's working memory doesn't have to.
2. **Never make him remember.** Cross-meal hand-offs, start times, and allergy facts are surfaced in place, at the moment they matter — never recalled.
3. **Legible under load.** High contrast, generous space, arm's-length type, big tap targets. It has to work with wet hands in a bright kitchen.
4. **Calm over dense.** When in doubt, remove. Whitespace and rhythm carry structure; chrome is minimal.
5. **Safety is not decoration.** The apple-allergy warning is unmissable and uses a reserved danger color that appears nowhere else.

## Accessibility & Inclusion

- WCAG 2.1 AA minimum on every text/background pair; body text targets ≥7:1 against its surface, meta text ≥4.5:1.
- Full light and dark themes via `prefers-color-scheme`; both must pass contrast.
- `prefers-reduced-motion` honored on every transition (crossfade or instant fallback; no exceptions).
- ADHD-inattentive accommodation is the core need, not an add-on: minimal simultaneous choices, explicit current-state, no hidden modes, no reliance on memory.
- Tap targets ≥44px; timer/cook controls usable one-handed.
- Color is never the only signal (icons + text accompany every status and tag).
