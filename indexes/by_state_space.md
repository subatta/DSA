# Algorithm State Space Classification

This index organizes algorithmic problems by their **state space structure** - the set of all possible solutions or configurations that must be considered. Understanding state space helps identify optimization opportunities and select appropriate algorithmic techniques.

---

## **Understanding State Space Cardinality**

### **Critical Distinction: n(n-1)/2 vs n(n+1)/2**

The difference between **n(n-1)/2** and **n(n+1)/2** is **NOT** about how we generate solutions—it's about **what the problem asks for**:

| Aspect | All Pairs | All Subarrays |
|--------|-----------|---------------|
| **Cardinality** | n(n-1)/2 | n(n+1)/2 |
| **Problem requires** | Two **different** elements | **Any** contiguous sequence |
| **Single element valid?** | ❌ No (pair needs 2 distinct elements) | ✅ Yes (subarray of length 1) |
| **Loop pattern** | `for j = i+1` (enforces i ≠ j) | `for end = start` (allows start = end) |
| **Mathematical basis** | Combinations C(n,2) - choosing 2 positions | Triangular number 1+2+...+n |

**Example with `[2, 3, 5]` (n=3):**

```
All Pairs (n(n-1)/2 = 3):
  (2,3), (2,5), (3,5)
  ❌ No (2,2), (3,3), (5,5) - problem says "two different elements"

All Subarrays (n(n+1)/2 = 6):
  [2], [3], [5], [2,3], [3,5], [2,3,5]
  ✅ Includes [2], [3], [5] - single elements are valid subarrays
```

**Code Pattern Difference:**
```csharp
// All Pairs - Problem: "Find TWO DIFFERENT numbers that sum to target"
for (int i = 0; i < n; i++) 
    for (int j = i + 1; j < n; j++)  // j = i+1 (skip diagonal)
        if (nums[i] + nums[j] == target) ...

// All Subarrays - Problem: "Find contiguous subarray with sum = target"  
for (int start = 0; start < n; start++)
    for (int end = start; end < n; end++)  // end = start (include diagonal)
        if (SubarraySum(start, end) == target) ...
```

---

## **State Space Categories**

### **1. All Pairs: O(n²)**

**Reference:** See [All Pairs State Space](../state_spaces/all_pairs.md) for comprehensive mathematical derivation
Problems requiring **two distinct elements** from the array.

**Characteristics:**
- Cardinality: **n(n-1)/2** pairs (diagonal excluded by problem constraint)
- Generation: Nested loops `for i: for j = i+1`
- Common optimizations: Two Pointers, Hash Map lookup

| Problem | Brute Force | Optimized Pattern | Complexity Reduction |
|---------|-------------|-------------------|---------------------|
| [Two Sum II](../variants/two_pointers/variant_1_two_sum_sorted.md) | O(n²) nested loops | Two Pointers | → O(n) |
| [Two Sum](../variants/hash_map/variant_1_two_sum.md) | O(n²) nested loops | Hash Map | → O(n) |
| [Container With Most Water](../variants/two_pointers/variant_4_container_with_most_water.md) | O(n²) all pairs | Two Pointers | → O(n) |
| [Valid Palindrome](../variants/two_pointers/variant_2_valid_palindrome.md) | O(n²) comparisons | Two Pointers | → O(n) |
| [3Sum](../variants/two_pointers/variant_5_3sum.md) | O(n³) triplets | Sort + Two Pointers | → O(n²) |
| 4Sum | O(n⁴) quadruplets | Sort + Two Pointers | → O(n³) |
| Find Pair with Given Difference | O(n²) pairs | Hash Set | → O(n) |

**Key Insight:** When data is sorted or has monotonic properties, Two Pointers can eliminate one dimension of enumeration. Hash Maps convert O(n) inner loop to O(1) lookup.

---

### **2. All Contiguous Subarrays: O(n²)**

**Reference:** See [All Contiguous Subarrays State Space](../state_spaces/all_contiguous_subarrays.md) for comprehensive mathematical derivation

Problems where **single elements are valid subarrays** and sequences must be contiguous.

**Characteristics:**
- Cardinality: **n(n+1)/2** subarrays (diagonal included)
- Generation: Nested loops `for start: for end = start`
- Common optimizations: Sliding Window, Prefix Sum

