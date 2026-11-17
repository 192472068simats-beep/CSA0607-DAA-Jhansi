# CSA06 — DESIGN AND ANALYSIS OF ALGORITHMS
## Assignment 3 – CO3: Divide and Conquer Algorithms
**Course Outcome:** CO3 – Design Divide and Conquer algorithms for Merge Sort, Quick Sort, Binary Search and Matrix Multiplication  
**Mode:** Pair Work  
**Student A:** Mallapu Vidyadhar (192511467)  
**Student B:** Shaik Mohammad Sohan (192511489)  

---

# Q1 – Decomposition and Elimination in Ordered Data (6 Marks)

## Part A – Finding Maximum and Minimum (Tournament Divide & Conquer)

### Shared Group Work
- **Problem Formulation:** Given an unsorted array $A[0 \dots n-1]$ of $n$ elements, determine both the minimum and maximum values using optimal comparisons.
- **D&C / Tournament Strategy:** Recursively divide the array into two halves, compute the pair $(\min_1, \max_1)$ for the left half and $(\min_2, \max_2)$ for the right half, and combine them with two scalar comparisons: $\min = \min(\min_1, \min_2)$ and $\max = \max(\max_1, \max_2)$.
- **Base Cases:**
  - $n=1$: $\min = \max = A[low]$ (0 comparisons).
  - $n=2$: 1 comparison between $A[low]$ and $A[high]$ sets initial min and max.
- **Return Value:** A 2-tuple $(	ext{min\_val}, 	ext{max\_val})$.

### Student A – Design & Mathematical Analysis (Mallapu Vidyadhar)
- **Decomposition Tree:**
  At root $n=16$, split into $8$ and $8$. Each $8$ splits into $4$ and $4$, down to pairs of $2$.
- **Combine Operation:**
  For subproblems returning $(m_1, M_1)$ and $(m_2, M_2)$, combine using:
  $m = m_1 < m_2 \ ? \ m_1 : m_2$ (1 comparison)
  $M = M_1 > M_2 \ ? \ M_1 : M_2$ (1 comparison)
  Total combine cost = 2 comparisons per node.
- **Recurrence & Derivation:**
  $$T(n) = 2T(n/2) + 2 \quad 	ext{for } n > 2, \quad T(2) = 1, \ T(1) = 0$$
  For $n = 2^k$:
  $$T(n) = rac{3n}{2} - 2$$
- **Mathematical Comparison:**
  - Independent Linear Scan for Min and Max: $(n-1) + (n-1) = 2n - 2$ comparisons.
  - For $n=16$: Linear Scan $= 30$ comparisons. Tournament D&C $= 1.5(16) - 2 = 22$ comparisons.
  - Percentage reduction $= 26.7\%$.

### Student B – Experimental Validation (Shaik Mohammad Sohan)
- **Dataset:** Deterministic random 16-element array: `[24, 13, 88, 45, 12, 67, 91, 3, 55, 76, 42, 19, 8, 99, 31, 60]`.
- **Measured Comparisons:** Exactly 22 comparisons.
- **Discrepancy:** 0 discrepancy with the theoretical bound $1.5(16)-2 = 22$.

---

## Part B – Binary Search

### Shared Group Work
Derive search on sorted array eliminating half of the candidates per step.

### Student A – Experimental Investigation (Mallapu Vidyadhar)
- **Target Found (55):** Found at index 9, Comparisons = 3 ($\le \lfloor \log_2 16 floor + 1 = 5$).
- **Target Not Found (50):** Terminated after 4 comparisons with status not found.

### Student B – Elimination Logic (Shaik Mohammad Sohan)
- **Decision Rule:** Compare target with $A[mid]$. If target $< A[mid]$, eliminate subarray $A[mid \dots high]$ because the array is monotonically sorted, ensuring all elements at or above $mid$ are strictly greater than target.
- **Termination:** When $low > high$ (search space empty) or target matches.
- **Efficiency:** Halving the remaining search space at every step yields logarithmic complexity $\Theta(\log n)$.


