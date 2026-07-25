# 04 · A Stuck Packing Writes Its Own Excuse

**The Nash-Williams–Tutte tree-packing theorem as certificate duality.**
Open `index.html` in any browser. No server, no build step, no network — every library is vendored into `lib/`.

---

## The idea

The theorem is an `iff`, and the two halves are wildly asymmetric.

> **Nash-Williams–Tutte (1961).** A finite multigraph `G = (V,E)` has `k` pairwise edge-disjoint
> spanning trees **iff** for every partition `P` of `V` into `r` nonempty parts,
> `e_G(P) ≥ k(r−1)`.

Necessity is a one-line count: contract each part, each spanning tree becomes a connected spanning
submultigraph of the `r`-vertex quotient and so keeps `≥ r−1` crossing edges; the trees are
edge-disjoint, so the counts add. That is §1 of the page, and it is the whole easy half.

Sufficiency is the theorem. And its real content is not "the trees exist" — it is a **good
characterisation** (a *TONCAS*: The Obvious Necessary Condition Is Also Sufficient):

- either `k` edge-disjoint spanning trees exist (witness A: `k(n−1)` labelled edges), **or**
- a partition with `e_G(P) < k(r−1)` exists (witness B: one integer per vertex),
- **never neither** (the hard direction), **never both** (the easy direction),
- and **either witness is short and independently checkable** in near-linear time.

So the angle of this artifact: run the packing algorithm live, let it fail productively, and watch
the wreckage of the failed search *become* the certificate. The user is never asked to trust the
algorithm — the emitted partition is handed to a verifier that shares no code with the solver and
recounts from scratch.

---

## What actually runs in the page

Nothing here is a canned animation of a pre-scripted failure. On every graph/`k` change the page
runs the real thing and records a full step trace with a state snapshot per step, so the timeline
scrub is an exact pure function of the step index.

**State.** `owner: Int32Array(m)` (which forest each edge is in, `−1` = unused) plus one
path-compressed union-find per forest, rebuilt from `owner`.

**Grow.** Edge `f = uv` may enter `F_i` exactly when `find_i(u) ≠ find_i(v)`.

**Blocked.** If `f` fits in no forest, then for each `i` adding `f` to `F_i` closes exactly one
cycle — the `F_i`-path from `u` to `v`, found by BFS inside the forest — and any single edge on
that path could be evicted to make room.

**Chase.** A breadth-first search over *edges*, seeded with every homeless edge at once. From edge
`f`, for each forest `i` not containing `f`, every edge on the `F_i`-path becomes a labelled
successor. If any reached edge is directly addable to some forest it is not in, the labels unwind
into an augmenting **swap chain**, applied end-first; the packing total goes up by one. Because
BFS yields a *shortest* chain in the exchange digraph, no shortcut exists and the whole chain can
be applied wholesale. After every augmentation the page re-verifies acyclicity from scratch and
**throws** if a cycle ever appeared.

**Death → certificate.** If the search closes with nothing found, let `A` be the reached edge set
and let the parts be the connected components of `(V, A)`. Every reached edge is now interior, so
`e_G(P) ≤ |E| − |A|`. The identity quoted (and explicitly flagged as quoted) on the page,
`Σ|F_i| = (|E| − |A|) + k(n − c(A))` at a maximum packing, turns "stuck" into
`|E| − |A| < k(r−1)` — the shortfall. The page prints both sides of that identity measured on the
live graph and marks whether it holds.

**Degenerate case, handled honestly.** When the packing consumes *every* edge and is still short
(`C₆`, Petersen), there are no homeless edges, the search never wakes, `A = ∅`, and the components
of `(V, ∅)` are the singletons. The general construction degenerates to the all-singletons
partition — i.e. to the crude `m ≥ k(n−1)` headcount test. The page shows this rather than hiding it.

### Correctness, tested rather than asserted

- The solver was stress-tested offline on **3 904 random multigraphs** (`n` 3–8, `k` 1–3) against a
  brute-force minimum over all set partitions. **Zero disagreements**, and in every stuck case the
  emitted certificate was not merely valid but the *minimum-slack* one.
- In-page, §5 re-runs that enumeration live for every preset with `n ≤ 10` (`B₁₀ = 115 975`
  partitions) and reports agree/disagree per row.
- Both witnesses are re-checked by verifiers (`verifyPackingWitness`, `verifyCertificateWitness`)
  that share no code path with `runPacking`.

### Worked numbers (all computed in-page)

| graph | n | m | k | packed | k(n−1) | verdict | certificate |
|---|---|---|---|---|---|---|---|
| K₄ | 4 | 6 | 2 | 6 | 6 | YES | — (tight, zero slack) |
| K₅ | 5 | 10 | 2 | 8 | 8 | YES | — (2 edges spare) |
| K₆ | 6 | 15 | 3 | 15 | 15 | YES | — (uses every edge) |
| C₆ | 6 | 6 | 2 | 6 | 10 | NO | r = 6, 6 < 10 |
| Petersen | 10 | 15 | 2 | 15 | 18 | NO | r = 10, 15 < 18 |
| 2 × K₅ + 1 bridge | 10 | 21 | 2 | 17 | 18 | NO | **r = 2, 1 < 2** |
| 3 × K₅ + 3 bridges | 15 | 33 | 2 | 27 | 28 | NO | **r = 3, 3 < 4** |