#### **2a. Variable-Length Subarrays (with constraints)**

| Problem | State Space | Brute Force | Optimized Pattern | Reduction |
|---------|-------------|-------------|-------------------|-----------|
| [Longest Substring No Repeat](../variants/sliding_window/variant_1_longest_substring_no_repeat.md) | n(n+1)/2 substrings | O(n³) | Sliding Window | → O(n) |
| [Minimum Window Substring](../variants/sliding_window/variant_3_min_window_substring.md) | n(n+1)/2 substrings | O(n²·m) | Sliding Window + Hash Map | → O(n+m) |
| [Subarray Product < K](../variants/sliding_window/variant_4_subarray_product_less_k.md) | n(n+1)/2 subarrays | O(n²) | Sliding Window + Counting | → O(n) |
| [Min Size Subarray Sum ≥ S](../variants/sliding_window/variant_5_min_size_subarray_sum.md) | n(n+1)/2 subarrays | O(n²) | Sliding Window (positive only) | → O(n) |
| [Min Size Subarray Sum (Negatives)](../variants/sliding_window/variant_5b_min_size_subarray_sum_negative.md) | n(n+1)/2 subarrays | O(n²) | Prefix Sum + Monotonic Deque | → O(n) |
| [Longest K Distinct Chars](../variants/sliding_window/variant_7_k_distinct_characters.md) | n(n+1)/2 substrings | O(n²) | Sliding Window + Hash Map | → O(n) |

**Key Insight:** Sliding Window maintains one dynamic window instead of enumerating all O(n²) subarrays explicitly. **Important:** Sliding window requires monotonic property (e.g., all positive integers). For arrays with negative integers, techniques like Prefix Sum + Monotonic Deque are needed.

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
| [Subarray Sum = K](../variants/hash_map/variant_3_subarray_sum_equals_k.md) | n(n+1)/2 subarrays | O(n²) | Prefix Sum + Hash Map | → O(n) |

**Key Insight:** Prefix Sum converts O(n) range calculation into O(1) by precomputation. Hash Map enables counting patterns efficiently.

#### **2d. Grouping / Partitioning Problems**

| Problem | State Space | Brute Force | Optimized Pattern | Reduction |
|---------|-------------|-------------|-------------------|-----------|
| [Group Anagrams](../variants/hash_map/variant_2_group_anagrams.md) | n strings, n groups | O(n² × k) compare all | Hash Map (sorted key) | → O(n × k log k) |
| [Longest Consecutive Seq](../variants/hash_map/variant_4_longest_consecutive_sequence.md) | n numbers | O(n log n) sort | Hash Set + smart iteration | → O(n) |
| [Contains Duplicate II](../variants/hash_map/variant_5_contains_duplicate_ii.md) | n(n-1)/2 pairs | O(n × k) | Hash Map (sliding window) | → O(n) |

**Key Insight:** Hash Map enables O(1) grouping by key. Avoids comparing all pairs or sorting.

---

### **3. All Subsets: O(2ⁿ)**

**Reference:** See [All Subsets (Power Set) State Space](../state_spaces/all_subsets.md) for comprehensive mathematical derivation

Problems that generate all possible subsets (power set).

**Characteristics:**
- Cardinality: 2ⁿ subsets
- Generation: Recursive include/exclude decisions
- Optimization: Early pruning, constraint propagation

| Problem | State Space | Technique | Notes |
|---------|-------------|-----------|-------|
| [Generate All Subsets](../variants/backtracking/variant_1_subsets.md) | 2ⁿ subsets | Backtracking | Can't reduce below O(2ⁿ) if generating all |
| [Combination Sum](../variants/backtracking/variant_3_combination_sum.md) | 2ⁿ combinations | Backtracking | Prune invalid branches |
| Subset Sum | 2ⁿ subsets | Backtracking or DP | DP for decision, not generation |
| Partition Equal Subset | 2ⁿ partitions | DP | O(n·sum) decision problem |

**Key Insight:** When generating all subsets, O(2ⁿ) is unavoidable. Optimization focuses on early pruning or converting to decision problem.

---

### **4. All Permutations: O(n!)**

**Reference:** See [All Permutations State Space](../state_spaces/all_permutations.md) for comprehensive mathematical derivation

