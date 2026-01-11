# Algorithm State Space Classification

This index organizes algorithmic problems by their **state space structure** - the set of all possible solutions or configurations that must be considered. Understanding state space helps identify optimization opportunities and select appropriate algorithmic techniques.

---

## **State Space Categories**

### **1. All Pairs: O(n²)**
Problems that enumerate all possible pairs of elements.

**Characteristics:**
- Cardinality: n(n-1)/2 pairs
- Generation: Nested loops `for i: for j > i`
- Common optimizations: Two Pointers, Hash Map lookup

| Problem | Brute Force | Optimized Pattern | Complexity Reduction |
|---------|-------------|-------------------|---------------------|
| [Two Sum II](../variants/two_pointers/variant_1_two_sum_sorted.md) | O(n²) nested loops | Two Pointers | → O(n) |
| Container With Most Water | O(n²) all pairs | Two Pointers | → O(n) |
| Valid Palindrome | O(n²) comparisons | Two Pointers | → O(n) |
| 3Sum | O(n³) triplets | Sort + Two Pointers | → O(n²) |

**Key Insight:** When data is sorted or has monotonic properties, Two Pointers can eliminate one dimension of enumeration.

---

### **2. All Contiguous Subarrays: O(n²)**
Problems that examine all contiguous sequences (subarrays/substrings).

**Characteristics:**
- Cardinality: n(n+1)/2 subarrays
- Generation: Nested loops `for start: for end >= start`
- Common optimizations: Sliding Window, Prefix Sum

#### **2a. Variable-Length Subarrays (with constraints)**

| Problem | State Space | Brute Force | Optimized Pattern | Reduction |
|---------|-------------|-------------|-------------------|-----------|
| [Longest Substring No Repeat](../variants/sliding_window/variant_1_longest_substring_no_repeat.md) | n(n+1)/2 substrings | O(n³) | Sliding Window | → O(n) |
| [Minimum Window Substring](../variants/sliding_window/variant_3_min_window_substring.md) | n(n+1)/2 substrings | O(n²·m) | Sliding Window + Hash Map | → O(n+m) |
| [Subarray Product < K](../variants/sliding_window/variant_4_subarray_product_less_k.md) | n(n+1)/2 subarrays | O(n²) | Sliding Window + Counting | → O(n) |
| [Min Size Subarray Sum ≥ S](../variants/sliding_window/variant_5_min_size_subarray_sum.md) | n(n+1)/2 subarrays | O(n²) | Sliding Window | → O(n) |
| [Longest K Distinct Chars](../variants/sliding_window/variant_7_k_distinct_characters.md) | n(n+1)/2 substrings | O(n²) | Sliding Window + Hash Map | → O(n) |

**Key Insight:** Sliding Window maintains one dynamic window instead of enumerating all O(n²) subarrays explicitly.

#### **2b. Fixed-Length Subarrays**

| Problem | State Space | Brute Force | Optimized Pattern | Reduction |
|---------|-------------|-------------|-------------------|-----------|
| [Maximum Sum Size K](../variants/sliding_window/variant_2_max_sum_size_k.md) | n-k+1 windows | O(n·k) | Sliding Window | → O(n) |
| [Sliding Window Maximum](../variants/sliding_window/variant_6_sliding_window_maximum.md) | n-k+1 windows | O(n·k) | Sliding Window + Monotonic Deque | → O(n) |
| Maximum Average Subarray | n-k+1 windows | O(n·k) | Sliding Window | → O(n) |

**Key Insight:** Fixed windows allow incremental updates (add one, remove one) in O(1) per slide.

#### **2c. Subarray Sum Queries**

| Problem | State Space | Brute Force | Optimized Pattern | Reduction |
|---------|-------------|-------------|-------------------|-----------|
| Range Sum Query | n(n+1)/2 ranges | O(n) per query | Prefix Sum | → O(1) per query |
| Subarray Sum = K | n(n+1)/2 subarrays | O(n²) | Prefix Sum + Hash Map | → O(n) |
| Contiguous Array | n(n+1)/2 subarrays | O(n²) | Prefix Sum + Hash Map | → O(n) |

**Key Insight:** Prefix Sum converts O(n) range calculation into O(1) by precomputation.

---

### **3. All Subsets: O(2ⁿ)**
Problems that generate all possible subsets (power set).

**Characteristics:**
- Cardinality: 2ⁿ subsets
- Generation: Recursive include/exclude decisions
- Optimization: Early pruning, constraint propagation

| Problem | State Space | Technique | Notes |
|---------|-------------|-----------|-------|
| Generate All Subsets | 2ⁿ subsets | Backtracking | Can't reduce below O(2ⁿ) if generating all |
| Combination Sum | 2ⁿ combinations | Backtracking | Prune invalid branches |
| Subset Sum | 2ⁿ subsets | Backtracking or DP | DP for decision, not generation |
| Partition Equal Subset | 2ⁿ partitions | DP | O(n·sum) decision problem |

**Key Insight:** When generating all subsets, O(2ⁿ) is unavoidable. Optimization focuses on early pruning or converting to decision problem.

---

### **4. All Permutations: O(n!)**
Problems that generate all orderings of elements.

**Characteristics:**
- Cardinality: n! permutations
- Generation: Recursive swapping or choices
- Optimization: Early termination, constraint checking