The last row is the centrepiece: `m = 33 ≥ 28`, so the crude headcount test passes with room, and
`r = 2` passes too. The graph fails at exactly one clustering, `r = 3` — and the exchange search
finds it without being told where to look.

---

## Honesty caveats surfaced in the UI (not buried)

1. **The exchange lemma is quoted, not proved.** The identity `Σ|F_i| = (|E|−|A|) + k(n−c(A))` at a
   maximum packing is the load-bearing, genuinely intricate step. §3 states it in a boxed callout
   as the one thing on the page taken on faith, and §5 repeats it under "waved".
2. **Greedy failure proves nothing.** A callout in §2 says so plainly: a stuck run is a prompt to
   produce a certificate, not a proof. Only the recount settles it — and the recount alone *is* a
   complete proof of impossibility via §1, whatever route produced the partition.
3. **No matroid machinery.** The generalisation is mentioned in one aside in §5 without naming or
   using any of the apparatus; nothing on the page needs it.
4. **Both witnesses are polynomially checkable** — stated explicitly on the two dichotomy cards
   with their sizes and checking costs, because that is what "good characterisation" means.

---

## Libraries (all vendored, all classic `<script>` globals)

| lib | version | why this one |
|---|---|---|
| **D3.js** | 7.9.0 | The whole scene is a data-join: `m` edges, `n` vertices and a BFS search structure that all change identity on every step. `selection.join` with edge-index keys makes each of the ~150 timeline steps a pure re-render with no manual DOM bookkeeping. Also supplies `d3.polygonHull` (partition blobs), `d3.line` + `curveCatmullRom` (the augmenting-chain overlay), `d3.group` (BFS depth rows) and the table joins. **No `d3.transition` is used** — animation belongs to Anime.js, so the two libraries never overlap. |
| **Anime.js** | 3.2.2 | All choreography: the §3 flood timeline (reached set → blob hardening → cross-edge count-in, four chained tweens over a plain state object whose `update` triggers a D3 re-render), the §1 weld stagger, and the swap flash in §2. Chosen over GSAP (taken by artifact 03) and over `d3.transition` (would double up with D3's job). |
| **KaTeX** | 0.16.11 | Mandatory and shared. Every statement on screen — the theorem, `e_G(P) ≥ k(r−1)`, the necessity chain, the exchange identity and its rearrangement — goes through a single `[data-tex]` / `[data-tex-d]` sweep. All 20 `.woff2` faces are vendored. |

Deliberately *not* used: Cytoscape.js / ECharts / Paper.js / GSAP (taken by artifacts 01–03), and
any force-directed layout — every graph here has a hand-designed geometric layout because the
*shape* of the certificate (three islands, ten singletons) is the point.

---

## Interaction contract

**§1 toll widget** — `r` slider (1–7) and `k` slider (1–3) redraw `r` clusters and `k × (r−1)`
welding arcs, with the toll `k(r−1)` recomputed and narrated.

**§2 the machine** — pick any of 7 graphs and any `k ∈ {1,2,3}` (recomputes the whole trace);
transport bar (restart / step back / play / step forward / jump to end), 5-position speed control,
a scrub slider over every step, and a 6-chapter rail (grow · blocked · search · swap · maximum ·
verdict) that jumps to the first step of a phase and disables chapters that never occur. Every step
updates: the graph (forest colours + dash patterns, reached-set halos, the highlighted `F_i`-cycle
path, the augmenting chain overlay), the per-forest ledger (edges / `n−1`, component count, spend
bar), the total against `k(n−1)`, the growing BFS search structure, the narration and the trace log.
Hovering any edge gives its name, its forest, and whether the search reached it.

**§3 the certificate** — reads §2's live graph and `k`. "Flood the reached set" plays the Anime.js
timeline; the "harden" slider scrubs the same morph by hand; every position re-renders from the
real certificate. The verification checklist, the `e_G(P)` vs `k(r−1)` bar, the per-unit tally and
the printed identity are all recomputed live.

**§4** — the two dichotomy cards light up according to the live verdict; the survey table computes
all 7 presets on load and any row loads that preset back into §2/§3.

**§5** — runs the brute-force partition enumeration on demand and tabulates true-min vs emitted slack.

---

## Files

```
index.html          the whole artifact
lib/d3.min.js       D3 7.9.0 (UMD)
lib/anime.min.js    Anime.js 3.2.2 (UMD)
lib/katex.min.js    KaTeX 0.16.11 (UMD)
lib/katex.min.css
lib/fonts/*.woff2   all 20 KaTeX faces
```

Verified by loading `file://…/index.html` in headless Chrome **without**
`--allow-file-access-from-files`: zero exceptions, zero console errors, 81 KaTeX nodes rendered,
and a full sweep over all 7 graphs × 3 values of `k` × every timeline step with no errors.
