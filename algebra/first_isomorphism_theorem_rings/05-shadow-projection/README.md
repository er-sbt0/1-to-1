# The Shadow of a Ring — First Isomorphism Theorem (3D)

An interactive 3D explainer for the **First Isomorphism Theorem for rings**, told through
one grand spatial metaphor: a ring homomorphism is a **projection casting a shadow**.

> φ : R → S a ring homomorphism ⟹ **R / ker φ ≅ im φ** via `r + ker φ ↦ φ(r)`.

## The idea

The ring `R` is a real object in space; `φ` projects it straight down onto its shadow (= `im φ`).
Everything lying along the same projection line collapses to one shadow point — those lines are the
**fibers = cosets of `ker φ`**, and the collapse direction *is* `ker φ`. Bundling `R` into these
parallel slices gives `R / ker φ`, and the theorem says the set of slices is a perfect copy of the shadow.

You rotate the object, toggle the fibers as vertical lines, then **cast the shadow** and watch the
slices land on the shadow points one-to-one.

## The concrete map (honest, computed in code)

- **R = (ℤ/3ℤ)³** — a genuine 3×3×3 lattice of **27 points**.
- **φ : (ℤ/3ℤ)³ → (ℤ/3ℤ)²,  (x, y, z) ↦ (x, y)** — forget the third coordinate. Because everything
  is componentwise, φ respects `+` and `×`, so it is a real ring homomorphism.
- **ker φ = {(0, 0, z)}** — literally the **vertical axis**, the honest collapse direction. `|ker φ| = 3`.
- **fibers** = the 9 vertical **columns** (vary z); each is a coset `(a,b,z) + ker φ`.
- **im φ = (ℤ/3ℤ)²** — the **9 shadow discs** on the floor.
- Count: `#slices = |R| / |ker φ| = 27 / 3 = 9 = |im φ|`, shown live and matched 1-to-1.

The lattice points, φ, the fibers, the kernel column and the image are all **computed in JavaScript**
(not hard-coded positions); the spheres and discs are drawn from that computed data.

### Honesty about the metaphor (surfaced in the UI)

The cube is a picture of a *finite, discrete* ring, not a continuous solid. The projection faithfully
shows the **set map** and the **additive** structure — fibers are exactly cosets of `ker φ`, which is why
the shadow really equals `im φ`. The picture does **not** prove multiplication is preserved; that is
verified in the algebra (φ is componentwise, so `φ(ab) = φ(a)φ(b)`). Clicking a column shows the coset ↦
shadow-point correspondence.

## Interaction

- **Orbit / zoom** the 3D object (drag, scroll).
- **Cast the shadow** slider + **Collapse / Lift back** buttons — smooth GSAP-driven collapse of every
  column down its kernel direction onto its single shadow point.
- Toggles: **Fibers**, **Kernel axis**, **Shadow floor**, **Auto-spin**.
- **Hover / click** any column to highlight its coset and its one shadow point, with a KaTeX label
  `(a,b,z) + ker φ ↦ (a,b)`.
- A **staged narration** (6 panels): R as an object → the projection φ → kernel as collapse direction →
  fibers = cosets → cast the shadow → the ≅ conclusion. Each panel re-poses the scene.
- Live **counter**: |R| = 27, |ker φ| = 3, #slices = #shadow points = 9.

## Libraries (all vendored locally in `lib/`, no internet, no build step)

- **Three.js r137** (`three.min.js`, UMD/global classic-script build) — real WebGL 3D. Chosen so this is
  the only *genuinely 3D* concept in the set; the projection metaphor is literal, not faked.
- **OrbitControls** (`OrbitControls.js`, the classic global build that attaches to `window.THREE`) — used
  instead of the ES-module `examples/jsm` version because that fails from `file://` (CORS origin `null`,
  no importmaps). r137 is the version whose `examples/js/` global controls still exist on the CDN.
- **GSAP** (`gsap.min.js`) — buttery collapse / lift animation.
- **KaTeX** (`katex.min.js`, `katex.min.css`, `auto-render.min.js`, `fonts/*.woff2`) — all math typesetting.

## How to open

Just open `index.html` in a browser (double-click, or `file://…/index.html`). No server, no build,
no network required — every asset is local and referenced by relative path.
