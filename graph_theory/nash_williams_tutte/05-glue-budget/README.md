# 05 · The Glue Budget at Every Zoom Level

**Nash-Williams–Tutte as one continuous family of tests, run at every resolution.**
Open `index.html` in any browser. No server, no build step, no network — every library is vendored
into `lib/`, every number is computed in the page from edge lists.

---

## The idea

A spanning tree is **glue**. Squint at a graph until only `r` clusters of vertices are visible: any
one spanning tree must spend exactly `r−1` of its edges running *between* clusters, or the clusters
would not be welded into one connected piece. So `k` edge-disjoint spanning trees owe a toll of
`k(r−1)` crossing edges — **at every possible way of clustering the vertices.**

> **Nash-Williams (1961); Tutte (1961).** A finite multigraph `G = (V,E)` has `k` pairwise
> edge-disjoint spanning trees **iff** for every partition `P` of `V` into `r` nonempty parts,
> `e_G(P) ≥ k(r−1)`.

The miracle is that this obvious toll is the *only* toll. There is no second obstruction. The sole
way tree-packing can fail is for some clustering to be too sparsely stitched together.

### The non-trivial thing this artifact is built to deliver

The two tests everybody already knows are **the two endpoints of one continuum**:

| | condition | reads as |
|---|---|---|
| `r = 2` | `min_{|P|=2} e_G(P) ≥ k` | `G` is `k`-edge-connected |
| `r = n` | `e_G(P) = |E| ≥ k(n−1)` | the raw edge headcount |

Neither suffices. **And neither do both together** — a graph can be perfectly sparse at some
*intermediate* scale while looking healthy at both ends. The whole content of the theorem is that
this one family of tests, run at *every* `r`, is **complete**.

The page proves that claim empirically with four presets that fail in four different places:

| graph | `n` | `|E|` | `λ(G)` | `r=2` (k=2) | `r=n` (k=2) | fails at `r =` | max trees |
|---|---|---|---|---|---|---|---|
| K₆ | 6 | 15 | 5 | ✓ | ✓ | — | 3 |
| K₅ | 5 | 10 | 4 | ✓ | ✓ | — | 2 |
| Q₄ hypercube | 16 | 32 | 4 | ✓ | ✓ | — | 2 |
| **2×K₅ + one bridge** | 10 | 21 | 1 | ✗ | ✓ | **2** (coarse end only) | 1 |
| **Petersen** | 10 | 15 | 3 | ✓ | ✗ | **8, 9, 10** (fine end only) | 1 |
| **Three districts** | 11 | 21 | 2 | ✓ | ✓ | **3, 6, 9** (middle only) | 1 |
| **3×K₅ + 3 bridges** | 15 | 33 | 2 | ✓ | ✓ | **3** (middle only) | 1 |

The last two rows are the point: both famous tests pass, and there are still no two trees.

---

## The centrepiece: one 3-D object, two readings

The **tower** holds every resolution at once, stacked vertically.

- **Level `r`** is the quotient graph after clustering `V` into `r` parts. Cluster nodes are spheres
  with radius `∝ √|part|`; the cross edges of the quotient are drawn between them, parallel edges
  fanned sideways so multiplicity is visible; interior edges have vanished into the nodes.
- **Trunks** connect each cluster to the cluster it merges into one level down — the nesting
  relation. In *minimisers* mode **the trunks disappear**, because the per-`r` optima do not nest.
  That absence is the visual proof of the honesty caveat below.
- **The budget ladder** hangs beside the tower: per level, a bar for `e_G(P)` and a tick for the toll
  `k(r−1)`, with the shortfall hatched in red and a red halo ring around any failing level.
- Because the toll is linear in `r` and the levels are evenly spaced, the toll ticks form a perfectly
  **straight dashed line** while the cross counts form a jagged rising curve. **Viewed edge-on, the
  side face of the tower *is* the `f(r)` vs `k(r−1)` chart.** Viewed from directly above, the same
  object is a graph collapsing into clusters. One object, two readings — the two ways to think about
  the theorem.

Scrolling the sticky section **descends the tower**: page position maps to `r`, from `r = n` at the
top to `r = 2` at the bottom.

---

## What actually runs in the page

Nothing is hard-coded. On load, all seven presets are analysed; every graph/`k`/mode change re-runs
everything.

