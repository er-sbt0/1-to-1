# 03 · The Factorial Conveyor

**A series built to survive differentiation.**

Open `index.html` in any browser. No build step, no server, no network — every library is
vendored under `lib/`.

---

## The idea

Lay the terms of `exp(x) = Σ xⁿ/n!` out as coloured blocks on a conveyor belt. Press **d/dx** and
every block slides exactly one slot left, landing on the value its neighbour was already holding.
The factorial is the *gear ratio* that makes the shift lossless:

```
d/dx [ xⁿ/n! ] = n·xⁿ⁻¹/n! = xⁿ⁻¹/(n−1)!
```

The `n` that falls out of the power cancels the `n` in the factorial. Nothing is gained, nothing is
lost — so differentiating the whole belt reproduces the whole belt. That is `f′ = f` rendered as a
mechanism.

Run the same belt with a *finite* number of blocks and it breaks: the top slot has no supplier, so
it empties. Keep pressing and the polynomial is eaten from the top down until nothing is left. That
is the honest picture of "the only polynomial with `p′ = p` is `p ≡ 0`", and it is *why*
self-derivativeness demands an infinite series.

### Where this sits among the five

Artifact **02** proved **uniqueness** (`g = f·e⁻ˣ` flatlines, so at most one solution exists) and
explicitly deferred **existence**. This page supplies it: it constructs the function from scratch,
shows the coefficients were forced, and proves the construction converges on all of ℝ.
Uniqueness + existence = *the* exponential function. The number `e` arrives last, as the reading at
`x = 1`.

---

## The mathematics on screen

**§1 — the coefficients are forced (derived, not asserted).**
Suppose `f(x) = Σ aₙxⁿ` with `f′ = f`. Then

```
f′(x) = Σ_{n≥1} n·aₙ x^{n−1} = Σ_{k≥0} (k+1)a_{k+1} x^k
```

Matching coefficients with `f` gives `(k+1)a_{k+1} = aₖ`, i.e. `a_{k+1} = aₖ/(k+1)`, so
`aₙ = a₀/n!`. **The ODE fixes the shape; `f(0) = 1` fixes the scale.** There was never a choice.
The interactive lets you type arbitrary coefficients, watches the residual `rₖ = aₖ − (k+1)a_{k+1}`
per slot, and then runs the recurrence to drag every value home.

**§3 — why finite always fails.** `deg p′ ≤ d−1 < d = deg p`, so differentiation strictly lowers
degree and `p′ = p` forces `p ≡ 0`. Quantitatively, `S_N − S_N′ = x^N/N!` — the failure is exactly
one term, the top one. The chart checks this against a derivative computed *independently* by
central finite differences, so the agreement is evidence rather than a restatement.

**§5 — convergence for every x.** `|t_{n+1}/tₙ| = |x|/(n+1) → 0` for any fixed `x`; radius of
convergence ∞. The honest part is that convergence is **not monotone**: at `x = 20` the terms grow
until `n ≈ 20`, peaking near `4.31×10⁷`, before collapsing superexponentially.

**§6 — e as a byproduct.** Exact `BigInt` rationals: with `Aₙ = n·A_{n−1} + 1`, `A₀ = 1`, the
partial sum is exactly `Sₙ = Aₙ/n!` (`1/1, 2/1, 5/2, 16/6, 65/24, 326/120, …`), long-divided to 24
decimal places. Digits that already agree with `e` are highlighted. The error bound `0 < e − Sₙ <
1/(n!·n)` is plotted against the actual error.

**§7 — where the picture lies.** Summing `Σ xⁿ/n!` at `x = −20` in doubles gives ~173 % relative
error (no correct digits): the terms swing to `4.3×10⁷` while the answer is `2.06×10⁻⁹`, so rounding
noise `ε·max|tₙ| ≈ 9.6×10⁻⁹` swamps the signal. Computing `1/Σ 20ⁿ/n!` instead is accurate to
`2×10⁻¹⁶`. Both numbers are computed live in the page, not quoted.

### Caveats surfaced in the UI, not hidden

- **Term-by-term differentiation is a theorem**, not bookkeeping — it needs uniform convergence on
  compact subsets of the disc of convergence. Named where used, not proved. The page proves the
  radius is ∞ *before* leaning on the licence.
- **Matching coefficients assumes uniqueness of the power series representation** (the identity
  theorem). Stated.
- **Everything displayed is a finite partial sum in IEEE-754 doubles**, with the term count and the
  resulting truncation error always shown. `Math.exp` is used only as a reference and is labelled as
  itself an approximation. The only exact arithmetic is the `BigInt` work in §6.

---

## Libraries (all vendored in `lib/`, all classic `<script>` globals so `file://` works)

| Library | Version | Job in *this* scene | Why it, and not something else |
|---|---|---|---|
| **KaTeX** | 0.16.11 | every mathematical statement, including the labels *inside* the moving blocks | mandatory notation layer; renders to DOM, so a block's label can be a real formula that changes mid-animation |
| **GSAP** | 3.12.5 | the belt choreography: brightness flash → staggered slide → label simplification → tail refill / wall, plus the number-tweening of coefficients | the shift is a *sequenced* multi-phase timeline with staggers and promise-awaited phases; CSS transitions cannot express it, and a timeline is exactly the right abstraction |
| **Apache ECharts** | 5.5.1 | five plots: deficit identity, partial-sum family, term-magnitude bars (log), ratio test (log), error-vs-bound | one library covering both the log-axis bar chart and the multi-series line charts; ECharts' log axes and `markLine` do the "ratio = 1" and "probe x" annotations natively |
| *(none)* | — | exact rational coefficients | native `BigInt` is enough for `Aₙ/nₖ!` and long division; adding fraction.js/big.js would be a dependency doing nothing the platform doesn't |

**Deliberately not used:** D3 and decimal.js (taken by 01), Paper.js and math.js (taken by 02).

**Why DOM blocks rather than a canvas library (p5/Two/Pixi):** the whole point is that each block
*carries a formula that changes as it moves* (`xⁿ/n!` → `n·xⁿ⁻¹/n!` → `xⁿ⁻¹/(n−1)!`). KaTeX renders
DOM, so absolutely-positioned `<div>` blocks moved by GSAP transforms give real typeset mathematics
on a moving object for free. A canvas scene graph would have forced rasterised or hand-drawn
formulas — strictly worse, for one extra dependency.

## Files

```
index.html          the whole artifact
lib/katex/          katex.min.js, katex.min.css, fonts/*.woff2 (20 files)
lib/gsap.min.js     UMD build
lib/echarts.min.js  UMD build
manifest.json       machine-readable inventory
```
