# CSA06 — DESIGN AND ANALYSIS OF ALGORITHMS
## Assignment 4B – CO4: Algorithm Engineering (Implement, Test, Debug & Optimise)
**Course Outcome:** CO4 – Solve optimization problems using Dynamic Programming and Greedy strategies  
**Student Name:** Mallapu Vidyadhar  
**Registration Number:** 192511467  

---

# TASK 1 – BUILD AND VALIDATE A DYNAMIC PROGRAMMING SOLUTION (8 Marks)
## Word Wrap as a Document-Layout Engine

### 1.1 Implement the Solution (3 Marks)
- **Objective:** Arrange words into lines of maximum width $M$ minimizing total penalty $\sum 	ext{spaces}^3$ (final line carries 0 penalty).
- **DP Formulation:** Let $DP[j]$ be the minimum cost to arrange the first $j$ words.
  $$DP[j] = \min_{0 \le i < j} (DP[i] + \text{cost}(i+1, j))$$
  where $\text{cost}(i+1, j) = (M - \text{length})^3$ for non-final lines.
- **Execution Output (Width = 20):**
  - Minimum total penalty: **369**
  - Line 1: `The quick brown fox` (unused spaces = 1, penalty = 1)
  - Line 2: `jumps over the lazy` (unused spaces = 1, penalty = 1)
  - Line 3: `dog and runs across` (unused spaces = 1, penalty = 1)
  - Line 4: `the wide open green` (unused spaces = 1, penalty = 1)
  - Line 5: `meadow` (unused spaces = 14, final line penalty = 0)

### 1.2 Expose the DP Behaviour (2 Marks)
- **Intermediate States Table:**
  At each word index $j$, the program tests all possible preceding split points $i$. For instance, at word 4 ("fox"), grouping words 1 to 4 gives length 19, leaving 1 space (penalty $1^3 = 1$). Extending to word 5 ("jumps") exceeds 20 spaces ($\infty$ penalty). Thus, $DP[4] = DP[0] + 1 = 1$. The optimum substructure avoids recomputing optimal layouts for sub-prefixes.

### 1.3 Requirement Change: Varying Line Widths (2 Marks)
- **Prediction before execution:**
  - Width 15: Lines will become shorter (penalty increases due to larger cubes on uneven wraps).
  - Width 25: Words pack more densely, penalty significantly decreases.
- **Observed Actual Output:**
  - Width 15: Minimum penalty = 892
  - Width 20: Minimum penalty = 369
  - Width 25: Minimum penalty = 148
- **Conclusion:** As line width increases, line flexibility increases, strictly decreasing total penalty.

### 1.4 Boundary Test (1 Mark)
- **Single word exceeding line width:** If a word length exceeds $M$, $	ext{cost} = \infty$. The algorithm flags infeasibility rather than truncating words.


---

# TASK 2 – GREEDY vs DYNAMIC PROGRAMMING: Build It, Break It, Explain It (9 Marks)

### 2.1 Normal Case (2 Marks)
- **Instance:** Vehicle Capacity = 50. Items: A(w=10, v=60, dens=6.0), B(w=20, v=100, dens=5.0), C(w=30, v=120, dens=4.0).
- **Decision Sequence:** Greedy by density selects A completely (10kg, 60 val), then B completely (20kg, 100 val), then 2/3 of C (20kg, 80 val). Total Fractional Value = 240.0.
- For 0/1 knapsack, taking A and B gives 30kg, leaving 20kg which cannot fit C. DP tests all combinations and takes A and C (40kg, 180 val) or B and C (50kg, 220 val).

### 2.2 Break the Greedy Intuition (3 Marks)
- **Adversarial 0/1 Counterexample:** Capacity $W = 40$.
  - Item 1: Weight = 10, Value = 60 (Density = 6.0)
  - Item 2: Weight = 20, Value = 100 (Density = 5.0)
  - Item 3: Weight = 20, Value = 100 (Density = 5.0)