**Cross counting.** `e_G(P)` counts edges with endpoints in different parts. Loops never cross;
parallel edges count with multiplicity (*Three districts* is a genuine multigraph and exercises this).

**`f(r) = min_{|P|=r} e_G(P)` — exact where possible.**
- `n ≤ 11`: **exhaustive** enumeration of every set partition via restricted-growth strings with
  incremental cross counting. Petersen and 2×K₅ are 115,975 partitions each; *Three districts* is
  678,570. All run in tens of milliseconds and give the exact minimum plus the whole density cloud.
- `n > 11` (3×K₅ at `n=15`, Q₄ at `n=16`): a stated **search returning upper bounds** — 502
  agglomerative passes (average-linkage and heaviest-edge linkage, plus 500 randomised restarts
  alternating between them), then single-vertex-move local search at each `r`, then a downward pass
  merging each level-`(r+1)` solution and re-refining it as a level-`r` candidate. **Endpoints are
  pinned exact**: `r = 2` by Stoer–Wagner minimum cut, `r = n` by counting non-loop edges.

**Monotonicity.** Merging two parts never increases the cross count and drops `r` by one, so
`f(r−1) ≤ f(r)`. The search enforces this by explicit merge repair; the result is verified monotone
on every preset.

**The nested chain** the scrubber walks is deterministic agglomerative clustering: merge the pair
maximising `w/(|A||B|)` (average linkage), ties by larger `w`, then lowest index. It is *one path*,
and where it misses the true minimum the readout says so in words.

**Minimum cut** `λ(G)`: Stoer–Wagner, exact, multiplicities respected.

**The `k` trees**: real matroid union — greedy growth by per-forest union-find, then a breadth-first
exchange search over edges with end-first swap-chain application. The result is **re-verified from
the edge-ownership array alone**: each colour class re-unioned from scratch and checked for exactly
`n−1` edges, no cycle, one component, and pairwise disjointness.

**Maximum packing**: `max k = min_{r≥2} ⌊f(r)/(r−1)⌋`, cross-checked against what `packTrees`
actually achieves for `k = 1, 2, 3` on every preset — agreement everywhere.

### Correctness, tested rather than asserted

- Every routine was validated in Node against brute force before it went in the page.
- The heuristic used on the large graphs is run **in the page, on load**, against exhaustive truth on
  all five enumerable presets and compared at every `r`. §4 prints the result: agreement on every
  `r` of every enumerable preset (912,772 partitions of ground truth in total).
- `argPart[r]` is checked to be a genuine partition into exactly `r` parts whose recounted cross
  total equals the claimed `f(r)`.

---

## Honesty caveats, surfaced in the UI rather than buried

1. **"Every zoom level" is not a chain.** A scrubber implies a linear hierarchy; the theorem
   quantifies over all `B_n` partitions (`B₁₅ = 1,382,958,545`). §4 draws the *cloud* — every
   partition for the enumerable graphs, a stated 40,000-partition sample otherwise — with the true
   lower envelope `f(r)` in blue and the tower's single chain in orange, visibly different lines.
   Toggling the tower to **minimisers** mode drops the trunks entirely, because the optimal partition
   at `r` is generally not a coarsening of the optimal one at `r+1`.
2. **Sufficiency is asserted, not proved.** The necessity half (§1) is proved on the page in three
   lines. The deep half is stated and attributed, and the reader is pointed at the companion artifact
   *04 · certificate duality* for the argument as an algorithm.
3. **Upper bounds prove failure, not success.** For `n > 11` the intermediate `f(r)` are upper
   bounds. A dip below the toll is still a *proof* of failure — it exhibits one violating partition,
   and §1 rules the packing out from that alone. A clean run does not by itself prove success; what
   proves success is the packing in §5 exhibiting the trees. Rows resting on a bound are marked
   `·bound` in the §3 table.
4. **Cloud sampling is stated.** Random restricted-growth labellings concentrate on many parts, so
   half the sample is random edge-contraction chains, which sweep every `r`. Both facts are printed.
5. **Conventions.** Loops never cross; parallel edges count with multiplicity; `r = 1` is vacuous
   (`0 ≥ 0`) and the tower therefore starts at `r = 2`.

---

## Libraries (all vendored, all classic `<script>` globals)