---

# Q2 – The Quality of Decomposition in Sorting (7 Marks)

### Shared Group Work
Implement Merge Sort and Lomuto-partition Quick Sort.

### Student A – Quick Sort Investigation (Mallapu Vidyadhar)
- **Balanced Input (Random):**
  $n=16$ random elements. Maximum recursion depth $= 5 pprox \lceil \log_2 16 ceil + 1$. Total comparisons $= 45$.
- **Severely Unbalanced Input (Sorted Array):**
  $n=16$ strictly ascending array. Every partition isolates 1 element: subproblems of size $0$ and $n-1$.
  Maximum recursion depth $= 15 = n-1$. Total comparisons $= rac{16 	imes 15}{2} = 120$.
- **Pivot Influence:** Pivot selection directly governs the balance of the recursion tree. Fixed-endpoint pivots fail catastrophically on sorted or patterned inputs.

### Student B – Comparative D&C Analysis (Shaik Mohammad Sohan)
- **Merge Sort Decomposition:** Divides exactly at the static index midpoint $\lfloor n/2 floor$, independent of values.
- **Recursion Tree Height:** Merge Sort tree is guaranteed height $\lceil \log_2 n ceil$. Quick Sort varies between $\log_2 n$ and $n$.
- **Predictability & Worst Case:** Merge Sort guarantees $\Theta(n \log n)$ time in all cases. Quick Sort degrades to $O(n^2)$ when splits are skewed $1 : n-1$.

### Joint Design Question
**Question:** If characteristics of input data are unknown in advance, what information is required before choosing between Merge Sort and Quick Sort?
**Answer:** We require:
1. Memory budget (Merge Sort requires $O(n)$ auxiliary space, while in-place Quick Sort requires $O(\log n)$ call-stack space).
2. Stability requirements (Merge Sort preserves original order of equal keys).
3. Worst-case latency guarantees (Critical systems require Merge Sort's deterministic $O(n \log n)$ ceiling rather than risking Quick Sort's $O(n^2)$ stall).


---

# Q3 – Breaking the Quadratic Barrier: Karatsuba Multiplication (7 Marks)

### Shared Group Work
Implemented standard $O(n^2)$ digit-by-digit multiplication and Karatsuba's divide-and-conquer algorithm.

### Student A – Algorithmic Derivation (Mallapu Vidyadhar)
- **Integer Decomposition:** Let $X = X_H \cdot 10^m + X_L$ and $Y = Y_H \cdot 10^m + Y_L$, where $m = \lceil n/2 ceil$.
  Product $XY = X_H Y_H \cdot 10^{2m} + (X_H Y_L + X_L Y_H) \cdot 10^m + X_L Y_L$.
  Standard approach requires 4 multiplications: $X_H Y_H, X_H Y_L, X_L Y_H, X_L Y_L$.
- **Karatsuba's Algebraic Reduction:**
  $Z_2 = X_H Y_H$
  $Z_0 = X_L Y_L$
  $Z_1 = (X_H + X_L)(Y_H + Y_L) - Z_2 - Z_0 = X_H Y_L + X_L Y_H$
  This requires only 3 multiplications instead of 4.
- **Recurrence & Solution:**
  $$T(n) = 3T(n/2) + \Theta(n)$$
  By Master Theorem ($a=3, b=2$): $\log_2 3 pprox 1.585 > 1$.
  $$T(n) = \Theta(n^{\log_2 3}) pprox \Theta(n^{1.585})$$

### Student B – Crossover Investigation (Shaik Mohammad Sohan)
- **Experimental Operations:**
  For 16-digit integers:
  Standard multiplication: $16 	imes 16 = 256$ single-digit operations.
  Karatsuba recursive calls: 81 calls ($3^4 = 81$).
- **Crossover Point:** Due to addition, subtraction, and call-stack overheads, the practical runtime crossover occurs around 32 to 64 digits (or words). Below this threshold, grade-school multiplication is faster.

