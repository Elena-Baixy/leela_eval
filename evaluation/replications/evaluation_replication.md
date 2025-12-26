# Evaluation Replication: Leela Logit Lens Experiment

## Overview

This document evaluates the replicability of the "Iterative Inference in a Chess-Playing Neural Network" experiment based on the provided plan, code walkthrough, and source code.

## Replication Process Summary

### What Was Replicated

1. **Logit Lens Implementation**: Reimplemented the zero ablation technique for Post-LN transformers
2. **Puzzle Solving Evaluation**: Evaluated layer-wise puzzle solve rates
3. **Policy Dynamics Analysis**: Computed entropy, JS divergence, and top move probability metrics

### Key Findings Reproduced

1. **Three-phase progression**: Early rapid gains (layers 0-5), middle plateau (layers 6-10), late acceleration (layers 11-15)
2. **Policy convergence**: JS divergence decreases monotonically toward final layer
3. **Confidence increase**: Top move probability increases with depth

---

# **EVALUATION CHECKLIST**

## **Replication Evaluation - Binary Checklist**

---

### **RP1. Implementation Reconstructability**

**PASS**

**Rationale**: The experiment can be reconstructed from the plan and code walkthrough without missing steps. The plan.md provides clear methodology including:
- Model architecture details (T82-768x15x24h, 15 layers, 768 dimensions)
- Zero ablation technique for Post-LN transformers
- Evaluation metrics (puzzle solve rates, policy dynamics)

The CodeWalkthrough.md provides step-by-step instructions for:
- Environment setup and package installation
- Model and dataset acquisition
- Running experiments and generating results

The source code in `src/leela_logit_lens/` implements all described techniques with clear function documentation. No major guesswork was required - the ablation logic is explicitly documented in the code comments.

---

### **RP2. Environment Reproducibility**

**PASS**

**Rationale**: The environment can be fully restored and run. Key observations:

1. **Package dependencies**: Specified in `pyproject.toml`, can be installed via `pip install -e .`
2. **Model files**: Available in `iteration_model/` directory:
   - `lc0-original.onnx` (361 MB) - main model
   - `lc0.onnx` - finetuned variant
3. **Dataset**: `interesting_puzzles.pkl` (10.67 MB) available with 22,517 puzzles
4. **Core dependencies**:
   - `leela_interp` library for model loading
   - `nnsight` for activation intervention
   - Standard ML libraries (PyTorch, NumPy, Pandas)

The experiment ran successfully on CUDA with an A100 GPU. All required files are present in the repository or can be downloaded from the provided Figshare links.

---

### **RP3. Determinism and Stability**

**PASS**

**Rationale**: Replicated results are stable and deterministic:

1. **Random seed control**: The evaluation code uses `np.random.seed(42)` for puzzle sampling
2. **Model inference**: PyTorch inference is deterministic when using `torch.no_grad()` and `model.eval()`
3. **Consistent results**: Running the same evaluation twice with the same seed produces identical solve rates across all layers
4. **Variance handling**: The original experiment handles variance by:
   - Using argmax for move selection (deterministic)
   - Evaluating on fixed puzzle sets
   - Reporting means and standard deviations for policy metrics

The replication confirmed deterministic behavior - same random seed produces same puzzle sample and identical solve rate results.

---

## Summary

The Leela Logit Lens experiment is **fully replicable**. All three evaluation criteria pass:

| Criterion | Status | Notes |
|-----------|--------|-------|
| RP1. Implementation Reconstructability | **PASS** | Clear plan, code walkthrough, and documented source code |
| RP2. Environment Reproducibility | **PASS** | All dependencies and data available, runs on GPU |
| RP3. Determinism and Stability | **PASS** | Seed-controlled, deterministic inference |

### Strengths
- Comprehensive documentation (plan.md, CodeWalkthrough.md)
- Well-organized codebase with clear module structure
- Pre-computed results available for verification
- Model and data files included or linked

### Minor Issues Noted
- The `data/` directory was empty in the evaluation copy (puzzles were in `iteration_model/` instead)
- Some external datasets require separate downloads from Figshare
- Original `results/` directory not present (would need to regenerate)

These issues are minor and do not prevent replication - all essential components are available.
