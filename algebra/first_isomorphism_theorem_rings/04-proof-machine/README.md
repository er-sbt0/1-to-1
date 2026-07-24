# Proof Machine — First Isomorphism Theorem for Rings

An interactive, step-by-step **animated proof** of the First Isomorphism Theorem for rings,
staged as a single **commuting triangle**.

> If `φ : R → S` is a ring homomorphism, then `R / ker φ ≅ im φ`.

## The idea / angle

The other four concepts in this comparison show *what* the theorem says (fiber collapse,
coset partitions, ideal-vs-subring tests). This one shows **why it is true** — the proof itself,
choreographed as a diagram.

Everything hangs on one triangle:

```
        φ
   R --------→ im φ
   |          ↗
 π |        ↗ φ̄
   ↓      ↗
 R/ker φ
```

- `π : R → R/ker φ`, `r ↦ r + ker φ`  — the natural (surjective) projection.
- `φ̄ : R/ker φ → im φ`, `φ̄(r + ker φ) = φ(r)`  — the *induced* map.
- The triangle **commutes**: `φ = φ̄ ∘ π`.

The proof that `φ̄` is an isomorphism is exactly four beats, each of which lights up the
relevant nodes/edges and reveals the precise KaTeX statement:

1. **Well-defined** — `r+K = r′+K ⇒ r−r′ ∈ ker φ ⇒ φ(r) = φ(r′)`. The value does not depend on the representative.
2. **Homomorphism** — `φ̄` preserves `+`, `×`, and `1`, inherited from `φ` and `π`.
3. **Injective** — `ker φ̄ = {0}`: `φ̄(r+K)=0 ⇒ φ(r)=0 ⇒ r∈K ⇒ r+K = 0`. (Quotienting *by the kernel* kills exactly the ambiguity — this is the whole point.)
4. **Surjective** — every `y ∈ im φ` equals `φ(r) = φ̄(r+K)`, by construction.

Bijective homomorphism ⟹ isomorphism, and `φ̄` is the **unique** map making the triangle commute.

## The concrete companion

To keep the abstraction honest, a worked instance runs through the same triangle:

> `φ : ℝ[x] → ℂ`, `x ↦ i`.  Then `ker φ = (x²+1)`, `im φ = ℂ`, so **`ℝ[x]/(x²+1) ≅ ℂ`**.

You type a real polynomial `p(x)`. The app **computes** (not fakes):

- its coset `π(p) = p mod (x²+1) = a + bx` (reduction replaces `x²` by `−1`),
- the image `φ̄(a+bx) = a + bi`,
- the direct value `φ(p) = p(i) = a + bi`,

and animates a token travelling `R → R/ker φ → im φ` (with a ghost token going straight along `φ`),
confirming `φ̄(π(p)) = φ(p)`. The **"Other representative"** button shows `p` and `p+(x²+1)`
landing on the same coset and the same image — well-definedness (Beat 1), live.

## Libraries (and why)

All vendored locally in `lib/` — no build step, no network.

- **Two.js** (`lib/two.min.js`) — lightweight animated 2D scene graph. Draws the triangle:
  node boxes, arrows/arrowheads, glow highlights, the travelling tokens. Ships a UMD/global
  build that works from `file://`. Chosen over raw Canvas so shapes are retained objects GSAP
  can tween.
- **GSAP** (`lib/gsap.min.js`) — timeline choreography. Drives the beat-to-beat highlight
  transitions and the token's motion along the edges. `gsap.ticker` re-renders Two.js each frame.
- **KaTeX** (`lib/katex.min.js`, `lib/katex.min.css`, `lib/fonts/*.woff2`) — every mathematical
  statement (node/edge labels, the four proof beats, the computed example) is real typeset math.

No p5.js / D3.js / hand-rolled Canvas (those are the other concepts). No physics/3D libs.

## How to open

Just open `index.html` in any modern browser — double-click it, or:

```
file:///…/04-proof-machine/index.html
```

No server, no internet connection required.

## Controls

- **Setup / 1–4 / ★ Iso** tabs, or **Prev / Next**, or **Play** — step through the proof beats.
- **Send it around the triangle** — animate the current polynomial through `π` then `φ̄`.
- **Other representative** — witness representative-independence.
- Edit the coefficient boxes `c₀…c₄` to try your own `p(x)`.
