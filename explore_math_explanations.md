# explore_math_explanations

Turn **any** mathematical statement into N competing, self-contained, interactive visual
explainers — each a different conceptual angle and tool stack, each built by its own subagent,
then compared.

**Input:** one theorem / definition / statement (e.g. *"Tychonoff's theorem"*, *"the Fourier
transform"*, *"Bayes' rule"*, *"the Central Limit Theorem"*).

**Output:** N standalone directories (default N = 5) under `./<topic>/<theorem>/<idx>/`, each
opening offline by double-click, plus a comparison writeup and a rolled-up tech inventory.

**The split:** a **Fable 5 subagent** does the mathematical reasoning — essence, non-trivial
insight, decomposition, and the N concept angles (Phases 0–1), returning a text brief and no
code. You record the plan (Phase 2), then launch **Opus coding subagents one at a time**
(Phase 3), relaying the relevant essence from Fable's brief **inline** into each coder's spec.
Each coder picks its own libraries and builds; none re-derives the math. You compare in Phase 4.

Run end to end with no approval stops.

---

## Phases 0 + 1 — one Fable 5 subagent

Launch with the Agent tool, `subagent_type: "general-purpose"`, `model: "fable"`. Send it this
brief verbatim with the brackets filled. Its returned text drives Phases 2–4.

```
You are a mathematician. Reason from first principles about the statement below and return a
STRUCTURED BRIEF (text only — no files, no code). Downstream, separate coding agents turn your
brief into interactive visual explainers, so your job is to nail the MEANING they must convey,
not how to draw it.

THEOREM: [statement]
AUDIENCE: [level — default: motivated student who has seen the basics of the field]
N: [number of concept angles — default 5]

PHASE 0 — understand it. North star: find the essence. The goal is never to decorate the
formula; it is to convey what the statement *means* and *why it is true*. Answer first: if
someone forgets every symbol but keeps one idea, what is that idea?

PHASE 1 — generate N *general* concepts. Stay at the idea level: the angle and what it
conveys. Library choice and the detailed build belong to the coding agents, not you. Spread
the concepts across different pieces from Phase 0 — a good default: (1) the cleanest concrete
special case, (2) the subtlety / failure mode when a hypothesis is dropped, (3) the core
definition made spatial, (4) the proof animated step by step, (5) the grand metaphor. Adapt to
the theorem; not every one has all five, some have more.

RETURN:
  1. The precise + plain-language statement, and the discipline.
  2. THE ESSENCE (1–2 sentences) — the "aha" that survives once the notation is gone.
  3. THE NON-TRIVIAL INSIGHT — the surprising / counterintuitive / easy-to-get-wrong thing
     that separates real understanding from symbol-pushing.
  4. A decomposition table: `piece | why it's hard | candidate visual metaphor`. Cover the
     objects, hypotheses, claim, proof idea, failure modes, and canonical example.
  5. N concept angles, each: `name · piece it conveys · key interaction/visual (in general
     terms) · zero-padded index + slug` (e.g. `01-cantor-tree`, `02-product-vs-box`), plus the
     exact math facts / proof steps and the honesty caveats that angle depends on, so its
     coder gets correct content.
  6. A coverage check: the facets are genuinely varied AND at least one angle goes straight at
     the essence + non-trivial insight. Varied-but-shallow is a failure.
  7. If two angles would naturally pull the same library, say so, so they can be nudged apart.
```

---

## Phase 2 — record the outline (no approval gate)

Note the plan for the record: essence + non-trivial insight, the concept table, and the
assumptions (audience, N, `<topic>/<theorem>` slugs). This is a checkpoint, not a stop — go
straight to Phase 3.

---

## Phase 3 — build the artifacts, one Opus subagent at a time

`subagent_type: "general-purpose"` on the default/session model — do **not** set
`model: "fable"` here. Launch **sequentially**, waiting for each to finish, so you can tell the
next subagent what the earlier ones built (angle + stack) and keep them distinct.

Write products to `./<topic>/<theorem>/<idx>/` — `<topic>` = discipline slug, `<theorem>` =
statement slug, `<idx>` = zero-padded index + short slug (`topology/tychonoff/01-cantor-tree`).
Create each directory immediately before launching its subagent.

Fill the brackets below from Fable's brief — the coder does not re-derive the math.

```
You are building ONE standalone interactive web artifact that explains [THEOREM] through
[GENERAL CONCEPT ANGLE]. This is one of N approaches that will be compared — make yours
excellent and distinct. [Already built: <prior angles + stacks> — pick a different stack.]

WORKING DIRECTORY (create all files here): <absolute path>/<topic>/<theorem>/<idx>/

THE IDEA: [one-line general framing].
THE ESSENCE (this is the point — everything serves it):
  - [the "aha" that survives once notation is gone]
  - [the NON-TRIVIAL insight to spotlight]
MATHEMATICAL CONTENT (must be correct):
  - [precise statement + the specific facts/proof steps this angle depends on]
  - [the honesty caveats that must be surfaced, not glossed]

YOUR JOB — think from first principles, then implement. The bar is not "a pretty picture of
the formula" — it is: does a viewer walk away understanding the essence and the non-trivial
insight above? If a visual is decorative but doesn't move that needle, cut it.
  1. Choose your libraries from the palette below by what fits THIS scene.
  2. Decide exactly what the mathematical object on screen is — the data structure (a tree of
     paths? a parameterized surface? a swarm of samples? a cover of open sets?).
  3. Build visuals that REFLECT THE ESSENCE — a manipulable object, a before/after contrast, a
     "watch it break" failure mode. Prefer one deep, revealing visual over several shallow ones.
  4. Compute, don't fake — actually compute the subcover, the transform, the eigen-
     decomposition, the limit, so the picture can't lie. Label any metaphor/projection as such,
     and surface caveats (finite-vs-infinite, projection-vs-embedding, discretization) in the UI
     where the user sees them.
  5. Define the interaction contract: what the user drags/toggles/steps, and what updates.
  6. Define the narration: staged steps/panels carrying the argument, with the KaTeX statement
     at each step.

PAGE LAYOUT — fold DOWN, not just out. Lay the explanation out as a vertical scroll of
separated sections, each one clean beat of the argument, so the viewer descends at their own
pace rather than facing one overloaded canvas. Within a section, split LEFT/RIGHT to spotlight
the object next to its explanation (or formula, or the contrasting case).

LIBRARY PALETTE — a MENU, not a mandate. Reason from the scene to the tool; pick the fewest
libraries that do the job and never two that do the same job. Rows overlap by design (2D, 3D,
physics, CAS are each interchangeable) — pick ONE per job and drop the rest. KaTeX plus one
animation lib are fine to reuse across concepts.

  Math / numerical: math.js (expressions, matrices, complex, numerics) · ml-matrix
    (decompositions, solvers) · gl-matrix (fast vec/mat/quat for 3D) · numeric.js (linalg,
    optimization, ODEs) · jStat or simple-statistics (distributions, regression) ·
    fraction.js (exact rationals) · decimal.js (arbitrary precision) · ndarray
  Symbolic / CAS: Nerdamer · Algebrite
  Plotting: D3.js (low-level, relational — trees/graphs/sets/covers/morphs) · Plotly.js
    (scientific: contours, surfaces, 3D) · Observable Plot (concise statistical charts)
  Creative 2D: p5.js (free-form, simulations, particles) · Paper.js (vector paths, geometry) ·
    PixiJS (GPU, thousands of points) · regl (custom shaders)
  3D: Three.js (use a UMD/global build) · Babylon.js
  Physics: Matter.js (2D rigid body) · Planck.js (Box2D-accurate 2D) · cannon-es (3D, pairs
    with Three) · Rapier.js (WASM, vendor the .wasm)
  Animation: GSAP (timeline-based, precise) · Anime.js (light DOM/SVG/CSS)
  Interaction: interact.js (drag, resize, snap) · Hammer.js (touch gestures)
  Graphs: Cytoscape.js (viz + graph algorithms) · Sigma.js + Graphology (large graphs)
  Geometry: flatten-js (points/lines/polygons/intersections) · polygon-clipping (booleans) ·
    Delaunator (Delaunay) · Earcut (triangulation)
  Notation (mandatory for any statement on screen): KaTeX. MathJax only for heavy AMS macros.

  Offline check BEFORE you commit to a lib: it must load from `file://` via a classic <script>
  tag — a plain global/UMD/IIFE build. ESM-only packages (including Three's default
  `three.module.js`) FAIL from file:// (CORS `origin: null`); no importmaps. WASM engines need
  their `.wasm` vendored into lib/, sometimes with an explicit local path. If a lib has no
  usable non-ESM build, pick a neighbor from the same row instead of fighting the loader.

DELIVERY REQUIREMENTS (strict):
  - A single `index.html` that opens directly in a browser with NO build step and NO internet
    at view time. Start with `<!DOCTYPE html>` + `<head>`/`<body>` — KaTeX refuses to render in
    quirks mode.
  - Vendor ALL libraries LOCALLY into `lib/` (curl/wget; include KaTeX's .css and .woff2
    fonts). Relative paths only. Fetch built bundles from a CDN mirror
    (`https://cdn.jsdelivr.net/npm/<pkg>` or unpkg), not a GitHub source tree.
  - For Three.js: UMD/global build (e.g. r149) + a globalized OrbitControls. Confirm classic
    <script> loading.
  - Polished, legible design: good typography, semantic color, responsive layout.
  - A short `README.md`: the idea, the math, the libs you chose and why, how to open.
  - A machine-readable `manifest.json` — this gets mined across concepts to automate artifact
    creation later, so use EXACTLY these keys and be precise and literal, naming real reusable
    units rather than vague prose:
    ```json
    {
      "concept": "<idx-slug, e.g. 02-product-vs-box>",
      "angle": "<one-line: the piece of the math this artifact conveys>",
      "libraries": [
        {"name": "<lib>", "version": "<pinned>", "role": "<its job in THIS scene>",
         "build": "umd|iife|global|esm", "source": "<CDN URL you vendored from>"}
      ],
      "building_blocks": [
        "<reusable COMPONENT a generator would need to emit, e.g. 'scroll-driven section",
        "stepper', 'KaTeX step-caption renderer', 'draggable open-cover overlay',",
        "'live eigen-decomposition', 'before/after failure-mode toggle'>"
      ],
      "computations": ["<real math computed in-page, e.g. 'FFT via math.js', 'subcover search'>"],
      "interactions": ["<what the user drags/toggles/steps and what updates>"],
      "assets": ["<non-lib vendored assets, e.g. 'katex .woff2 fonts', '.wasm'>"],
      "gotchas": ["<offline/file:// or lib pitfalls you hit and how you resolved them>"]
    }
    ```

Work autonomously. Do not ask questions. When done, briefly report what you built and why you
chose your stack.
```

---

## Phase 4 — compare

Present a comparison table (`# | dir | angle | stack | standout interaction | how well it lands
the essence`), call out shared gotchas, note any math-honesty flags, and offer next steps: a
**landing page** linking all N for side-by-side judging, a **scored comparison** after the user
looks, or **polish the strongest one**.

Then **aggregate the tech inventory**: read every concept's `manifest.json` and roll them up
into one `_inventory.json` at `./<topic>/<theorem>/` — the union of libraries (versions, roles,
build types, how many concepts used each), the union of `building_blocks`, and the recurring
`computations` / `interactions` / `gotchas`. Flag what recurs across concepts (candidates to
templatize) versus one-offs. This roll-up is what feeds automating artifact creation.

---

## Quick invocation

> Follow `explore_math_explanations.md` for the statement: **"[THEOREM]"**.
> Use N = [5]. Audience = [level]. Run end to end with no approval stops: launch a **Fable 5**
> subagent for the essence + concepts (Phases 0–1), then **Opus** coding subagents one at a
> time, relaying Fable's essence inline into each, and compare when they're built.
