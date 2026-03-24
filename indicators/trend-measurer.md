# Indicator: Trend Measurer

## Identity

- **Serves:** Layer 1 (Meaningful Pivots) — Operation 2
- **Role:** Component — computes trend characteristics on the candle window between consecutive pivots. Measurement only, no judgment.
- **Status:** To build from scratch

## What It Measures

The characteristics of price action between two consecutive pivots. Given a previous pivot and a current pivot, it measures the candles in that window across three dimensions: magnitude, duration, and volatility. It does not classify or filter — it only measures and outputs values.

## Mathematical Formulation

### Target Version

```
Given: previous_pivot (price, time), current_pivot (price, time)
       candles = all candles between previous_pivot and current_pivot

magnitude = |price_at_current_pivot - price_at_previous_pivot|
duration = number_of_candles(candles)
volatility = measure_volatility(candles)  # ATR, std, range-based, or other — TBD
```

The trend window is explicitly defined by the Pivot Detector's output — no ambiguity about boundaries.

### Original Version

Previously part of the "Preceding Trend Classifier" which combined measurement and qualification. Separated into two indicators for clarity and potential reuse of the measurement component.

## Parameters

| Parameter | Value | Justification |
|-----------|-------|---------------|
| Volatility measure | TBD | ATR, standard deviation, average range, or other. Choice affects what "volatility character" means. |

## Inputs

- OHLC candle data between consecutive pivots
- Pivot locations from Pivot Detector (defines the window boundaries)

## Dependencies

- Pivot Detector (provides the window boundaries)

## Known Edge Cases

- **Session gaps:** If the window spans an overnight gap, magnitude includes the gap but the candles don't represent continuous trading. May need to handle gaps separately.
- **Very short windows:** Two pivots only 2-3 candles apart — volatility measurement becomes unreliable with so few samples.
- **Direction:** Magnitude is absolute. The sign (up-trend vs. down-trend) is implicit in the pivot sequence (low→high = up, high→low = down).

## Open Design Questions

1. Which volatility measure? ATR captures range, std captures dispersion around mean, average body size captures directional consistency. Each tells a different story.
2. Should magnitude be normalised (e.g., as a multiple of ATR) for cross-instrument comparability, or kept raw for this layer?
3. Are there additional characteristics beyond magnitude/duration/volatility worth measuring? (e.g., directional efficiency = magnitude / total path length)

## Noise Sensitivity

Low — the measurements are straightforward given well-defined window boundaries. Noise primarily enters through the Pivot Detector's output (noisy pivots = noisy windows).

## Regime Dependency

The raw measurements are regime-independent. However, what constitutes "large magnitude" or "high volatility" varies by regime. That interpretation belongs to Operation 3 (Trend Qualifier), not here.
