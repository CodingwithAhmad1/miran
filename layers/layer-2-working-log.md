# Layer 2 — Directional Sentiment — Working Log

Development evolution trail for Layer 2. Captures iterations, rejected approaches, observations, and reasoning specific to this layer's development. Read this before working on Layer 2 to understand how we got to the current state.

---

## Current State Summary

Layer 2 gauges trend direction and returns a binary signal (long/short). This is the most modular layer — designed to be swapped out cleanly. Any standard directional indicator can fill this role.

**Key components:**
- MTF Gradient WMA (exists)
- Signed Delta Accumulator (exists)
- Normalised Delta Accumulation (needs evolution)
- Volume-Weighted Gradient (needs evolution)
- Prior-Session Levels (exists)

**What's been decided:**
- This layer requires the least design effort. Many existing indicators already serve this function.
- For initial development, even a simple EMA 200 would suffice. The specific indicator choice is not the priority.
- Binary output for now, potentially confidence-scored later.
- Clean interface contract: input is data + pivot location, output is direction or discard. Everything behind the interface is replaceable.
- This layer is parked until Layers 1 and 3 are further along.

---

## Iterations and Evolution

*(New entries added as development progresses.)*

---
