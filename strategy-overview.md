# Miran — Strategy Overview

## What It Is

Systematic intraday trend-continuation strategy on sub-15-minute timeframes. Three sequential eliminatory layers. Any layer failure discards the setup completely.

## Layer Architecture

| Layer | Name | Purpose | Output | Status |
|-------|------|---------|--------|--------|
| Layer 1 | Meaningful Pivots | Detect all pivots, then discard those whose preceding trend lacks sufficient characteristics | Binary (meaningful pivot / discard) | In development |
| Layer 2 | Directional Sentiment | Gauge trend direction via technical analysis. Modular — designed to be swappable across instruments or strategy iterations | Binary (long / short), potentially evolving to confidence-scored | Not started |
| Layer 3 | Execution System | Two sub-phases: (A) Regime Classification — classify risk at pivot and determine entry type; (B) Order Construction — execute entry, manage stop-loss, take-profit, and order lifecycle | Risk tier → entry type → executed orders | Not started |

## Layer Sequencing and Dependencies

```
Layer 1 (Meaningful Pivots) → pass/discard
  ↓ pass
Layer 2 (Directional Sentiment) → long/short/discard
  ↓ direction confirmed
Layer 3 (Execution System)
  Phase A: Regime Classification → risk tier → entry type
  Phase B: Order Construction → order placement and management
```

Layers 1 and 2 are fully independent of each other in implementation (though sequential in execution). Layer 3 depends on outputs from both Layer 1 (pivot location) and Layer 2 (direction), plus its own internal classification.

## Modularity

- **Layer 1 (Meaningful Pivots):** Relatively ticker/market agnostic. Pivots and trend characteristics exist in all instruments. Framework is universal; parameters may be instrument-scoped.
- **Layer 2 (Directional Sentiment):** The most swappable layer. Can be replaced with any directional indicator (MACD, different MA system, etc.) without affecting the rest of the strategy. Designed as a clean interface.
- **Layer 3 (Execution System):** Framework is relatively agnostic (all instruments have varying risk regimes and market structures). Parameters and fine-tuning are instrument-scoped, particularly wick behaviour and microstructure characteristics.

## Trade Structure

Two-contract entry at the same price:
- **Contract 1 (harvester):** Shorter, dynamically-calculated take-profit (e.g., ATR-derived). Higher win rate. Purpose: capture immediate continuation, pay for the trade, reduce risk on Contract 2.
- **Contract 2 (runner):** Held longer via trailing mechanism + signal-based exit (e.g., Layer 2 directional flip or regime change). Lower win rate, larger potential payoff. Purpose: capture tail moves.

The combined structure targets a higher Sharpe ratio by compressing the loss distribution (Contract 1's frequent small wins) while preserving exposure to larger moves (Contract 2).

*Note: Two-contract optimisation comes later. Avoid overfitting the split.*

## Entry Types (Layer 3 Output)

| Risk Tier | Entry Type | Description |
|-----------|-----------|-------------|
| Extreme | No entry | Discard — conditions too adverse |
| High | Wick-limited | Entry only on specific wick rejection behaviour |
| Medium | Breakout | Conditional entry on breakout confirmation — no limit orders, no market orders |
| Minor | Limit-breakout | Limit order placed at a better price than the expected breakout level. We're entering anyway; might as well get a better fill. |
| Low | Immediate market order | Conditions are clean — enter immediately |

## Development Approach

- Build in order: Layer 1 → Layer 2 → Layer 3.
- Within Layer 3, initially narrow scope to a single risk classification and its corresponding execution logic. Build the easiest one that can be done correctly and profitably first.
- If a given risk classification proves unprofitable, ignore it and don't trade in that regime.
- Layer 3 Order Construction sub-phase differs per entry type in at least one way. Each entry type gets its own execution logic.

## Working Principles

- Framework is ticker/market agnostic. Parameters may be instrument-scoped where necessary.
- Every parameter must have a defensible reason to exist. No free parameters.
- Indicators are validated against their intended measurement, not just backtest performance.

## Existing Indicators (Available)

| Indicator | Functional Description | Used By |
|-----------|----------------------|---------|
| Percentile Price Position | Price positioning over a rolling lookback window | Layer 1 |
| MTF Gradient WMA | Multi-timeframe gradient-based weighted moving averages across 1m, 5m, 15m | Layer 2 |
| Prior-Session Levels | Structural price levels derived from prior-day range | Layer 2, Layer 3 |
| Signed Delta Accumulator | Sub-candle signed volume delta accumulated over N candles, proxying directional volume pressure | Layer 1, Layer 2 |
| Short-Period MA Gradient Crossover | Three closely-spaced MA lengths with gradient crossover | TBD |

## Indicators Requiring Evolution

| Indicator | Current State | Target State | Used By |
|-----------|--------------|-------------|---------|
| Normalised Delta Accumulation | Raw signed delta sum | Rolling z-score applied for cross-session/cross-instrument comparability | Layer 1, Layer 2 |
| Volume-Weighted Gradient | MA gradient signal only | Gradient scaled by concurrent delta magnitude — quality-filtered momentum | Layer 2 |

## Indicators To Build From Scratch

| Indicator | Purpose | Used By |
|-----------|---------|---------|
| Pivot Detector | Identify all local highs/lows (structural pivots) in price | Layer 1 (Op 1) |
| Trend Measurer | Compute magnitude, duration, volatility on candles between consecutive pivots | Layer 1 (Op 2) |
| Trend Qualifier | Classify trend type and apply type-specific thresholds | Layer 1 (Op 3) |
| Wick-to-Body Ratio | Real-time measurement across short candle window at pivot | Layer 3 |
| Regime Classifier | Multivariate risk tier classification — core Layer 3 Phase A output | Layer 3 |
| Market Structure Classifier | Classify current price action as stagnating, choppy, trending, etc. Used for risk gauging and breakout detection | Layer 3 |
| Breakout Detector | State-transition based detection (e.g., "no longer stagnating" + additional conditions), not arbitrary price-level based | Layer 3 |

## Cross-Layer Observations

- Layer 2 is intentionally thin and swappable. Avoid building complex dependencies from other layers into Layer 2's internals.
- Layer 3 is the most complex layer — regime classification, market structure classification, breakout detection, order construction, stop/TP management all live here. Internal modularity within Layer 3 is critical.
- Market structure classification serves dual purpose: risk input for Phase A and breakout trigger for Phase B. This is a shared dependency within Layer 3.

## Open Questions

- How are trend "types" defined in Layer 1? Need to enumerate the typology (sharp/fast, slow grind, staircase, etc.) and what makes each valid vs. what disqualifies (choppy small moves, violent single-candle spikes).
- Breakout detection as state transition: what defines the state boundaries? Classifier oscillation at boundaries is a design risk.
- Second contract exit: how do trailing and signal-based exits interact? Does signal override trail, or are they independent triggers?
- Which risk tier / entry type to build first for Layer 3 narrowed scope?
