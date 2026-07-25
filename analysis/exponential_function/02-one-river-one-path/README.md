# 02 · One River, One Path

**The exponential function as the unique fixed point of differentiation — proved in the slope field.**

Open `index.html` directly in any browser (double-click, or `file://…/index.html`).
No build step, no server, no network access at view time. Every library is vendored in `lib/`.

---

## The idea

`y' = y` is usually read as an equation to be *solved*. Read it instead as a **law of the plane**:
at the point `(x, y)` the current runs with slope `y`. That is an arrow attached to every point.
Drop a boat anywhere and it is swept along one — and only one — path. The path through `(0,1)` is `exp`.

The angle this artifact owns is the **uniqueness theorem**: there is nothing else. Its one-line proof
is the spine of the page, and everything else is built to make that line feel inevitable before it is
read, and honest after.

The non-trivial insight the page is built to deliver: **the function comes first, the number is a
byproduct.** `e` is not an ingredient of this story — it is `exp(1)`, a height read off the traced
path at time 1. And because uniqueness is a *theorem*, "rate proportional to amount" does not merely
*suggest* exponential behaviour anywhere in nature — it **forbids everything else**.

## The seven beats (the page folds down, one point per section)

1. **The law is a field, not a formula.** Hover the field: the arrow depends only on the height `y`,
   never on `x`. The isoclines are horizontal lines — which is *why* solutions are horizontal
   translates of one another, i.e. `e^{x+c} = e^c·e^x`, seen as geometry before any algebra.
2. **Drop a boat.** Click anywhere; the point is integrated forwards and backwards through the field
   by RK4 (`h = 0.01`), using only the local rule `slope = y`. Curves never cross; `y ≡ 0` is a wall.
   `e` is then *read off* the gold path at `x = 1` (`2.718281828`), never typed in.
3. **Sliding sideways = scaling upwards.** A shift slider moves `e^{x+s}` and draws `e^s·e^x` on top
   of it; they never separate. Reflection reaches `c < 0`; shifts reach exactly `c > 0`; `c = 0` is
   reachable by neither. Dropping the initial condition gives the family, not chaos.
4. **Try to escape the current.** Draw a freehand curve. Paper.js fits Béziers to the stroke; the
   page samples 261 analytic tangents and paints each stretch by its disagreement with the field.
   The "cheat" is measured, not asserted: % of arc length obeying, mean angle error, `max|y'_yours − y|`,
   and the vertical gap to `e^x`.
5. **The proof, in nine steps.** Type or pick any candidate `f`. math.js differentiates it
   *symbolically*, the page measures `max|f' − f|` and plots both `f` and `g(x) = f(x)e^{-x}`.
   Watch `g` flatline exactly when the hypothesis holds. The stepper walks:
   `g := f·e^{-x}` → product rule → factor → `g' = (f'−f)e^{-x} = 0` → **MVT ⇒ `g` constant** →
   `g ≡ g(0) = f(0)` → `f = f(0)e^x` → `f(0)=1 ⇒ f = exp`.
6. **What this proof does *not* do.** Three caveats, stated plainly rather than glossed:
   *existence* is not proved (the argument assumes `e^{-x}` and its derivative — that is the power
   series' job); "zero derivative ⇒ constant" is the **Mean Value Theorem**, and the word *interval*
   is load-bearing; and every traced curve is a **numerical** object — pick RK4/Heun/Euler and a step
   size and read the measured error against `e^x`.
7. **Why nature has no choice.** A `k` slider turns the field into `y' = ky`; the same one line with
   `g = f·e^{-kx}` settles every "rate ∝ amount" process at once.

## The mathematics, precisely

- **Field.** `(x, y) ↦ m = ky` (with `k = 1` except in §7). Horizontally invariant, hence solutions
  are horizontal translates and the group of shifts acts on the family by `c ↦ e^s c`.
- **General solution.** `y = c·e^{kx}`, one curve through each point of the plane; `y ≡ 0` equilibrium.
- **Uniqueness proof.** For any differentiable `f` on `ℝ` with `f' = f`, set `g = f·e^{-x}`
  (legal everywhere since `e^{-x} > 0`). Then `g' = (f' − f)e^{-x} = 0`, so by the **corollary of the
  Mean Value Theorem** `g` is constant on the interval `ℝ`; hence `g ≡ g(0) = f(0)` and
  `f(x) = f(0)e^x`. With `f(0) = 1`, `f = exp`. ∎
- **Failure mode shown, not hidden.** Drop `f(0) = 1` and you get exactly `c·e^x` — the initial
  condition chooses the curve, the law makes it exponential.
- **Integration.** Classical RK4 / Heun (RK2) / forward Euler on `y' = ky`, forward and backward,
  with the closed form `y₀e^{k(x−x₀)}` overlaid and `max|numeric − exact|` and the max relative error
  reported for every traced curve. RK4 at `h = 0.01` sits at ~5·10⁻⁹ over `[−1, 3]`; Euler at `h = 1`
  returns `2` at `x = 1` and `8` at `x = 3`. A deliberate bonus: RK4 with `h = 1` returns
  `2.708333… = 1 + 1 + 1/2 + 1/6 + 1/24` — on this field a fourth-order RK step *is* the fourth-order
  Taylor polynomial.
- **Freehand check.** Tangents come from the fitted Bézier path analytically; the angle error is
  measured on screen (the disagreement the eye sees) and also reported coordinate-free as
  `|y'_yours − y|`. Segments with a near-vertical tangent are flagged *not a graph of a function of x*.

## Libraries (all vendored in `lib/`, all classic `<script>` globals so `file://` works)

| library | version | why *this* scene needs it |
|---|---|---|
| **Paper.js** (`paper-core.min.js`) | 0.12.18 | A retained-mode **vector scene graph** is the right shape for a mostly-static field with layered, re-projectable content: 500+ arrow paths in one layer, integral-curve polylines in another, hover/overlay art in a third, redrawn only where something changed. Decisively: `Path.simplify()` fits Béziers to the raw freehand stroke and `getPointAt`/`getTangentAt` then give **analytic tangents** — which is exactly the quantity §4 must compare against the field. Doing that by hand on a raw canvas would mean writing a curve fitter. `paper-core` (no PaperScript/acorn) is used, driven purely from JS with one `PaperScope` per canvas. |
| **math.js** (`math.min.js`) | 12.4.3 | §5 lets the reader type an arbitrary candidate `f`. math.js parses it, **differentiates it symbolically** (`math.derivative` + `math.simplify`), renders both to TeX, and compiles both to fast numeric evaluators. The verdict "does this `f` satisfy `f' = f`?" is therefore computed, not looked up in a table of blessed examples. |
| **KaTeX** | 0.16.11 | Every statement on screen, static and dynamic (the proof steps, the live `e^{x+s} = e^s e^x` identity). Vendored with its `.woff2` fonts; the stylesheet's `url(fonts/…)` paths resolve locally. |

Deliberately **not** used: D3 and decimal.js (owned by the sibling `01-slope-dial`), and no
animation/physics library — the nudge for this concept is a click-to-trace, step-through-the-proof
page, so the only motion is a one-shot ~0.6 s curve trace with a wall-clock completion guarantee.

## Files

```
index.html          the whole artifact (structure, style, and all the mathematics)
lib/paper-core.min.js
lib/math.min.js
lib/katex/katex.min.js, katex.min.css, fonts/*.woff2
manifest.json       machine-readable inventory
README.md           this file
```
