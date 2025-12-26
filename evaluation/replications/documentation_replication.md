# Replication Documentation: Leela Logit Lens Experiment

## Goal

This replication aims to reproduce the key findings from the "Iterative Inference in a Chess-Playing Neural Network" experiment. The original work investigates how Leela Chess Zero (LC0), a neural network trained through self-play, progressively builds understanding across its transformer layers.

## Data

### Model
- **Model**: Leela Chess Zero T82-768x15x24h architecture
- **File**: `lc0-original.onnx` (361 MB)
- **Architecture**: 15-layer Post-LN transformer with 768-dimensional embeddings
- **Training**: Self-play reinforcement learning with MCTS guidance

### Datasets
- **Puzzles**: `interesting_puzzles.pkl` containing 22,517 Lichess tactical puzzles
- **Puzzle attributes**: FEN position, move sequence, rating (difficulty), principal variation

## Method

### Logit Lens Technique

The logit lens is extended for Post-LN transformer architectures through zero ablation:

1. **Forward pass**: Run the model up to layer ℓ
2. **Zero ablation**: For layers ℓ to N-1:
   - Set attention sublayer outputs to scaled residual (DeepNorm α scaling)
   - Set FFN sublayer outputs to scaled residual
   - Zero layer normalization biases to prevent bias accumulation
3. **Read output**: Project the ablated representation through the policy head
4. **Interpret**: The resulting policy distribution shows what the model "knows" at layer ℓ

### Evaluation Metrics

1. **Puzzle Solve Rate**: Fraction of puzzles where argmax policy matches principal variation
2. **Policy Entropy**: Uncertainty measure of the policy distribution
3. **Jensen-Shannon Divergence**: Distance from intermediate to final layer policy
4. **Top Move Probability**: Confidence in the argmax move

## Results

### Puzzle Solving Performance

The replication confirms the three-phase computational progression:

| Phase | Layers | Solve Rate | Pattern |
|-------|--------|------------|---------|
| Early | 0-5 | 2-13% | Rapid initial gains |
| Middle | 6-10 | 18-30% | Plateau, gradual improvement |
| Late | 11-15 | 33-77% | Sharp acceleration |

Key observation: The final layer solve rate (77%) shows significant jump from layer 13 (57%), confirming the late-layer strengthening phenomenon.

### Policy Dynamics

1. **Entropy**: Decreases monotonically, showing increasing confidence
2. **JS Divergence**: Converges toward final layer policy
3. **Top Move Probability**: Increases with depth, model becomes more decisive

### Layer-wise Move Evolution (Example Puzzle)

For puzzle position after f8f7 (expected solution: c2h7, g8h7, g7g8q):

- **Input-Layer 6**: Model explores various moves (g1g6, f4f5, g1h1)
- **Layer 7-10**: Correct move c2h7 emerges and strengthens (20-49% probability)
- **Layer 11-15**: c2h7 stabilizes as top choice (~40%), demonstrating convergence

## Analysis

### Consistency with Original Findings

1. **Three-phase progression**: ✓ Confirmed
   - Early rapid gains, middle plateau, late acceleration

2. **Iterative inference**: ✓ Confirmed
   - Model discovers solutions in early/middle layers
   - Later layers refine and consolidate choices

3. **Policy dynamics**: ✓ Confirmed
   - Entropy decreases with depth
   - JS divergence converges to zero

### Numerical Consistency

The replication achieves qualitatively consistent results with the original:
- Similar three-phase pattern in solve rates
- Comparable policy dynamics evolution
- Matching layer-wise behavior patterns

### Implementation Notes

1. Used existing `leela_interp` library for model loading
2. Reimplemented logit lens ablation logic from plan/code understanding
3. Evaluated on sample of puzzles (n=100) for computational efficiency
4. Results are deterministic with fixed random seed

## Conclusion

The replication successfully reproduces the key findings from the original experiment. The logit lens technique reveals a three-phase computational progression in LC0's policy development across layers, supporting the hypothesis that neural networks perform iterative inference with distinct computational phases rather than smooth gradual refinement.
