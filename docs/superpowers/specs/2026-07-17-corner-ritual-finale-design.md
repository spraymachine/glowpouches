# Corner-ritual finale — design

Date: 2026-07-17
Target: `index.html` (immersive 3D page; `immersive.html` is the older desktop-only copy and is not touched)

## Goal

Extend the scroll experience past the current GLOW finale with a new "ritual" chapter:
the end logo becomes **GLOW POUCHES**, stays pinned dead-center, and the four ritual
steps appear one per scroll tick in the four screen corners, each entering with a
**Focus** animation (over-bright blur snapping sharp) layered with a **chromatic split**
(cyan/magenta ghosts converging) and a **small focus-lock ring** pulse from the step number.

Validated in the visual companion: variant A (bare neon type), heading-only copy
(no paragraph text), Focus entry, add-ons = chromatic split + focus-lock ring (small).

## Content

Center (pinned, unchanged position): `GLOW POUCHES` word + existing "Find your Glow" CTA.

| tick | corner | accent | heading |
|------|--------------|-----------------|--------------------------|
| 1 | top-left | mint `#4dffc3` | Pick flavour & strength |
| 2 | top-right | citrus `#ffd24d`| Tuck it under your lip |
| 3 | bottom-left | ice `#4dc9ff` | Feel the slow release |
| 4 | bottom-right | rose `#ff4d9e` | Bin it & go |

Each step = small step number (`01`…`04`, accent colour, glow) + white heading. No body copy.

## Structure

- `#logoEnd .word` text changes `GLOW` → `GLOW POUCHES`. Desktop stays one line;
  ≤640px it may wrap to two centered lines (drop `white-space:nowrap` inside the
  existing mobile media query, reduce letter-spacing to `.18em`).
- Four new overlay children inside `.overlay` (inherits `pointer-events:none`):

```html
<div class="rstep tl" id="rs1" style="--sc:var(--mint)">
  <div class="num">01<span class="ring"></span></div>
  <h2>Pick flavour &amp; strength</h2>
  <span class="gh gc" aria-hidden="true">Pick flavour &amp; strength</span>
  <span class="gh gm" aria-hidden="true">Pick flavour &amp; strength</span>
</div>
```

  (same shape for `rs2`–`rs4`; ghosts duplicate the heading text only).
- Corner placement: fixed insets with safe-area padding
  (`top/left/right/bottom: calc(24px + env(safe-area-inset-*))`); right-side steps
  right-aligned. `max-width:42vw` so corners never collide with the centered word.

## Motion (scrubbed GSAP timeline, extends existing `tl`)

- `#track` height `640vh` → `840vh` (≈4 extra ticks of scroll at current pacing).
- After the existing `end` label, add per step *i*:
  - label `step<i>` — snap handled automatically by existing `snapTo:'labelsDirectional'`.
  - heading: `from {autoAlpha:0, filter:'blur(16px) brightness(2.4)', scale:1.1}`
    → `{autoAlpha:1, filter:'blur(0px) brightness(1)', scale:1}`, `power2.out`, dur `.4`,
    then textShadow settle `0 0 16px --sc` → `0 0 7px --sc`, dur `.2`.
  - ghosts: cyan from `x:-12`, magenta from `x:+12`, `opacity:.7→0`, converge to `x:0`
    over the first `.25` of the entry.
  - ring: `scale:.4→1.4`, `opacity:.9→0`, dur `.3`, starting at the focus-lock moment
    (entry start `+=.3`). Ring is a ~26px circle centred on the step number.
  - gap `.25` between steps; final `ritualEnd` label + `.4` hold closes the page.
- All tweens are scrubbed (no `ScrollTrigger` instances added), so scrolling up
  reverses the entries cleanly. Steps persist once landed (final page state =
  centered GLOW POUCHES + four lit corners).

## Constraints / non-goals

- No new three.js work (scene-echo add-on was considered and not selected).
- No changes to existing phases (rest → sets → ring → logo reveal) other than the
  word swap and track height.
- GSAP animates `filter` and `textShadow` strings directly — no CSS keyframes needed;
  CSS holds only static styling for `.rstep`, `.gh`, `.ring`.
- Mobile: same corner layout (2×2 reads fine on 375px with 13px headings);
  headings `clamp(13px, 3.6vw, 20px)`; `GLOW POUCHES` word `clamp(28px, 6vw, 84px)`.
- Reduced motion: not currently handled anywhere on the page; out of scope here.

## Testing

- Browser-pane verification: scroll through full track, confirm 4 snap ticks after
  logo, entries + reversal, no console errors, mobile viewport (375×812) corners
  don't overlap the centered word, `GLOW POUCHES` centered at all widths.
