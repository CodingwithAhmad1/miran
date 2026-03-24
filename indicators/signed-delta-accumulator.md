# Indicator: Signed Delta Accumulator

## Identity

- **Serves:** Layer 1 (Pivot Identification — delta consistency sub-condition), Layer 2 (Directional Bias — delta exhaustion input)
- **Role:** Component — feeds into Counter-Move Quality Classifier (Layer 1) and delta exhaustion detection (Layer 2)
- **Status:** Existing (functional)

## What It Measures

Directional volume pressure over a rolling window. Accumulates signed volume delta (buy volume minus sell volume at sub-candle resolution) over N candles. Positive accumulation = net buying pressure. Negative = net selling pressure.

## Mathematical Formulation

### Current Version

```
For each candle i:
  delta_i = buy_volume_i - sell_volume_i  (at sub-candle tick/trade level)

signed_delta_sum(N) = sum(delta_i) for i in [current - N + 1, current]
```

### Original Version

*(This is the original — no prior version exists)*

## Parameters

| Parameter | Value | Justification |
|-----------|-------|---------------|
| Accumulation window N | TBD | Must span enough candles to distinguish sustained participation from noise, but not so many that the signal becomes stale |

## Inputs

- Sub-candle trade data (tick-level buy/sell classification) or exchange-reported delta

## Dependencies

- Requires tick-level data or exchange-provided delta. Availability is instrument-scoped.

## Known Edge Cases

- Instruments with poor tick data classification (e.g., crypto with no trade-level buy/sell tagging)
- Low-volume periods where delta is dominated by single large trades
- Pre-market / post-market sessions with thin liquidity

## Noise Sensitivity

Moderate. Single large institutional trades can spike the accumulator. The Normalised Delta Accumulation (z-score version) is intended to address this.

## Regime Dependency

Delta signals are more informative in liquid, actively-traded instruments. In thin markets, delta becomes noisy and potentially misleading.
