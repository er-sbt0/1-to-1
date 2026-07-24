# Tychonoff's Theorem (finite case) — the Tube Lemma, made spatial

An interactive 3D artifact that proves **X × Y is compact when X and Y are compact**,
by making the definition of compactness *visible*: open cover → finite subcover → Tube Lemma.

Open `index.html` directly in any modern browser. No build step, no server, no internet
required — all libraries and fonts are vendored locally in `lib/`.

## The idea

The product space is the compact square **[0,1] × [0,1]**, rendered in 3D:

- **world-X** is the factor **X**,
- **world-Z** is the compact factor **Y**,
- **world-Y** (height) is used only to make the open sets, slices, and tubes visible as slabs.

An **open cover** of X×Y is drawn as translucent colored boxes. The proof then unfolds in
six choreographed steps.

## The math (and why each step is correct)

1. **Compactness** = *every open cover has a finite subcover.* That is the goal for X×Y.
2. **Fix x₀; look at the slice {x₀}×Y.** This slice is homeomorphic to **Y**, hence compact.
3. **Finite subcover of the slice.** Because the slice is compact, finitely many sets of the
   cover already cover it (the demo uses 3 sets N₁, N₂, N₃, shown in gold).
4. **Tube Lemma.** Intersect the X-projections of those finitely many sets to get an open
   interval **W ∋ x₀**. Then the whole **tube W×Y** lies inside N₁∪N₂∪N₃ — a vertical green
   slab covered by finitely many sets. (This step genuinely needs Y compact: only a *finite*
   intersection of open X-parts is guaranteed open and nonempty.)
5. **Sweep x₀ across X.** Every x₀ yields a tube; the tubes form an open cover of the compact
   factor **X**, so **finitely many tubes** cover all of X×Y (the sweep lays down 9 tubes).
6. **Conclusion.** Each tube is covered by finitely many original sets; union those finitely
   many finite collections ⇒ **finitely many open sets cover X×Y** ⇒ X×Y is compact. ∎
   Induction extends this to any finite product X₁×···×Xₙ.

The cover, subcover selection, and greedy sweep are computed in code (not faked): W is a true
intersection of the chosen X-intervals, and the sweep provably covers [0,1].

## Interactions

- Orbit / zoom the scene (drag, scroll) via Three.js OrbitControls.
- **Prev / Next** buttons, clickable step **dots**, or **← / →** arrow keys.
- **▶ Autoplay** advances through the proof with GSAP-animated transitions.
- KaTeX renders the lemma statement and the key equation at each step.

## Libraries (all local, in `lib/`)

- **Three.js r137** (`three.min.js`) — 3D scene, global `THREE` build for reliable `file://` loading.
- **OrbitControls** (`OrbitControls.js`) — the classic global build that attaches to `THREE`.
- **GSAP 3.12** (`gsap.min.js`) — choreographs the proof stages and camera moves.
- **KaTeX 0.16** (`katex.min.js`, `katex.min.css`, `fonts/`) — math typesetting.

## How to open

Double-click `index.html`, or:

```
xdg-open index.html      # Linux
open index.html          # macOS
```