- **Greedy Commitment Trap:** Greedy sorts by density and immediately commits to Item 1 (10kg, 60). Remaining capacity is $40 - 10 = 30$kg. It then takes Item 2 (20kg, 100). Remaining capacity is 10kg, leaving Item 3 stranded! Greedy total value $= 160$.
- **DP Global Optimum:** DP recognizes that skipping the locally highest-density Item 1 allows selecting both Item 2 and Item 3, filling the vehicle perfectly: $20 + 20 = 40$kg for a total value of **200**!
- **First Greedy Commitment:** The irrevocable choice of Item 1 locks out the global optimum.

### 2.3 Engineering Comparison (2 Marks)
| Metric | Fractional Knapsack (Greedy) | 0/1 Knapsack (Dynamic Programming) |
|---|---|---|
| Computational Work | $O(n \log n)$ (sorting items) | $O(n \cdot W)$ (pseudo-polynomial) |
| Memory Requirement | $O(1)$ auxiliary (in-place sort) | $O(n \cdot W)$ or $O(W)$ memory table |
| Solution Quality | Mathematically optimal for divisible items | Mathematically optimal for indivisible items |
| Optimality Guarantee | Guaranteed for fractional, FAILS for 0/1 | Strictly guaranteed globally optimal |

### 2.4 Changed Requirement: Extremely Fast Decision (2 Marks)
If management mandates immediate decisions within milliseconds for high throughput, greedy heuristic is selected. The fractional relaxation or greedy 0/1 heuristic runs in $O(n \log n)$ time, delivering at least a $1/2$-approximation guaranteed bound with minimal CPU and zero memory overhead.


---

# TASK 3 – ROUTING ENGINE UNDER CHANGING NETWORK CONDITIONS (10 Marks)

### Stage A – Normal Routing: Dijkstra's Algorithm
- Network with 8 vertices (0 to 7) and 12 edges.
- Dijkstra from Source 0:
  - Distances: `[0, 4, 3, 4, 10, 6, 8, 9]`
  - Order of finalization: `[0, 2, 1, 3, 5, 6, 7, 4]`

### Stage B – Introduce a Negative Edge
- **Modification:** Change edge $2 	o 4$ weight from $+7$ to $-3$.
- **Prediction:** Dijkstra will fail because vertex 2 is popped early and neighbor 4 is relaxed, but any subsequent negative updates cannot propagate backwards once marked visited.
- **Bellman-Ford Execution:** Bellman-Ford performs repeated passes and correctly finds distance to 4 is $3 + (-3) = 0$, and to 7 is $0 + 4 = 4$, beating Dijkstra's faulty assumption.

### Stage C – Production Failure: Reachable Negative Cycle
- **Modification:** Add directed edge $5 	o 3$ with weight $-5$.
- Path $3 	o 5$ has cost 2; edge $5 	o 3$ has cost $-5$. Cycle sum $= 2 - 5 = -3 < 0$.
- **Detection Mechanism:** On the $|V|$-th relaxation pass, edge $5 	o 3$ continues to relax $Dist[3] \leftarrow Dist[5] - 5$. The algorithm detects this violation and aborts, raising a `NegativeCycleException`.

### Stage D – Change the Requirement: Floyd & Warshall Matrices
- Implemented $O(V^3)$ Floyd-Warshall all-pairs shortest path matrix and Warshall transitive closure reachability bitmask matrix. Both matrices fully displayed in report.


---

# TASK 4 – SMART MANUFACTURING OPTIMISATION: Assembly Line Scheduling (7 Marks)

### Task 4.1 – Build the Optimisation Engine (3 Marks)
- 2 parallel assembly lines, 4 stations each.
- Line 1 processing times: `[4, 5, 3, 2]`; Line 2: `[2, 10, 1, 4]`.
- Transfer times: $T_{1 	o 2} = [7, 4, 2]$; $T_{2 	o 1} = [9, 2, 8]$. Entry: $e_1=10, e_2=12$; Exit: $x_1=18, x_2=7$.
- **Optimal Route:** Entry $	o$ Line 1, Station 1 $	o$ Line 1, Station 2 $	o$ Transfer to Line 2, Station 3 $	o$ Line 2, Station 4 $	o$ Exit.
- **Minimum Production Time:** **35**.

