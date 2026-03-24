# Indicator: Trend Qualifier

## Identity

- **Serves:** Layer 1 (Meaningful Pivots) — Operation 3
- **Role:** Component — classifies trend type and applies type-specific thresholds. Produces the final Layer 1 binary output.
- **Status:** To build from scratch

## What It Measures

Whether a trend (as measured by the Trend Measurer) has the characteristics that make a continuation setup worth trading. Classifies the trend into a type, then applies that type's specific minimum thresholds. This is the judgment step — it takes measurements and produces a pass/fail.

## Mathematical Formulation

### Target Version

```
Given: magnitude, duration, volatility (from Trend Measurer)

Step 1 — Classify trend type:
  trend_type = classify(magnitude, duration, volatility)
  # Types (to be enumerated):
  #   - Sharp/fast: high magnitude, short duration
  #   - Slow grind: moderate magnitude, long duration, low volatility
  #   - Staircase: moderate magnitude, moderate duration, structured pullbacks
  #   - (others TBD)

Step 2 — Apply type-specific thresholds:
  thresholds = threshold_set[trend_type]
  qualified = (magnitude >= thresholds.min_magnitude
               AND duration >= thresholds.min_duration
               AND volatility meets thresholds.volatility_criteria)

Output: qualified (binary)
```

**Explicitly discarded (regardless of type):**
- Choppy or "smaller" slow moves — insufficient directional conviction
- Violent single-candle spikes — noise, not a tradeable trend

These may be caught by type classification (they classify into a "discard" type) or by failing any type's minimum thresholds.

### Original Version

Previously part of the "Preceding Trend Classifier" which combined measurement and qualification. The trend origin was also undefined — now resolved as "previous pivot to current pivot."

## Parameters

| Parameter | Value | Justification |
|-----------|-------|---------------|
| Trend type definitions | TBD | Mathematical criteria that classify a trend into a type based on its measured characteristics |
| Per-type magnitude threshold | TBD | Minimum displacement for each trend type |
| Per-type duration threshold | TBD | Minimum candle count for each trend type |
| Per-type volatility criteria | TBD | May be a range, minimum, or maximum depending on type |

## Inputs

- Magnitude, duration, volatility (from Trend Measurer)

## Dependencies

- Trend Measurer (provides the characteristic values)

## Known Edge Cases

- **Trend type boundary ambiguity:** A trend that sits between two types. Classification must be deterministic — overlapping boundaries cause inconsistency.
- **Single-candle "trends":** Duration = 1. Should be auto-discarded regardless of type.
- **Type proliferation risk:** Too many types = too many parameter sets = overfitting risk. Keep the typology as small as possible while capturing genuinely distinct trend characters.

## Open Design Questions

1. **What is the complete trend typology?** Need to enumerate all types and what mathematically distinguishes each. This is the most important open question for Layer 1.
2. **Are thresholds strictly conjunctive per type?** Must all three dimensions pass, or can strength in one partially compensate for weakness in another within a type?
3. **How many types before we're overfitting?** Each type is a parameter set. More types = more degrees of freedom = more overfit risk. Need the minimum number that captures genuinely different trend characters.
4. **Should "discard" be a type or a catch-all?** Choppy moves and single-candle spikes — do they classify into their own type (with thresholds that always fail), or are they caught by pre-classification rules?

## Noise Sensitivity

Primarily in the classification step. If magnitude/duration/volatility values land near type boundaries, small input changes could reclassify the trend into a different type with different thresholds, potentially flipping the output. Boundary hysteresis may be needed.

## Regime Dependency

The type-specific thresholds are where regime dependency lives. A "large" move in a low-vol regime is small in a high-vol regime. Thresholds may need to be adaptive or normalised to account for this. Alternatively, if the Trend Measurer already normalises its outputs (e.g., magnitude in ATR units), regime dependency is absorbed there.
