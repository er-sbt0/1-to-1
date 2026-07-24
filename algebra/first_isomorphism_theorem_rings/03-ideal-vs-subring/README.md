# Ideal vs. Subring — why `ker φ` must be an ideal

An interactive, single-file explainer for the **First Isomorphism Theorem for rings**
(`R/ker φ ≅ im φ`), told through its *failure mode*: coset multiplication is
well-defined **iff** the kernel absorbs products, i.e. **iff it is an ideal**. A mere
subring is not enough.

## The idea

Same ring, two subsets, opposite fates. You pick a subset `I` of the small ring
`R = ℤ/3ℤ × ℤ/3ℤ` (9 elements), the ideal axioms are checked **live**, and then you
multiply two cosets by choosing representatives and watch where the product lands:

- **Ideal** `I = {0}×ℤ/3ℤ` → absorption holds → the product coset never moves →
  `R/I` is a genuine ring, and `I` really is `ker φ` for the projection `φ(x,y)=x`,
  so `R/I ≅ im φ = ℤ/3ℤ`.
- **Subring only** `Δ = {(a,a)}` → additive subgroup **and** closed under `×`, but
  absorption **fails** (`(1,0)·(1,1)=(1,0) ∉ Δ`) → the *same* product of two cosets
  lands in **different** cosets depending on the representatives → `R/Δ` is not a
  ring and "`≅ im φ`" is meaningless.

You can also switch to **Custom** and click cells to build your own subset.

## The math (all verified in code)

- **Ideal:** `I ⊆ R` is a two-sided ideal iff `(I,+)` is a subgroup **and**
  `r·x, x·r ∈ I` for all `r∈R, x∈I` (absorption).
- **Why kernels always qualify:** if `φ(x)=0` then `φ(rx)=φ(r)φ(x)=0`, so `rx∈ker φ`.
- **Where absorption is forced:** with `a'=a+i`, `b'=b+j` (`i,j∈I`),
  `a'b' = ab + (aj + ib + ij)`. This lies in `ab+I` for *all* choices **iff**
  `aj, ib, ij ∈ I` — exactly absorption. So coset `×` is well-defined ⟺ `I` is an ideal.
- Why `ℤ/3ℤ × ℤ/3ℤ`: it is small enough to display every element, yet has an honest
  subring that is **not** an ideal (the diagonal) — impossible in `ℤ/nℤ`, where every
  additive subgroup is automatically an ideal.

The ring arithmetic, the subgroup/subring/absorption tests, the coset partition, and
the ambiguity detector all run in the page; nothing is hard-coded. (`verify.js`-style
checks confirmed the diagonal squared coset spans all three cosets.)

## Libraries (and why)

- **Plain HTML5 Canvas** — the 3×3 ring grid, coset color bands, gold "∈ I" rings,
  representative markers, and the pulsing product / red "landing" highlights are all
  drawn by hand. No charting/graph library needed, and deliberately **not** p5.js or
  D3.js (those are used by the sibling concepts).
- **KaTeX** (mandatory) — all mathematics is typeset with KaTeX, including the dynamic
  readout of `a'·b'`. Fully vendored (CSS + JS + `.woff2` fonts) into `lib/`.

No build step, no framework, no ES modules (classic UMD `<script>` so it works from
`file://` with origin `null`). No network access at runtime.

## How to open

Just open `index.html` in any modern browser:

```
file:///…/03-ideal-vs-subring/index.html
```

Everything (KaTeX, fonts) is local under `lib/`. Nothing to install or serve.

## Files

```
index.html        the whole interactive explainer (HTML + CSS + JS inline)
lib/katex.min.css, katex.min.js, auto-render.min.js
lib/fonts/*.woff2  KaTeX fonts (20)
README.md
```
