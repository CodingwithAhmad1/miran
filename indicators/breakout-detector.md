# Indicator: Breakout Detector

## Identity

- **Serves:** Layer 3 (Execution System) — Phase B (order construction trigger for breakout and limit-breakout entry types)
- **Role:** Component — provides the execution trigger for conditional entries
- **Status:** To build from scratch

## What It Measures

Whether a breakout from the current market structure has occurred. This is state-transition based, not price-level based. The trigger is a change in market structure classification (e.g., "was stagnating, now trending") combined with additional confirming conditions.

## Mathematical Formulation

### Target Version

```
Inputs:
  market_structure_current = Market Structure Classifier output (current bar)
  market_structure_previous = Market Structure Classifier output (previous state)
  additional_conditions = TBD (e.g., volume confirmation, delta direction)

breakout_detected = (
  state_transition_occurred(market_structure_previous, market_structure_current)
  AND transition_is_bullish_or_bearish(consistent with Layer 2 direction)
  AND additional_conditions_met
)
```

The key insight: breakout is defined as "the market structure has changed" rather than "price crossed a specific level." This avoids arbitrary level-setting and instead relies on the structural state of the market.

### Original Version

*(No prior version — building from scratch)*

## Parameters

| Parameter | Value | Justification |
|-----------|-------|---------------|
| Required state transition | TBD | Which transitions constitute a breakout (e.g., stagnating → trending, but not choppy → trending?) |
| Additional conditions | TBD | What else must be true beyond the state transition |
| Confirmation delay | TBD | How many bars must the new state persist to confirm it's not a false transition |

## Inputs

- Market Structure Classifier output (current and historical states)
- Layer 2 directional output (to confirm breakout direction aligns)
- Additional confirming data TBD

## Dependencies

- Market Structure Classifier (primary input)
- Layer 2 output (directional alignment check)

## Known Edge Cases

- **False transitions:** Market Structure Classifier briefly flips to "trending" then reverts. Without confirmation delay, this triggers a false breakout.
- **Slow transitions:** Market gradually shifts from stagnating to trending — when exactly does the breakout "occur"? The discrete state change may lag the actual breakout.
- **Multiple transitions:** Stagnating → choppy → trending — is the breakout at the first transition or the second?

## Open Design Questions

1. Which state transitions count as breakouts?
2. How much confirmation delay before acting on a transition?
3. What additional conditions beyond the state transition are needed?
4. How does this interact with the limit-breakout entry type? For limit-breakout, the order is placed *before* breakout confirmation — so this detector's role is different (it may serve as the cancellation trigger if breakout doesn't occur within a time window).

## Noise Sensitivity

Directly inherits noise characteristics from the Market Structure Classifier. If the classifier oscillates, the breakout detector fires false signals.

## Regime Dependency

*(To be assessed)*
