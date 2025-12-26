# Plan
## Objective
Investigate how neural networks progressively build understanding across layers by extending the logit lens technique to analyze the policy network of Leela Chess Zero, examining whether representations are refined through smooth gradual processes or more complex computational mechanisms involving iterative inference with distinct phases.

## Hypothesis
1. Neural networks perform iterative inference with capability progression occurring in distinct computational phases rather than smooth gradual refinement
2. Leela's inference process combines algorithmic computation with learned heuristic priors, where safety-oriented heuristics can override tactical solutions

## Methodology
1. Extend logit lens to Post-LN transformer architectures by applying zero ablation to sublayer outputs beyond layer ℓ while preserving subsequent layer normalizations and ablating layer normalization biases
2. Analyze T82-768x15x24h transformer model with 15 layers and 768-dimensional embeddings using Post-LN architecture with DeepNorm scaling, projecting intermediate representations of all 64 chess squares through the policy head
3. Evaluate performance through round-robin tournaments with BayesElo ratings, Lichess bot deployment across time controls, and puzzle-solving on 10,000 Lichess puzzles using argmax selection
4. Characterize intermediate policy dynamics using Jensen-Shannon divergence, policy entropy, probability of final top move, and Kendall's τ ranking correlation between layers
5. Measure layer-wise concept preferences by computing expected concept change using Stockfish 8's handcrafted evaluation terms weighted by layer-wise move probabilities

## Experiments
### Internal tournament playing strength evaluation
- What varied: Layer depth (input, layers 0-13, full model) and temperature (τ=0 deterministic, τ=1 stochastic)
- Metric: Elo rating computed using BayesElo from 200 Encyclopedia of Chess Openings positions
- Main result: Three-phase progression: early layers show rapid gains through layer 5, middle layers plateau through layer 10, late layers show sharp strengthening from layer 11

### Real-world Lichess deployment
- What varied: Layer depth across Bullet, Blitz, and Rapid time controls with temperature τ=1.0 for first five moves
- Metric: Lichess Elo rating in actual games against other bots
- Main result: Similar three-phase pattern with clear late-layer strengthening, though less pronounced separation due to greater variability

### Puzzle-solving performance by difficulty
- What varied: Layer depth and puzzle difficulty (Elo ranges from 200-3000)
- Metric: Solve rate percentage using argmax selection to reproduce principal variation
- Main result: Final-phase acceleration clearly visible, particularly for harder puzzles where improvement rates exceed 60 times the middle phase

### Solution discovery and forgetting analysis
- What varied: Layer depth tracked across current solve rate, cumulative discoveries, and first solution appearance
- Metric: Solve rate percentage and median probability assigned to principal variation moves
- Main result: Gap between current and cumulative rates shows solutions discovered and subsequently discarded, with final cumulative solve rate exceeding last layer's rate

### Intermediate policy dynamics characterization
- What varied: Layer depth on 1000 positions from CCRL dataset
- Metric: Jensen-Shannon divergence, entropy, top move probability, Kendall's τ correlation
- Main result: Kendall's τ initially negative, stays low through middle layers, rises sharply in final layers; entropy stable; most positions remain divergent until late

### Layer-wise concept preference evolution
- What varied: Layer depth measuring expected concept changes for Stockfish evaluation terms
- Metric: Expected concept delta (Δcℓ) in centipawns for material, king safety, threats, and total evaluation
- Main result: Early and middle layers favor aggressive concepts; later layers shift toward balanced evaluation, increasing king safety and reducing opponent threats; total evaluation increases through layer 12 then declines