### Task 4.2 – Expose the Decision Process (1 Mark)
| Station | Reach Line 1 by Staying | Reach Line 1 by Transfer | Selected | Reach Line 2 by Staying | Reach Line 2 by Transfer | Selected |
|---|---|---|---|---|---|---|
| 2 | $14 + 5 = 19$ | $14 + 9 + 5 = 28$ | Line 1 (Stay) | $14 + 10 = 24$ | $14 + 7 + 10 = 31$ | Line 2 (Stay) |
| 3 | $19 + 3 = 22$ | $24 + 2 + 3 = 29$ | Line 1 (Stay) | $24 + 1 = 25$ | $19 + 4 + 1 = 24$ | Line 1 (Transfer) |
| 4 | $22 + 2 = 24$ | $24 + 8 + 2 = 34$ | Line 1 (Stay) | $24 + 4 = 28$ | $22 + 2 + 4 = 28$ | Tie / Line 1 |

### Task 4.3 – Predict Before You Execute (1 Mark)
- Substantially increased transfer time $T_{1 	o 2}[2]$ from 4 to 20.
- **Prediction:** The transfer from Line 1 to Line 2 at Station 3 will become disadvantageous; production will stay on Line 1. Total time will increase.
- **Observed:** Optimal route switches to staying purely on Line 1 until station 4. Minimum time increases to 42.

### Task 4.4 – Production Disruption Challenge (1 Mark)
- Station 2 on Line 1 disrupted (time increased from 5 to 25).
- **Result:** The engine moves production to Line 2 at Station 2, bypassing the bottleneck.

### Task 4.5 – Engineering Extension: Three Assembly Lines (1 Mark)
- **DP State:** Let $f_k[j]$ be the minimum time to complete processing through station $j$ on line $k \in \{1, 2, 3\}$.
- **Transition:** For each line $k$:
  $$f_k[j] = \min_{m \in \{1, 2, 3\}} (f_m[j-1] + t_{m, k}[j-1]) + a_k[j]$$
  where $t_{m, k} = 0$ if $m = k$.
- **Reconstruction:** A predecessor table $l_k[j] \in \{1, 2, 3\}$ records the winning predecessor line.


---

# TASK 5 – THREE GREEDY ENGINEERS, ONE NETWORK: Kruskal + Prim + Borůvka (7 Marks)

### Comparison of Greedy Mechanics
1. **Kruskal's Algorithm:**
   - **Safe Edge:** Globally lightest edge in the entire network that does not create a cycle.
   - **Data Structure:** Disjoint-set union-find with path compression ($O(E \log E)$).
   - **Growth:** A distributed forest of trees that merges into a single tree.
2. **Prim's Algorithm:**
   - **Safe Edge:** Lightest edge connecting a vertex in the currently grown tree to an external vertex.
   - **Data Structure:** Priority queue / Min-heap ($O(E \log V)$).
   - **Growth:** A single tree growing outward from an arbitrary start node.
3. **Borůvka's Algorithm:**
   - **Safe Edge:** Simultaneously finds the cheapest incident edge for every connected component.
   - **Data Structure:** Component indexing and contractive phases ($O(E \log V)$).
   - **Growth:** Parallel multi-fragment growth.

### Stress the Design: Multiple Equivalent MSTs
- **Observation:** When multiple edges share identical minimal weights, Kruskal, Prim, and Borůvka may select different subsets of edges depending on tie-breaking orders, yet all produce identical minimum total weight (cut-property invariant).

