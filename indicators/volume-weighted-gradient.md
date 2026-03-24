# Indicator: Volume-Weighted Gradient

## Identity

- **Serves:** Layer 2 (Directional Bias)
- **Role:** Component — quality-filtered momentum signal for directional confirmation
- **Status:** Needs evolution (from gradient-only to gradient × delta)

## What It Measures

Momentum quality. A price move accompanied by strong directional delta is higher quality than the same price move on mixed or weak delta. This indicator scales the MA gradient signal by concurrent delta magnitude, producing a signal that is strong only when both price momentum and volume participation agree.

## Mathematical Formulation

### Current Version (Pre-Evolution)

MA gradient signal only — no volume weighting.

```
gradient = MA(price, P, current) - MA(price, P, lookback)
```

### Target Version

```
gradient = MA(price, P, current) - MA(price, P, lookback)
delta_magnitude = abs(normalised_delta(N))  # or raw delta, TBD

volume_weighted_gradient = gradient * delta_magnitude
```

The product is large only when gradient is steep AND delta is strong. Weak delta dampens even a steep gradient. Mixed delta (near zero) kills the signal regardless of price movement.

## Parameters

| Parameter | Value | Justification |
|-----------|-------|---------------|
| MA period P | TBD | Inherited from or aligned with MTF Gradient WMA |
| Gradient lookback | TBD | How far back to measure slope |
| Delta source | TBD | Whether to use raw or normalised delta as the scaling factor |

## Inputs

- Price (for gradient)
- Delta (raw or normalised — from Signed Delta Accumulator or Normalised Delta Accumulation)

## Dependencies

- Signed Delta Accumulator or Normalised Delta Accumulation

## Known Edge Cases

- Large delta on a flat gradient: product stays near zero — correct behaviour (volume without price movement isn't momentum)
- Steep gradient on thin delta: product stays low — correct behaviour (price movement without participation is suspect)
- Sign handling: gradient sign indicates direction, delta magnitude scales it. Need to ensure delta sign alignment is checked separately, not baked into this indicator.

## Noise Sensitivity

Lower than either component alone — the product acts as a natural noise filter since both inputs must agree.

## Regime Dependency

Performs best in liquid markets with reliable delta. In thin markets, delta noise may distort the scaling.

## Evolution Notes

Key design decision: should the delta scaling use raw or normalised delta? Raw delta preserves the absolute magnitude relationship. Normalised delta makes cross-session comparison possible but introduces the z-score's own window sensitivity. May depend on whether this indicator's output is compared across sessions or only used within-session.
