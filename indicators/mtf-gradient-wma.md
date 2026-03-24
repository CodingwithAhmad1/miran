# Indicator: Multi-Timeframe Gradient Weighted Moving Average

## Identity

- **Serves:** Layer 2 (Directional Bias)
- **Role:** Standalone — provides gradient alignment check across timeframes
- **Status:** Existing (functional)

## What It Measures

Directional alignment of weighted moving average gradients across multiple timeframes (1m, 5m, 15m) and multiple periods (short, medium, long) within each timeframe. All gradients pointing the same direction = aligned. Any disagreement = misaligned.

## Mathematical Formulation

### Current Version

WMA computed at three period lengths on each of three timeframes. Gradient = sign of WMA slope over recent bars.

```
For each timeframe T in {1m, 5m, 15m}:
  For each period P in {short, medium, long}:
    wma_gradient(T, P) = sign(WMA(T, P, current) - WMA(T, P, lookback))

aligned = all gradients share same sign
```

### Original Version

*(This is the original — no prior version exists)*

## Parameters

| Parameter | Value | Justification |
|-----------|-------|---------------|
| Timeframes | 1m, 5m, 15m | Covers the sub-15m intraday spectrum the strategy operates on |
| Period lengths (per TF) | TBD (short, medium, long) | Must be spaced enough to capture different trend scales but not so wide that the long period is stale |
| Gradient lookback | TBD | How far back to measure slope — too short = noisy, too long = lagging |

## Inputs

- Price data on 1m, 5m, 15m timeframes

## Dependencies

None.

## Known Edge Cases

*(To be populated during development)*

## Noise Sensitivity

*(To be assessed)*

## Regime Dependency

*(To be assessed)*
