# Indicator: Prior-Session Structural Levels

## Identity

- **Serves:** Layer 2 (Directional Bias — confidence modulator), Layer 3 (Regime Classification — risk input)
- **Role:** Standalone — provides structural reference levels
- **Status:** Existing (functional)

## What It Measures

Key price levels derived from the prior trading session's range. These act as structural reference points where price is more likely to encounter reaction (support/resistance, liquidity clusters).

## Mathematical Formulation

### Current Version

Derived from prior-day range. Specific derivation method TBD (could be high/low/close, VWAP levels, or volume profile nodes).

### Original Version

*(This is the original — no prior version exists)*

## Parameters

| Parameter | Value | Justification |
|-----------|-------|---------------|
| Session definition | TBD | What constitutes "prior session" — RTH only, ETH included, etc. |
| Level derivation method | TBD | Which levels from the prior session are extracted |

## Inputs

- Prior-session OHLCV data

## Dependencies

None.

## Known Edge Cases

- Monday opens (prior session = Friday — gap risk)
- Holiday-shortened sessions
- Overnight gaps that make prior levels irrelevant

## Noise Sensitivity

Low — these are fixed reference levels, not computed in real-time.

## Regime Dependency

Structural levels are more respected in range-bound/mean-reverting regimes. In strong trending regimes, price may blow through them without reaction. This is accounted for by the fact that this indicator modulates confidence rather than gates.
