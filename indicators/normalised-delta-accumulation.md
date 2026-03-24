# Indicator: Normalised Delta Accumulation

## Identity

- **Serves:** Layer 1 (Pivot Identification — cross-session comparable delta), Layer 2 (Directional Bias — delta exhaustion detection)
- **Role:** Component — evolved version of Signed Delta Accumulator for cross-context comparability
- **Status:** Needs evolution (from raw sum to z-score normalisation)

## What It Measures

The same directional volume pressure as the Signed Delta Accumulator, but normalised via rolling z-score so that readings are comparable across sessions (varying volume profiles) and across instruments (different absolute delta scales).

## Mathematical Formulation

### Current Version (Pre-Evolution)

Raw signed delta sum — identical to Signed Delta Accumulator. Not normalised.

### Target Version

```
raw_delta_sum = signed_delta_accumulator(N)

rolling_mean = mean(raw_delta_sum) over lookback window M
rolling_std = std(raw_delta_sum) over lookback window M

normalised_delta = (raw_delta_sum - rolling_mean) / rolling_std
```

Output is a z-score: 0 = average delta, positive = unusually strong buying, negative = unusually strong selling. Magnitude indicates how extreme relative to recent history.

## Parameters

| Parameter | Value | Justification |
|-----------|-------|---------------|
| Accumulation window N | TBD | Inherited from Signed Delta Accumulator |
| Z-score lookback M | TBD | Must be long enough to establish a stable distribution but short enough to adapt to changing volume regimes |

## Inputs

- Output of Signed Delta Accumulator

## Dependencies

- Signed Delta Accumulator

## Known Edge Cases

- Session opens: z-score lookback may span overnight gap where volume characteristics differ dramatically
- Regime shifts in volume (e.g., news event): the rolling distribution may not adapt fast enough
- Division by zero: rolling_std = 0 in dead markets

## Noise Sensitivity

Lower than raw delta — the normalisation absorbs baseline volume variation. But the z-score lookback itself becomes a noise source if M is too short.

## Regime Dependency

The z-score normalisation is specifically designed to reduce regime dependency. However, if M is too short, the normalisation window itself becomes regime-sensitive.

## Evolution Notes

The core question is the z-score lookback window M. Too short and the normalisation is unstable. Too long and it fails to adapt to intraday volume regime shifts (e.g., London open vs. US open vs. afternoon lull). May need to consider session-aware windowing rather than a flat rolling window.
