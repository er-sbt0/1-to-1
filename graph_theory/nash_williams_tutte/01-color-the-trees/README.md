# 01 — Color the Trees

**Two Trees in K₄ — the edge-coloring puzzle.** An interactive explainer for the
**Nash-Williams–Tutte tree-packing theorem** (1961).

Open `index.html` directly in any browser. No build step, no server, no internet.

---

## The idea

Make the object tactile first, then let the failure explain itself.

You paint the edges of a graph with *k* colors and try to make **each color class a
spanning tree**. K₄ with k = 2 works and is perfectly tight (6 = 2·3, zero slack). The
Petersen graph with k = 2 does not work — and the payoff is that the failure comes with a
**one-line certificate**: 15 edges < 2·9 = 18, i.e. the all-singleton partition already
forbids it.

The essence the page is built to deliver:

> A spanning tree must spend exactly r−1 of its edges to weld r clusters of vertices into
> one piece. So k edge-disjoint spanning trees obviously need ≥ k(r−1) edges running
> *between* clusters, for **every** way of clustering. The theorem's miracle is that this
> obvious toll is the **only** toll: if every clustering is stitched together well enough,
> the k trees genuinely exist. **The only possible obstruction to packing k spanning trees
> is some clustering of the vertices that is too sparsely stitched.**

The non-trivial understanding spotlighted: *when a packing is impossible there is always a
short, checkable excuse.* Failure is never mysterious.

## The math

**Theorem (Nash-Williams; Tutte, 1961).** Let *G* = (V, E) be a finite multigraph and
k ≥ 1. *G* contains *k* pairwise edge-disjoint spanning trees **iff** for every partition
𝒫 = {V₁,…,V_r} of V (every r, 1 ≤ r ≤ |V|), the number of edges with endpoints in
different parts satisfies

&nbsp;&nbsp;&nbsp;&nbsp; e_G(𝒫) ≥ k(r − 1).

Facts the page computes rather than asserts:

| graph | n | m | edge-conn. | 2 edge-disjoint spanning trees? |
|---|---|---|---|---|
| K₄ | 4 | 6 | 3 | **yes** — exactly 6 distinct decompositions |
| Petersen | 10 | 15 | 3 | **no** — certificate at r = n: 15 < 18 |
| 3×K₅ joined in a triangle | 15 | 33 | 2 | **no** — certificate at r = 3: 3 < 4 |

* **K₄, k = 2 is tight.** m = 6 = k(n−1) = 2·3. Every edge is used, no color may waste
  one on a cycle. Example packing: T₁ = {12, 23, 34}, T₂ = {13, 14, 24}.
* **Petersen** (outer 5-cycle v₀…v₄, inner pentagram uᵢ–u_{i+2}, spokes vᵢ–uᵢ) has 15
  edges but needs 18. The finest partition (r = 10, every vertex alone) is the whole
  proof. It is also **3-edge-connected**, so it shows that (2k−1)-edge-connectivity is
  **not** sufficient for k = 2. (2k-edge-connectivity *is* sufficient — the standard
  corollary — and Petersen misses it by one.) Petersen does contain a spanning tree plus a
  disjoint spanning *forest* (9 + 6 edges, the forest in 4 pieces); the "Best of 3000
  greedy attempts" button finds one.
* **The honesty section (§06).** Petersen's obstruction is detected by a headcount, which
  is only one partition. Three copies of K₅ joined in a triangle by 3 edges **passes** the
  edge count (33 ≥ 28) and **passes all 16 383 cuts** (min cut 2 ≥ 2), yet fails at the
  intermediate partition r = 3 (3 < 4). The page checks all of that exhaustively, live.
  So "for every partition" is load-bearing; totals and cuts are not enough.
* The page never claims that your failure to find a packing is a proof. The proofs on
  offer are (a) exhaustive enumeration and (b) a partition certificate — and it says which
  is which. Greedy results are labelled "heuristic only".

## What is actually computed in the browser

Nothing on this page is hardcoded arithmetic; every number comes out of a real routine.

* **Union-find (path-compressed, union-by-rank)** per color class → edge count, component
  count, forest test, spanning-tree test, and *which* edge closed a cycle (marked with a
  white dashed overlay on the board).
