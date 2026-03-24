# Layer 2 — Directional Sentiment

## Purpose

Gauge the trend direction using technical analysis and return a directional signal. This is the most modular layer — designed to be swapped out cleanly for different instruments or strategy iterations without affecting Layers 1 or 3.

## Output

Binary: long / short. Potentially evolving to confidence-scored (e.g., percentage confidence, positive or negative).

For now: binary only.

## Logic Summary

Technical indicator-based directional assessment. The specific indicators and composition logic are already partially defined (MTF Gradient WMA, delta exhaustion, structural level proximity) but the key design principle is that this entire layer is a replaceable module.

The interface contract is simple:
- **Input:** Price/volume data, pivot location from Layer 1
- **Output:** Direction (long/short) or discard

Anything behind that interface can change without ripple effects.

## Component Indicators

| Indicator | Role in This Layer | Output Type |
|-----------|-------------------|-------------|
| MTF Gradient WMA | Gradient alignment across timeframes — binary prerequisite | Binary (aligned / misaligned) |
| Signed Delta Accumulator | Delta exhaustion detection at pivot | Continuous |
| Normalised Delta Accumulation | Cross-session comparable delta for exhaustion detection | Continuous (z-score) |
| Volume-Weighted Gradient | Quality-filtered momentum signal | Continuous |
| Prior-Session Levels | Structural reference — confidence modulator | Price levels |

*These are the current indicator set. The entire set can be swapped for alternatives (e.g., MACD, different MA system) as long as the interface contract is maintained.*

## Composition Logic

Current design (subject to replacement):

```
gradient_aligned = all MTF gradients directionally consistent
IF NOT gradient_aligned → discard

delta_exhausting = delta flattening OR reversing at pivot extreme
IF NOT delta_exhausting → discard

structural_proximity = distance to nearest prior-session level
confidence_modulation = f(structural_proximity)  # future use when output becomes confidence-scored

output = long / short (based on gradient direction)
```

## Design Principles

- **Swappability over optimality.** A slightly less optimal but cleanly replaceable Layer 2 is preferable to one that's deeply entangled with Layers 1 and 3.
- **No complex dependencies.** Layer 2 should not require internal knowledge of how Layer 1 detects pivots or how Layer 3 classifies regimes.
- **Clean interface.** Input: data + pivot location. Output: direction or discard. Nothing else crosses the boundary.

## Edge Cases and Failure Modes

- **Conflicting signals:** Gradient says long, delta says exhaustion of buying. Resolution logic needed.
- **Flat/directionless markets:** Layer 2 should discard rather than force a direction.

## Status

Not started. MTF Gradient WMA, Signed Delta Accumulator, and Prior-Session Levels exist. Normalised Delta Accumulation and Volume-Weighted Gradient need evolution.

## Layer-Level Indicator

The final Layer 2 indicator is a single callable function that takes price/volume data and a pivot location, and returns a directional signal. This entire layer is designed to be swappable — the layer-level indicator is the interface boundary. Anything behind it can change.

**Location:** TBD

**Calls:** Current component set (subject to replacement):
1. [[mtf-gradient-wma|MTF Gradient WMA]] → alignment check
2. [[signed-delta-accumulator|Signed Delta Accumulator]] / [[normalised-delta-accumulation|Normalised Delta Accumulation]] → delta exhaustion
3. [[prior-session-levels|Prior-Session Levels]] → confidence modulation

**Input:** Price/volume data, qualified pivot from Layer 1
**Output:** Direction (long/short) or discard
