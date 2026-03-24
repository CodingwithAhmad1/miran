# Layer 1 — Meaningful Pivots — Working Log

Development evolution trail for Layer 1. Captures iterations, rejected approaches, observations, and reasoning specific to this layer's development. Read this before working on Layer 1 to understand how we got to the current state.

---

## Current State Summary

Layer 1 has three distinct operations:
1. **Pivot Detection** — identify all structural highs/lows in price (Pivot Detector)
2. **Trend Measurement** — compute magnitude, duration, volatility on candles between consecutive pivots (Trend Measurer)
3. **Trend Qualification** — classify trend type, apply type-specific thresholds, discard pivots whose trend doesn't qualify (Trend Qualifier)

The trend window is defined as previous pivot → current pivot. No ambiguity about trend origin.

**Key components:**
- Pivot Detector — identifies all local highs/lows (not yet built)
- Trend Measurer — computes characteristics between consecutive pivots (not yet built)
- Trend Qualifier — classifies trend type and applies thresholds (not yet built)
- Percentile Price Position — candidate mechanism for pivot detection (exists)
- Signed Delta Accumulator — potential input for trend measurement (exists)

**Critical unsolved problems:**
- Trend origin identification: how do we find where the preceding trend started?
- Trend typology: what are all the valid trend types, and what mathematically distinguishes each?
- Existing indicator code will be shared that is intended for this layer (and possibly Layer 3)

**What's been decided:**
- The quality gate is on the preceding trend, not the counter-move into the pivot. This was a fundamental reframe from the original spec. (See Design Log Entry 002)
- Choppy small moves and violent single-candle spikes are explicitly targeted for discard.
- Each trend type gets its own threshold set — this is not a single universal bar, and not a compensatory/composite score.

---

## Iterations and Evolution

### Iteration 001 — Three-Operation Separation and Trend Origin Resolution (2026-03-24)

**What changed:** Layer 1 was previously described as two steps (detect pivots, then filter). Refined into three distinct operations:
1. **Pivot Detection** — find all structural highs/lows
2. **Trend Measurement** — compute characteristics on candles between consecutive pivots
3. **Trend Qualification** — classify trend type, apply type-specific thresholds, output binary

**Key resolution: Trend origin is now defined.** The trend window runs from the previous pivot to the current pivot. This was previously flagged as an unsolved prerequisite ("where does the trend start?"). Now resolved cleanly — consecutive pivots define the boundaries.

**Indicator split:** The "Preceding Trend Classifier" indicator was split into two separate indicators:
- **Trend Measurer** (Operation 2) — measures magnitude, duration, volatility. No judgment.
- **Trend Qualifier** (Operation 3) — classifies type, applies thresholds. Produces the binary output.

Rationale: separating measurement from judgment allows the measurement component to be reused (e.g., by Layer 3 if needed) and keeps each indicator's responsibility clean.

**What was kept:** The overall logic is the same as what was discussed earlier (detect all, then clean). The three-operation separation just makes the distinct responsibilities explicit.

---
