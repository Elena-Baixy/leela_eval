# Documentation: Replication of "Iterative Inference in a Chess-Playing Neural Network"

## Goal

This replication aims to verify the key findings from the paper "Iterative Inference in a Chess-Playing Neural Network" which investigates how neural networks progressively build understanding across layers using the logit lens technique applied to Leela Chess Zero (LCZ).

The main hypothesis being tested is that neural networks perform iterative inference with capability progression occurring in distinct computational phases rather than smooth gradual refinement.

## Data

### Model
- **Model**: Leela Chess Zero (T82-768x15x24h)
  - Architecture: Post-LN transformer with DeepNorm scaling
  - Layers: 15 transformer layers
  - Hidden dimension: 768
  - Model file: `lc0-original.onnx` (non-finetuned, uses position history)

### Datasets
- **CCRL Dataset**: Computer Chess Rating Lists game collection
  - Used for policy distribution metrics evaluation
  - Sampled 100 unique positions from `data/cclr/train/`
  - Positions sampled with seed 42 for reproducibility

### Puzzle Position
- A tactical puzzle position was used for the demo:
  - FEN: `Q1b3k1/5ppp/p3r3/2p1Nn2/2Pq1P2/8/PP1P2PP/R1B2R1K b - - 0 17`
  - This is a well-known position from "Evidence of Learned Look-Ahead" paper

## Method

### Logit Lens Technique

The logit lens technique was applied to analyze intermediate layer representations:

1. **Zero Ablation**: For each layer index ℓ, all layers from ℓ onwards are ablated:
   - Sublayer outputs are zeroed (FFN and attention)
   - Layer normalization biases are set to zero
   - Alpha scaling (DeepNorm) is preserved
   - Subsequent layer normalizations are maintained

2. **Policy Extraction**: After ablation, the policy head produces a distribution over moves, showing what the network "thinks" at each layer depth.

### Metrics Computed

1. **Jensen-Shannon Divergence**: Measures distribution difference between intermediate and final layer policies
   - Computed over legal moves only
   - Uses base-2 logarithm

2. **Normalized Entropy**: Policy entropy normalized by maximum possible entropy
   - Indicates uncertainty/confidence in move selection
   - Range: [0, 1]

3. **Kendall's τ (Tau) Ranking Correlation**: Measures agreement between intermediate and final move rankings
   - Computed using top-5 moves to reduce noise
   - Range: [-1, 1], where 1 indicates perfect agreement

4. **Top Prediction Probability**: Probability assigned by each layer to the final model's top move
   - Tracks how early the final decision emerges

### Three-Phase Pattern Analysis

The paper hypothesizes three distinct phases:
- **Early Phase (Input→L5)**: Rapid capability gains
- **Middle Phase (L5→L10)**: Plateau with minimal change
- **Late Phase (L10→Final)**: Sharp strengthening

## Results

### Demo: Layer-wise Policy Evolution

The puzzle position demonstrated clear policy evolution:
- **Input Encoding**: Initial moves considered (d4d2, d4g1, d4f2)
- **Early Layers (0-5)**: Policy shifts toward defensive moves (d4b2, e6e5)
- **Middle Layers (6-10)**: King safety moves emerge (g8h8, f7f6)
- **Late Layers (11-14)**: Tactical solution discovered (f5g3 reaches 78.5%)

### Policy Distribution Metrics (100 positions)

| Metric | Early (L0-5) | Middle (L6-10) | Late (L11-14) |
|--------|--------------|----------------|---------------|
| JS Divergence | 0.773 | 0.652 | 0.380 |
| Entropy | 0.525 | 0.539 | 0.518 |
| Kendall τ | -0.022 | 0.218 | 0.537 |
| Top Pred Prob | 0.033 | 0.115 | 0.282 |

### Three-Phase Pattern Verification

Kendall τ changes by phase:
- **Early Phase** (Input→L5): +0.314 ✓
- **Middle Phase** (L5→L10): +0.092 ✓ (slower)
- **Late Phase** (L10→Final): +0.758 ✓ (sharpest)

**All three phases verified:**
1. Early rapid gain: YES
2. Middle plateau: YES (change < 50% of early phase)
3. Late sharpening: YES (change > middle phase)

## Analysis

### Key Findings Replicated

1. **Three-Phase Computation Pattern**: The replication confirms that Leela's inference follows a three-phase pattern:
   - Early layers establish basic position understanding
   - Middle layers show relatively stable representations
   - Late layers perform sharp refinement toward the final decision

2. **Kendall τ Trajectory**: Starting from negative correlation (early representations may prefer different moves), the ranking correlation steadily increases and sharply rises in final layers.

3. **JS Divergence Decline**: The policy distribution becomes increasingly similar to the final output as depth increases, with the steepest decline in late layers.

4. **Entropy Stability**: Unlike what might be expected from pure "focusing" behavior, entropy remains relatively stable across layers, suggesting the network maintains consideration of multiple options while refining rankings.

### Consistency with Original Paper

The replicated results are consistent with the paper's main claims:
- Iterative inference occurs in distinct phases
- Late-layer strengthening is particularly pronounced
- The pattern differs from smooth gradual refinement

### Limitations

1. **Sample Size**: Used 100 positions (vs 1000 in original) for computational efficiency
2. **Single Model**: Only tested on `lc0-original.onnx`
3. **No Tournament Evaluation**: Did not replicate Elo rating experiments

## Figures

Generated figures are available in `figures/`:
- `js_divergence.png`: JS divergence trajectories
- `entropy.png`: Normalized entropy trajectories
- `tau_correlation.png`: Kendall τ correlation trajectories
- `top_prediction.png`: Top prediction probability trajectories

## Conclusion

The replication successfully verifies the core finding that Leela Chess Zero performs iterative inference with three distinct computational phases. The logit lens technique effectively reveals how intermediate representations evolve toward the final policy, with the most dramatic changes occurring in the final layers.
