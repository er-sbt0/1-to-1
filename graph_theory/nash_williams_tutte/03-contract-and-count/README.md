# 03 · The Contraction Camera

**Nash-Williams–Tutte tree-packing theorem — the necessity direction, made physical.**

Open `index.html` in any browser. No build step, no server, no internet. Everything is vendored into `lib/`.

---

## The idea

Most presentations of Nash-Williams–Tutte state the partition condition

> for every partition `P` of `V` into `r` parts, `e_G(P) ≥ k(r−1)`

as a technical inequality and then get on with the hard proof. This artifact takes the opposite
stance: **the inequality is not an invention, it is a shadow.** If a graph already carries `k`
edge-disjoint spanning trees and you defocus the graph — blur clusters of vertices into single dots —
each tree is still holding the `r` dots together, so it must have kept at least `r−1` of its own
edges. The trees share no edges. The bill adds up to `k(r−1)`. That is the whole necessity proof, and
here you do it with your hands.

The signature interaction is a **camera slider**. Drag vertices into piles; each pile becomes a blob;
then pull the camera back and the blobs physically collapse to supernodes. Interior edges dissolve
(they become loops and are deleted), cross edges survive and **fan apart into visibly parallel
quotient edges** as the contraction proceeds — so the multigraph nature of `G/P` is something you
watch happen rather than something you are told.

## What is on screen, precisely

The mathematical object is `(G, part, colour)` where

- `G` = a fixed edge list plus vertex positions,
- `part : V → ℤ` = the vertex→part map, mutated by dragging/lassoing,
- `colour : E → {0..k−1} ∪ {−1}` = which pre-packed spanning tree owns each edge (`−1` = no tree).

Everything else is recomputed from those three on every single change — nothing is cached and nothing
is hard-coded:

1. **`verifyPacking(G)`** — union–find over each colour class: is it `n−1` edges, acyclic, spanning?
   Section 1 prints the raw trace for K₄.
2. **`analyze(G, part, k)`** — canonicalises the partition, splits `E` into cross edges and loops,
   assigns each cross edge a **fan index** among its parallel class, and then for each colour replays
   its surviving edges through a *second* union–find on the `r` supernodes, recording which edges
   **weld** two components and which are **surplus** (they close a cycle in `G/P`).
3. **`partComps`** — per part, the components of the induced subgraph. If any part has more than one,
   the refinement button appears.
4. **`refinePartition`** — splits every disconnected part into its components.

The ledger reports, per colour: edges kept, the obligation `r−1`, whether it pays, and whether the
colour's image is connected on `G/P` — plus the total against `k(r−1)`.

## The sections

| # | Beat | Graph | The point |
|---|------|-------|-----------|
| 1 | The trees are already there | K₄, k=2 | The packing is verified by union–find in the page, not asserted. |
| 2 | The camera | K₆, k=3 (three Hamiltonian paths, 15 = 3·5 edges) | Drag/lasso a clustering; watch it contract; read the live ledger and the live quotient multigraph. |
| 3 | Why r−1 is forced | K₆, your clustering from §2 | Feed one colour's survivors in one at a time. `r` pieces → 1 piece takes exactly `r−1` welds. The rest are surplus, and they are why a tree's image need not be a tree. |
| 4 | Parts must be connected | Octahedron K₂,₂,₂, k=2 | Three antipodal pairs induce three *disconnected* parts. Refine: `r` 3→6, cross count **unchanged** at 12, requirement 4→10. Set the toll to k=3 and the refined partition becomes a certificate that no 3-packing exists. |
| 5 | Reading the receipt backwards | 3×K₅ + 3 bridges, n=15, m=33 | Three supernodes, three surviving edges, but two trees would owe four. No packing is loaded because none exists — and the page says so rather than faking colours. |
| 6 | What this does not prove | — | The converse (condition ⇒ trees) is the hard half and is **not** proved here. Plus the three easy-to-miss caveats. |

## Mathematical content

- **Statement.** `G` a finite multigraph, `k ≥ 1`. `G` has `k` pairwise edge-disjoint spanning trees
  **iff** for every partition of `V` into `r` nonempty parts (all `1 ≤ r ≤ |V|`), the number of cross
  edges is `≥ k(r−1)`.
- **Necessity (proved here).** Contract each part. `T_i` connected and spanning ⇒ `T_i/P` connected
  and spanning on `r` supernodes (any two supernodes are joined by the image of a `T_i`-path) ⇒
  `|T_i ∩ E(P)| ≥ r−1`. The `T_i` are edge-disjoint, so those sets are pairwise disjoint subsets of
  `E(P)`, hence `e_G(P) ≥ Σ|T_i ∩ E(P)| ≥ k(r−1)`. ∎
