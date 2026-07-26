# explore_math_explanations

Turn **any** mathematical statement into N competing, self-contained, interactive visual
explainers — each taking a different deep angle and a different visual approach to the same
meaning, each built by its own subagent, then compared.

**Input:** one theorem / definition / statement (e.g. *"Tychonoff's theorem"*, *"the Fourier
transform"*, *"Bayes' rule"*, *"the Central Limit Theorem"*).

**Output:** N standalone directories under `./<topic>/<theorem>/<idx>/`, each opening offline by
double-click, plus a comparison writeup and a rolled-up tech inventory.

| arg | values | default | meaning |
|---|---|---|---|
| `math-model` | `fable` \| `opus` | `fable` | the `model:` of the **reasoning** subagent (Phases 0–1) only — coding subagents are always Opus, never `fable` |
| `N` | integer | `2` | how many competing explainers to build |
| `audience` | free text | motivated student who has seen the basics of the field | level to write for |

**The split:** one **reasoning subagent** returns a text brief — essence, non-trivial insight,
decomposition, N concept angles — and no code (Phases 0–1). You record it (Phase 2), then launch
**Opus coding subagents one at a time** (Phase 3), relaying the relevant essence from that brief
**inline** into each coder's spec; each coder picks its own libraries and builds, and none
re-derives the math. You compare in Phase 4.

Every artifact carries the whole arc — statement, meaning, proof idea, why it isn't obvious, the
boundary, an honest ledger, visuals that carry all of it. The N differ in **which deep angle**
they take and **how they use visuals**, never in whether they cover the arc. Full spec: the
`WHAT THE PAGE MUST DELIVER` block in Phase 3.

Run end to end with no approval stops.

---

## Phases 0 + 1 — one reasoning subagent

Launch with the Agent tool, `subagent_type: "general-purpose"`, `model` set from `math-model`.
Send this brief verbatim with the brackets filled. Its returned text drives Phases 2–4.

```
You are a mathematician. Reason from first principles about the statement below and return a
STRUCTURED BRIEF (text only — no files, no code). Downstream, separate coding agents turn your
brief into interactive visual explainers, so your job is to nail the MEANING they must convey,
not how to draw it.

THEOREM: [statement]
AUDIENCE: [level — default: motivated student who has seen the basics of the field]
N: [number of concept angles — default 2]

PHASE 0 — understand it. North star: find the essence. The goal is never to decorate the
formula; it is to convey what the statement *means* and *why it is true*. Answer first: if
someone forgets every symbol but keeps one idea, what is that idea?

PHASE 1 — generate N *general* concepts. Every concept will explain the WHOLE theorem —
statement, meaning, and proof idea — so they do not divide that work between them. What differs
is (a) the DEEP ANGLE each takes into the meaning, and (b) the VISUAL APPROACH it uses to make
that meaning tangible. Stay at the idea level; library choice and the build belong to the
coding agents.

  Angles worth considering, adapted to this theorem: the cleanest concrete case made tangible ·
  what breaks when a hypothesis is dropped · the core definition made spatial · the proof
  argument stepped through · the spatial or dynamic metaphor that makes it obvious.

  Visual approaches worth considering: a manipulable object the viewer rebuilds · a side-by-side
  contrast where one side fails · a spatial metaphor made literal · a live computation
  converging. The visual must correspond to the math it conveys — not decoration around it.

RETURN:
  1. The precise + plain-language statement, and the discipline.
  2. THE ESSENCE (1–2 sentences) — the "aha" that survives once the notation is gone.
  3. THE NON-TRIVIAL INSIGHT — the surprising / counterintuitive / easy-to-get-wrong thing
     that separates real understanding from symbol-pushing.
  4. THE PROOF IDEA in 2–4 beats — why it is true, in a form every artifact can carry.
  5. A decomposition table: `piece | why it's hard | candidate visual metaphor`. Cover the
     objects, hypotheses, claim, proof idea, failure modes, and canonical example.
  6. N concept angles, each: `name · the deep angle it takes · its visual approach · zero-padded
     index + slug` (e.g. `01-cantor-tree`, `02-product-vs-box`), plus the exact math facts /
     proof steps and the honesty caveats that angle depends on, so its coder gets correct
     content.
  7. A coverage check: the angles are genuinely distinct in BOTH deep angle and visual approach,
     and each one can carry the statement + meaning + proof idea on its own. Two concepts
     landing on the same visual approach is a miss — say so. Varied-but-shallow is a failure.
  8. If two angles would naturally pull the same library, say so, so they can be nudged apart.
```

---

## Phase 2 — record the outline (no approval gate)

Note for the record: essence + non-trivial insight, the concept table, and the assumptions
(`math-model`, audience, N, `<topic>/<theorem>` slugs). A checkpoint, not a stop — go to Phase 3.

---

## Phase 3 — build the artifacts, one Opus subagent at a time

`subagent_type: "general-purpose"` on Opus (the default/session model). Launch **sequentially**,
waiting for each to finish, so you can tell the next subagent what the earlier ones built (angle
+ visual approach + stack) and keep them distinct on all three.

Write products to `./<topic>/<theorem>/<idx>/` — `<topic>` = discipline slug, `<theorem>` =
statement slug, `<idx>` = zero-padded index + short slug (`topology/tychonoff/01-cantor-tree`).
Create each directory immediately before launching its subagent.

Fill the brackets below from the reasoning brief — the coder does not re-derive the math.

```
You are building ONE standalone interactive web artifact that explains [THEOREM] through
[GENERAL CONCEPT ANGLE], visualized as [VISUAL APPROACH]. This is one of N approaches that will
be compared — they all explain the same theorem, and differ in their deep angle and how they
use visuals. Make yours excellent and distinct.
[Already built: <prior angles + visual approaches + stacks> — pick a different stack.]

WORKING DIRECTORY (create all files here): <absolute path>/<topic>/<theorem>/<idx>/

THE IDEA: [one-line general framing].
THE ESSENCE (this is the point — everything serves it):
  - [the "aha" that survives once notation is gone]
  - [the NON-TRIVIAL insight to spotlight]
MATHEMATICAL CONTENT (must be correct):
  - [precise statement + the specific facts/proof steps this angle depends on]
  - [the proof idea in 2–4 beats]
  - [the honesty caveats that must be surfaced, not glossed]

THE VISUAL RULE — every visual, and every highlight, must correspond to something real in the
math: the object, the hypothesis, the step, the thing that breaks. No motion that illustrates
nothing, no chart whose axes carry no theorem content, no 3D that only spins. If a visual could
be removed and nothing about the meaning is lost, it was decoration — cut it. Prefer one deep,
revealing visual over several shallow ones. This rule governs everything below.

WHAT THE PAGE MUST DELIVER — this artifact is opened on its own, so it carries the whole arc:
  1. THE THEOREM — what it says, precisely and in plain words.
  2. THE MEANING — what it means once the notation is gone.
  3. THE PROOF IDEA — why it is true, as an argument the viewer can follow.
  4. WHY IT ISN'T OBVIOUS — what a reader would naively expect, and why that fails. A theorem
     with no visible obstacle reads as a definition.
  5. THE BOUNDARY — drop a hypothesis and show, computed and on screen, what breaks. Knowing
     where a theorem stops IS knowing the theorem.
  6. AN HONEST LEDGER — tag every claim on the page: argued here / computed live in-page /
     taken on faith with a pointer. Never let the third masquerade as the first.
  7. VISUALS THAT CARRY 1–6.
  Your angle is HOW you get through these, not a substitute for any of them.

YOUR JOB — think from first principles, then implement. The bar is not "a pretty picture of
the formula" — it is: does a viewer walk away understanding the theorem, its meaning, and why
it is true?
  1. Choose your libraries from the palette below by what fits THIS scene.
  2. Decide exactly what the mathematical object on screen is — the data structure (a tree of
     paths? a parameterized surface? a swarm of samples? a cover of open sets?).
  3. Compute, don't fake — actually compute the subcover, the transform, the eigen-
     decomposition, the limit, so the picture can't lie. Label any metaphor/projection as such,
     and surface caveats (finite-vs-infinite, projection-vs-embedding, discretization) in the UI
     where the user sees them.
  4. Define the interaction contract: what the user drags/toggles/steps, and what updates.
  5. Define the narration: staged beats carrying the argument, with the KaTeX statement at each.

FOCUS — write for a reader who wants the math. Say the thing and move on: no intro paragraphs,
no recaps, no restating the formula in prose. Text earns its place by saying what the visual
cannot. A short page that lands the meaning beats a long one that circles it.

PAGE FORMAT — two axes. The page is a vertical stack of SECTIONS. Each section owns ONE
sticky visual and a sequence of BEATS over it, and is split LEFT/RIGHT: the beat's prose on
one side, the sticky visual on the other.

  ↑ ↓ / SCROLL move between SECTIONS, and the move is SMOOTH either way. ↑ ↓ animate to the
    previous / next section — one full section per press, from any beat, never gated. Free
    scroll and trackpad keep working and are never hijacked; sections settle into frame with
    gentle snapping (`scroll-snap-type: y proximity` + `scroll-snap-align: start`) — proximity,
    never `mandatory`, so a deliberate scroll past a section is never fought. Leaving a section
    at any beat and returning restores the beat they were on; a first visit starts at beat 1.
  ← → move between beats INSIDE the current section — the one owning the viewport, whose sticky
    visual is on screen. → stops at the last beat; it does not carry into the next section.
    Arrow keys are the only beat control for now — no dot rail, no prev/next buttons, no swipe.
  Both axes are keyboard-driven only when nothing else claims the keys: bind at the document
  and skip when focus sits in a field, slider, or other control that uses arrows itself. Honor
  `prefers-reduced-motion` — jump instead of animating, on both axes.

A BEAT is one state of the section's visual plus the one paragraph explaining that state.
Advancing cross-fades caption and visual together as a single move: only the current beat's
text is on screen and the section does not reflow. Because the prose does NOT accumulate,
anything the reader must hold in view — a chain of inequalities, a partial construction, a
running count — has to live in the VISUAL, not in text above it. A section may have one beat.

The visual stays LIVE at every beat. Beats set its configuration; the reader may grab, drag
and probe at any moment. When they move it off the beat's configuration, mark that quietly and
offer a one-click restore — and keep every readout honest for the configuration actually on
screen, not the one the prose assumed.

HIGHLIGHTING — optional, and earned. Where a beat's sentence refers to a specific object, the
visual may spotlight exactly that object as the beat becomes active. Granularity follows the
math — a whole subgraph, one node, or a single symbol inside a KaTeX line (`\htmlClass` with
`trust: true`) — never a fixed level applied uniformly because the mechanism exists.
Highlighting nothing beats highlighting for the sake of it.

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
      "angle": "<one-line: the deep angle this artifact takes into the meaning>",
      "visual_approach": "<one-line: how it uses visuals to convey that meaning>",
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

Present a comparison table (`# | dir | angle | visual approach | stack | how well it lands the
meaning + proof idea`), call out shared gotchas, note any math-honesty flags, and offer next
steps: a **landing page** linking all N for side-by-side judging, a **scored comparison** after
the user looks, or **polish the strongest one**.

Then **aggregate the tech inventory**: read every concept's `manifest.json` and roll them up
into one `_inventory.json` at `./<topic>/<theorem>/` — the union of libraries (versions, roles,
build types, how many concepts used each), the union of `building_blocks`, and the recurring
`computations` / `interactions` / `gotchas`. Flag what recurs across concepts (candidates to
templatize) versus one-offs. This roll-up is what feeds automating artifact creation.

---

## Quick invocation

> Follow `explore_math_explanations.md` for the statement: **"[THEOREM]"**.
> `math-model=[fable|opus]`. Use N = [2]. Audience = [level]. Run end to end, no approval stops.

Omit `math-model` and it defaults to `fable`. Examples:

> Follow `explore_math_explanations.md` for **"Tychonoff's theorem"**. `math-model=opus`, N = 3.

> Follow `explore_math_explanations.md` for **"the Fourier transform"**. `math-model=fable`.