### Joint Conclusion
**Answer:** Superior asymptotic complexity dominates only as $n 	o \infty$. When constant factors and additive overheads exceed the computational savings of fewer multiplications, asymptotic superiority does not translate into better performance on small inputs.


---

# Q4 – Strassen's Matrix Multiplication (7 Marks)

### Shared Group Work
Study block matrix multiplication and implement both conventional $O(n^3)$ and Strassen's $O(n^{\log_2 7})$ methods.

### Student B – Algebraic Design & Correctness (Shaik Mohammad Sohan)
- **Block Decomposition:** Partition matrices $A$ and $B$ into four $(n/2) 	imes (n/2)$ submatrices.
- **Eight Standard Multiplications:**
  $C_{11} = A_{11}B_{11} + A_{12}B_{21}$, $C_{12} = A_{11}B_{12} + A_{12}B_{22}$, etc.
- **Strassen's 7 Products:**
  $M_1 = (A_{11} + A_{22})(B_{11} + B_{22})$
  $M_2 = (A_{21} + A_{22})B_{11}$
  $M_3 = A_{11}(B_{12} - B_{22})$
  $M_4 = A_{22}(B_{21} - B_{11})$
  $M_5 = (A_{11} + A_{12})B_{22}$
  $M_6 = (A_{21} - A_{11})(B_{11} + B_{12})$
  $M_7 = (A_{12} - A_{22})(B_{21} + B_{22})$
- **Reconstruction:**
  $C_{11} = M_1 + M_4 - M_5 + M_7 = A_{11}B_{11} + A_{12}B_{21}$ (Algebraically exact).

### Student A – Experimental & Engineering Analysis (Mallapu Vidyadhar)
- **4x4 Matrix Multiplication Counts:**
  Standard algorithm: $n^3 = 4^3 = 64$ multiplications.
  Strassen algorithm: $7^{\log_2 4} = 7^2 = 49$ multiplications (23.4% reduction).
- **Overhead Analysis:** Strassen requires 18 matrix additions/subtractions per recursive level, non-contiguous memory allocations, and cache penalties.

### Joint Engineering Decision
**Conditions for Conventional Multiplication:** Conventional multiplication is preferred when:
1. Matrix dimensions are below crossover ($n < 64$ or $128$).
2. Memory is highly constrained (Strassen requires substantial auxiliary memory).
3. Numerical stability is vital (Strassen introduces higher accumulated round-off errors).


---

# Q5 – Upgrading Closest Pair Using Divide and Conquer (6 Marks)

### Shared Group Work
Implemented $O(n^2)$ pairwise brute force and $O(n \log n)$ divide-and-conquer closest-pair algorithm.

### Student A – Decomposition Design (Mallapu Vidyadhar)
- **Organization:** Presort points by $X$-coordinate ($P_x$) and $Y$-coordinate ($P_y$).
- **Decomposition:** Divide by vertical line $x = x_{mid}$ into left half $Q$ and right half $R$.
- **Recursive Return:** $\delta = \min(\delta_L, \delta_R)$.
- **Combine Step & Strip:** Construct strip of points within distance $\delta$ from vertical dividing line. For each point in strip, only points within $\delta$ vertical distance need inspection (at most 7 subsequent points).

### Student B – Comparative Evidence (Shaik Mohammad Sohan)
- **Measured Operations ($n=10$):**
  Brute-force comparisons: $rac{10 	imes 9}{2} = 45$ distance calculations.
  D&C comparisons: 18 distance calculations.
- **Cross-Understanding:** The strip inspection only requires checking at most 7 neighbors because no two points in the same half can be closer than $\delta$, geometrically packing at most 8 points in a $\delta 	imes 2\delta$ rectangle.


---

# Q6 – Advanced Divide-and-Conquer Mini-Investigations (7 Marks)

