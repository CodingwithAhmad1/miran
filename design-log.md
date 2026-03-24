# Miran — Design Log

Chronological record of design decisions, rejected approaches, and key observations. Each entry tagged by relevant layer/indicator.

---

## Entry 001 — Knowledge Base Initialisation

**Date:** 2026-03-24
**Scope:** Architecture

Established the persistent knowledge base structure for the Miran strategy. Documents organised as:
- `strategy-overview.md` — top-level reference
- `design-log.md` — this file, chronological decisions
- `layers/` — one document per filter layer, describing how component indicators compose into the layer output
- `indicators/` — one document per indicator, covering formulation, parameters, edge cases, development status

Key structural decision: layer documents describe the *composition logic* — how individual indicator outputs combine to produce the single layer output. Indicator documents describe *what each indicator measures and how*. This separation matters because some indicators serve multiple layers, and the composition logic is a layer-level concern.

---

## Entry 002 — Major Architectural Revision

**Date:** 2026-03-24
**Scope:** All layers

Significant reframe of the strategy architecture based on detailed specification from the strategist. Key changes:

**Naming:** Filters renamed to Layers. Filter 1 → Layer 1 "Meaningful Pivots", Filter 2 → Layer 2 "Directional Sentiment", Filter 3 → Layer 3 "Execution System".

**Layer 1 reframe:** The quality gate shifted from assessing the *counter-move into the pivot* to assessing the *preceding trend that led to the pivot*. The logic is now: detect all pivots → measure the preceding trend's magnitude, duration, volatility → classify the trend type → apply type-specific thresholds → discard pivots whose preceding trend is insufficient. Counter-Move Quality Classifier replaced by Preceding Trend Classifier. New Pivot Detector indicator added.

**Layer 2 reframe:** Emphasised as the most modular/swappable layer. Clean interface contract: input is data + pivot, output is direction or discard. Everything behind the interface is replaceable. Binary output for now, potentially confidence-scored later.

**Layer 3 expansion:** Now called "Execution System" with two sub-phases:
- Phase A: Regime Classification — risk tier classification (five tiers, not four)
- Phase B: Order Construction — entry execution, stop-loss, take-profit, order lifecycle management

Previously Filter 3 and a new Filter 4 — merged because they serve one purpose (effective order execution) and Phase B depends entirely on Phase A.

**Five risk tiers established:** Extreme (no entry), High (wick-limited), Medium (breakout), Minor (limit-breakout), Low (immediate market order). The "minor" tier and "limit-breakout" entry type are novel — placing a limit order at a better price than the expected breakout level.

**Two-contract trade structure defined:** Contract 1 (harvester) with short dynamic TP for high win rate. Contract 2 (runner) with trailing + signal-based exit for tail capture. Combined structure targets higher Sharpe by compressing loss distribution.

**New indicators identified:** Pivot Detector, Market Structure Classifier, Breakout Detector. The Breakout Detector uses state-transition logic (market structure changes) rather than arbitrary price levels.

**Development approach clarified:** Build Layer 3 entry types one at a time, starting with the easiest profitable one. Unprofitable risk tiers get excluded rather than forced.

**Decision: Preceding trend typology.** Multiple trend types exist (sharp/fast, slow grind, staircase, etc.), each with its own threshold set. Explicitly discard: choppy small moves and violent single-candle spikes. Full typology not yet enumerated.

---
