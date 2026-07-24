# Fiber Collapse — First Isomorphism Theorem for Rings

An interactive, offline-first explainer that shows the **First Isomorphism Theorem
for rings** as a literal, watchable *collapse*: the fibers (preimage bundles) of a
ring homomorphism fly together and merge onto their shared image points.

Open `index.html` directly in any modern browser — **no build step, no server, no
internet**. (Double-click it, or `file://…/index.html`.)

## The idea

A ring homomorphism `φ: R → S` squashes `R` onto its image. Everything that lands
on the same image point forms a **fiber** `φ⁻¹(s)`. Those fibers are *exactly* the
cosets of `ker φ`. Gluing each fiber to a single point **is** the construction of
the quotient ring `R / ker φ`, and the result is a perfect copy of `im φ`. That is
the theorem, made physical.

## The concrete map (honestly computed)

    φ : ℤ → ℤ/nℤ,   φ(r) = r mod n        (reduction mod n — a ring homomorphism)

- `ker φ = nℤ`  → drawn as row `[0]`, which really does contain exactly the
  multiples of `n` inside the window.
- The **fibers** are the residue classes; `φ(a) = φ(b) ⇔ a − b ∈ ker φ = nℤ`.
- `R / ker φ = ℤ/nℤ  ≅  im φ = ℤ/nℤ`, via `a + nℤ ↦ φ(a)`.

Everything on screen is computed, not faked: the domain window is the integer array
`−18 … 18`, each integer's image is `((v % n) + n) % n`, and the dots are grouped
into cosets in code before the merge animation. The modulus `n` is user-selectable
(2–8) and the grouping/collapse recompute live.

### Honesty caveats (surfaced in the UI)

- **ℤ is infinite** — we show only a finite *window*; the `…` at each end mean every
  fiber continues forever.
- **`ker φ` is an ideal**, not merely an additive subgroup: it absorbs
  multiplication (`s·(nk) ∈ nℤ`). That is precisely why the quotient is a *ring*, so
  the collapse respects both `+` and `×`.

## How to use it

- **Modulus n** buttons pick the homomorphism.
- **Next / Back** walk the four-step argument: `R` (number line) → partition into
  fibers/cosets → collapse → `ℤ/ker φ ≅ im φ`.
- **Hover** any dot to highlight its whole fiber and see `a + nℤ` in real math type.
- **Replay collapse** replays the merge.

## Stack (and why)

- **p5.js** (vendored, `lib/p5.min.js`) — free-form 2D canvas animation is the
  natural fit for bundles of dots easing together and merging; a chart/DOM library
  would fight this. Used in instance mode so it doesn't pollute globals.
- **KaTeX** (vendored, `lib/katex/` + all 20 `.woff2` fonts) — fast, accurate math
  typesetting for the on-screen statements. Fonts are local so it renders with no
  network. The page starts with `<!DOCTYPE html>` so KaTeX is not blocked by quirks
  mode.
- No framework, no bundler, no ES modules — plain classic `<script>` UMD builds so
  the whole thing runs from `file://` with `origin: null`.

## Files

    index.html                 the whole app (markup + CSS + JS inline)
    lib/p5.min.js              p5.js 1.9.4 (UMD)
    lib/katex/katex.min.js     KaTeX 0.16.11 (UMD)
    lib/katex/katex.min.css
    lib/katex/fonts/*.woff2    all 20 KaTeX web fonts
    README.md
