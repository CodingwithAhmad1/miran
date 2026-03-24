# Layer 3 — Execution System — Working Log

Development evolution trail for Layer 3. Captures iterations, rejected approaches, observations, and reasoning specific to this layer's development. Read this before working on Layer 3 to understand how we got to the current state.

---

## Current State Summary

Layer 3 is the most complex layer and the primary focus of current design effort. Two sub-phases:

**Phase A — Regime Classification:**
Classifies risk of getting stopped out based on market microstructure and indicator-derived characteristics at the pivot. Outputs one of five risk tiers, each mapping to a specific entry type:
- Extreme → no entry
- High → wick-limited
- Medium → breakout
- Minor → limit-breakout (limit order at better price than expected breakout level)
- Low → immediate market order

Inputs: volatility state, wick behaviour, market structure classification, structural level proximity.

**Phase B — Order Construction:**
Executes orders based on Phase A's output. Each entry type has its own execution logic. Also manages stop-loss, take-profit, and order lifecycle. Two-contract structure: Contract 1 (harvester, short dynamic TP, high win rate) and Contract 2 (runner, trailing + signal-based exit, lower win rate, larger payoff).

**Key components:**
- Regime Classifier — core Phase A output (not yet built)
- Market Structure Classifier — trending/choppy/stagnating classification, serves both risk input and breakout detection (not yet built)
- Wick-to-Body Ratio — rejection behaviour at pivot (not yet built)
- Breakout Detector — state-transition based, not price-level based (not yet built)
- Prior-Session Levels (exists)
- Volatility state sub-indicator (not yet specified)

**Critical design concerns:**
- Market Structure Classifier has a stability vs. responsiveness tension: Phase A needs stability (risk assessment shouldn't flicker), Phase B needs responsiveness (breakout detection relies on timely state transitions)
- Breakout detection is conditional/state-transition based, not arbitrary price levels — avoids "icky" arbitrary boundaries
- Classifier oscillation at state boundaries would cause false breakout signals — needs hysteresis or smoothing
- Existing indicator code will be shared that may be used in this layer

**What's been decided:**
- Originally two separate layers (Filter 3 and Filter 4), merged because they serve one purpose: effective order execution. Phase B depends entirely on Phase A.
- Five risk tiers, not four. The "minor" tier with limit-breakout entry is novel.
- Development will start with a single risk classification and its execution logic — the easiest one to get correct and profitable. Expand incrementally.
- Unprofitable risk classifications get excluded, not forced.
- Each entry type's Phase B logic differs in at least one way.
- Stop-loss and take-profit are part of this layer. Contract 1 TP is dynamically calculated (ATR-derived or similar). Contract 2 uses trailing + signal-based exit combination.

**What's NOT been decided:**
- Which entry type to build first
- Volatility state sub-indicator specification
- Market structure state definitions and transition boundaries
- Limit-breakout price placement logic
- Wick-limited confirmation criteria
- Stop-loss methodology
- Trailing mechanism specifics for Contract 2
- How trailing and signal-based exits interact (independent triggers? priority?)

---

## Iterations and Evolution

*(New entries added as development progresses. Each entry should include: what was tried, what the outcome was, why it was kept/rejected, and what it taught us.)*

---
