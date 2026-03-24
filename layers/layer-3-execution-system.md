# Layer 3 — Execution System

## Purpose

The most complex layer. Two sub-phases that together convert a validated, directioned pivot into an executed trade with appropriate risk management.

## Output

Phase A: Risk tier + entry type.
Phase B: Executed orders with stop-loss and take-profit management.

---

## Phase A — Regime Classification

### Purpose

Classify risk of getting stopped out based on market microstructure and indicator-derived characteristics at the pivot. Map risk tier to entry type.

### Logic Summary

Assess risk through:
1. **Volatility** — current volatility state relative to recent history
2. **Wick behaviour** — wick-to-body ratio at the pivot (rejection signals)
3. **Market structure** — stagnating, choppy, trending, or other classified states
4. **Structural level proximity** — distance to prior-session levels

These inputs combine to produce a five-tier risk classification:

| Risk Tier | Entry Type | Rationale |
|-----------|-----------|-----------|
| Extreme | No entry | Conditions too adverse — high probability of stop-out |
| High | Wick-limited | Enter only on confirmed wick rejection — need extra evidence |
| Medium | Breakout | Unsure if trend continues — wait for breakout confirmation, no limit/market orders |
| Minor | Limit-breakout | Trend likely continues — place limit order at better price than expected breakout level |
| Low | Immediate market order | Conditions are clean — enter immediately |

### Component Indicators (Phase A)

| Indicator | Role | Output Type |
|-----------|------|-------------|
| Wick-to-Body Ratio | Rejection behaviour at pivot | Continuous (ratio) |
| Market Structure Classifier | Classify price action as stagnating/choppy/trending/etc. | Categorical |
| Regime Classifier | Combine all inputs into risk tier | Categorical (5-tier) |
| Prior-Session Levels | Structural proximity for risk assessment | Price levels |

### Market Structure Classification

Classifies current price action character. Serves dual purpose:
1. **Risk input** — stagnation and choppiness increase stop-out risk
2. **Breakout detection trigger** — state transitions (e.g., "no longer stagnating") feed into Phase B's breakout logic

Classifications (initial, may expand):
- **Trending** — clean directional movement
- **Choppy** — directionless oscillation with no clear structure
- **Stagnating** — low-volatility compression, price going nowhere

*Breakout detection is state-transition based, not price-level based. The classifier must update near-real-time and state transitions must be well-defined to avoid oscillation at boundaries.*

---

## Phase B — Order Construction

### Purpose

Execute orders based on Phase A's risk tier and entry type. Each entry type has its own execution logic — they differ in at least one way.

### Logic Per Entry Type

**Extreme (No entry):**
- No action.

**High (Wick-limited):**
- Monitor for wick rejection at pivot.
- Enter only on confirmed rejection pattern.
- Execution details TBD.

**Medium (Breakout):**
- Wait for breakout confirmation (state-transition based via Market Structure Classifier).
- No limit orders, no market orders until breakout confirmed.
- On confirmation → execute entry.

**Minor (Limit-breakout):**
- Place limit order at a price better than the expected breakout level.
- If breakout occurs, order fills at better price.
- If no breakout, order is not filled — no loss.
- Limit price placement logic TBD (needs to be informed by structure).

**Low (Immediate market order):**
- Execute market order immediately.

### Trade Structure (All Entry Types)

Two contracts entered at the same price:

**Contract 1 — Harvester:**
- Shorter take-profit, dynamically calculated (e.g., ATR-derived)
- Higher win rate
- Purpose: capture immediate continuation, pay for the trade, reduce risk on Contract 2

**Contract 2 — Runner:**
- Held longer via combination of trailing mechanism + signal-based exit
- Signal-based exit examples: Layer 2 directional flip, regime change
- Lower win rate, larger potential payoff
- Purpose: capture tail moves

### Stop-Loss

TBD. Will be implemented in this phase. Likely dynamic, informed by volatility and pivot structure.

### Order Lifecycle Management

Phase B manages the full lifecycle:
- Order placement
- Condition monitoring (for breakout and wick-limited types)
- Fill detection
- Stop-loss management
- Take-profit management (both contracts)
- Trailing logic (Contract 2)
- Signal-based exit monitoring (Contract 2)
- Order cancellation if conditions change

---

## Development Approach

Initially narrow scope to a **single risk classification and its corresponding execution logic**. Build the easiest entry type that can be done correctly and profitably first. Expand to additional entry types incrementally.

If trading a given risk classification is unprofitable, do not trade in that regime — simply exclude it.

## Open Design Questions

1. Which entry type to build first? (Low/immediate market order is simplest mechanically but may not be the most profitable to validate first.)
2. How does the Market Structure Classifier define state boundaries to avoid oscillation?
3. Limit-breakout: how is the limit price determined? What structure informs "better than expected breakout level"?
4. Wick-limited: what constitutes "confirmed wick rejection"? How many candles, what ratio threshold?
5. Trailing mechanism for Contract 2: fixed trail (e.g., ATR-based), or structure-based (trail behind swing points)?
6. How do trailing and signal-based exits interact for Contract 2? Does one override the other, or are they independent triggers (first one hit = exit)?
7. Volatility state sub-indicator: ATR percentile, realised vol z-score, or something else?

## Status

Not started. Prior-Session Levels exist. All other components need to be built from scratch.

## Layer-Level Indicator

The final Layer 3 indicator is a single callable function that takes a qualified pivot and direction, classifies regime risk, determines entry type, and executes orders. Internally split into two phases but externally it's one call.

**Location:** TBD

**Calls, in order:**

*Phase A:*
1. [[market-structure-classifier|Market Structure Classifier]] → current structure state
2. [[wick-to-body-ratio|Wick-to-Body Ratio]] → rejection behaviour at pivot
3. [[prior-session-levels|Prior-Session Levels]] → structural proximity
4. Volatility state (sub-indicator TBD)
5. [[regime-classifier|Regime Classifier]] → risk tier → entry type

*Phase B:*
6. Entry execution per entry type logic
7. [[breakout-detector|Breakout Detector]] (for breakout/limit-breakout types)
8. Stop-loss, take-profit, order lifecycle management

**Input:** Qualified pivot from Layer 1, direction from Layer 2, price/volume data
**Output:** Executed orders (or no entry)