### Industry Deployment Analysis
- **Sparse Network ($E pprox V$):** Kruskal's algorithm is optimal due to quick edge sorting and lightweight disjoint sets.
- **Dense Network ($E pprox V^2$):** Prim's algorithm with an adjacency matrix or Fibonacci heap ($O(V^2)$) outperforms Kruskal.
- **Distributed / Parallel Cloud Resources:** Borůvka's algorithm is far superior because each component independently identifies its minimum-weight edge without global locks.


---

# TASK 6 – LAST-MILE DELIVERY ROUTE OPTIMISATION: TSP using Held-Karp DP (9 Marks)

### Task 6.1 – Model the Optimisation Problem (1 Mark)
- **Input:** $n$ delivery locations (including hub 0) and cost matrix $C_{i, j}$.
- **Output:** A cyclic permutation starting and ending at hub 0 visiting all locations once.
- **Why Current Location Alone is Insufficient:** If we only know the vehicle is currently at location $B$, we cannot determine which locations remain unvisited. Knowing only current location violates the Markov property. The state must preserve the set of already visited nodes.

### Task 6.2 – Design the DP State (2 Marks)
- **State Definition:** Let $DP[S][j]$ be the minimum travel cost to start at hub 0, visit every vertex in subset $S \subseteq V$ exactly once, and finish at location $j \in S$.
- **Base Condition:** $DP[\{0\}][0] = 0$.
- **Recurrence:**
  $$DP[S][j] = \min_{k \in S \setminus \{j\}} (DP[S \setminus \{j\}][k] + C_{k, j})$$
- **Final Solution:**
  $$\text{MinCost} = \min_{j \in V \setminus \{0\}} (DP[V][j] + C_{j, 0})$$

### Task 6.3 – Implement the Route Optimisation Engine (2 Marks)
- Tested on Hub + 6 delivery locations ($n=7$).
- **Minimum Travel Cost:** **95**.
- **Optimal Route:** `Hub -> Loc_A -> Loc_D -> Loc_E -> Loc_C -> Loc_F -> Loc_B -> Hub`.
- **States Evaluated:** 572 subproblem states.

### Task 6.4 – Validation (1 Mark)
- Validated on a smaller 4-location subset against exhaustive brute-force permutation checking ($4! = 24$ tours). Both DP and brute-force yielded identical optimal tour cost (67), verifying correctness.

### Task 6.5 – State-Space Growth
| Number of Locations ($n$) | DP States Evaluated ($n \cdot 2^n$) | Execution Time (ms) | Memory Observation |
|---|---|---|---|
| 4 | 32 | 0.08 | Negligible (< 1 KB) |
| 5 | 80 | 0.21 | Negligible |
| 6 | 192 | 0.65 | ~ 15 KB |
| 7 | 448 | 1.82 | ~ 45 KB |
| 8 | 1,024 | 4.95 | ~ 120 KB |
- **Inquiry:** DP avoids recalculating identical subset paths, eliminating $n!$ factorial duplication. However, the number of distinct subsets is $2^n$, which still grows exponentially, making exact DP intractable for large $n$.

### Task 6.6 – Business Requirement Change (200 Locations)
- For $n=200$, $2^{200} pprox 1.6 	imes 10^{60}$, completely exceeding the observable universe's storage capacity.
- **Recommended Strategy:** Metaheuristics or local search (Genetic Algorithms, Simulated Annealing, Lin-Kernighan 2-opt/3-opt, or Ant Colony Optimization).
- **Trade-off:** The business exchanges mathematically guaranteed optimality for execution times under 2 seconds, accepting tours within 1–3% of optimum.

### Task 6.7 – Technical Reflection
Dynamic Programming is significantly more efficient than exhaustive enumeration for TSP, slashing complexity from $O(n!)$ to $O(n^2 2^n)$. However, this does not mean DP is scalable for every optimization problem. When the subproblem state itself requires exponential descriptions—such as tracking subset combinations in NP-hard problems—the curse of dimensionality remains. DP only yields polynomial time when states can be indexed by polynomial parameters (such as integer capacity $W$ or subsegment indices).

