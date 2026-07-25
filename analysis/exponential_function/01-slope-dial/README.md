# The Slope Dial — *e* is found, not given

An interactive, fully offline explainer for the exponential function, built around a single
manipulable object: **a slider for the base `a`**. You turn it until `a^x` and its own derivative
fall exactly on top of each other. The base where that happens is what we call `e`.

Open `index.html` in any browser. No build step, no server, no network.

---

## The idea

Students usually have the logic backwards. They think `e = 2.71828…` is a magic number handed
down from somewhere, and that `e^x` inherits its nice properties from it.

The truth runs the other way:

1. **Every** exponential is already proportional to its own derivative. This is pure algebra:
   `a^{x+h} − a^x = a^x(a^h − 1)`, so the difference quotient factors as
   `a^x · (a^h − 1)/h`. The right-hand bracket contains **no `x`** — the *same* constant multiplies
   every point on the curve. Let `h → 0`:

   ```
   d/dx a^x = C(a) · a^x        C(a) = lim_{h→0} (a^h − 1)/h
   ```

   `C(a)` is just the slope of `a^x` at `x = 0`.

2. `C` is continuous, strictly increasing, and `C(1) = 0`. So there is **exactly one** base at
   which the proportionality constant is exactly `1`.

3. That base is `e`. **`e` is the output of a condition, not an input.** The number is a byproduct
   of the function, not its source.

The page is deliberately narrow in scope: it is the base-slider story and nothing else. It says
nothing about power series, compound interest, ODE uniqueness, or Euler's formula.

## The six beats (vertical scroll, one point each)

| # | Beat | What it makes tangible |
|---|------|------------------------|
| 1 | **The factoring** | Drag two sample points and the step width `h`. The two rises stay in a fixed ratio `a^{x₂−x₁}` no matter what `h` is — the same factor `(a^h − 1)` scaled by local height. A table then verifies `f′(x)/f(x)` is *the same number* at five different `x`. |
| 2 | **Measuring C** | `(a^h − 1)/h` tabulated as `h → 0`, side by side in **double precision** and **60-digit** arithmetic. Green digits mark agreement with the converged value. The double column gains a digit per row, then dies of catastrophic cancellation, then returns exactly `0` at `h = 1e-16`. Watch-it-break, and an honest lesson in why the high-precision column exists. |
| 3 | **The hunt** *(main event)* | `a^x` in blue against its **numerically differentiated** self in amber, with a signed gap ribbon between them (blue = lags, red = overshoots). At `a = 2` the derivative lags; at `a = 4` it overshoots; the ribbon collapses at exactly one base. Auto-sweep drives the dial through the crossing so you *see* it happen. |
| 4 | **The signature** | The subtangent. Run the tangent at any `P` down to the x-axis; the horizontal distance is `1/C(a)`. Drag `P` — **it never changes** (that *is* the proportionality, geometrically). Then turn the dial until the amber bar matches the grey unit ruler. Subtangent `= 1` at every point is the checkable fingerprint of `e`. |
| 5 | **Solving C(a) = 1** | `C` plotted as a function (every point a measured limit). `C(2) < 1 < C(3)`, plus monotonicity ⇒ IVT gives a unique root. A live bisection console squeezes the bracket, first in doubles, then a 128-step run in 70-digit arithmetic. |
| 6 | **The reveal** | *Only now:* the dial function has a name. `C(a) = ln a`, so `d/dx a^x = (ln a)a^x` and `C(a) = 1 ⟺ a = e`. Read left to right — the logarithm did not come first. |

## Non-circularity (the constraint that shaped the build)

The whole point collapses if `e` or `ln` is smuggled in as an input. So:

- `C(a)` is **never** defined as `ln a`. It is computed from difference quotients only.
- The root of `C(a) = 1` is found by **bisection on the numerically measured `C`**, not by
  reading `Math.E`.
