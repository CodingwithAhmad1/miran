# Indicator: Market Structure Classifier

## Identity

- **Serves:** Layer 3 (Execution System) — Phase A (risk input) and Phase B (breakout trigger)
- **Role:** Component — dual purpose within Layer 3
- **Status:** To build from scratch

## What It Measures

The current character of price action, classified into discrete states. Serves two distinct purposes within Layer 3:
1. **Risk input (Phase A):** Stagnation and choppiness increase stop-out risk, feeding into the Regime Classifier.
2. **Breakout trigger (Phase B):** State transitions (e.g., "no longer stagnating") are the basis for breakout detection. This is a conditional/state-transition approach, not a price-level approach.

## Mathematical Formulation

### Target Version

```
Inputs: recent candle data (OHLC, volume, delta)

Classify into one of:
  - Trending: clean directional movement
  - Choppy: directionless oscillation, no clear structure
  - Stagnating: low-volatility compression, price going nowhere
  - (potentially others TBD)

Method TBD. Likely involves:
  - Directional efficiency: net displacement / total path length
  - Range expansion/contraction rate
  - Candle body consistency (are bodies mostly one direction?)
  - Volatility compression detection
```

### Original Version

*(No prior version — building from scratch)*

## Parameters

| Parameter | Value | Justification |
|-----------|-------|---------------|
| Classification window | TBD | How many bars to assess for structure classification |
| State definitions | TBD | Mathematical criteria for each state |
| Transition smoothing | TBD | How to prevent oscillation at state boundaries |

## Inputs

- OHLC data
- Potentially volume/delta data

## Dependencies

None directly, but its output feeds into both the Regime Classifier and the Breakout Detector.

## Known Edge Cases

- **Boundary oscillation:** Price action right at the boundary between "stagnating" and "trending" could cause rapid state flipping. Needs hysteresis or smoothing.
- **Mixed structure:** Market is trending on 5m but choppy on 1m. Which timeframe's structure matters?
- **Transitions during classification window:** Structure changes mid-window.

## Critical Design Concern

This classifier must be near-real-time responsive for Phase B (breakout detection relies on state transitions), but also stable enough for Phase A (risk assessment shouldn't flip rapidly). These two requirements are in tension. May need separate parameterisations for each purpose, or a two-output design.

## Open Design Questions

1. How many structure states? Starting with three (trending/choppy/stagnating) but may need more.
2. What timeframe? Or multi-timeframe?
3. How to handle the stability vs. responsiveness tension between Phase A and Phase B usage?
4. Hysteresis mechanism for state transitions?

## Noise Sensitivity

*(To be assessed)*

## Regime Dependency

This indicator *is* a regime classifier in a narrow sense (classifying local market structure). Must be distinct from the broader Regime Classifier that combines multiple inputs.
