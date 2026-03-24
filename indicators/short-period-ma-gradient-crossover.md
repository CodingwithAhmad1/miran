# Indicator: Short-Period MA Gradient Crossover

## Identity

- **Serves:** TBD — not yet assigned to a specific filter layer
- **Role:** TBD
- **Status:** Existing (functional)

## What It Measures

Momentum shifts detected via gradient crossover of three closely-spaced short-period moving averages. When the shortest MA gradient crosses above/below the others, it signals a shift in near-term momentum direction.

## Mathematical Formulation

### Current Version

Three MAs with closely-spaced periods. Crossover detected on their gradients (slopes), not their values.

```
ma_short, ma_mid, ma_long = MA(price, P1), MA(price, P2), MA(price, P3)
  where P1 < P2 < P3 and all are short-period

gradient_i = ma_i(current) - ma_i(lookback)

crossover_signal = sign change in (gradient_short - gradient_mid) or (gradient_short - gradient_long)
```

### Original Version

*(This is the original — no prior version exists)*

## Parameters

| Parameter | Value | Justification |
|-----------|-------|---------------|
| P1, P2, P3 (MA periods) | TBD | Must be close enough to detect fine momentum shifts, far enough apart to not be redundant |
| MA type | TBD | SMA, EMA, WMA — choice affects lag and smoothing |
| Gradient lookback | TBD | How far back to measure slope |

## Inputs

- Price (close)

## Dependencies

None.

## Known Edge Cases

*(To be populated)*

## Noise Sensitivity

*(To be assessed — closely-spaced MAs may produce frequent false crossovers in choppy conditions)*

## Regime Dependency

*(To be assessed)*
