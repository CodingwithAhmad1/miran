# Indicator: Wick-to-Body Ratio

## Identity

- **Serves:** Layer 3 (Regime Classification)
- **Role:** Component — wick behaviour input for risk tier classification
- **Status:** To build from scratch

## What It Measures

The ratio of wick length to body length across a short window of candles at the pivot location. Long wicks at the pivot suggest price rejection (participants defending a level). Short or absent wicks suggest momentum may carry through the level.

## Mathematical Formulation

### Target Version

```
For each candle i in window at pivot:
  body_i = |close_i - open_i|
  upper_wick_i = high_i - max(open_i, close_i)
  lower_wick_i = min(open_i, close_i) - low_i
  relevant_wick_i = upper_wick_i if pivot is upper extreme, lower_wick_i if lower extreme

  wick_to_body_i = relevant_wick_i / body_i  (handle body = 0 case)

wick_to_body_score = aggregate(wick_to_body_i) over window
  # aggregation: mean, median, or max — TBD
```

### Original Version

*(No prior version — building from scratch)*

## Parameters

| Parameter | Value | Justification |
|-----------|-------|---------------|
| Window size | TBD | Number of candles at pivot to assess — too few = single-candle noise, too many = dilutes the pivot-specific signal |
| Aggregation method | TBD | Mean, median, or max across the window |
| Doji handling | TBD | How to treat body ≈ 0 candles (pure wick) — infinite ratio, cap, or special classification |

## Inputs

- OHLC data at pivot location
- Pivot direction (upper/lower extreme — determines which wick is "relevant")

## Dependencies

- Layer 1 output (pivot location and direction)

## Known Edge Cases

- Doji candles (body ≈ 0): ratio goes to infinity. Need a capping or classification rule.
- Very small bodies with proportionally normal wicks: ratio is large but may not indicate rejection.
- Timeframe sensitivity: wick character on 1m vs 5m candles may tell different stories at the same pivot.

## Open Design Questions

1. **Which timeframe's candles?** The strategy operates sub-15m. Wick behaviour on 1m candles is noisy. 5m candles may be more informative. Or aggregate across both?
2. **Directional wick only?** Should we only measure the wick pointing away from the trend (the rejection wick), or both wicks?
3. **Body = 0 handling:** Cap the ratio? Classify doji separately? Exclude from average?

## Noise Sensitivity

*(To be assessed)*

## Regime Dependency

*(To be assessed)*