- `Math.log` appears exactly once (beat 6's comparison table) and `Math.E` exactly once
  (beat 5's accuracy-check box). Both are labelled in the UI as external checks.
  Delete them and nothing on the page stops working.
- `Math.log10` is used once for display formatting only (counting matching digits); it is
  commented as such in the source.

**Result:** the 70-digit bisection recovers

```
2.718281828459045235360287471352662497…
```

agreeing with the published value of `e` to **35 decimal places** — from difference quotients alone.

## Honesty caveats surfaced in the UI

- **Existence of the limit** `lim (a^h − 1)/h` is *asserted, not proved*. It is provable by
  monotonicity/bounding arguments; no such proof appears here, and everything downstream rests on it.
  Stated in a callout in beat 2.
- **Continuity and strict monotonicity of `C`** are likewise assumed, and they are what license the
  IVT step in beat 5. Stated in beat 6's "what was assumed" panel.
- Numerical evidence is presented as **evidence, not proof**, in as many words.

## Numerical methods

- **Pointwise derivative** — symmetric quotient `(a^{x+h} − a^{x−h})/2h`, whose error expands in
  even powers of `h`, with **4 levels of Richardson extrapolation** (`h₀ = 1/64`, halving).
  This kills the `h²`, `h⁴`, `h⁶` terms while staying above the floating-point cancellation floor,
  landing within ~`1e-14` of the true value. Verified against `ln a` at 8 bases.
- **High-precision `C(a)`** — same symmetric quotient at `h = 1e-30` in 70-digit `decimal.js`
  arithmetic: ~40 correct digits.
- **The beat-2 table** deliberately uses the *forward* quotient `(a^h − 1)/h`, the literal definition,
  so the convergence (and the numerical failure) is the honest one.
- **Root find** — bisection on `[2, 3]`; 50 steps in doubles, 128 steps at 70 digits.
- The "noise" flag in beat 2 compares the observed error against the *legitimate* truncation term
  `≈ C²h/2`; rows are only flagged when rounding noise exceeds that by 20×. Rows that merely
  haven't converged yet are **not** flagged.

## Libraries (all vendored locally in `lib/`, all classic `<script>`-loadable)

| Library | Version | Role in this scene | Build |
|---|---|---|---|
| **KaTeX** | 0.16.11 | Every mathematical statement on screen. Mandatory. | global |
| **D3.js** | 7.9.0 | Scales, axes, tick generation, `d3.line`, and `d3.drag` for the draggable handles in beats 1 and 4. Used as a *toolkit*, not a charting library — every mark is hand-authored SVG. | UMD |
| **decimal.js** | 10.4.3 | Arbitrary-precision arithmetic. This is the one that earns its place: it lets the page show the difference quotient genuinely converging past the point where doubles fail, and lets the bisection reach 35 correct decimals of `e`. | UMD |

Three libraries, three disjoint jobs. No plotting library (Plotly/Chart.js/Observable Plot) was used —
the visuals are bespoke geometry (gap ribbons, subtangent bars, unit rulers, secant blocks), which a
charting API would fight rather than help. No animation library — the single auto-sweep is a plain
`requestAnimationFrame` loop.

## Gotchas hit

- **KaTeX in quirks mode** silently refuses to lay out. The file starts with a proper
  `<!DOCTYPE html>` + `<head>`/`<body>`.
- **KaTeX fonts** — `katex.min.css` references `url(fonts/*.woff2)` relatively, so the `fonts/`
  directory must sit next to the CSS. All 20 `.woff2` files are vendored.
- **`Decimal.set({precision})` is global**, so the beat-2 table helper saves and restores
  `Decimal.precision` around its call rather than leaving the global precision changed.
- **Scroll-fade could hide content.** The fade-in start state is applied only under
  `html.js-anim`, added by JS *after* `IntersectionObserver` is confirmed to exist, plus a 2.5 s
  belt-and-braces timeout. A JS or observer failure can never leave the page blank.
  `prefers-reduced-motion` disables it entirely.
- **Render gating and staleness.** Only the expensive 60-digit beat is gated on visibility, and on a
  deliberately generous `400px` root margin. Every other beat re-renders on every dial change, so no
  section can ever be caught showing a stale value — the cheap beats cost well under a millisecond.
- **ESM-only builds fail from `file://`.** Both D3 and decimal.js were pulled as UMD bundles from
  jsDelivr (`/dist/d3.min.js`, `/decimal.min.js`), not from GitHub source trees.

## Interaction contract

- **Base `a`** — one global dial, mirrored in beats 1–4; every copy stays in sync. Slider,
  `±.001`/`±.01` nudge buttons, preset chips (`a = 2, 3, 4`), arrow keys (Shift = fine),
  auto-sweep, and *snap to the base we found*.
- **Beat 1** — drag either sample point along the curve; slide the step width `h`.
- **Beat 4** — drag the point of tangency `P`.
- **Beat 5** — `Step once`, `Run to double limit`, `Finish in 70-digit arithmetic`, `Reset`.
