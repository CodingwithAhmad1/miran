# Indicator: Pivot Detector

## Identity

- **Serves:** Layer 1 (Meaningful Pivots) — Step 1
- **Role:** Standalone — identifies all structural pivot candidates before filtering
- **Status:** To build from scratch

## What It Measures

Local highs and lows in price — structural swing points where price reversed direction, even temporarily. This is a geometric/structural operation. It casts a wide net; the subsequent Preceding Trend Classifier handles quality filtering.

## Mathematical Formulation

### Target Version

TBD. Candidates include:
- Simple N-bar high/low (price is highest/lowest within N bars on either side)
- Percentile-based detection (price at extreme percentile within rolling window — existing Percentile Price Position indicator)
- Fractal-based detection

The approach will be developed in detail later. The key requirement is that it identifies all genuine swing points without excessive false positives from noise.

### Original Version

*(No prior version — building from scratch. Percentile Price Position exists as a candidate mechanism.)*

## Parameters

| Parameter | Value | Justification |
|-----------|-------|---------------|
| TBD | TBD | Depends on chosen detection method |

## Inputs

- Price data (OHLC)

## Dependencies

- Potentially Percentile Price Position (if used as detection mechanism)

## Known Edge Cases

- **Flat markets:** Many small pivots that are just noise
- **V-reversals:** Pivot is a single bar — detection lag means we identify it late
- **Double tops/bottoms:** Two pivots at nearly the same price — treat as one or two?

## Downstream Impact

This indicator's output defines the window boundaries for Operation 2 (Trend Measurer). Each consecutive pair of pivots becomes a trend window. Noisy pivot detection = noisy trend windows = unreliable measurements downstream. The Pivot Detector can afford to be somewhat liberal (Trend Qualifier handles filtering), but excessively noisy detection propagates through the entire layer.

## Open Design Questions

1. Detection method selection — will be developed later
2. How much lag is acceptable? Pivot can only be confirmed after price moves away from it.
3. Minimum separation between pivots — spatial (price) and temporal (bars)?

## Noise Sensitivity

Highly dependent on detection method. A wide net will catch noise; too narrow misses real pivots. The Preceding Trend Classifier downstream handles quality filtering, so this indicator can afford to be somewhat liberal.

## Regime Dependency

Pivot frequency varies dramatically by regime — trending markets produce fewer pivots, choppy markets produce many. This is expected and handled by Layer 1's filtering, not by the detector itself.
