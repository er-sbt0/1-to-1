# 02 — Hidden Resolution

**Three Islands, Three Bridges — sparsity hides at an intermediate resolution.**
An interactive explainer for the **Nash-Williams–Tutte tree-packing theorem** (1961).

Open `index.html` directly in any browser. No build step, no server, no internet.

---

## The idea

The learner's instinct is that *k*-edge-connectivity should be enough to pack *k*
spanning trees. When that breaks, the next instinct is *"then also count the edges."*
This page destroys both by exhibiting a graph that **passes the cut test and passes the
headcount test and still fails** — because the violating partition lives at an
intermediate number of parts.

The essence the page is built to deliver:

> A spanning tree must spend exactly r−1 of its edges to weld r clusters of vertices into
> one piece. So k edge-disjoint spanning trees need at least k(r−1) edges running
> *between* clusters, for **every** way of clustering. The theorem's miracle is that this
> obvious price is the **only** price: the sole possible obstruction to packing k spanning
> trees is some clustering of the vertices that is too sparsely stitched together.

The non-trivial understanding spotlighted: **checking cuts is not enough, checking the
total edge count is not enough, and checking both is not enough.** Sparsity is not a
property of a graph at one scale — it is a property of a graph *at a resolution*, and it
can hide in the middle. The condition genuinely quantifies over all partitions, at every r.

## The math

**Theorem (Nash-Williams; Tutte, 1961).** Let *G* = (V, E) be a finite multigraph and
k ≥ 1. *G* contains *k* pairwise edge-disjoint spanning trees **iff** for every partition
𝒫 = {V₁,…,V_r} of V into r nonempty parts (every r, 1 ≤ r ≤ |V|),

&nbsp;&nbsp;&nbsp;&nbsp; e_G(𝒫) ≥ k(r − 1),

where e_G(𝒫) counts the edges whose endpoints lie in different parts.

* **r = 2** ⇔ every cut has ≥ k edges ⇔ *G* is k-edge-connected.
* **r = n** (all singletons) ⇔ |E| ≥ k(n − 1).

### Counterexample A — the warm-up (§02)

**C₆ with k = 2.** Every one of its 31 cuts has ≥ 2 edges, so C₆ *is* 2-edge-connected
and passes the r = 2 test. Partition it into three arcs of two vertices: r = 3, cross
edges = 3 < k(r−1) = 4. The page brute-forces **all Bell(6) = 203 partitions** and prints
the exact minimum at every r.

Honest caveat surfaced in the page: **C₆ also fails the headcount** (6 < 2·5 = 10), so it
only refutes "cuts are enough", not "cuts and count together are enough".

### Counterexample B — the punchline (§03–§04)

**Three disjoint copies of K₅ joined pairwise by a single edge** — a triangle of islands.
n = 15, m = 3·10 + 3 = 33. For k = 2:

| test | resolution | computed | required | verdict |
|---|---|---|---|---|
| cuts | r = 2 | min cut **2** (all 16 383 bipartitions enumerated) | 2 | **passes**, exactly |
| headcount | r = n = 15 | **33** edges | 2·14 = 28 | **passes**, +5 slack |
| the islands | r = 3 | **3** cross edges | 2·2 = 4 | **FAILS** |

So no two edge-disjoint spanning trees exist. The obstruction is invisible at r = 2 and
invisible at r = 15; it lives only at r = 3, and it is the *only* r that is short.

### The positive result (§06)

**Corollary.** Every 2k-edge-connected graph has k edge-disjoint spanning trees.

*Proof (double counting).* Fix a partition into r parts. Each part V_i is one side of a
cut, so |δ(V_i)| ≥ 2k. Summing over parts counts each crossing edge exactly twice, so
e_G(𝒫) = ½ Σ|δ(V_i)| ≥ ½·2k·r = kr > k(r−1). Every partition passes, so the hard
direction of the theorem produces the k trees. ∎

The page makes this live: a **bridges-per-pair toggle** thickens each island pair from 1
to 2 bridges. The minimum cut rises 2 → 4 = 2k, the corollary's hypothesis is met, the
whole resolution scan lifts above the toll line, and a randomised first-fit packer really
does find two edge-disjoint spanning trees (28/28 tree edges), painted on the graph.

### Sharpness and honesty caveats (surfaced in the page, not hidden)

* **(2k−1)-edge-connectivity does not suffice for k ≥ 2.** The Petersen graph is
  3-edge-connected but has 15 < 18 = 2·9 edges. The page brute-forces **all
  Bell(10) = 115 975 partitions** of Petersen — and the first resolution that comes up
  short is **r = 8**, two resolutions before the headcount at r = 10 notices anything.
  A second, independent instance of the obstruction hiding away from r = 2.
