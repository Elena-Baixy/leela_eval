# Replication Documentation: Iterative Inference in a Chess-Playing Neural Network

## Goal

This replication aims to reproduce the core experiments from the "Iterative Inference in a Chess-Playing Neural Network" repository, which extends the logit lens technique to analyze how policy representations evolve across layers in Leela Chess Zero, a chess-playing neural network.

The key objectives are:
1. Load and initialize the Leela Chess Zero model with the logit lens wrapper
2. Apply the logit lens to extract intermediate policy distributions from each layer
3. Analyze how the probability of the correct solution evolves through the network
4. Validate numerical consistency of the extracted policies

## Data

### Model
- **Model file**: `lc0-original.onnx` (378MB)
- **Architecture**: T82-768x15x24h transformer
  - 15 transformer layers
  - 768-dimensional embeddings
  - Post-LN architecture with DeepNorm scaling

### Test Position
- **FEN**: `Q1b3k1/5ppp/p3r3/2p1Nn2/2Pq1P2/8/PP1P2PP/R1B2R1K b - - 0 17`
- **Solution (Principal Variation)**: `['f5g3', 'h2g3', 'e6h6']` (Ng3+, hxg3, Rh6#)
- This is the same puzzle used in the original demo notebook from the repository

## Method

### Logit Lens Implementation

The logit lens technique is implemented through zero ablation of sublayer outputs. The `LeelaLogitLens` class performs:

1. **Zero Ablation**: From layer `layer_idx` onwards, all transformer layers are ablated by:
   - Zeroing out the attention sublayer outputs
   - Zeroing out the FFN sublayer outputs
   - Preserving layer normalization scaling
   - Preserving DeepNorm alpha scaling

2. **Policy Extraction**: After ablation, the policy head is read to obtain intermediate policy distributions for all 1858 possible chess moves.

### Analysis Steps

1. **Single Layer Analysis**: Apply logit lens at layer 10 to see intermediate policy
2. **Multi-Layer Analysis**: Apply logit lens at all 16 layers (0-15) to track policy evolution
3. **Policy Metrics**: Compute entropy, JS-divergence, and top-move probability at each layer
4. **Puzzle Solving Verification**: Check which layers predict the correct first move

### Key Functions Used

- `LeelaLogitLens.__call__()`: Apply logit lens at a single layer
- `LeelaLogitLens.multi_layer_lens()`: Apply logit lens across all layers
- Policy returned as both tensor (1858 dimensions) and dictionary (move -> probability)

## Results

### Policy Evolution

The probability of the correct move (f5g3 = Ng3+) across layers:

| Layer | Probability |
|-------|-------------|
| Input Encoding | 0.014 |
| Layer 5 | 0.056 |
| Layer 10 | 0.128 |
| Layer 13 | 0.359 |
| Full Model | 0.785 |

### Puzzle Solving by Layer

- **Layers 0-12**: Predict incorrect moves (various distractors like d4g1, d4b2, f7f6)
- **Layer 13**: First layer to predict correct move (f5g3)
- **Full Model**: Correctly predicts f5g3 with 78.5% probability

### Final Model Predictions

Top 3 moves from full model:
1. f5g3 (Ng3+): 78.46%
2. e6e8: 6.54%
3. f5d6: 2.74%

### Numerical Validation

- Policy sum at each layer: 1.000000 (correctly normalized)
- All probabilities non-negative: True
- Policy tensor shape: torch.Size([1858])

## Analysis

### Key Findings

1. **Iterative Refinement**: The probability of the correct tactical solution increases progressively through the network layers (0.014 -> 0.785), demonstrating iterative inference.

2. **Late-Layer Strengthening**: The correct solution only becomes the top predicted move in the final layers (Layer 13 onwards), consistent with the paper's finding of late-layer tactical computation.

3. **Three-Phase Pattern**: Observable pattern of:
   - Early layers: Low probability for correct move, various heuristic moves preferred
   - Middle layers: Gradual increase but still not top choice
   - Late layers: Sharp increase with correct move becoming dominant

4. **Numerical Consistency**: The replication produces valid probability distributions that sum to 1 and are non-negative at all layers.

### Comparison with Original Repository

The results are consistent with:
- The demo notebook's visualization of policy evolution
- The paper's description of three-phase capability progression
- The documented logit lens methodology using zero ablation

### Limitations

1. **Single Position**: This replication focuses on one puzzle position rather than the full 10,000 puzzle evaluation from the paper
2. **No Tournament Evaluation**: Did not replicate the round-robin tournament experiments
3. **No Concept Analysis**: Did not replicate the Stockfish concept evaluation experiments

## Conclusions

The replication successfully demonstrates:
1. The logit lens technique can extract meaningful intermediate policy representations
2. Policy evolves through the network showing iterative refinement
3. The final layers are critical for correct tactical computation
4. Numerical outputs are consistent with expected probability distributions

The results faithfully reproduce the core demo functionality from the repository and are consistent with the paper's main findings about iterative inference in chess-playing neural networks.
