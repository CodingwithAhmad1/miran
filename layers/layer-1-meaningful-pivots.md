# Layer 1 — Meaningful Pivots

## Purpose

Detect all structural pivots in price, then qualify them by measuring and classifying the trend between each consecutive pair of pivots. Pivots whose preceding trend doesn't meet criteria for a tradeable continuation setup are discarded.

## Output

Binary: qualified pivot / discard.

## Logic Summary

Three distinct operations, executed sequentially:

**Operation 1 — Pivot Detection**

Identify all local highs and lows in price. This is a structural/geometric operation — find swing points. The percentile price position indicator is a candidate mechanism for this, though the exact approach will be developed later. This operation casts a wide net; qualification happens downstream.

**Operation 2 — Trend Measurement**

For each pair of consecutive pivots, measure the characteristics of the candles between them. The trend window is explicitly defined: it runs from the **previous pivot** to the **current pivot**. This resolves the trend origin question — no ambiguity about where the trend starts.

Characteristics measured:
- **Magnitude** — how far did price move (previous pivot to current pivot)?
- **Duration** — how many candles span the window?
- **Volatility** — what was the volatility character during the move?

This operation only measures. It does not judge or filter.

**Operation 3 — Trend Qualification**

Apply criteria to the measured characteristics to determine if this is a trend worth trading a continuation from. Different trend types are inherently different but can all be valid, so each type has its own set of minimum thresholds for magnitude, duration, and volatility.

Trends that fail their type-specific thresholds are discarded. Explicitly targeted for discard:
- Choppy or "smaller" slow moves — insufficient directional conviction
- Violent single-candle spikes — noise, not a tradeable trend

*The full trend typology (e.g., sharp/fast, slow grind, staircase, etc.) needs to be enumerated. Each type gets its own threshold set.*

## Component Indicators

| Indicator | Role in This Layer | Operation | Output Type |
|-----------|-------------------|-----------|-------------|
| Pivot Detector | Identifies all structural pivots (local highs/lows) | 1 | Pivot locations (price + time) |
| Percentile Price Position | Candidate mechanism for pivot detection | 1 | Continuous (percentile), thresholded |
| Trend Measurer | Computes magnitude, duration, volatility on candles between consecutive pivots | 2 | Characteristic values (continuous) |
| Trend Qualifier | Classifies trend type and applies type-specific thresholds | 3 | Binary (qualified / not) |
| Signed Delta Accumulator | Potential input for trend measurement | 2 | Continuous |
| Normalised Delta Accumulation | Cross-session comparable delta for trend measurement | 2 | Continuous (z-score) |

## Composition Logic

```
pivots = pivot_detector(price_data)  # Operation 1: all structural pivots

for each consecutive pair (previous_pivot, current_pivot):
  # Operation 2: measure the trend between them
  candles = get_candles_between(previous_pivot, current_pivot)
  magnitude = |price_at_current - price_at_previous|
  duration = len(candles)
  volatility = measure_volatility(candles)

  # Operation 3: qualify the trend
  trend_type = classify_trend_type(magnitude, duration, volatility)
  thresholds = get_thresholds_for_type(trend_type)
  
  qualified = (magnitude >= thresholds.min_magnitude
               AND duration >= thresholds.min_duration
               AND volatility meets thresholds.volatility_criteria)
  
  if NOT qualified → discard current_pivot
```

## Edge Cases and Failure Modes

- **Trend type boundary ambiguity:** A trend that sits between two types — which thresholds apply? Need clear classification boundaries.
- **Session boundaries:** A pivot pair may span an overnight gap or session boundary. The candles between them may include a discontinuity. How to handle?
- **First pivot of session:** No previous pivot exists within the current session. Use prior session's last pivot, or discard?
- **Consecutive same-direction pivots:** E.g., higher-high to higher-high with a minor pullback between. The "trend" between them is really a pullback + continuation. Does this qualify as a single trend?

## Open Design Questions

1. What is the complete trend typology? Need to enumerate all valid types and what mathematically distinguishes each.
2. Are the three dimensions (magnitude, duration, volatility) strictly conjunctive per type, or do some types relax one dimension?
3. Does delta (volume participation) factor into trend measurement, or is it purely price-based at this layer?
4. Should Operation 2 (Trend Measurer) and Operation 3 (Trend Qualifier) be one indicator or two? They produce a single binary output together, but separating them allows the measurement to be reused elsewhere.

## Status

In development. Percentile Price Position and Signed Delta Accumulator exist. Pivot Detector, Trend Measurer, and Trend Qualifier need to be built.

## Layer-Level Indicator

The final Layer 1 indicator is a single callable function that chains the three operations together. It is the glue — not a separate analytical component. When the full strategy is assembled, this is what gets called.

**Location:** TBD (will be created when components are built)

**Calls, in order:**
1. [[pivot-detector|Pivot Detector]] → all structural pivots
2. [[trend-measurer|Trend Measurer]] → characteristics for each consecutive pivot pair
3. [[trend-qualifier|Trend Qualifier]] → binary qualification per pivot

**Input:** Price data (OHLC)
**Output:** Set of qualified pivots (price, time, direction)