- **Contraction details.** Edges inside one part become loops and are **deleted**. Edges between
  distinct parts survive **with multiplicity** — two distinct edges joining the same two parts give
  two parallel quotient edges, so `|E(G/P)| = e_G(P)` exactly. `T_i/P` may contain cycles: it is
  connected and spanning, *not* necessarily a tree, which is why the bound is `≥ r−1`.
- **Connected-parts reduction.** If `A ∈ P` induces components `A_1..A_j` (`j ≥ 2`), replacing `A` by
  them gives `r' = r + j − 1` and `e_G(P') = e_G(P)` (no edge of `G` joins two components of `G[A]`).
  The requirement rises to `k(r'−1) > k(r−1)` at no cost, so the refined partition is a strictly
  stronger test — hence it suffices to check partitions with connected parts.
- **Honesty.** The converse is not proved here. Parallel quotient edges are counted, never merged.
  A tree's image need not be a tree. All three are stated explicitly in §6, and §3's live readout
  shows a colour paying 5 where 2 was owed.

Pre-packed decompositions used (all verified in-page by union–find, none taken on trust):

- K₄ — `T₁ = {12,23,34}`, `T₂ = {13,14,24}`.
- K₆ — three Hamiltonian paths: `{01,12,23,34,45}`, `{13,35,05,02,24}`, `{03,04,14,15,25}` (a perfect
  decomposition: 15 = 3·5, so at `r = n` the ledger sits exactly on the toll with zero slack).
- Octahedron — `T₁ = {04,02,12,13,35}`, `T₂ = {34,03,05,15,25}`, two edges spare.
- 3×K₅ — no packing exists; bridge endpoints are chosen **geometrically** at build time (the vertex of
  each island nearest the target island's centre), not hard-coded.

## Libraries — and why these

| Library | Version | Build | Job in this scene |
|---|---|---|---|
| **Paper.js** (`paper-core`) | 0.12.18 | UMD | Every canvas. Chosen because the scene is a *persistent vector document* that morphs: organic blob hulls (round-join offsets of a convex hull, then `Path.smooth`), quadratic-bezier cross edges whose control offset grows with the contraction parameter to fan parallel edges apart, and per-scope view fitting (`viewToProject`, `view.zoom`) so one logical coordinate space works at any canvas size. A retained-mode vector API is the right tool here; an immediate-mode sketch library (p5) would have meant re-deriving hit geometry every frame, and a physics engine would have added non-determinism to a picture that has to be *exactly* the quotient. `paper-core` rather than `paper-full` because PaperScript is unused. |
| **GSAP** | 3.12.5 | UMD | Time. The camera tween (`t: 0→1`, `power2.inOut`, redrawing on every update) and the weld playback (a monotone tween whose `onUpdate` steps the reveal counter through one colour's surviving edges). Paper does geometry, GSAP does easing — no overlap. |
| **KaTeX** | 0.16.11 | UMD | All notation, with the full set of 20 `.woff2` faces vendored. |

Deliberately *not* used: no charting library (the ledger is plain HTML — bars are two absolutely
positioned divs and a tick, which is more legible at this size than any chart), no graph-layout
library (all positions are computed geometrically and the interesting motion is contraction, not
layout), no drag library (Paper's project coordinates plus pointer events are simpler than adding
interact.js).

Colours are the validated categorical slots 1–3 for a dark surface (`#3987e5`, `#d95926`, `#199e70`),
checked all-pairs against the page surface with the dataviz palette validator. Colour is never the
only channel: each tree also carries a glyph (● ▲ ■) and a stroke style (solid / dashed / dotted).

## Interaction contract

- **Drag a vertex** onto another vertex or into a blob → it joins that part. Dropped in empty space →
  it becomes a singleton part. Vertices **stay where you drop them**, so you can physically pile them.
- **Lasso** on empty canvas → everything enclosed becomes one new part.
- **Double-click** a vertex → set it free.
- **Camera slider / contract / expand** → morphs `t ∈ [0,1]`. Touching the canvas while zoomed out
  returns the camera to 0 first (you edit the graph, not the blur).
- **Presets** per graph, plus *reset layout*.
- **Isolate T₁/T₂/T₃** (§2) → dims every other colour in both the camera and the quotient.
- **Colour + edges-fed-in slider + play weld** (§3) → steps one colour's survivors through union–find.
- **Refine / undo refine** (§4) → splits every disconnected part into components.
- **Toll for k = 1/2/3** (§4) → changes only the requirement, never the loaded trees; the page says so.

Every one of those recomputes the quotient, the per-colour tallies, the ledger, the part chips and the
connectivity report from scratch.

## Files

```
index.html          the whole artifact
lib/paper-core.min.js
lib/gsap.min.js
lib/katex.min.js
lib/katex.min.css
lib/fonts/KaTeX_*.woff2   (all 20 faces)
manifest.json
README.md
```
