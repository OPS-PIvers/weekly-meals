# Design

Direction codename: **Mise en place** — the calm of everything prepped and in its place.

## Theme

Phone-first personal cooking companion. Calm and focused: a single quiet column, generous vertical rhythm, hairline rules instead of boxes, and exactly one dominant action per screen. Editorial warmth from a literary serif used only for content (dish names, big numerals), a crisp humanist sans for everything you operate. A living green carries freshness; a reserved red carries danger and nothing else. Pure surfaces keep it from drifting into cream-kitchen cliché; the green and the type keep it from going sterile. Full light + dark via `prefers-color-scheme`, both AA.

Color strategy: **Restrained** — neutral surfaces, one green primary, a sparing warm accent, one reserved danger red.

## Color

OKLCH. **Light is the app's default** (it does not follow the OS into dark); a remembered Light/Dark toggle in the masthead switches themes, persisted to `localStorage` (`mep:theme`). Dark is opt-in and intentionally *not* a cold graphite + neon-mint scheme — it's a deep pine-ink surface so it reads as a deliberate "evening kitchen", distinct from the old v1 look.

### Light (default scene: bright kitchen counter, clean daylight)

```css
--bg:            oklch(1 0 0);              /* pure white */
--surface:       oklch(0.976 0.004 160);    /* sticky bars, scheduler, sheet */
--surface-2:     oklch(0.958 0.006 160);    /* inputs */
--line:          oklch(0.916 0.005 160);    /* hairline dividers */
--line-2:        oklch(0.860 0.006 160);    /* stronger borders */
--ink:           oklch(0.240 0.018 160);    /* primary text  (~13:1 on bg) */
--muted:         oklch(0.470 0.018 160);    /* secondary text (~5:1) */
--faint:         oklch(0.560 0.015 160);    /* meta (~3.7:1, used at >=14px) */
--primary:       oklch(0.520 0.130 160);    /* interactive, active, progress, checks */
--primary-strong:oklch(0.450 0.130 160);    /* primary text emphasis on bg */
--primary-ink:   oklch(1 0 0);              /* text on a primary fill (white) */
--primary-wash:  oklch(0.955 0.030 160);    /* pale green fill */
--primary-line:  oklch(0.520 0.130 160 / 0.30);
--accent:        oklch(0.500 0.150 65);     /* warm clay — try-new / start-time only (AA on wash + white) */
--accent-ink:    oklch(1 0 0);
--accent-wash:   oklch(0.950 0.040 65);
--warn:          oklch(0.520 0.200 25);     /* allergy / danger ONLY */
--warn-bg:       oklch(0.960 0.035 25);
--warn-line:     oklch(0.520 0.200 25 / 0.35);
```

### Dark (opt-in scene: evening prep, deep pine-ink — not v1 graphite)

```css
--bg:            oklch(0.190 0.024 178);    /* deep pine-ink, clearly tinted (not cold near-black) */
--surface:       oklch(0.225 0.026 178);
--surface-2:     oklch(0.255 0.028 178);
--line:          oklch(0.310 0.022 178);
--line-2:        oklch(0.400 0.024 178);
--ink:           oklch(0.950 0.012 95);     /* warm off-white (~16:1 on bg) */
--muted:         oklch(0.750 0.020 170);    /* ~8.3:1 */
--faint:         oklch(0.620 0.018 172);    /* ~5.1:1 */
--primary:       oklch(0.740 0.130 160);    /* calmer green, less neon than v1 */
--primary-strong:oklch(0.800 0.130 160);
--primary-ink:   oklch(0.170 0.030 165);    /* dark text on the green fill */
--primary-wash:  oklch(0.300 0.055 170);
--primary-line:  oklch(0.740 0.130 160 / 0.32);
--accent:        oklch(0.800 0.115 72);     /* warm clay carries more weight in dark */
--accent-ink:    oklch(0.170 0.025 72);
--accent-wash:   oklch(0.310 0.050 60);
--warn:          oklch(0.720 0.160 25);
--warn-bg:       oklch(0.310 0.060 20);
--warn-line:     oklch(0.720 0.160 25 / 0.40);
```

Filled-fill text rule: light-mode primary (L 0.52) takes white text; dark-mode primary (L 0.74) takes dark `--primary-ink`. Always light-fill→dark-text, dark-fill→light-text. Red is reserved exclusively for the apple-allergy warning; the warm clay accent is used only for the "try something new" flag and the back-calculated start time.

