# Comparative Study of Performance of Parallel Alpha-Beta Pruning for Different Architectures

**Authors:** Shubhendra Pal Singhal and M. Sridevi  
**Published in:** 9th International Conference on Advanced Computing (IACC), 2019

---

## Abstract

Optimization of selecting the best possible action in complex game trees often involves the minimax algorithm, which becomes computationally impractical in large environments like chess. Alpha-Beta pruning improves this by eliminating unpromising branches. This paper investigates sequential Alpha-Beta pruning and its parallel implementations across different architectures — particularly mesh-based GPU (CUDA) and shared memory model (OpenMP). The study compares the performance, efficiency, and speedup of these implementations using chess as a test application.

---

## 1. Introduction

Game-tree search algorithms are foundational in artificial intelligence for strategic games, where decision-making under uncertainty and optimal move selection are critical. The minimax algorithm is a classical approach for such problems but struggles with scalability in complex games. This research paper addresses the challenge of computational efficiency in game-tree search and investigates how parallel algorithms and architectural choices can improve performance.

---

## 2. Minimax Algorithm: What, Why, and Its Limitations

### 2.1. What is Minimax?

The minimax algorithm is a recursive, depth-first search algorithm used for decision-making in two-player, zero-sum games like tic-tac-toe, checkers, and chess. It systematically explores the entire game tree to find the optimal move, assuming both players play optimally.

- **Flow of Minimax:**
  1. Start at the current board state (root of the tree).
  2. Recursively generate all possible moves and subsequent states (children).
  3. Alternate between maximizing (AI's turn) and minimizing (opponent's turn) the evaluation value at each level.
  4. At terminal states (win, lose, draw, or max depth), assign a value.
  5. Propagate these values back up the tree, each player choosing the move that optimizes their outcome.

- **Example:**
  - In tic-tac-toe, the entire game tree has only about 255,168 possible games, enabling minimax to explore every possibility within milliseconds.
  - In chess, the branching factor is about 35 and average game length is 80 moves, resulting in a search space of 10^120 possible games—far too large for brute-force minimax.

### 2.2. Why is Minimax Only Practical for Simple Games Like Tic-Tac-Toe?

- **Low Branching Factor:** Tic-tac-toe’s small number of moves per turn.
- **Shallow Game Tree:** Few moves to reach terminal states.
- **Complete Search Feasible:** Every possible game can be traversed quickly with limited computational resources.

**Limitation:** In complex games like chess, minimax is infeasible due to exponential growth in possible states—thus, optimization is necessary.

---

## 3. Alpha-Beta Pruning: Principle, Flow, and Figure Explained

### 3.1. What is Alpha-Beta Pruning?

Alpha-Beta pruning is an optimization of minimax that “prunes” or eliminates branches of the game tree that cannot possibly influence the final decision, based on already-explored alternatives.

- **Alpha:** The best value that the maximizer can guarantee at that level or above.
- **Beta:** The best value that the minimizer can guarantee at that level or above.

### 3.2. Alpha-Beta Pruning Flow

1. **Start** with initial alpha = -infinity and beta = +infinity.
2. **Traverse** the tree depth-first, just like minimax.
3. **At each node:**
   - If the current value is worse than the best alternative found so far (alpha or beta), prune (do not explore) this branch further.
   - Update alpha/beta as better moves are found.
4. **Continue** until all relevant nodes have been evaluated.

#### **Alpha-Beta Pruning Figure Explanation**

- Imagine a tree where you first evaluate one branch and find a promising value.
- When evaluating other branches, if you encounter a position that is guaranteed to be worse for the player (because of previous alpha or beta values), you stop evaluating further nodes in that branch.
- **Visualization:** In figures (not included here), pruned branches are often marked with an "X" or shaded to show that they are skipped, saving computation.

- **Key Benefit:** If child nodes are ordered so that the best moves are searched first, Alpha-Beta can achieve O(b^(d/2)) time complexity, essentially doubling the effective search depth compared to plain minimax.