| lib | version | why this one |
|---|---|---|
| **Three.js** | r149 (0.149.0, UMD `build/three.min.js`) | The scene is genuinely spatial: a stack of `n−1` graph drawings, a dendrogram of trunks between them, and a budget ladder in a plane beside it. In 2-D that is either an unreadable isometric tangle or three separate pictures. In 3-D it is one object whose *side face is the chart* and whose *top view is the graph* — which is exactly the claim the artifact is making. Also drives §1's toll widget and §5's tree layers, so one engine covers every spatial scene. |
| **OrbitControls** | from three@0.137.5 `examples/js` (globalised, sets `THREE.OrbitControls`) | Orbit/zoom/damping for all three scenes. r149 deleted `examples/js`, so the last version that still ships a non-module build is vendored; it uses only stable core APIs and runs unchanged against r149. |
| **Chart.js** | 4.4.1 (UMD `dist/chart.umd.js`) | §4's honesty panel is a statistical chart, not a scene: a density cloud of up to 678,570 partitions plus three lines and a status marker, with axes, a legend and tooltips. Different job from Three, so no overlap. D3 / ECharts / Paper.js / Cytoscape are claimed by artifacts 01–04. |
| **KaTeX** | 0.16.11 (UMD) | Mandatory and shared. All notation goes through one `[data-tex]` / `[data-tex-d]` sweep with an idempotence guard; all 20 `.woff2` faces vendored. |

Colours were run through the data-viz validator against this page's dark surfaces (`#141821` and
`#0b0d12`): slots `#3987e5 / #d95926 / #199e70` pass every gate under `--pairs all` in dark mode
(worst CVD ΔE 9.4, worst normal-vision ΔE 20.9, all ≥ 3:1 contrast). Status red `#d03b3b` is
reserved for the shortfall and never used as a series. Every tree also carries a glyph (● ■ ▲) and a
direct label, so identity is never colour-alone.

---

## Interaction contract

**§1 toll widget** — `r` slider (2–8) and `k` slider (1–3) rebuild `r` cluster discs and `k` stars of
`r−1` welding arcs, each tree bowed to its own height; the toll `k(r−1)` is recomputed in text and in
KaTeX. Orbit/zoom.

**§2 the tower** — graph picker (7 presets), `k` picker (1/2/3), levels mode (**nested chain** ↔
**minimisers**), three camera presets (**tower** / **ladder** / **look down**, tweened), a resolution
slider, a **worst resolution** jump, and an at-a-glance strip of one cell per `r` (green/red from the
*true* `f(r)`) that is itself clickable. On wide screens **scrolling the sticky section drives `r`**,
top = `r = n` down to bottom = `r = 2`, and the slider stays in sync with the scroll position.
Every change updates: the 3-D tower (focus level opacity, member dots, interior edges, cluster hulls,
red failure halos), the ladder readout sprite, the meter with its hatched shortfall and toll tick,
the KaTeX inequality, the cluster-size chips, the narration (which is different at `r = n`, `r = 2`
and in between), the "is this the minimum at this `r`?" note, the verdict strip, the §4 chart and the
§5 packing.

**§3 survey** — computed for all 7 presets at the live `k`; each row breaks out the two endpoint
tests, the failing resolutions and the maximum packing. Clicking a row loads that graph into the
tower.

**§4 profile** — toggle the partition cloud, toggle the nested chain, open a table view. Hovering a
cloud point reports how many partitions share that `(r, e(P))`.

**§5 trees** — a **peel apart** slider lifts each tree into its own layer above the wiring
(0 = all coincident, i.e. the graph; 1 = fully separated), plus auto-rotate and orbit. If the current
`k` is impossible, the scene shows the largest packing that *does* exist and names the partition that
rules the rest out.

---

## Files

```
index.html          the whole artifact (single file, ~96 KB)
lib/three.min.js    Three.js r149 (UMD)
lib/OrbitControls.js  globalised OrbitControls
lib/chart.umd.js    Chart.js 4.4.1 (UMD)
lib/katex.min.js    KaTeX 0.16.11 (UMD)
lib/katex.min.css
lib/fonts/*.woff2   all 20 KaTeX faces
```

Verified by loading `file://…/index.html` in headless Chrome **without**
`--allow-file-access-from-files`: zero exceptions, zero console errors, zero failed requests, 74
KaTeX nodes rendered, all four canvases live — across a sweep of all 7 graphs × `k ∈ {1,2,3}` ×
both level modes × every resolution × all three camera presets, plus a scroll pass down the whole
tower track and a 720 px narrow-viewport pass with no horizontal overflow.
