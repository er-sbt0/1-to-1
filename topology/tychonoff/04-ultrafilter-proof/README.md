# Tychonoff's Theorem via Ultrafilter Convergence

An interactive, single-file web artifact that walks through the *slick* modern proof of
**Tychonoff's theorem**: an arbitrary product of compact spaces is compact.

## How to open

Just open `index.html` in any modern browser. No build step, no server, no internet
required — every library is vendored locally in `lib/`.

```
open index.html      # macOS
xdg-open index.html  # Linux
```

## The idea

Instead of wrestling with open covers, we use the ultrafilter characterization of
compactness:

> A space `K` is **compact** ⇔ **every ultrafilter on `K` converges** (to at least one point).

The proof is then almost a one-liner:

1. Let `𝒰` be an arbitrary ultrafilter on the product `X = ∏ᵢ Xᵢ`.
2. For each coordinate `i`, the **pushforward** `πᵢ(𝒰) = { B ⊆ Xᵢ : πᵢ⁻¹(B) ∈ 𝒰 }`
   is again an ultrafilter — now living on the factor `Xᵢ`.
3. Each `Xᵢ` is compact, so `πᵢ(𝒰)` **converges** to some `xᵢ ∈ Xᵢ`. There may be several
   limits, so we use the **Axiom of Choice** to pick one `xᵢ` per coordinate.
4. Assemble `x = (xᵢ) ∈ X`.
5. **Key topological fact:** convergence in the product topology *is* coordinatewise
   convergence, and a *basic* open neighborhood of `x` constrains only **finitely many**
   coordinates: `V = πᵢ₁⁻¹(U₁) ∩ … ∩ πᵢₖ⁻¹(Uₖ)`.
6. For each factor, `πᵢⱼ(𝒰) → xᵢⱼ` gives `Uⱼ ∈ πᵢⱼ(𝒰)`, i.e. `πᵢⱼ⁻¹(Uⱼ) ∈ 𝒰`. Since a
   filter is closed under **finite** intersections, the whole box `V ∈ 𝒰`.
7. So every neighborhood of `x` lies in `𝒰`, which means `𝒰 → x`. The arbitrary ultrafilter
   converges ⇒ the product is compact. ∎

### The two pillars emphasized in the visual
- **Product topology = coordinatewise convergence**, and basic opens touch only finitely
  many coordinates (this is exactly why coordinatewise limits assemble into a genuine limit).
- **The Axiom of Choice** selects one limit per coordinate.

### Filter / ultrafilter facts used (stated precisely in the sidebar)
- A **filter** is a nonempty family of subsets, not containing ∅, that is upward closed and
  closed under finite intersections.
- An **ultrafilter** additionally contains, for every subset `A`, either `A` or its
  complement `S ∖ A`.
- **Convergence:** `𝒰 → x` iff every open neighborhood of `x` belongs to `𝒰`.

## The visual metaphor

The product space is drawn as **parallel coordinate axes** `X₁, X₂, …`. Each sample of the
ultrafilter is a thin polyline crossing every axis — together they form the teal **swarm**.

- **Projection `πᵢ`:** where the polylines cross axis `i` is exactly the pushforward
  ultrafilter on that coordinate.
- **Convergence:** each axis *funnels* its crossings down to the gold limit point `xᵢ`.
- **Assembly:** the gold thread through all the `xᵢ` is the point `x`.
- **Basic open box:** the violet bands pin only finitely many coordinates while the rest stay
  free — the whole point of why the argument works even for infinitely many factors.

### Interactions
- Step forward/back through the 8-stage proof (buttons or `←` / `→` keys).
- Toggle **2 / 3 / ∞** coordinates to see the argument scale (the ∞ mode makes the
  "finitely many constrained, infinitely many free" idea vivid).
- **Hover any coordinate axis** to open a mini panel showing that coordinate's pushforward
  ultrafilter funneling to its limit.
- Replay the current step's animation.

## Libraries (all local, in `lib/`)
- **p5.js** — the particle/parallel-coordinates canvas visualization.
- **GSAP** — timeline-driven staged animations (funnels, box reveal, limit thread).
- **KaTeX** — crisp math rendering in the narration panel and glossary (with woff2 fonts in
  `lib/fonts/`).

## Files
```
index.html        # the whole artifact (markup + CSS + JS)
README.md         # this file
lib/
  p5.min.js
  gsap.min.js
  katex.min.js
  katex.min.css
  fonts/          # KaTeX woff2 fonts
```
