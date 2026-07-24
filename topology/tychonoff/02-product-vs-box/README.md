# Product vs. Box Topology — Why Tychonoff Works

An interactive, single-page explainer for **Tychonoff's theorem** built around one contrast:
the **product topology** versus the **box topology** on an infinite product of spaces.

## The idea

A point of a product `∏ Xᵢ` is an infinite tuple `(x₁, x₂, x₃, …)`. A *basic open set* is a
product of open constraints `∏ Uᵢ`, one per coordinate. The two topologies differ on **how many
coordinates you are allowed to constrain**:

- **Product topology:** `Uᵢ = Xᵢ` for **all but finitely many** `i`. You may only pin down
  finitely many coordinates at once.
- **Box topology:** `Uᵢ ⊆ Xᵢ` open in **every** coordinate. You may pin all of them.

That single word — *finitely* — is exactly the hypothesis a finite-subcover argument needs.

## The math (and it is correct)

- **Tychonoff (product topology):** `∏ Xᵢ` is compact **iff** every `Xᵢ` is compact. In particular
  the Cantor space `{0,1}^ℕ` and the Hilbert cube `[0,1]^ℕ` are compact.
- **Why it works:** a basic product-open set fixes only finitely many coordinates, so it always
  contains a whole *cylinder* (infinitely many free coordinates). Open sets are unavoidably "fat,"
  points can never be isolated, and finitely many cylinders cover the space. E.g. `{x₁=0}` and
  `{x₁=1}` already cover `{0,1}^ℕ` with just two open sets.
- **Box topology fails Tychonoff.** In the box topology on `{0,1}^ℕ` every singleton
  `∏ {xᵢ} = {x}` is open, so the space is **discrete**. An infinite discrete space is not compact:
  the open cover by singletons has **no finite subcover** (one set per point, infinitely many).
  Since `{0,1}^ℕ` is a closed subspace of `[0,1]^ℕ` and inherits the discrete (box) topology, the
  box product `[0,1]^ℕ` is not compact either.

## What you can do

1. **Toggle Product ⇄ Box.** The mode colors the whole UI (green = product, red = box).
2. **Build a basic open set.** Drag the two handles on each of the 8 coordinate axes to shrink a
   coordinate to an open interval `Uᵢ`. A live counter tracks how many coordinates are pinned.
3. **The tail.** The "⋯" tail stands for the infinitely many remaining coordinates.
   - In **product** mode the tail is **locked free** — that lock *is* Tychonoff's hypothesis.
   - In **box** mode you can tick "constrain the tail" to pin *every* coordinate at once, collapsing
     the box to a single point.
4. **Animate the open cover.** The compactness panel plays the cover:
   - Product: two fat cylinders blanket `{0,1}^ℕ` → **finite subcover** → ✅ compact.
   - Box: singletons captured one point at a time, counter running to ∞ → **no finite subcover** →
     ❌ not compact.

## Tech / libraries

- **[JSXGraph](https://jsxgraph.org/) 1.10.1** — draggable coordinate axes and interval handles.
- **[KaTeX](https://katex.org/) 0.16.11** — mathematical notation.
- Plain HTML / CSS / JavaScript otherwise; the cover animation is hand-rolled SVG.

All libraries are **vendored locally** in `lib/` (JS, CSS, and the KaTeX `.woff2` fonts). There is
**no build step and no internet access required** at view time.

## How to open

Just open `index.html` in any modern browser:

```
xdg-open index.html      # Linux
open index.html          # macOS
```

Or double-click the file. No server needed.

## Files

```
index.html        the whole artifact
README.md         this file
lib/
  jsxgraph.min.js
  jsxgraph.css
  katex.min.js
  katex.min.css
  fonts/          20 KaTeX .woff2 font files
```
