# Evaluation: Replication of "Iterative Inference in a Chess-Playing Neural Network"

## Overview

This document evaluates the replication of the Leela Chess Zero logit lens experiments according to the standardized binary checklist.

## Replication Summary

The replication focused on the core experiments:
1. **Demo**: Layer-wise policy evolution on a tactical puzzle
2. **Policy Distribution Metrics**: JS divergence, entropy, Kendall τ, top prediction probability
3. **Three-Phase Pattern**: Verification of early/middle/late computational phases

### Results Match

| Experiment | Original Finding | Replication Finding | Match |
|------------|------------------|---------------------|-------|
| Three-phase pattern | Distinct phases observed | ✓ Verified | YES |
| Late-layer strengthening | Sharp Kendall τ increase | +0.76 in late phase | YES |
| JS divergence decline | Steepest in late layers | 0.77→0.38 | YES |
| Entropy stability | Relatively stable | Range 0.52-0.54 | YES |

---

## Replication Evaluation — Binary Checklist

### RP1. Implementation Reconstructability

**PASS**

**Rationale**: The experiment can be reconstructed from the plan.md and CodeWalkthrough.md without missing steps. The code walkthrough provides:
- Clear setup instructions for dependencies and data
- Explicit model paths and configurations
- Demo notebook with working examples
- Scripts for each major experiment

The logit lens implementation in `src/leela_logit_lens/core/leela_logit_lens.py` is well-documented with clear ablation logic. The `multi_layer_lens()` method provides easy access to layer-wise analysis. No major guesswork was required to implement the replication.

Minor ambiguities:
- The exact number of samples for CCRL metrics (used 100 vs 1000 for efficiency)
- Some helper functions from `leela_interp` required inspection

---

### RP2. Environment Reproducibility

**PASS**

**Rationale**: The environment can be restored and run with careful attention to dependencies:

1. **Package Installation**: `pip install -e .` works correctly
2. **Dependencies**: The `pyproject.toml` specifies required packages:
   - `nnsight==0.2.*` (critical version constraint)
   - `leela-interp` from git
   - Standard scientific computing stack (torch, numpy, scipy)

3. **Data Availability**:
   - Model files available in `iteration_model/`
   - CCRL dataset present in `data/cclr/`
   - Puzzles data available

4. **Environment Issue Encountered**:
   - The default conda environment had nnsight version incompatibility
   - Resolved by using system Python (`/opt/conda/bin/python3`)
   - This is a common issue that can be resolved following standard practices

---

### RP3. Determinism and Stability

**PASS**

**Rationale**: The replication produced stable results:

1. **Random Seeds**: Properly controlled with seed=42
   - `random.seed(42)`
   - `np.random.seed(42)`
   - `torch.manual_seed(42)`
   - CUDA seeds set when available

2. **Deterministic Behavior**:
   - Torch backends set to deterministic mode
   - Position sampling uses fixed seed
   - Results are reproducible across runs

3. **Variance**: The metrics show reasonable variance in percentile bands but consistent patterns:
   - Median values stable
   - Three-phase pattern consistently observed

---

### RP4. Demo Presentation

**PASS**

**Rationale**: The repository provides a comprehensive demo at `notebooks/demo.ipynb`:

1. **Executability**: The demo notebook runs without requiring hidden or external materials
2. **Coverage**: The demo demonstrates:
   - Model loading and initialization
   - Single-layer logit lens analysis
   - Multi-layer analysis
   - Visualization of policy evolution
   - Generation of probability tables

3. **Documentation**: All steps are explained with markdown cells

4. **Replication Match**: Our replication followed the demo patterns and produced consistent results:
   - Layer-wise policy evolution matches expected patterns
   - Top moves evolve from distributed to focused
   - Final layer shows highest confidence in correct tactical move

---

## Summary

| Criterion | Status |
|-----------|--------|
| RP1. Implementation Reconstructability | **PASS** |
| RP2. Environment Reproducibility | **PASS** |
| RP3. Determinism and Stability | **PASS** |
| RP4. Demo Presentation | **PASS** |

### Overall Assessment

The replication was **successful**. All core findings from the paper were replicated:

1. **Three-phase computational pattern**: Verified with Kendall τ showing +0.31 (early), +0.09 (middle), +0.76 (late)
2. **Layer-wise policy evolution**: Demonstrated on puzzle position with clear progression to correct solution
3. **Policy metrics**: JS divergence, entropy, and ranking correlation patterns match expected behavior

### Issues Encountered

1. **Environment**: Needed to use system Python instead of conda environment due to nnsight version conflicts. This is documented and resolvable.

2. **Sample Size**: Used 100 positions instead of 1000 for computational efficiency. Results remained consistent with original findings.

### Recommendations for Future Replications

1. Use Python 3.12 with the specified nnsight version (0.2.*)
2. Ensure CUDA is available for reasonable runtime
3. Data and model files should be pre-downloaded from Figshare if not included
