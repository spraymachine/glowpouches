# Corner-Ritual Finale Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Extend the immersive page's scroll experience past the GLOW finale: the logo becomes "GLOW POUCHES" pinned center, and four ritual steps blink into the four screen corners (one per scroll tick) with a Focus entry + chromatic split + focus-lock ring.

**Architecture:** Single-file change to `index.html`. Static CSS for the four `.rstep` corner overlays; the entry motion is appended to the existing scrubbed GSAP master timeline `tl` after the `end` label, so snap/reversal come for free from the existing ScrollTrigger config. Scroll track grows 640vh → 840vh.

**Tech Stack:** GSAP 3 + ScrollTrigger (already on page), plain CSS/HTML. No build step, no test framework — verification is via the Browser pane on a static file server.

Spec: `docs/superpowers/specs/2026-07-17-corner-ritual-finale-design.md`

---

### Task 1: DOM + CSS for corner steps, word swap, longer track

**Files:**
- Modify: `index.html` (style block ~line 19 and ~44-66; overlay markup ~line 109-115)

- [ ] **Step 1: Grow the scroll track**

In the style block, change:

```css
#track{height:640vh}
```
to
```css
#track{height:840vh}
```

- [ ] **Step 2: Swap the finale word**

Change:

```html
<div class="word">GLOW</div>
```
to
```html
<div class="word">GLOW POUCHES</div>
```

- [ ] **Step 3: Add `.rstep` CSS**

Insert after the `#logoEnd .cta{...}` rule:

```css
.rstep{position:absolute;max-width:42vw;--sc:var(--mint);opacity:0}
.rstep .num{position:relative;display:inline-block;font-size:clamp(10px,1.2vw,13px);
    letter-spacing:.3em;color:var(--sc);text-shadow:0 0 8px var(--sc);margin-bottom:8px}
.rstep h2{font-size:clamp(15px,1.8vw,24px);font-weight:500;letter-spacing:.02em;color:#fff;margin:0}
.rstep .gh{position:absolute;left:0;bottom:0;width:100%;font-size:clamp(15px,1.8vw,24px);
    font-weight:500;letter-spacing:.02em;opacity:0;pointer-events:none}
.rstep .gc{color:#0ff}
.rstep .gm{color:#f0f}
.rstep .ring{position:absolute;left:50%;top:50%;width:26px;height:26px;margin:-13px 0 0 -13px;
    border:1px solid var(--sc);border-radius:50%;opacity:0}
.rstep.tl{top:calc(24px + env(safe-area-inset-top));left:calc(24px + env(safe-area-inset-left))}
.rstep.tr{top:calc(24px + env(safe-area-inset-top));right:calc(24px + env(safe-area-inset-right));text-align:right}
.rstep.bl{bottom:calc(24px + env(safe-area-inset-bottom));left:calc(24px + env(safe-area-inset-left))}
.rstep.br{bottom:calc(24px + env(safe-area-inset-bottom));right:calc(24px + env(safe-area-inset-right));text-align:right}
```

Notes: `.overlay` already has `pointer-events:none`, so steps never block the strength buttons. `.gh` ghosts get `width:100%` + same font metrics so they wrap identically to the `h2` they shadow.

- [ ] **Step 4: Mobile adjustments**

Inside the existing `@media (max-width:640px)` block, replace:

```css
#logoEnd .word{letter-spacing:.28em}
```
with
```css
#logoEnd .word{letter-spacing:.18em;font-size:clamp(26px,7.5vw,44px)}
.rstep{max-width:44vw}
.rstep h2,.rstep .gh{font-size:13px}
.rstep .num{margin-bottom:5px}
.rstep.tl,.rstep.bl{left:calc(16px + env(safe-area-inset-left))}
.rstep.tr,.rstep.br{right:calc(16px + env(safe-area-inset-right))}
.rstep.tl,.rstep.tr{top:calc(16px + env(safe-area-inset-top))}
.rstep.bl,.rstep.br{bottom:calc(16px + env(safe-area-inset-bottom))}
```

- [ ] **Step 5: Add corner markup**

Insert after the closing `</div>` of `#logoEnd`, before `<div class="hint">`:

