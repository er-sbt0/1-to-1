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
and generate the **general** concept ideas (Phase 1), then **pause for approval** (Phase 2).
After approval you launch subagents **one at a time** (Phase 3) — each subagent chooses its
own libraries, designs the build from first principles, and implements the artifact. You
compare the results in Phase 4.

---

## Phase 0 — Understand the statement from first principles

Before any ideas, extract the math. Do not skip this — the quality of every downstream
visual depends on it.

1. **State it precisely.** Write the theorem formally (with quantifiers/hypotheses) and in
   one plain-language sentence. Note the discipline (topology, analysis, probability, linear
   algebra, number theory, …).
2. **Decompose into conveyable pieces.** List the sub-concepts a learner must grasp: the
   objects, the hypotheses, the claim, the mechanism/proof idea, the failure modes (what
   breaks if a hypothesis is dropped), and the canonical example.
3. **Identify the intuition gaps.** For each piece, name *what is hard to picture* and the
   visual metaphor that would resolve it. This list is the seed for the concepts in Phase 1.
4. **Pick the audience level** (default: motivated student who has seen the basics of the
   field). One concept may go deeper for the proof-curious.

Produce a short table: `piece | why it's hard | candidate visual metaphor`.

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
general terms) · a zero-padded index** (`01`, `02`, … ordered).

Run a **coverage check**: confirm the facets are genuinely varied before presenting.

---

## Phase 2 — Outline and get approval  ⏸️

Present to the user: the Phase 1 concept table + the assumptions (delivery target, audience,
N, and the `<topic>/<theorem>` slugs). **Wait for explicit approval.** Let them swap
concepts or change N before spending subagent budget. Do not spin up subagents until
approved.

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
# e.g. calculus/integral_fundamental_theorem/01
```
`<topic>` = discipline slug, `<theorem>` = statement slug, `<idx>` = zero-padded concept
index. Create the directory for a concept immediately before launching its subagent.

**Subagent spec template** (fill the brackets per concept):
```
You are building ONE standalone interactive web artifact that explains [THEOREM] through
[GENERAL CONCEPT ANGLE]. This is one of N approaches that will be compared — make yours
excellent and distinct. [Already built: <prior angles + stacks> — pick a different stack.]

WORKING DIRECTORY (create all files here): <absolute path>/<topic>/<theorem>/<idx>/

THE IDEA: [one-line general framing from Phase 1].
MATHEMATICAL CONTENT (must be correct):
  - [precise statement + the specific facts/proof steps this angle depends on]
  - [the honesty caveats that must be surfaced, not glossed]

YOUR JOB — think from first principles, then implement:
  1. Choose your libraries. Consider the palette below and pick what fits THIS scene; don't
     run four libraries that do the same job.
  2. Decide what the mathematical object on screen is, exactly — the data structure (a tree
     of paths? a parameterized surface? a swarm of samples? a cover of open sets?).
  3. Prefer honest computation over faked visuals — actually compute the subcover, the
     transform, the eigen-decomposition, the limit. Label any metaphor/projection as such.
  4. Define the interaction contract: what the user can drag/toggle/step, and what updates.
  5. Define the narration: the staged steps/panels that carry the argument, with the KaTeX
     statement at each step.
  Then build it.

LIBRARY PALETTE (reference — choose per scene):
  - Math-native diagrams / presentation: MathBox (on Three.js), JSXGraph, Mafs (React), CindyJS
  - Structured / relational (trees, graphs, sets, covers, morphs): D3.js
  - Free-form 2D animation (particles, custom metaphors): p5.js
  - 3D / spatial (product spaces, projections, camera): Three.js (UMD/global build), MathBox
  - Fast 2D at scale (thousands of points, shaders): Pixi.js, regl, raw WebGL/GLSL
  - Notation: KaTeX (fast, sync, no runtime network dep — default), MathJax (heavy AMS macros)
  - Choreography (step-by-step narration): GSAP (timelines), anime.js
  - Controls (sliders/toggles): lil-gui, Tweakpane
  - Numerics (if needed): mathjs, ml-matrix
  Overlap rule: D3 / JSXGraph / p5 / CindyJS all cover 2D — pick by scene type
  (relational→D3, draggable-geometry→JSXGraph, free-form→p5) and drop the rest for that scene.
  KaTeX/GSAP are fine to reuse as shared supporting libs across concepts.

DELIVERY REQUIREMENTS (strict):
  - A single `index.html` that opens directly in a browser with NO build step and NO
    internet at view time. Start with `<!DOCTYPE html>` + `<head>`/`<body>` (KaTeX refuses
    to render in quirks mode).
  - Vendor ALL libraries LOCALLY into a `lib/` subfolder (download with curl/wget; include
    KaTeX's .woff2 fonts and .css). Reference with relative paths only.
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

When all subagents have reported, present a comparison table
(`# | dir | angle | stack | standout interaction`), call out shared gotchas the agents hit,
note any math-honesty flags the agents surfaced, and offer next steps: a **landing page**
linking all N for side-by-side judging, a **scored comparison** after the user looks, or
**polish the strongest one**.

---

## Hard-won lessons (bake these into every subagent brief)

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

> Follow `explore_math_explainations.md` for the statement: **"[THEOREM]"**.
> Use N = [5]. Delivery = standalone offline dirs under `./<topic>/<theorem>/<idx>/`.
> Audience = [level]. Outline the general concepts and wait for my approval before
> spinning up subagents (one at a time).
