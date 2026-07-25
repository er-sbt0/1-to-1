# 04 — The Interest-Rate Ceiling: growth in slow motion

`exp` as the **limit of discrete compounding**, `(1 + x/n)^n → e^x`, plus the functional
equation `exp(x+y) = exp(x)exp(y)`.

**Open it:** double-click `index.html`. No build step, no server, no network at view time.
Everything (libraries, KaTeX fonts) is vendored under `lib/`.

---

## The idea

A bank pays 100% interest a year on your $1. Compound once → $2. Twice → $2.25. Daily →
$2.7146. So compounding infinitely often gives infinite money?

No. It gives $2.71828…, and not one cent more. The page is built around that shock, but the
shock is only the doorway. The actual point is underneath it:

> **The discrete compounding process *is* the law "rate = current amount" written in slow
> motion.** Each period multiplies the balance by `(1 + x/n)` precisely because the increase
> over that slice is (rate) × (amount) × (Δt). Refining the slices does not construct a new
> object — it takes the limit of the same law. And the limit is finite.

Said another way, and this is the non-trivial insight the page is engineered to deliver:
`(1 + x/n)^n` is **exactly Euler's method** for `y' = y, y(0) = 1` with step `h = x/n` on
`[0, x]`. The compound-interest story and the "speed equals position" story are the same
story at two zoom levels. The **function comes first**; the number `e` is the byproduct you
get by evaluating it at `x = 1`.

## The mathematics on the page

1. **The recursion.** `y_{k+1} − y_k = 1 · y_k · h` ⟹ `y_{k+1} = y_k(1 + x/n)` ⟹
   `y_n = (1 + x/n)^n`. Presented as an actual ledger of periods, balances and interest.
2. **The ceiling.** `(1 + 1/n)^n` for n = 1 … 10⁶, with the exact values, the shortfall
   `e − value`, and the first-order prediction `e/(2n)`. The whole prize (yearly →
   continuous) is $0.71828; monthly captures 85.3% of it, daily 99.48%, and everything
   past hourly is worth $0.000155.
3. **The binomial bridge** (the intellectual heart). Binomial expansion gives
   `(1 + x/n)^n = Σ_k (x^k/k!) · B_k(n)` with the bracket
   `B_k(n) = (1−0/n)(1−1/n)⋯(1−(k−1)/n) → 1`. Two consequences fall out at once:
   the sequence is **increasing** in n (every bracket rises, and more terms appear), and the
   limit **is** the power series `Σ x^k/k!`. Bars for the actual vs. limiting term, with the
   bracket displayed factor by factor. (At n = 12, k = 5 the bracket is 0.381944, so the
   term runs at 38.19% of `1/5!`.)
4. **Existence proof.** Every bracket ≤ 1 and `k! ≥ 2^{k−1}`, so
   `(1+1/n)^n ≤ Σ 1/k! ≤ 1 + 1 + ½ + ¼ + … = 3`. Increasing + bounded ⟹ convergent. A
   complete elementary construction of `e` that never assumes `e` exists.
5. **Additivity.** With a **fixed** step `h`, `(1+h)^{m+k} = (1+h)^m (1+h)^k` is already
   exactly true *before* any limit — it is the integer power law. The limit inherits it,
   which is why exp turns addition of time into multiplication of amount, and why the
   notation `e^x` is legitimate at all (`exp(r) = exp(1)^r` for rational r, then continuity).
   Shown as a rescaled-copy overlay: the second growth segment is the first one magnified
   by `A = exp(x)`.
6. **Where it breaks** (three honest panels):
   - **Negative time.** For `x < 0` and `n < |x|` the factor `1 + x/n` is negative and the
     ledger alternates in sign; `x = −5, n = 2` reports $2.25 for an account holding
     $0.0067. At `n = |x|` the factor is 0 and the balance is annihilated. The limit is
     still correct — the finite-n picture is a liar. Section 1 is therefore restricted to
     `x > 0`, and negatives are recovered from `exp(−x) = 1/exp(x)`.
   - **Floating point.** `(1+1/n)^n` in doubles staggers around n ≈ 10¹⁵ (it returns 3.035)
     and collapses to exactly 1.0 at n = 10¹⁶, because `1 + 1/n` rounds to 1. Shown
     side by side with the 70-digit value.
   - **Cost.** Compounding error is `≈ e/(2n)` (first order); the power series error is
     `≈ 1/(N+1)!`. Ten digits costs ≈ 2.7 × 10¹⁰ compoundings versus 13 series terms. Both
     define the same function; only one is a reasonable algorithm. This is the honest
     contrast with sibling artifact 03.

Numbers are **computed in-page**, never hard-coded, and labelled by provenance:
`exact` = exact rational `(n+1)^n / n^n` from native BigInt integer powers;
`70 dp` = big.js decimal arithmetic; the floating-point panel is the only place raw doubles
are shown as results, and it says so.

## Libraries (all vendored in `lib/`, all classic `<script>`-loadable from `file://`)

| Library | Version | Why this one, for this scene |
|---|---|---|
| **p5.js** | 1.9.4 | The two bespoke canvases: the ledger staircase (n multiplicative risers, the smooth `e^t` behind, the shortfall bracket, per-slice pointer picking linked to the table) and the additivity movie with its rescaled-copy overlay. Immediate-mode drawing is the right shape for a slider-driven scene that is redrawn from a data structure every frame, and none of this is a "chart". |
| **Chart.js** | 4.4.3 | The three genuinely statistical plots: balance-vs-n on a log axis (the ceiling), the binomial term bars (actual vs. limit, log y), and the log-log error-vs-work comparison. Declarative, UMD, no adapters needed for numeric/log scales. |
| **big.js** | 6.2.1 | High-precision decimal arithmetic for every headline value. `bpow` does binary exponentiation with rounding to 70 dp after each multiply — big.js multiplication is exact, so unrounded squaring would double the digit count 20 times over. Chosen over decimal.js/math.js because it is 7 kB and this page needs exactly one thing from it. |
| **KaTeX** | 0.16.11 | Every mathematical statement on screen, static and generated. |
| *(native BigInt)* | — | Exact rationals `(n+1)^n / n^n` for n ≤ 400. Not a library; no dependency needed. |

Deliberately **not** used: D3 and decimal.js (artifact 01), Paper.js and math.js (02),
GSAP and ECharts (03). No animation library — the staircase "run" is a frame counter inside
the p5 draw loop, and everything else is a CSS transition. No interaction library — the
controls are native `<input type=range>` and pointer picking inside the canvas.

## Layout

Folds **down**: seven separated sections, one beat of the argument each, with left/right
splits inside a section only where two views need to be read against each other (staircase ↔
ledger; term bars ↔ bracket factors; growth movie ↔ the two accounts).
