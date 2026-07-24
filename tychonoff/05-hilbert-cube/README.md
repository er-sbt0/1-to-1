# The Hilbert Cube — Tychonoff's Theorem in Infinite Dimensions

An interactive 3D artifact that conveys **Tychonoff's theorem** through the
**Hilbert cube**, the infinite-dimensional compact product

$$\mathcal{H} = [0,1]^{\mathbb{N}} = \prod_{n=1}^{\infty} [0,1].$$

Open `index.html` in any modern browser — **double-click it, no server, no build
step, no internet required.**

---

## The idea

Intuition says "more dimensions = harder to keep compact." Tychonoff's theorem
says the opposite is fine: **an arbitrary product of compact spaces is compact.**
Since each factor `[0,1]` is compact, the countable product `[0,1]^ℕ` — the
Hilbert cube — is compact too, no matter how many dimensions we stack on.

This piece makes that tangible:

- A **rotating projection** grows from a segment `[0,1]` → square `[0,1]²` →
  cube `[0,1]³`, then keeps *adding dimensions*: a 4-, 5-, 6-cube projected down
  to 3D with a tesseract-style tumble. New dimensions **fade in one at a time**,
  each in its own color (cyan → pink), while a counter climbs `n = 1, 2, 3, … → ∞`.
- A **point is a whole infinite sequence** `x = (x₁, x₂, x₃, …)`, each `xₙ ∈ [0,1]`.
  The side panel shows the sequence live; drag any coordinate bar (or drag the
  gold point in 3D) and watch it move.
- A **basic open neighborhood** gizmo (the `k` slider) demonstrates the product
  topology: a basic open set

  $$U = \Big(\textstyle\prod_{n\le k} I_n\Big)\times \prod_{n>k}[0,1]$$

  constrains only the **first `k`** coordinates (shown thin/green, `xₙ ± ε`) and
  lets **all the rest range freely** over `[0,1]`. That "only finitely many
  coordinates are pinned" is exactly the definition of the product topology, and
  it is why Tychonoff's theorem is subtle — the box topology would *not* stay
  compact.

## The math (all correct, rendered with KaTeX)

- **Definition:** `ℋ = [0,1]^ℕ = ∏[0,1]`.
- **Compactness (Tychonoff):** each `[0,1]` compact ⟹ `∏[0,1]` compact — with
  **no bound on the number of factors**.
- **Metrizable:** `d(x,y) = Σ 2^⁻ⁿ |xₙ − yₙ|` induces the product topology, so the
  Hilbert cube is a compact metric space (also homeomorphic to `∏[0, 1/n]`).
- **Basic open set:** finitely many coordinates constrained, the rest free.

## Honesty note

Infinite dimensions cannot be embedded in 3D. The spinning object is an explicit
**projection / metaphor** — an `n`-cube shadow with `n` climbing toward ∞ — and
the UI says so. The mathematics in the panels is exact; only the picture is a
finite stand-in.

---

## Interactions

- **Orbit / zoom:** drag the background to orbit, scroll to zoom (OrbitControls).
- **Auto-orbit** toggle and **Replay build-up** button.
- **Drag coordinate bars** in the right panel, or **drag the gold point** in 3D
  (horizontal = x₁, vertical = x₂).
- **`k` slider:** size of the basic open neighborhood (how many coordinates are
  constrained). **ε slider:** its radius.

## Libraries (all vendored locally in `lib/` — nothing loads from the network)

| Library | Version | File |
|---|---|---|
| Three.js (UMD global build) | r149 | `lib/three.min.js` |
| OrbitControls (converted to a global that attaches `THREE.OrbitControls`) | r149 | `lib/OrbitControls.global.js` |
| GSAP (dimension-fade choreography) | 3.12.5 | `lib/gsap.min.js` |
| KaTeX (math typesetting) + fonts | 0.16.9 | `lib/katex/` |

**Why the UMD global build (not ES modules)?** Browsers block ES-module
`import` over the `file://` protocol (CORS, `origin: null`). A classic global
`THREE` build plus a global OrbitControls keeps the file openable by
double-click with no local server. `OrbitControls.global.js` is the official
r149 `jsm` control with its `import … from 'three'` replaced by a destructure of
the global `THREE`.

## How to open

Just open the file:

```
# double-click index.html, or:
xdg-open index.html      # Linux
open index.html          # macOS
start index.html         # Windows
```

No build, no server, no internet needed.
