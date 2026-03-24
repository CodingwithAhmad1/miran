# Miran — Discussion Log

Personal notebook capturing raw thinking, observations, half-formed ideas, and session discussions. Timestamped and conversational. This is not the structured decision record (that's `design-log.md`) — this is where thoughts live before they become decisions.

---

## Session — 2026-03-24

### Layer Priority and Focus

Discussed the general indicator mapping across all three layers. Key takeaways on where effort should go:

**Layer 1 (Meaningful Pivots):** The approach has been discussed in detail architecturally. The precise indicator-level work comes next. There's existing indicator code that will be shared later — it will definitely be used in Layer 1 and possibly in Layer 3 as well. No immediate action needed beyond what's already documented.

**Layer 2 (Directional Sentiment):** Requires the least effort. Literally anything works here for now — an EMA 200, any moving average, a MACD, whatever. There's already a bunch of indicators built that can serve this function. This is not where time should be spent. The most important and toughest things come first, and Layer 2 is neither.

**Layer 3 (Execution System):** This is the primary focus going forward. The hardest and most important layer. Will dive into specifics later, but this is where collaborative design effort should concentrate.

### General Sequencing Thinking

The instinct is to tackle the hardest things first — Layer 3 primarily, Layer 1 secondarily. Layer 2 is a solved problem in the sense that many existing indicators can fill the role, so it gets attention last. This is a pragmatic deviation from the strict "build Layer 1 → 2 → 3" order — the *architectural* sequence is 1 → 2 → 3 (each layer's output feeds the next), but the *design effort* sequence is 3 → 1 → 2 because that's where the intellectual challenge and strategic value lie.

**Update:** Reconsidered. Layer 1 should be developed first after all, because Layer 3 looks at what's happening at the region classified by Layer 1. Without Layer 1's output, Layer 3 development would rely on visual approximation of pivots, which isn't rigorous enough. Build order reverts to Layer 1 → Layer 3 → Layer 2.

### Layer 1 — Three-Operation Separation

Clarified that Layer 1 is three distinct operations, not two:
1. Detect all pivots
2. Measure the trend between consecutive pivots (magnitude, duration, volatility)
3. Qualify the trend — is it worth trading a continuation from?

This was always the intent ("detect all, then clean"), but separating measurement from qualification makes the responsibilities explicit and the components potentially reusable. The trend window is now concretely defined: previous pivot to current pivot.

---
