# Miran — Index

## Quick Navigation

### Core Documents
- [[strategy-overview|Strategy Overview]] — Full architecture, layer definitions, indicator inventory, trade structure, open questions
- [[instruction-guide|Instruction Guide]] — Prompt for AI sessions. How to think, what to read, what not to do
- [[design-log|Design Log]] — Structured decision record. Every architectural choice and why
- [[discussion-log|Discussion Log]] — Personal notebook. Raw thinking, observations, session conversations

---

### Layer Specifications
- [[layer-1-meaningful-pivots|Layer 1 — Meaningful Pivots]] — Detect pivots, filter by preceding trend quality
  - [[layer-1-working-log|Layer 1 — Working Log]] — Development evolution, iterations, rejected approaches
- [[layer-2-directional-sentiment|Layer 2 — Directional Sentiment]] — Directional signal. Modular, swappable
  - [[layer-2-working-log|Layer 2 — Working Log]] — Development evolution, iterations, rejected approaches
- [[layer-3-execution-system|Layer 3 — Execution System]] — Regime classification → entry type → order construction
  - [[layer-3-working-log|Layer 3 — Working Log]] — Development evolution, iterations, rejected approaches

---

### Indicators

**Existing (functional):**
- [[percentile-price-position|Percentile Price Position]] — Price percentile within rolling window → Layer 1
- [[mtf-gradient-wma|MTF Gradient WMA]] — Multi-timeframe gradient alignment → Layer 2
- [[prior-session-levels|Prior-Session Levels]] — Structural levels from prior day → Layer 2, Layer 3
- [[signed-delta-accumulator|Signed Delta Accumulator]] — Directional volume pressure → Layer 1, Layer 2
- [[short-period-ma-gradient-crossover|Short-Period MA Gradient Crossover]] — Momentum shift detection → TBD

**Needs evolution:**
- [[normalised-delta-accumulation|Normalised Delta Accumulation]] — Z-scored delta for cross-session comparability → Layer 1, Layer 2
- [[volume-weighted-gradient|Volume-Weighted Gradient]] — Gradient × delta magnitude → Layer 2

**To build:**
- [[pivot-detector|Pivot Detector]] — Identify all structural swing points → Layer 1 (Op 1)
- [[trend-measurer|Trend Measurer]] — Compute magnitude, duration, volatility between consecutive pivots → Layer 1 (Op 2)
- [[trend-qualifier|Trend Qualifier]] — Classify trend type, apply thresholds → Layer 1 (Op 3)
- [[wick-to-body-ratio|Wick-to-Body Ratio]] — Rejection behaviour at pivot → Layer 3
- [[regime-classifier|Regime Classifier]] — Five-tier risk classification → Layer 3
- [[market-structure-classifier|Market Structure Classifier]] — Trending / choppy / stagnating → Layer 3
- [[breakout-detector|Breakout Detector]] — State-transition based breakout detection → Layer 3

---

### Current Focus
Layer 1 (Meaningful Pivots) — primary design effort. Building first because Layer 3 needs its output.
Layer 3 (Execution System) — next priority after Layer 1.
Layer 2 (Directional Sentiment) — parked. Any standard directional indicator works for now.