### Student A – Median of Medians (Mallapu Vidyadhar)
- **Grouping:** Elements partitioned into $\lfloor n/5 floor$ groups of 5.
- **Pivot Selection:** Compute median of each 5-element group, then recursively find the median of these medians.
- **Guarantee:** At least $3 	imes (\lceil rac{1}{2} \lceil rac{n}{5} ceil ceil) pprox rac{3n}{10}$ elements are strictly smaller and $rac{3n}{10}$ larger.
- **Worst-Case Bound:**
  $$T(n) \le T(n/5) + T(7n/10) + \Theta(n) \implies T(n) = \Theta(n)$$
  Guarantees deterministic $O(n)$ selection.

### Student B – Meet-in-the-Middle (Shaik Mohammad Sohan)
- **Subset Sum Decomposition:** Array of $n$ elements split into two equal halves of size $n/2$.
- **Space Reduction:** Generate $2^{n/2}$ sums for each half. Sort second half and binary search for $Target - s_1$.
- **Complexity:** Reduced from $O(2^n)$ to $O(2^{n/2} \log(2^{n/2})) = O(n \cdot 2^{n/2})$.
- **Trade-off:** Achieves massive time reduction by using $O(2^{n/2})$ auxiliary memory.


---

# Q7 – Day-4 Design Challenge: Divide & Conquer from First Principles (10 Marks)

### Stage 1 – Independent Design Proposals
- **Student A Initial Proposal:** Divide array into equal halves. Maximum subarray either lies completely in left half, completely in right half, or spans across midpoint. Base case: single element returns itself.
- **Student B Initial Proposal:** Divide into halves, return prefix sums, suffix sums, and total sum to allow $O(1)$ combine step.

### Stage 2 – Compare the Two Designs
Both students agreed on the tripartite decomposition. Student B's proposal allows combining in $O(1)$ if 4-tuples are maintained, while Student A's linear crossing scan is simple and gives $O(n \log n)$. The team selected Student A's intuitive crossing scan for primary implementation and Student B's for constant-time combine analysis.

### Stage 3 – Joint Final Design & Pseudocode
```
ALGORITHM MaxSubarray(A, low, high)
  if low == high then
    return (A[low], low, high)
  mid = (low + high) / 2
  (left_sum, l_low, l_high) = MaxSubarray(A, low, mid)
  (right_sum, r_low, r_high) = MaxSubarray(A, mid + 1, high)
  (cross_sum, c_low, c_high) = MaxCrossingSubarray(A, low, mid, high)
  return max of (left_sum, right_sum, cross_sum)
```

### Stage 4 – Mathematical & Experimental Validation
- **Student A (Mathematical):** $T(n) = 2T(n/2) + \Theta(n)$. By Master Theorem ($a=2, b=2, d=1$), $T(n) = \Theta(n \log n)$.
- **Student B (Experimental):** Input `[-2, 1, -3, 4, -1, 2, 1, -5, 4]`. Optimum subarray found: `[4, -1, 2, 1]` with sum $= 6$. Total operations $= 25$.

### Stage 5 – Cross-Defence
- **Student A:** Validated that Kadane's dynamic programming achieves $O(n)$, but D&C provides tree-level parallelism where independent partitions can be computed on separate processor cores.
- **Student B:** Formulated the recurrence from the exact recursive calls and linear midpoint scan.

### Contribution Record
| Question | Student A Contribution (M. Vidyadhar) | Student B Contribution (S. M. Sohan) | Joint Work |
|---|---|---|---|
| Q1 | Mathematical derivation & Recurrence | Experimental setup & Elimination logic | Max/Min Tournament Strategy |
| Q2 | Quick Sort investigation & recursion tree | Merge Sort predictability analysis | Sorting component selection |
| Q3 | Algorithmic derivation of $O(n^{1.585})$ | Crossover point investigation | Overhead analysis |
| Q4 | Experimental matrix operations | Algebraic 7-product correctness | Engineering usage decision |
| Q5 | 2D Geometry decomposition & Strip design | Experimental distance counter & validation | Boundary 7-point geometry proof |
| Q6 | Median of Medians deterministic $O(n)$ | Meet-in-the-Middle subset sum | Selection vs search space trade-off |
| Q7 | Independent proposal & Recurrence solve | Independent proposal & Test implementation | Joint D&C architecture |

