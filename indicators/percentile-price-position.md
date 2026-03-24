# Indicator: Percentile Price Position

## Identity

- **Serves:** Layer 1 (Pivot Identification)
- **Role:** Standalone — provides percentile extreme detection, prerequisite for pivot candidacy
- **Status:** Existing (functional)

## What It Measures

Where current price sits within the distribution of prices over a rolling lookback window. A reading at the 95th percentile means price is higher than 95% of prices in the window.

## Mathematical Formulation

### Current Version

Percentile rank of current price within rolling window of N bars.

```
percentile(price, window_N) = (count of bars where price_i <= current_price) / N * 100
```

### Original Version

*(This is the original — no prior version exists)*

## Parameters

| Parameter | Value | Justification |
|-----------|-------|---------------|
| Lookback window N | TBD | Must be long enough to capture a meaningful distribution but short enough to remain relevant to intraday context |
| Upper threshold | TBD | Defines "extreme" — too tight = false signals, too loose = misses genuine exhaustion |
| Lower threshold | TBD | Symmetric or asymmetric to upper, depending on instrument behaviour |

## Inputs

- Price (close, or potentially high/low depending on formulation)

## Dependencies

None.

## Known Edge Cases

*(To be populated during development)*

## Noise Sensitivity

*(To be assessed)*

## Regime Dependency

*(To be assessed)*
