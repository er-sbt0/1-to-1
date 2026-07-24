# Cantor Space is Compact — a concrete lens on Tychonoff's Theorem

An interactive, single-file web artifact that explains **Tychonoff's theorem**
("an arbitrary product of compact spaces is compact") through its cleanest
concrete instance: **Cantor space** `{0,1}^ℕ`, drawn as an infinite binary tree.

## The idea

Open `index.html` in any browser. You get one interactive binary tree and three
linked activities (tabs on the right):

1. **Explore** — a point of Cantor space is an *infinite path* from the root.
   Each edge fixes one more coordinate to `0` (left) or `1` (right); each node is
   a finite binary *address*. A depth slider grows the tree; drag to pan, scroll
   to zoom.
2. **Open sets** — build a finite prefix and watch the matching **cylinder set**
   (the whole subtree below that node) light up. This is the crux of the *product*
   topology: a basic open set constrains only **finitely many** coordinates and
   leaves infinitely many free.
3. **Compactness** — drop *N* points into the leaves, then run the **pigeonhole
   descent**: with only two children, one child of every node must contain
   infinitely many of the points, so you follow it. The branch you trace forever
   is a **limit point** — a convergent subsequence — which is exactly why the
   product stays compact.

## The math (all statements are correct)

- Cantor space is `{0,1}^ℕ = ∏_{n∈ℕ} {0,1}`, the product of countably many copies
  of the finite discrete 2-point space. Each factor is finite, hence compact.
- **Tychonoff:** if `X_i` is compact for every `i ∈ I`, then `∏ X_i` is compact in
  the product topology. Applying it to the two-point factors gives: Cantor space is
  compact.
- **Product topology basis:** a basic open (cylinder) set fixes a finite prefix
  `s = (s_0,…,s_{k-1})` and leaves the rest free — i.e. the subtree hanging below a
  finite-depth node.
- **Why it works (pigeonhole / König's lemma):** given an infinite sequence of
  points, some child of the root contains infinitely many of them; recurse. The
  infinite path so traced is a limit point / convergent subsequence. For this
  metrizable product, sequential compactness delivers Tychonoff directly. (The
  finite point-cloud in the demo illustrates the "majority child" step; the honest
  statement needs *infinitely* many points, which the panels make explicit.)

## Libraries (all vendored locally in `lib/`, no network at view time)

- **D3.js v7** (`lib/d3.min.js`) — tree layout, links, zoom/pan, transitions.
- **KaTeX 0.16.9** (`lib/katex.min.js`, `lib/katex.min.css`, `lib/fonts/*.woff2`) —
  math typesetting.

## How to open

Just double-click `index.html`, or:

```
xdg-open index.html      # Linux
open index.html          # macOS
```

No build step, no server, no internet connection required.