## Typography

Two families on a serif↔sans contrast axis. Serif is content-only (never on a control or label).

- **Display / numerals — Newsreader** (Google), 400 + 500, italic for blurbs; optical sizing on. Fallback `Georgia, 'Times New Roman', serif`. Used for: dish names, screen titles, the night index numeral, the "begin at" clock, the grocery grand total.
- **UI / body — Figtree** (Google), 400 / 500 / 600 / 700. Fallback `-apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif`. Used for everything operable: labels, buttons, bullets, meta, tags, inputs, body.
- Clock times and prices: `font-variant-numeric: tabular-nums`.
- Fixed rem scale (product register), ratio ~1.2. Steps: 0.75 / 0.8125 / 0.875 / 1 / 1.125 / 1.375 / 1.75 / 2.5 / 3.25rem. Display numerals run larger than headings.
- `text-wrap: balance` on dish names; `text-wrap: pretty` on blurbs. No all-caps body. No tracked-uppercase eyebrow above every section.

## Spacing & layout

- Phone-first single column, `max-width: 560px`, centered, 20px side gutters.
- 4px base; rhythm via 8 / 12 / 16 / 24 / 32 / 48. Vary it — generous space around the one focal action.
- **No cards.** Content is lists separated by 1px `--line` dividers. A narrow left rail (≈3.25rem) holds the night numeral / step clock / step index; content sits in the right column (CSS grid, 2 columns).
- Sticky masthead (top) and fixed bottom tab bar (This Week · Shopping); both `--surface`. Tab bar hides in cook mode.
- The only rounded "surfaces" are genuine controls: the scheduler panel, the timer panel, the bottom sheet. Content never lives in a rounded box.
- z-index scale: base → sticky(100) → tabbar(200) → sheet-scrim(300) → sheet(310) → cookmode(400).

## Components

Every interactive element ships default / hover / focus-visible / active / disabled; toggles also checked; timer also running / done.

- **Night row**: serif index numeral (rail) · sans cuisine kicker · serif dish name · italic blurb · meta (hands-on · total) · hand-off pill tags · chevron. Whole row is the tap target.
- **Pill tag**: icon + short label, 1px border, no fill (uses) or `--primary-wash` fill (saves); warm `--accent-wash` for try-new; `--warn-bg` for allergy.
- **Stats strip**: three cells divided by vertical hairlines, bordered top/bottom — not three cards.
- **Scheduler panel** (rounded control): time `<input>` + a `--primary-wash` block with the serif back-calculated "begin at" time.
- **Step row**: left rail clock + index (P1 / 1); title; bullets; tags (Kid can help / Hands-off / N-min timer).
- **Carryover note**: full 1px border + small leading icon + `--primary-wash` tint (NOT a left side-stripe). "Uses from earlier" / "Save for later".
- **Cook mode**: full-screen, one step, phase label + x/n + close, slim progress bar, huge serif step title, big bullets, timer panel when present, Back / Next (Finish on last).
- **Timer panel** (rounded control): mm:ss tabular, Start / Pause / Reset; done state flips to `--primary-wash`, beeps (Web Audio, lazy on tap) + `navigator.vibrate`.
- **Shopping**: sticky summary (serif grand total · items-left · staples toggle); store sections (name · why · live subtotal) → section subgroups → item rows (check circle · name/qty/price/Move chip · note · Have toggle). Allergy banner + BBQ label warning in warn tokens.
- **Bottom sheet**: store reassign; slides up via transform, scrim fades.
- **Icons**: one inline-SVG set, 24px, `stroke="currentColor"`, ~1.6 stroke, `fill="none"`. No emoji ever.

## Motion

Product-register restraint: 160–240ms, ease-out (`cubic-bezier(0.22,1,0.36,1)`). Screen crossfade + 6px translateY; progress-bar width; sheet transform + scrim fade; subtle staggered entrance on the three night rows (first paint only). Motion conveys state, never decorates. Full `@media (prefers-reduced-motion: reduce)` fallbacks (instant / crossfade only).

## Accessibility

AA on every pair (verified above), focus-visible ring (`--primary`, 2px offset), ≥44px tap targets, color never the sole signal (icon + text on every tag/state), reduced-motion honored throughout.