| Problem | State Space | Technique | Notes |
|---------|-------------|-----------|-------|
| Generate All Permutations | n! permutations | Backtracking | Can't reduce if generating all |
| N-Queens | n! placements | Backtracking | Massive pruning via constraints |
| Traveling Salesman | n! routes | DP + Bitmask | → O(2ⁿ·n²) with DP |
| Sudoku Solver | 9⁸¹ states | Backtracking | Constraints eliminate most |

**Key Insight:** Constraints and early validation prune exponential search trees dramatically.

---

### **5. Matrix/Grid Traversal: O(4ⁿ) paths**
Problems exploring paths in 2D grids.

**Characteristics:**
- Cardinality: Up to 4ⁿ paths (4 directions, n steps)
- Generation: DFS/BFS from start
- Optimization: Visited tracking, early exit

| Problem | State Space | Technique | Reduction |
|---------|-------------|-----------|-----------|
| Number of Islands | All cells | DFS/BFS | O(m·n) with visited marking |
| Word Search | 4ⁿ paths | Backtracking | Prune with character matching |
| Shortest Path (unweighted) | All paths | BFS | First path = shortest |
| Maze Solving | All paths | BFS | O(m·n) visited once |

**Key Insight:** Visited tracking prevents revisiting states, reducing exponential to polynomial.

---

### **6. Nested Comparisons: O(n²)**
Problems requiring element-to-element comparisons.

**Characteristics:**
- Cardinality: n² comparisons
- Generation: Nested iteration
- Optimization: Monotonic stack, precomputation

| Problem | State Space | Brute Force | Optimized Pattern | Reduction |
|---------|-------------|-------------|-------------------|-----------|
| Next Greater Element | n² comparisons | O(n²) | Monotonic Stack | → O(n) |
| Daily Temperatures | n² comparisons | O(n²) | Monotonic Stack | → O(n) |
| Largest Rectangle Histogram | n² rectangles | O(n²) | Monotonic Stack | → O(n) |
| Trapping Rain Water | n² height checks | O(n²) | Monotonic Stack or Two Pointers | → O(n) |

**Key Insight:** Monotonic Stack maintains "potential candidates" by discarding elements that can't be the answer.

---

### **7. Interval Overlaps: O(n²) pairs**
Problems checking overlaps between ranges.

**Characteristics:**
- Cardinality: n(n-1)/2 pairs
- Generation: Compare all pairs
- Optimization: Sort + sweep

| Problem | State Space | Brute Force | Optimized Pattern | Reduction |
|---------|-------------|-------------|-------------------|-----------|
| Merge Intervals | n² pairs | O(n²) | Sort + Sweep | → O(n log n) |
| Meeting Rooms II | n² overlaps | O(n²) | Sort + Heap | → O(n log n) |
| Interval Intersection | n² pairs | O(n²) | Sort + Two Pointers | → O(n+m) |
| Employee Free Time | n² intervals | O(n²) | Sort + Merge | → O(n log n) |

**Key Insight:** Sorting enables linear sweep to detect overlaps without checking all pairs.

---

### **8. Dynamic Programming State Space**
Problems with overlapping subproblems.

**Characteristics:**
- Cardinality: Varies (often O(n), O(n²), or O(n·target))
- Generation: Recurrence relation
- Optimization: Memoization eliminates redundant computation

| Problem | State Space | Naive Recursion | DP Optimization | Reduction |
|---------|-------------|-----------------|-----------------|-----------|
| Fibonacci | O(n) states | O(2ⁿ) | DP | → O(n) |
| Climbing Stairs | O(n) states | O(2ⁿ) | DP | → O(n) |
| Coin Change | O(n·target) | Exponential | DP | → O(n·target) |
| Longest Increasing Subseq | O(n²) | O(2ⁿ) | DP | → O(n²) or O(n log n) |
| Knapsack | O(n·W) | O(2ⁿ) | DP | → O(n·W) |

**Key Insight:** Overlapping subproblems cause exponential redundancy. Memoization ensures each state computed once.

---

## **Cross-Reference: Pattern → State Space**

| Pattern | Primary State Space Category |
|---------|------------------------------|
| **Sliding Window** | All Contiguous Subarrays O(n²) |
| **Two Pointers** | All Pairs O(n²) |
| **Hash Map** | Various (often paired with above) |
| **Prefix Sum** | All Subarrays O(n²) |
| **Binary Search** | Sorted Search Space O(n) or O(log range) |
| **Monotonic Stack** | Nested Comparisons O(n²) |
| **Heap/Priority Queue** | Dynamic sorted access |
| **Intervals** | Interval Overlaps O(n²) |
| **Backtracking** | Subsets O(2ⁿ) or Permutations O(n!) |
| **Graph Traversal** | Grid/Graph Paths O(4ⁿ) or O(V+E) |
| **Dynamic Programming** | Overlapping Subproblems (various) |
| **Greedy** | Reduced to single pass O(n) |

---

## **Using This Index**

1. **Identify your problem's state space** from the problem statement
2. **Find the category** that matches (pairs, subarrays, subsets, etc.)
3. **Check optimizations** used by similar problems in that category
4. **Learn the technique** from linked variant files
5. **Apply pattern** to your specific problem

---

## **Related Indexes:**
- [By Pattern](by_pattern.md) - Traditional pattern-based organization
- [By Complexity](by_complexity.md) - Grouped by time/space complexity
- [By Hybrid Techniques](by_hybrid.md) - Problems using multiple patterns