```html
  <div class="rstep tl" id="rs1" style="--sc:var(--mint)">
    <div class="num">01<span class="ring"></span></div>
    <h2>Pick flavour &amp; strength</h2>
    <span class="gh gc" aria-hidden="true">Pick flavour &amp; strength</span>
    <span class="gh gm" aria-hidden="true">Pick flavour &amp; strength</span>
  </div>

  <div class="rstep tr" id="rs2" style="--sc:var(--citrus)">
    <div class="num">02<span class="ring"></span></div>
    <h2>Tuck it under your lip</h2>
    <span class="gh gc" aria-hidden="true">Tuck it under your lip</span>
    <span class="gh gm" aria-hidden="true">Tuck it under your lip</span>
  </div>

  <div class="rstep bl" id="rs3" style="--sc:var(--ice)">
    <div class="num">03<span class="ring"></span></div>
    <h2>Feel the slow release</h2>
    <span class="gh gc" aria-hidden="true">Feel the slow release</span>
    <span class="gh gm" aria-hidden="true">Feel the slow release</span>
  </div>

  <div class="rstep br" id="rs4" style="--sc:var(--rose)">
    <div class="num">04<span class="ring"></span></div>
    <h2>Bin it &amp; go</h2>
    <span class="gh gc" aria-hidden="true">Bin it &amp; go</span>
    <span class="gh gm" aria-hidden="true">Bin it &amp; go</span>
  </div>
```

- [ ] **Step 6: Verify statically**

Create `.claude/launch.json` if missing:

```json
{
  "version": "0.0.1",
  "configurations": [
    {"name": "glow", "runtimeExecutable": "python3", "runtimeArgs": ["-m", "http.server", "8123"], "port": 8123}
  ]
}
```

Open the Browser pane via preview_start `{name:"glow"}`, navigate to `http://localhost:8123/index.html`.
Expected: page renders exactly as before at rest (corner steps invisible — CSS `opacity:0`), no console errors, headline neon flicker plays.

- [ ] **Step 7: Commit**

```bash
git add index.html .claude/launch.json
git commit -m "Add corner ritual-step markup and styling for finale chapter"
```

### Task 2: Timeline extension — scrubbed corner entries

**Files:**
- Modify: `index.html` — module script, directly after the existing block that ends with `.addLabel('end').to({}, {duration:.3});` (~line 385-388)

- [ ] **Step 1: Append the ritual chapter to `tl`**

```js
/* ================= ritual chapter: four corner steps ================= */
const RS_ACCENT = ['#4dffc3','#ffd24d','#4dc9ff','#ff4d9e'];
['#rs1','#rs2','#rs3','#rs4'].forEach((sel,i)=>{
  const c = RS_ACCENT[i], at = 'step'+(i+1);
  tl.addLabel(at, '>.25')
    .fromTo(sel,
      {autoAlpha:0, scale:1.1, filter:'blur(16px) brightness(2.4)'},
      {autoAlpha:1, scale:1, filter:'blur(0px) brightness(1)', duration:.4, ease:'power2.out'}, at)
    .fromTo(sel+' h2',
      {textShadow:`0 0 16px ${c}, 0 0 36px ${c}`},
      {textShadow:`0 0 7px ${c}, 0 0 18px ${c}`, duration:.2, ease:'power1.out'}, at+'+=.3')
    .fromTo(sel+' .gc', {x:-12, opacity:.7}, {x:0, opacity:0, duration:.25, ease:'power2.out'}, at)
    .fromTo(sel+' .gm', {x:12,  opacity:.7}, {x:0, opacity:0, duration:.25, ease:'power2.out'}, at)
    .fromTo(sel+' .ring', {scale:.4, opacity:.9}, {scale:1.4, opacity:0, duration:.3, ease:'power2.out'}, at+'+=.3');
});
tl.addLabel('ritualEnd', '>.1')
  .to({}, {duration:.4});
```

Why this shape: labels give the existing `snapTo:'labelsDirectional'` one snap point per step; `fromTo` with default `immediateRender` pins each step hidden until its slot; everything rides the one scrubbed ScrollTrigger so scrolling back up reverses each entry.

- [ ] **Step 2: Verify in the Browser pane**

Reload `http://localhost:8123/index.html`, then scroll to the end of the page in steps.
Expected, in order: existing phases unchanged → GLOW POUCHES fades in centered with CTA → four further scroll ticks, each blinking one corner step in (01 TL mint, 02 TR citrus, 03 BL ice, 04 BR rose) with blur-focus entry, cyan/magenta ghosts converging, small ring pulse from the number → all four persist at page end. Scroll up: entries reverse. Console: no errors.

Check with `window.__glow.tl.labels` in the console that `step1..step4` and `ritualEnd` exist.

- [ ] **Step 3: Mobile viewport check**

Resize Browser pane to 375×812 (preset mobile). Expected: GLOW POUCHES fits centered (may wrap to two lines), corner steps read at 13px without touching the centered word, safe-area insets respected.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "Blink ritual steps into screen corners after GLOW POUCHES finale"
```

### Task 3: Post-change housekeeping

- [ ] **Step 1: Graph rebuild (project rule)**

Run: `python3 -c "from graphify.watch import _rebuild_code; from pathlib import Path; _rebuild_code(Path('.'))"`
Expected: succeeds if graphify is installed; if `ModuleNotFoundError`, note it in the final report and move on (no `graphify-out/` exists in this repo yet).

- [ ] **Step 2: Final screenshot proof**

Screenshot the finished finale state (desktop) from the Browser pane for the user.
