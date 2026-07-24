# explore_math_explanations

A repeatable procedure for turning **any** mathematical theorem or statement — in any
discipline — into a set of competing, self-contained, interactive visual explainers, each
built from first principles by its own subagent, then compared.

**Input:** one theorem / definition / statement (e.g. *"Tychonoff's theorem"*, *"the
Fourier transform"*, *"Bayes' rule"*, *"eigenvectors"*, *"the Central Limit Theorem"*).

**Output:** N standalone directories (default N = 5) under `./<topic>/<theorem>/<idx>/`, each
a different conceptual angle + tool stack, each opening offline by double-click, plus a
comparison writeup.

The orchestrating agent (you) does the light-touch work inline: understand the math (Phase 0)
and generate the **general** concept ideas (Phase 1), record the plan (Phase 2), then
**immediately** launch subagents **one at a time** (Phase 3) — each subagent chooses its
own libraries, designs the build from first principles, and implements the artifact. You
compare the artifacts in Phase 4.

---

## Phase 0 — Understand the statement from first principles

Before any ideas, extract the math. Do not skip this — the quality of every downstream
visual depends on it.

**North star: find the essence.** The goal is never to decorate the formula — it is to
convey what the theorem *means* and *why it is true*, and to spotlight the one non-trivial
insight a learner would not have guessed. Before anything else, answer in a sentence: *if
someone forgets every symbol but keeps one idea from this, what is that idea?* Everything
downstream — concepts, visuals, artifacts — serves that essence.

1. **State it precisely.** Write the theorem formally (with quantifiers/hypotheses) and in
   one plain-language sentence. Note the discipline (topology, analysis, probability, linear
   algebra, number theory, …).
2. **Name the essence.** In one or two sentences, state the deep idea the theorem is really
   about — the "aha" that survives once the notation is gone. Then name the **non-trivial
   understanding**: the thing that is surprising, counterintuitive, or easy to get wrong,
   that separates real understanding from symbol-pushing. This is what the artifacts must
   make land.
3. **Decompose into conveyable pieces.** List the sub-concepts a learner must grasp: the
   objects, the hypotheses, the claim, the mechanism/proof idea, the failure modes (what
   breaks if a hypothesis is dropped), and the canonical example.
4. **Identify the intuition gaps.** For each piece, name *what is hard to picture* and the
   visual metaphor that would resolve it. This list is the seed for the concepts in Phase 1.
5. **Pick the audience level** (default: motivated student who has seen the basics of the
   field). One concept may go deeper for the proof-curious.

Produce a short table: `piece | why it's hard | candidate visual metaphor`, and carry the
**essence + non-trivial insight** forward — every concept in Phase 1 should be judged by how
well it delivers them.

---

## Phase 1 — Generate diverse concept ideas

Produce N (default 5) **distinct** *general* concepts. Stay at the idea level — the angle and
what it conveys. The actual library choice and the detailed build are the subagent's job
(Phase 3); here you only spread the ideas so the final comparison is meaningful.

- **Facet axis** — cover different pieces from Phase 0. A good default spread:
  1. *Concrete example* — the cleanest special case, made tangible.
  2. *The subtlety / failure mode* — what breaks if a hypothesis is dropped (contrast).
  3. *The definition* — the core definition made spatial/interactive.
  4. *The proof* — the actual argument, animated step by step.
  5. *The grand metaphor* — the "wow" spatial or dynamic intuition.
  (Adapt these to the theorem; not every theorem has all five, and some have more.)
- **Tool diversity is a goal, not a decision you make here.** Each subagent picks its own
  stack — but if two ideas would naturally pull the same library, note it so you can nudge
  them apart in the brief (or tell the later subagent what the earlier one already used).

For each concept, write: **name · the piece it conveys · the key interaction/visual (in
general terms) · a zero-padded index with a short descriptive slug** (`01-cantor-tree`,
`02-product-vs-box`, … ordered — the slug lets you tell concepts apart without opening them).

Run a **coverage check**: confirm the facets are genuinely varied — *and* that at least one
concept goes straight at the **essence + non-trivial insight** from Phase 0, rather than every
concept only illustrating mechanics. A varied-but-shallow spread is a failure.

---

## Phase 2 — Record the outline (no approval gate)

Note the plan for the record: the Phase 1 concept table + the assumptions (delivery target,
audience, N, and the `<topic>/<theorem>` slugs). **Do not wait for approval** — this is a
checkpoint, not a stop. Proceed directly to Phase 3 and start launching subagents.

---

## Phase 3 — Build the artifacts, one subagent at a time

Launch subagents **sequentially — one at a time, waiting for each to finish before starting
the next** (not in parallel). Running them in sequence is deliberate: you can tell each new
subagent what the previous ones built (angle + stack) so it stays distinct.

Give each subagent only the **general** idea plus the offline delivery requirements. The
subagent decides everything else from first principles — which libraries fit the scene, what
the object on screen is, how the interaction works — and then implements it.

**Directory layout** — write products to `./<topic>/<theorem>/<idx>/`:
```
mkdir -p ./<topic>/<theorem>/<idx>
# e.g. topology/tychonoff/01-cantor-tree
#      calculus/integral_fundamental_theorem/01-riemann-sum
```
`<topic>` = discipline slug, `<theorem>` = statement slug, `<idx>` = zero-padded concept
index plus a short descriptive slug (e.g. `01-cantor-tree`). Create the directory for a
concept immediately before launching its subagent.

**Subagent spec template** (fill the brackets per concept):
```
You are building ONE standalone interactive web artifact that explains [THEOREM] through
[GENERAL CONCEPT ANGLE]. This is one of N approaches that will be compared — make yours
excellent and distinct. [Already built: <prior angles + stacks> — pick a different stack.]

WORKING DIRECTORY (create all files here): <absolute path>/<topic>/<theorem>/<idx>/

THE IDEA: [one-line general framing from Phase 1].
THE ESSENCE (this is the point — everything serves it):
  - [the deep idea the theorem is really about — the "aha" that survives once notation is gone]
  - [the NON-TRIVIAL understanding to spotlight — the surprising / counterintuitive / easy-to-
    get-wrong thing that separates real understanding from symbol-pushing]
MATHEMATICAL CONTENT (must be correct):
  - [precise statement + the specific facts/proof steps this angle depends on]
  - [the honesty caveats that must be surfaced, not glossed]

YOUR JOB — think from first principles, then implement. The bar is not "a pretty picture of
the formula" — it is: does a viewer walk away *understanding the essence and the non-trivial
insight above*? If a visual is decorative but doesn't move that needle, cut it.
  1. Choose your libraries. Consider the palette below and pick what fits THIS scene; don't
     run four libraries that do the same job.
  2. Decide what the mathematical object on screen is, exactly — the data structure (a tree
     of paths? a parameterized surface? a swarm of samples? a cover of open sets?).
  3. Construct visualizations and artifacts that REFLECT THE ESSENCE — each one earns its
     place by making the meaning or the non-trivial insight tangible (a manipulable object, a
     before/after contrast, a "watch it break" failure mode). Prefer one deep, revealing
     visual over several shallow ones.
  4. Prefer honest computation over faked visuals — actually compute the subcover, the
     transform, the eigen-decomposition, the limit. Label any metaphor/projection as such.
  5. Define the interaction contract: what the user can drag/toggle/step, and what updates.
  6. Define the narration: the staged steps/panels that carry the argument, with the KaTeX
     statement at each step.
  Then build it.

PAGE LAYOUT (structure the understanding, don't cram it into one screen):
  - Don't only fold OUT (expanding a single dense view). Fold DOWN: lay the explanation out as
    a vertical scroll of separated sections, each section one clean beat of the argument, so
    the viewer descends through the idea at their own pace.
  - Within a section, you may split LEFT/RIGHT to spotlight different parts side by side — e.g.
    the object/visual on one side and the corresponding explanation (or the formula, or the
    contrasting case) on the other, so what the text is talking about is visible next to it.
  - Keep each section focused on a single point; let scroll (and, where it fits, scroll-driven
    progression) carry the narrative rather than piling every control onto one busy canvas.

LIBRARY PALETTE (a MENU, not a mandate — reason from the scene to the tool, don't reach for
the first row). Pick the fewest libraries that do the job; never run two that do the same job.

  Math / numerical:
    - math.js — expressions, matrices, complex, units, numerics · https://github.com/josdejong/mathjs
    - decimal.js — arbitrary-precision decimals · https://github.com/MikeMcl/decimal.js
    - big.js — small arbitrary-precision decimals · https://github.com/MikeMcl/big.js
    - fraction.js — exact rationals/fractions · https://github.com/rawify/Fraction.js
    - ml-matrix — matrix ops, decompositions, solvers · https://github.com/mljs/matrix
    - ndarray — n-dimensional arrays · https://github.com/scijs/ndarray
    - gl-matrix — fast vec/mat/quat for 3D · https://github.com/toji/gl-matrix
    - numeric.js — linalg, optimization, interpolation, ODEs · https://github.com/sloisel/numeric
    - jStat — distributions & statistics · https://github.com/jstat/jstat
    - simple-statistics — descriptive stats, regression, clustering · https://github.com/simple-statistics/simple-statistics
  Symbolic / CAS:
    - Nerdamer — symbolic algebra, calculus, solving · https://github.com/jiggzson/nerdamer
    - Algebrite — CAS: symbolic calc/algebra, exact · https://github.com/davidedc/Algebrite
    - Cortex Compute Engine — MathJSON symbolic engine · https://github.com/cortex-js/compute-engine
  Visualization / plotting:
    - D3.js — low-level data-driven, relational (trees/graphs/sets/covers/morphs) · https://github.com/d3/d3
    - Plotly.js — scientific: lines, scatter, contours, surfaces, 3D · https://github.com/plotly/plotly.js
    - Observable Plot — concise high-level statistical charts · https://github.com/observablehq/plot
    - Vega / Vega-Lite — declarative visualization grammar · https://github.com/vega/vega · https://github.com/vega/vega-lite
    - Apache ECharts — feature-rich interactive charts · https://github.com/apache/echarts
    - Chart.js — simple responsive charts · https://github.com/chartjs/Chart.js
  Interactive / creative graphics:
    - p5.js — creative coding, free-form 2D, simulations, particles · https://github.com/processing/p5.js
    - PixiJS — GPU 2D at scale (sprites, thousands of points) · https://github.com/pixijs/pixijs
    - Paper.js — vector paths, curves, geometry · https://github.com/paperjs/paper.js
    - Two.js — lightweight animated 2D shapes (SVG/Canvas/WebGL) · https://github.com/jonobr1/two.js
    - regl — functional WebGL, custom GPU/shader rendering · https://github.com/regl-project/regl
  3D / WebGL / WebGPU:
    - Three.js — general 3D: scenes, meshes, cameras, shaders (use UMD/global build) · https://github.com/mrdoob/three.js
    - Babylon.js — full WebGL/WebGPU engine · https://github.com/BabylonJS/Babylon.js
    - PlayCanvas Engine — web 3D/game engine · https://github.com/playcanvas/engine
    - three-mesh-bvh — fast Three.js raycast/collision/geometry · https://github.com/gkjohnson/three-mesh-bvh
  Physics / dynamics:
    - Matter.js — accessible 2D rigid-body · https://github.com/liabru/matter-js
    - Rapier.js — WASM 2D/3D rigid-body (vendor the .wasm) · https://github.com/dimforge/rapier.js
    - cannon-es — light 3D rigid-body, pairs with Three · https://github.com/pmndrs/cannon-es
    - Planck.js — Box2D-style accurate 2D · https://github.com/piqnt/planck.js
    - Ammo.js — Bullet port, 3D (WASM) · https://github.com/kripken/ammo.js
    - Oimo.js — lightweight 3D rigid-body · https://github.com/lo-th/Oimo.js
  Animation / choreography (step-by-step narration):
    - GSAP — timeline-based, precise synchronized · https://github.com/greensock/GSAP
    - Anime.js — light DOM/SVG/CSS/JS values · https://github.com/juliangarnier/anime
    - Motion — modern UI/DOM/gesture/layout · https://github.com/motiondivision/motion
    - Popmotion — springs, inertia, interpolation · https://github.com/Popmotion/popmotion
  Interaction (drag/toggle/step controls):
    - interact.js — drag, resize, gesture, snap · https://github.com/taye/interact.js
    - Moveable — drag/resize/rotate/scale handles · https://github.com/daybrush/moveable
    - dnd-kit — drag-and-drop toolkit (React) · https://github.com/clauderic/dnd-kit
    - Hammer.js — touch gestures (pinch/swipe/pan/rotate) · https://github.com/hammerjs/hammer.js
  Graphs / networks:
    - Cytoscape.js — graph viz + graph algorithms · https://github.com/cytoscape/cytoscape.js
    - Sigma.js — GPU renderer for large graphs · https://github.com/jacomyal/sigma.js
    - Graphology — graph data structures/algorithms (pairs with Sigma) · https://github.com/graphology/graphology
    - vis-network — node-edge diagrams, physics layouts · https://github.com/visjs/vis-network
  Computational geometry:
    - flatten-js — 2D geometry: points, lines, circles, polygons, intersections · https://github.com/alexbol99/flatten-js
    - polygon-clipping — polygon boolean ops (union/intersect/diff/xor) · https://github.com/mfogel/polygon-clipping
    - Delaunator — fast 2D Delaunay triangulation · https://github.com/mapbox/delaunator
    - Earcut — fast polygon triangulation · https://github.com/mapbox/earcut
  Notation (mandatory for any statement on screen): KaTeX — fast, sync, no runtime network
    dep (default); MathJax only if you need heavy AMS macros.

  Overlap rule: many rows overlap (D3 / p5 / Paper / Two all cover 2D; Three / Babylon /
  PlayCanvas all cover 3D; the physics and CAS rows are interchangeable). Pick ONE per job by
  scene type and drop the rest. KaTeX and one animation lib (e.g. GSAP) are fine to reuse as
  shared supporting libs across concepts.

  Offline check BEFORE you commit to a lib: it must load from `file://` via a classic
  <script> tag — a plain global/UMD/IIFE build. ESM-only packages (Cortex, Motion, dnd-kit,
  three-mesh-bvh, and Three's default `three.module.js`) FAIL from file:// unless you use their
  UMD/global build. WASM engines (Rapier, Ammo) need their `.wasm` vendored into lib/ too, and
  some resolve it via a path you must point at the local file. If a lib has no usable non-ESM
  build, pick a neighbor from the same row instead of fighting the loader.

DELIVERY REQUIREMENTS (strict):
  - A single `index.html` that opens directly in a browser with NO build step and NO
    internet at view time. Start with `<!DOCTYPE html>` + `<head>`/`<body>` (KaTeX refuses
    to render in quirks mode).
  - Vendor ALL libraries LOCALLY into a `lib/` subfolder (download with curl/wget; include
    KaTeX's .woff2 fonts and .css). Reference with relative paths only. The palette URLs are
    GitHub repos — fetch the actual built bundle from a CDN mirror (jsDelivr
    `https://cdn.jsdelivr.net/npm/<pkg>` or unpkg), not the repo source tree.
  - For Three.js: use a UMD/global build (e.g. r149) + a globalized OrbitControls — ES-module
    + importmap FAILS from file:// (CORS origin: null). Confirm classic <script> loading.
  - Polished, legible design: good typography, semantic color, responsive layout.
  - Add a short `README.md`: the idea, the math, the libs you chose (and why), and how to open.

Work autonomously. Do not ask questions. When done, briefly report what you built and why you
chose your stack.
```

Use the `general-purpose` subagent type. Tell each agent it is one of N so it optimizes for
being distinct.

---

## Phase 4 — Compare

When all artifacts are built, present a comparison table
(`# | dir | angle | stack | standout interaction | how well it lands the essence`), call out
shared gotchas the agents hit, note any math-honesty flags surfaced, and offer next steps: a **landing page**
linking all N for side-by-side judging, a **scored comparison** after the user looks, or
**polish the strongest one**.

---

## Hard-won lessons (bake these into every subagent brief)

- **Essence over decoration.** The win condition is a viewer *understanding the deep idea and
  the non-trivial insight* — not a tidy animation of the formula. Every visual, artifact, and
  interaction must earn its place by moving that needle; cut the merely pretty.
- **Fold down, not just out.** A vertical scroll of separated, single-point sections (with
  left/right spotlighting inside a section) beats one overloaded canvas. Let the descent carry
  the argument.
- **`file://` blocks ES modules** (CORS `origin: null`). Use UMD/global script tags for
  Three.js and anything else that ships as ESM. No importmaps to CDN or local modules.
- **KaTeX needs standards mode** — always emit `<!DOCTYPE html>`, `<head>`, `<body>`.
- **Vendor everything locally.** "Self-contained" means no external asset references — all
  JS/CSS/fonts live under `lib/` and are referenced by relative path.
- **Compute, don't fake.** If the visual claims a subcover exists / a transform converges /
  a matrix diagonalizes, compute it in code so the picture can't lie.
- **Surface the caveat in the UI.** Finite-vs-infinite, projection-vs-embedding, and
  discrete approximations must be labeled where the user sees them.
- **Diversify on purpose.** Different facet *and* different tool per concept — that is what
  makes the comparison worth running. Sequential launches let you enforce this by telling
  each subagent what the earlier ones already used.

---

## Quick invocation

> Follow `explore_math_explanations.md` for the statement: **"[THEOREM]"**.
> Use N = [5]. Delivery = standalone offline dirs under `./<topic>/<theorem>/<idx>/`.
> Audience = [level]. Run the whole procedure end to end with no approval stops: outline the
> general concepts, then spin up subagents automatically (one at a time), and compare the
> artifacts when they're built.
