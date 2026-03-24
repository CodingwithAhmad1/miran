# Indicator: Regime Classifier

## Identity

- **Serves:** Layer 3 (Execution System) — Phase A. This IS the core Phase A output.
- **Role:** Composite — combines multiple inputs into the final five-tier risk classification
- **Status:** To build from scratch

## What It Measures

Risk of getting stopped out, based on market microstructure and indicator-derived characteristics at the pivot. Outputs a five-tier risk classification that maps directly to an entry type.

## Mathematical Formulation

### Target Version

```
Inputs:
  volatility_state = f(current_vol, recent_vol_distribution)  # sub-indicator TBD
  wick_signal = wick_to_body_ratio at pivot                    # from Wick-to-Body Ratio
  structural_distance = distance(price, nearest_prior_session_level)  # from Prior-Session Levels
  market_structure = Market Structure Classifier output         # trending / choppy / stagnating
  
Classification → one of:
  extreme  → no entry
  high     → wick-limited entry
  medium   → breakout entry
  minor    → limit-breakout entry
  low      → immediate market order

Classification method TBD: rule-based decision tree, weighted score, or lookup table.
```

### Original Version

*(No prior version — building from scratch. Expanded from four tiers to five since initial spec.)*

## Parameters

*(TBD — depends on classification approach)*

## Inputs

- Volatility state (sub-indicator to be specified)
- Wick-to-Body Ratio output
- Prior-Session Levels (distance calculation)
- Market Structure Classifier output

## Dependencies

- Wick-to-Body Ratio
- Prior-Session Levels
- Market Structure Classifier
- Volatility state indicator (not yet specified)

## Risk Tier → Entry Type Mapping

| Tier | Entry Type | Rationale |
|------|-----------|-----------|
| Extreme | No entry | Conditions too adverse — high stop-out probability |
| High | Wick-limited | Need extra confirmation via wick rejection before entering |
| Medium | Breakout | Unsure of continuation — wait for breakout, no pre-positioned orders |
| Minor | Limit-breakout | Continuation likely — position limit order at better price than expected breakout |
| Low | Immediate market order | Clean conditions — enter now |

## Open Design Questions

1. Classification method: rule-based vs. score-based vs. lookup? Interaction effects between inputs suggest simple additive scoring may be insufficient.
2. Tier boundary calibration: fixed, adaptive, or instrument-scoped?
3. Volatility state sub-indicator specification needed.
4. How much weight does market structure carry vs. wick behaviour vs. volatility?
5. Should the classifier output a continuous risk score that is then bucketed into tiers, or directly classify into tiers?

## Noise Sensitivity

Inherits noise from all component inputs. The most critical is Market Structure Classifier — if it oscillates, risk tier oscillates.

## Regime Dependency

This indicator is *designed* to handle regime variation — that's its entire purpose. The risk is circular dependency: it classifies regimes using inputs that may themselves be regime-dependent.