* **Exhaustive decomposition of K₄:** all C(6,3) = 20 edge-subsets → 16 spanning trees
  (matches Cayley's 4² = 16) → 12 with a spanning-tree complement → **6** distinct
  decompositions, each rendered as a clickable thumbnail that loads onto the board.
* **Exhaustive search on Petersen:** all C(15,9) = 5005 subsets → 2000 spanning trees →
  **0** admit a disjoint second spanning tree.
* **Exhaustive cut enumeration:** all 2^(n−1) − 1 bipartitions (511 for Petersen, 16 383
  for 3×K₅) to get the true edge-connectivity and confirm every r = 2 partition passes.
* **Cross-edge counting** for any user-built partition, plus per-color cross counts — the
  live demonstration that each tree individually pays ≥ r−1.
* **Randomized greedy packing** (shuffled edge orders, first-fit into color classes),
  reported as a heuristic best-effort, never as a proof.
* Convex hull (Andrew monotone chain) to draw cluster blobs; quotient multigraph built by
  grouping cross edges by part-pair and fanning them out as arcs.

## Interaction contract

| where | you can | what updates |
|---|---|---|
| §01 K₄ board | click an edge to cycle unpainted → color 1 → … → unpainted; switch k ∈ {1,2,3}; Clear; Show a solution | ledger (edges/components/forest/spanning-tree per color), cycle-closing edges marked, win banner, §02 budget bar |
| §02 | Enumerate all decompositions | search log + 6 clickable thumbnails that load a real packing onto the K₄ board |
| §03 Petersen board | click edges; Greedy attempt; Best of 3000 greedy attempts; Run exhaustive search | ledger, "dead on arrival" banner with the shortfall, search log |
| §05 partition explorer | click a **vertex** to move it to the next cluster; switch graph K₄ / Petersen; presets: every vertex alone / all in one / split in two | cluster blobs, ghosting of intra-cluster edges, quotient multigraph (colored by tree), r, e_G(𝒫), toll k(r−1), slack, verdict, and per-color "uses ≥ r−1 cross edges ✓ forced" |
| §06 3×K₅ | Check every partition I can afford to check | exhaustive r = n / r = 2 / r = 3 report |

## Libraries

Exactly **one** third-party library. This scene is a hand-laid-out graph with per-edge
click painting and live union-find feedback; the "graph layout" problem is already solved
by hand (K₄ planar, Petersen's canonical pentagon/pentagram, three pentagon blobs), so a
network library like Cytoscape or Sigma would only add weight and fight me for control of
the SVG. D3 would be used for nothing but `select` and `attr`. Everything visual is a few
hundred lines of direct SVG DOM construction, which keeps every edge a plain element I can
recolor, re-weight and hit-test at will.

| lib | version | role | build | source |
|---|---|---|---|---|
| KaTeX | 0.16.11 | render every mathematical statement (mandatory) | UMD global via `<script>` | `https://cdn.jsdelivr.net/npm/katex@0.16.11/dist/` |

Vendored into `lib/`: `katex.min.js`, `katex.min.css`, and all 20 `fonts/KaTeX_*.woff2`.

## Gotchas hit

* **`file://` + ESM.** Anything shipped only as an ES module fails from `file://` (origin
  `null` → CORS). KaTeX's `dist/katex.min.js` is a UMD bundle, so a classic `<script>` tag
  works offline. Verified by loading the page with `file://` in headless Chrome.
* **KaTeX fonts.** `katex.min.css` references `fonts/KaTeX_*.woff2` relatively; without the
  `.woff2` files vendored, math silently falls back to a system font and the spacing goes
  wrong. All 20 faces are included (~300 KB).
* **Quirks mode.** KaTeX misrenders without a doctype, so the file starts with
  `<!DOCTYPE html>`.
* **Reused typed array in the subset enumerator.** `forEachSubset` yields the same
  `Int32Array` buffer each call for speed; anything retained (the 6 K₄ decompositions) is
  copied with `Array.from` first.
* **Degenerate convex hulls.** A singleton cluster's hull is one point; `M x y` alone does
  not paint under a round linecap, so the path is closed as `M x y L x y` to get a dot.
  Two-vertex clusters become capsules for free from the same stroke trick.
* **`performance.now()` under headless virtual time** does not advance, so the timing
  readouts show `0.0 ms` in a screenshot harness; they are real in a normal browser.

## Verification

Checked in headless Chrome from `file://`: zero JS errors, 21 KaTeX blocks rendered with
zero `katex-error` nodes, and every button/edge/vertex driven programmatically with the
outputs inspected (K₄ → 6 decompositions and a verified win state; Petersen → 2000 trees,
0 packings; 3×K₅ → 33 ≥ 28 pass, min cut 2 pass, r = 3 fail).
