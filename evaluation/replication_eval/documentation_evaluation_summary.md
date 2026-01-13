# Documentation Evaluation Summary

## Evaluation: Replication of "Iterative Inference in a Chess-Playing Neural Network"

**Original Documentation:** `/net/scratch2/smallyan/leela_eval/documentation.pdf`  
**Replicated Documentation:** `/net/scratch2/smallyan/leela_eval/evaluation/replications/documentation_replication.md`

---

## Results Comparison

The replication is a **demo-only replication** that successfully verifies a subset of the original paper's findings using 100 chess positions (vs. the original's 1,000+ positions for distributional metrics and 10,000 for puzzle evaluation).

**Results that were successfully replicated:**
- **Three-phase pattern**: The replication confirms the distinct computational phases (early rapid gain, middle plateau, late sharpening) through Kendall τ analysis. The replicated Kendall τ trajectory shows: early phase +0.314 change, middle phase +0.092 change (<50% of early), and late phase +0.758 change (sharpest).
- **Kendall τ trajectory**: Starting from negative correlation (-0.022 in early layers), staying low through middle layers (0.218), and rising sharply in final layers (0.537) - matching the original paper's qualitative findings.
- **JS divergence trend**: Decreasing from 0.773 (early) → 0.652 (middle) → 0.380 (late), consistent with original.
- **Entropy stability**: Remaining relatively constant (0.525 → 0.539 → 0.518), matching the original observation.
- **Late-layer strengthening**: Top prediction probability increases primarily in late layers (0.033 → 0.115 → 0.282).

**Results not replicated (acknowledged as limitations):**
- Elo tournament evaluation
- Full 10,000 puzzle evaluation
- Lichess deployment results
- Concept preference analysis with Stockfish evaluations
- Solution "forgetting" quantitative analysis

The replicated results are **consistent** with the original within the scope of what was attempted.

---

## Conclusions Comparison

**Original conclusions:**
1. Leela exhibits distinct computational stages (early rapid improvement, middle plateau, late feature integration)
2. Move preferences are repeatedly reevaluated rather than gradually refined
3. Concept preference shifts from aggressive (early) to safety-oriented (late)
4. Iterative inference integrates algorithmic computation with learned heuristic priors

**Replicated conclusions:**
1. Confirms three-phase pattern with early rapid gain, middle plateau, late sharpening
2. Kendall τ trajectory supports non-smooth refinement
3. Entropy stability indicates maintained option consideration during ranking refinement
4. Iterative inference occurs in distinct phases, differing from smooth gradual refinement

The replicated conclusions are a **consistent subset** of the original conclusions. All claims made in the replication are supported by the original findings. The replication does not attempt to verify the more nuanced claims about concept preferences or the forgetting mechanism.

---

## External/Hallucinated Information

**No external or hallucinated information was detected:**
- All cited references (Leela Chess Zero, "Evidence of Learned Look-Ahead" paper) appear in the original paper
- Model specifications match exactly (T82-768x15x24h, Post-LN transformer, 15 layers, 768 dimensions)
- Methodology descriptions derive directly from the original paper
- The CCRL dataset is mentioned in the original paper
- All numerical results appear to be from actual replication experiments
- Limitations are clearly stated and appropriate

---

## Evaluation Checklist

| Criterion | Status | Notes |
|-----------|--------|-------|
| **DE1. Result Fidelity** | **PASS** | Demo-only replication results match the original's demo findings and distributional metrics show consistent trends. |
| **DE2. Conclusion Consistency** | **PASS** | Conclusions are a consistent subset of original claims with no contradictions. |
| **DE3. No External/Hallucinated Information** | **PASS** | All information traces back to original paper or acknowledged source materials. |

---

## Final Verdict

**PASS**

All three evaluation criteria (DE1-DE3) are satisfied. The replication documentation faithfully reproduces a subset of the original results and conclusions without introducing external or hallucinated information. The replication appropriately acknowledges its limitations (smaller sample size, no tournament evaluation, single model) while successfully verifying the core finding of three-phase iterative inference in Leela Chess Zero.