---

## 4. Research Methodology and Deep Dive into Paper’s Components

### 4.1. Sequential Alpha-Beta Pruning

- The paper first describes the classical, sequential implementation of Alpha-Beta pruning as a baseline.
- **Limitation:** Even with pruning, sequential execution is slow for deep or wide trees.

### 4.2. Parallel Alpha-Beta Pruning

The core research contribution is exploring **parallelization** of Alpha-Beta pruning to further reduce compute time.

#### a. **Root Splitting**
   - Split the children of the root node among different processors.
   - Each processor evaluates a whole subtree independently, then results are merged.
   - This approach minimizes dependencies and synchronization between threads.

#### b. **Beam Search**
   - At each level, only a limited number of the most promising nodes (beam width) are expanded.
   - Reduces the search space and enables parallel exploration, but may miss optimal moves if the beam is too narrow.

#### c. **Reordering**
   - Evaluating the most promising moves first (using heuristics or shallow searches) greatly increases pruning effectiveness.
   - Parallelizing with reordering requires careful synchronization, but can yield dramatic speedup.

### 4.3. Architectures: CUDA vs. OpenMP

#### a. **CUDA (GPU Mesh-Based)**
   - Utilizes thousands of lightweight GPU threads for massive parallelism.
   - Ideal for evaluating large numbers of independent subtrees simultaneously.
   - Requires restructuring algorithms to fit the GPU’s execution model; data transfer overhead can be a bottleneck.

#### b. **OpenMP (Shared Memory, Multi-core CPU)**
   - Easier to implement and debug.
   - Limited by the number of CPU cores (usually much fewer than GPU threads).
   - Better suited for problems with moderate parallelism and less fine-grained synchronization.

---

## 5. Experimental Setup and Evaluation

- The paper implements all approaches using chess as the test application, due to its complexity and relevance.
- Performance is measured in terms of:
  - **Speedup**: Ratio of parallel execution time to sequential.
  - **Efficiency**: How well hardware resources are used.
  - **Absolute time**: Time taken to reach a fixed search depth.

- **Testbed:** Experiments are run on both GPU (CUDA-enabled) and CPU (OpenMP-enabled) systems.

---

## 6. Experimental Results and Analysis

### 6.1. Speedup: Reordering vs. Beam Search

- Reordering consistently performs better than beam search when combined with parallel Alpha-Beta pruning.
- Reordering allows more pruning, thus reducing the total number of nodes evaluated and increasing speedup.

### 6.2. CUDA vs. OpenMP

- **CUDA** implementations show significantly greater speedup, especially as tree depth increases. This is due to the ability of GPUs to evaluate many branches in parallel.
- **OpenMP** provides moderate speedup, limited by core count and memory bandwidth.

- **Scalability:** GPU approaches scale better with larger tree depths and higher branching factors.

---

## 7. Conclusions: What the Paper Finds

- **Parallel Alpha-Beta pruning** yields substantial performance gains over sequential, especially with move reordering.
- **CUDA/GPU architectures** provide the best performance due to massive parallelism, making them preferable for deep, wide search trees as in chess.
- **OpenMP/CPU approaches** are easier to implement but are less scalable for very large search spaces.
- **Practical Implication:** For AI developers, combining smart move ordering with parallelization on GPU architectures is the most effective way to search complex game trees in real time.

---

## 8. What the Paper Wants to Convey

- The research systematically evaluates and compares parallelized Alpha-Beta pruning strategies on different hardware.
- It demonstrates the necessity of both algorithmic and architectural optimization for state-of-the-art game-tree search.
- The findings guide AI practitioners in choosing the right combination of search strategy and hardware for high-performance game-playing agents.

---

## References

Singhal, S. P., & Sridevi, M. (2019). Comparative Study of Performance of Parallel Alpha-Beta Pruning for Different Architectures. In *2019 9th International Conference on Advanced Computing (IACC)* (pp. 239-244). IEEE.