Problems that generate all orderings of elements.

**Characteristics:**
- Cardinality: n! permutations
- Generation: Recursive swapping or choices
- Optimization: Early termination, constraint checking

| Problem | State Space | Technique | Notes |
|---------|-------------|-----------|-------|
| [Generate All Permutations](../variants/backtracking/variant_2_permutations.md) | n! permutations | Backtracking | Can't reduce if generating all |
| [N-Queens](../variants/backtracking/variant_4_n_queens.md) | n! placements | Backtracking | Massive pruning via constraints |
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
| [Trapping Rain Water](../variants/two_pointers/variant_6_trapping_rain_water.md) | n² height checks | O(n²) | Monotonic Stack or Two Pointers | → O(n) |

**Key Insight:** Monotonic Stack maintains "potential candidates" by discarding elements that can't be the answer.

---

### **7. In-Place Array Manipulation: O(n) linear scan**
Problems modifying arrays in-place with partitioning or filtering.

**Characteristics:**
- Cardinality: Single pass through n elements
- Generation: Fast/slow pointer or multi-region partitioning
- Optimization: O(1) space by overwriting

| Problem | State Space | Brute Force | Optimized Pattern | Reduction |
|---------|-------------|-------------|-------------------|-----------|
| [Remove Duplicates](../variants/two_pointers/variant_3_remove_duplicates.md) | n elements | O(n) time, O(n) space | Fast/Slow Pointers | → O(1) space |
| [Move Zeros](../variants/two_pointers/variant_7_move_zeros.md) | n elements | O(n) time, O(n) space | Fast/Slow Pointers | → O(1) space |
| [Sort Colors](../variants/two_pointers/variant_8_sort_colors.md) | n elements | O(n) two-pass | Three-way Partition | → O(n) one-pass |
| Remove Element | n elements | O(n) space | Fast/Slow Pointers | → O(1) space |
| Partition Array | n elements | O(n) space | Two Pointers | → O(1) space |

**Key Insight:** Fast pointer scans, slow pointer writes. Since slow ≤ fast always, overwrites are safe.

---

### **8. Interval Overlaps: O(n²) pairs**
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

### **9. Cycle Detection / Linked List Traversal: O(n)**
Problems detecting cycles or finding positions in linked structures.

**Characteristics:**
- Cardinality: Up to n nodes (or infinite if cycle exists)
- Generation: Traversal with fast/slow pointers or HashSet
- Optimization: Floyd's algorithm (O(1) space vs O(n) space)

| Problem | State Space | Brute Force | Optimized Pattern | Reduction |
|---------|-------------|-------------|-------------------|-----------|
| [Linked List Cycle](../variants/fast_slow_pointers/variant_1_linked_list_cycle.md) | n nodes | O(n) time, O(n) space | Fast & Slow Pointers | → O(1) space |
| [Linked List Cycle II](../variants/fast_slow_pointers/variant_2_linked_list_cycle_ii.md) | n nodes | O(n) time, O(n) space | Floyd's Two-Phase | → O(1) space |
| [Find Middle](../variants/fast_slow_pointers/variant_3_find_middle.md) | n nodes | Two passes | Fast & Slow (1 pass) | → Single pass |
| [Palindrome Linked List](../variants/fast_slow_pointers/variant_4_palindrome_linked_list.md) | n/2 comparisons | O(n) space array | Middle + Reverse | → O(1) space |
| [Happy Number](../variants/fast_slow_pointers/variant_5_happy_number.md) | Number sequence | O(log n) space | Fast & Slow | → O(1) space |
| [Remove Nth From End](../variants/fast_slow_pointers/variant_6_remove_nth_from_end.md) | n positions | Two passes | Gap-based pointers | → Single pass |

**Key Insight:** Fast & Slow pointers eliminate need for visited tracking (HashSet) by exploiting speed difference. Works on any sequence, not just linked lists!

---

### **10. Dynamic Programming State Space**
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
| **Two Pointers** | All Pairs O(n²) or In-Place Manipulation O(n) |
| **Fast & Slow Pointers** | Cycle Detection / Linked List Traversal O(n) |
| **Hash Map / Hash Set** | Pairs O(n²) → O(n), Grouping O(n), Prefix Sum queries |
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
