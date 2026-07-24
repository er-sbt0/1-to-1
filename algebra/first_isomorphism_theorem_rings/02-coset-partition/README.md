# R / ker φ — The Quotient Ring, Built From Cosets

An interactive explainer for the **First Isomorphism Theorem for rings**, told through the
*definition of the quotient ring* rather than a vague "gluing." The angle: the quotient is a
concrete ring whose elements are cosets, with computable addition **and** multiplication tables
that turn out to be — cell for cell — the tables of `im φ`.

## The idea

For a ring homomorphism `φ: R → S`, the theorem states

```
R / ker φ  ≅  im φ ,   via   r + ker φ  ↦  φ(r)
```

This page treats the left side as a *work order*: build a ring out of cosets and show it matches
the right side on **both** operations.

## The concrete example

```
φ : ℤ/12ℤ → ℤ/4ℤ ,   φ(r) = r mod 4
ker φ = { 0, 4, 8 } =: I        (the ideal (4))
```

- **Cosets (the elements of R/I):** `{0,4,8}, {1,5,9}, {2,6,10}, {3,7,11}` — four of them, `12/3 = 4`.
- **Coset operations:** `(a+I)+(b+I) = (a+b)+I` and `(a+I)·(b+I) = (ab)+I`.
- Everything is finite, so the full 4×4 addition and multiplication tables fit on screen.
- `R/I ≅ ℤ/4ℤ = im φ`.

Every table, coset, and well-definedness check is **computed in code** (`answerCoset`,
`cellValue`), not hard-coded. A Node sanity check confirms coset multiplication is representative-
independent across all 32 cells and all representative choices.

## What you can do

1. **See the partition** — R painted by coset, then regrouped into four parallel bands (D3 tiling).
2. **Toggle representatives** for each coset and watch the *numbers* in the operation tables change
   while every cell keeps its *color*: the answer coset never moves. That invariance is
   well-definedness, shown rather than asserted.
3. **Click any cell** to trace `(a+I)+(b+I) = (a+b)+I = … = k+I` in KaTeX, with the resulting coset
   highlighted in the ring strip.
4. **Flip "Relabel by φ"** — the coset headers `r+I` become plain elements `φ(r)`, and the R/I
   tables snap onto the reference `ℤ/4ℤ` tables. That table-for-table sameness *is* the isomorphism.

## Honesty notes surfaced in the UI

- Coset **addition** needs only that `I` is a subgroup; coset **multiplication** is well-defined only
  because `I` is an **ideal** (it absorbs products) — the crux, and the failure mode if you pick a
  non-ideal subgroup. Step 4 shows the cross-term computation `a'b' − ab ∈ I`.
- The example is fully finite, so there is no window/infinity caveat.

## Libraries (and why)

- **D3.js v7** (`lib/d3.min.js`, UMD global) — data-driven tiling of the ring, the coset rows,
  the legend, and the dynamically rebuilt operation tables. A natural fit for binding the ring/coset
  arrays to DOM/SVG. (Deliberately *not* p5.js — this concept is about algebraic structure, not
  animated dots.)
- **KaTeX 0.16.11** (`lib/katex.min.js` + `lib/katex.min.css` + `lib/fonts/*.woff2`) — all
  mathematical typesetting, static prose and dynamic traces alike.

Both are vendored locally as classic `<script>`/`<link>` (no ES modules, no importmaps) so the page
runs from `file://` with no build step and no network.

## How to open

Just open `index.html` in any modern browser (double-click, or `file://…/index.html`). No server,
no build, no internet required.