* **No contradiction with the corollary.** The one-bridge island graph is
  **2**-edge-connected while the corollary asks for **4**. It misses the hypothesis by a
  factor of two, so it never contradicted the corollary — it refutes only the weaker
  instinct that *k*-edge-connectivity is enough. §06 states this where both facts are on
  screen together.
* **The corollary is not tight at k = 1**: it would demand 2-edge-connectivity, but plain
  connectivity already yields one spanning tree. The factor 2 is the price of a uniform
  statement.
* **Passing r = 2 and r = n together does not suffice** — that is the entire point of the
  island graph.

### What was actually searched (§05 says this on the page too)

* **Minimum cuts: exhaustive.** All 2ⁿ⁻¹−1 bipartitions (16 383 for n = 15, 511 for
  Petersen, 31 for C₆).
* **Minimum cross edges at each r: exact, by branch and bound.** Bell(15) = 1 382 958 545
  partitions is out of reach one-by-one, so the search is a DFS over *restricted-growth
  strings* (canonical, so part-label symmetry costs nothing) with the running cross-edge
  count as a monotone lower bound: any branch already at or above the incumbent is pruned
  with no loss. It is seeded with a randomised local-search optimum **used only as a
  pruning bound, never reported as the answer**. The page prints the surviving DFS node
  count per r and whether any node budget was hit (it is not — every branch resolves, so
  every bar is a proved exact minimum).
* **C₆ and Petersen: complete brute force** over every partition (203 and 115 975), no
  pruning at all — a cross-check on the branch-and-bound.
* **The greedy packer is explicitly labelled a heuristic**, never evidence of
  impossibility. The r = 3 certificate is what proves impossibility, and exhibiting one
  bad partition is a complete proof of the "no packing" direction.

## The interactive object

The mathematical object on screen is a triple:

* a **graph** `{n, edges:[[u,v]], pos, labels}` with a hand-authored layout,
* a **current partition**, a vertex → part map (`Int32Array`, normalised to canonical
  0,1,2,… labels on every change),
* the pair **(e_G(𝒫), k(r−1))** — cross-edge count versus the toll.

Everything drawn is a rendering of that triple. Cluster identity is carried by a
**labelled box, never by colour**, so the whole colour budget goes to the one thing that
matters: which edges cross, and whether they pay the toll.

**Interaction contract**

| you do | what updates |
|---|---|
| click a vertex | it moves to the next cluster (a new one is created past the last); boxes, cross edges, quotient, meter, stats and verdict all recompute |
| click a bar in the resolution scan | the exact minimising clustering for that r is loaded into the graph |
| change k (1 / 2 / 3) | the toll line and the meter move; the bars do not (they are properties of the graph) |
| toggle bridges per pair (1 / 2) | the graph is rebuilt, the min cut and per-part δ tally recompute, the comparison scan redraws |
| run the packer | 6000 randomised first-fit restarts; the best packing is painted onto the graph in two colours |
| presets | jump to the min cut, the three islands, all-singletons, all-in-one |

## Libraries (all vendored into `lib/`, loaded by classic `<script>`)

| library | version | why this one |
|---|---|---|
| **Cytoscape.js** | 3.30.2 | The scene *is* a graph with a live clustering. Cytoscape's **compound parent nodes** are exactly a partition — re-clustering is `node.move({parent})` and the boxes resize themselves — plus per-edge classes for cross/internal and hit-testing for click-to-recluster. Used for the four clustered graph views and the contracted quotient multigraph (parallel edges via `curve-style: bezier`). |
| **Apache ECharts** | 5.5.1 | The resolution scan needs two stacked grids on one shared category axis (bars + toll line above, diverging slack below), per-item status colours, mark-point annotations, a linked crosshair and click-to-select — all first-class in ECharts and all needed. Also drives the live toll meters. |
| **KaTeX** | 0.16.11 | Every statement on screen: the iff-theorem, the contraction/toll chain, the r = 2 and r = n specialisations, the corollary and its double-counting line. |

Deliberately *not* used: no second charting library, no second graph library, no layout
engine (all layouts are hand-authored `preset` positions so the geometry is stable and
meaningful).

Note on the palette: colours were validated with the dataviz skill's checker against this
page's chart surface `#161c28` — the two-series categorical pair (`#3987e5` / `#d95926`)
passes every gate in dark mode. Status colours (`#0ca30c` / `#fab219` / `#d03b3b`) are the
reserved status palette and always ship with a glyph (✓ / = / ✕) and a written verdict, so
state is never carried by colour alone.

## How to open

```
open index.html          # macOS
xdg-open index.html      # Linux
```

Or drag the file into a browser tab. Everything — minimum cuts, exact minimum cross-edge
counts, greedy packings, elapsed times — is computed in your browser at load time.
