# Algorithm Patterns by Time Complexity

This index organizes patterns and problems by their **time complexity**, helping you choose the right approach based on input size constraints.

---

## **Quick Reference: Constraint → Complexity**

| Input Size (n) | Maximum Complexity | Applicable Patterns |
|----------------|-------------------|---------------------|
| n ≤ 10 | O(n!) or O(2ⁿ) | Backtracking (all solutions) |
| n ≤ 20 | O(2ⁿ) | Backtracking (subset/combination) |
| n ≤ 100 | O(n³) or O(n²) | Two Pointers (3Sum), DP 2D |
| n ≤ 1,000 | O(n²) | Two Pointers, DP 1D (quadratic) |
| n ≤ 10,000 | O(n log n) | Binary Search, Sorting, Heap |
| n ≤ 100,000 | O(n log n) | Binary Search, Monotonic Stack |
| n ≤ 1,000,000+ | O(n) or O(log n) | Sliding Window, Hash Map, Prefix Sum, Two Pointers |

---

## **O(1) - Constant Time**

**Pattern:** Mathematical formulas, direct access

### Problems:
*(Most problems use O(1) as auxiliary operations within larger algorithms)*

---

## **O(log n) - Logarithmic Time**

**Patterns:** Binary Search

### Binary Search Pattern (6 variants)
1. **Classic Binary Search** - O(log n)
2. **Search Insert Position** - O(log n)
3. **Search in Rotated Sorted Array** - O(log n)
4. **Find First and Last Position** - O(log n)
5. **Find Peak Element** - O(log n)
6. **Koko Eating Bananas** - O(n log m) where m = max value

**Key Insight:** Monotonic search space allows halving

---

## **O(n) - Linear Time**

**Patterns:** Sliding Window, Two Pointers, Hash Map, Prefix Sum, Monotonic Stack, Fast & Slow Pointers

### Sliding Window Pattern (8 variants)
1. **Maximum Sum Subarray of Size K** - O(n)
2. **Longest Substring Without Repeating** - O(n)
3. **Minimum Window Substring** - O(n)
4. **Subarray Product Less Than K** - O(n)
5. **Minimum Size Subarray Sum** - O(n)
6. **Sliding Window Maximum** - O(n)
7. **K Distinct Characters** - O(n)

**Key Insight:** Single pass with window state maintenance

### Hash Map Pattern (5 variants)
1. **Two Sum** - O(n)
2. **Group Anagrams** - O(n × k) where k = string length
3. **Subarray Sum Equals K** - O(n)
4. **Longest Consecutive Sequence** - O(n)
5. **Contains Duplicate II** - O(n)

**Key Insight:** O(1) lookup eliminates nested loops

### Prefix Sum Pattern (4 variants)
1. **Range Sum Query** - O(n) preprocessing, O(1) query
2. **Product of Array Except Self** - O(n)
3. **Find Pivot Index** - O(n)
4. **Continuous Subarray Sum** - O(n)

**Key Insight:** Precompute cumulative values for fast queries

### Monotonic Stack Pattern (5 variants)
1. **Next Greater Element** - O(n)
2. **Daily Temperatures** - O(n)
3. **Largest Rectangle in Histogram** - O(n)
4. **Next Greater Element II** - O(n)
5. **Remove K Digits** - O(n)

**Key Insight:** Each element pushed/popped once (amortized)

### Fast & Slow Pointers Pattern (6 variants)
1. **Linked List Cycle** - O(n)
2. **Linked List Cycle II** - O(n)
3. **Find Middle of Linked List** - O(n)
4. **Palindrome Linked List** - O(n)
5. **Happy Number** - O(log n) for sum of squares
6. **Remove Nth Node From End** - O(n)

**Key Insight:** Two-pointer traversal in one pass

### Two Pointers (some variants)
1. **Valid Palindrome** - O(n)
2. **Remove Duplicates** - O(n)
3. **Move Zeros** - O(n)

**Key Insight:** Linear scan with coordinated pointers

### Dynamic Programming 1D (linear variants)
1. **Climbing Stairs** - O(n)
2. **House Robber** - O(n)
3. **Maximum Subarray (Kadane's)** - O(n)
4. **Decode Ways** - O(n)

**Key Insight:** Each state computed once in sequence

---

## **O(n log n) - Linearithmic Time**

**Patterns:** Intervals, Greedy (with sorting), Heap, Top K, Some DP variants

### Intervals Pattern (5 variants)
1. **Merge Intervals** - O(n log n) - sorting
2. **Insert Interval** - O(n) if sorted, O(n log n) if not
3. **Non-Overlapping Intervals** - O(n log n)
4. **Meeting Rooms II** - O(n log n)
5. **Interval List Intersections** - O(n + m) if both sorted

**Key Insight:** Sort by start time, then linear merge

### Greedy Pattern (6 variants)
1. **Activity Selection** - O(n log n) - sort by end time
2. **Jump Game** - O(n)
3. **Jump Game II** - O(n)
4. **Gas Station** - O(n)
5. **Task Scheduler** - O(n log n)
6. **Partition Labels** - O(n)

**Key Insight:** Sorting enables greedy choice validation

### Heap / Priority Queue Pattern (5 variants)
1. **Kth Largest in Stream** - O(log k) per insertion
2. **Find Median from Stream** - O(log n) per insertion
3. **Merge K Sorted Lists** - O(n log k) where n = total elements
4. **Task Scheduler** - O(n log n)
5. **K Closest Points** - O(n log k)

**Key Insight:** Heap operations are O(log n)

### Top K Elements Pattern (4 variants)
1. **Kth Largest Element** - O(n log k) with heap, O(n) with quickselect
2. **Top K Frequent Elements** - O(n log k)
3. **K Closest Points** - O(n log k)
4. **K Pairs with Smallest Sums** - O(k log k)

**Key Insight:** Maintain heap of size k

### Dynamic Programming (with sorting)
1. **Longest Increasing Subsequence** - O(n log n) with binary search

**Key Insight:** Binary search optimization on DP state

---

## **O(n²) - Quadratic Time**

**Patterns:** Two Pointers (nested), Dynamic Programming 1D, Graph (adjacency matrix)

### Two Pointers Pattern (quadratic variants)
1. **Two Sum II (Sorted)** - O(n) but brute force O(n²)
2. **Container With Most Water** - O(n) optimized from O(n²)
3. **3Sum** - O(n²) - outer loop + two pointers
4. **Trapping Rain Water** - O(n) optimized from O(n²)
5. **Sort Colors** - O(n)

**Key Insight:** Fixing one element, scanning with two pointers

### Dynamic Programming 1D (quadratic variants)
1. **Coin Change** - O(n × m) where n = amount, m = coins
2. **Longest Increasing Subsequence** - O(n²) with DP table
3. **Word Break** - O(n² × L) where L = avg word length
4. **Jump Game II** - O(n) greedy, O(n²) with DP

**Key Insight:** Nested loops for state transitions

### Graph Traversal Pattern (6 variants)
1. **Number of Islands** - O(m × n) for grid
2. **Clone Graph** - O(V + E)
3. **Course Schedule** - O(V + E)
4. **Pacific Atlantic Water Flow** - O(m × n)
5. **Word Ladder** - O(n × m × 26) where n = words, m = length
6. **Network Delay Time (Dijkstra)** - O(E log V)

**Key Insight:** Visit each node/edge once

---

## **O(2ⁿ) - Exponential Time**

**Patterns:** Backtracking (generate all solutions)

### Backtracking Pattern (7 variants)
1. **Subsets** - O(n × 2ⁿ) - power set generation
2. **Permutations** - O(n × n!) 
3. **Combination Sum** - O(2ⁿ) average, depends on target
4. **N-Queens** - O(n!)
5. **Palindrome Partitioning** - O(n × 2ⁿ)
6. **Word Search** - O(m × n × 4^L) where L = word length
7. **Generate Parentheses** - O(4ⁿ / √n) - Catalan number

**Key Insight:** Exploring all possible decision trees

**When to use:** n ≤ 20 (for 2ⁿ), n ≤ 10 (for n!)

---

## **Special Complexities**

### O(V + E) - Graph Traversal
**All Graph Pattern variants** - linear in vertices and edges

### O(√n) - Mathematical
**Binary Search variants** can achieve sublinear time for specific problems

### Amortized O(1)
**Stack operations** in Monotonic Stack - each element processed once

---

## **Choosing Based on Constraints**

### If n ≤ 10:
✅ **Backtracking** (can generate all solutions)
✅ **Brute force permutations** O(n!)

### If n ≤ 100:
✅ **O(n²) or O(n³)** algorithms acceptable
✅ Dynamic Programming (quadratic)
✅ Two Pointers (3Sum)

### If n ≤ 10,000:
✅ **O(n log n)** required
✅ Sorting + Binary Search
✅ Heap operations
✅ Intervals merging

### If n ≤ 1,000,000:
✅ **O(n) only**
✅ Sliding Window
✅ Hash Map
✅ Two Pointers (linear)
✅ Prefix Sum
✅ Monotonic Stack

### If n > 1,000,000:
✅ **O(log n) or O(1)**
✅ Binary Search only
✅ Mathematical formulas

---

## **Pattern Complexity Summary**

| Pattern | Typical Complexity | Max Input Size |
|---------|-------------------|----------------|
| **Binary Search** | O(log n) | 10⁹+ |
| **Sliding Window** | O(n) | 10⁶+ |
| **Two Pointers** | O(n) to O(n²) | 10³ - 10⁶ |
| **Fast & Slow Pointers** | O(n) | 10⁶+ |
| **Hash Map** | O(n) | 10⁶+ |
| **Prefix Sum** | O(n) | 10⁶+ |
| **Monotonic Stack** | O(n) | 10⁶+ |
| **Top K Elements** | O(n log k) | 10⁵ |
| **Heap** | O(n log n) | 10⁵ |
| **Intervals** | O(n log n) | 10⁵ |
| **Greedy** | O(n) to O(n log n) | 10⁵ - 10⁶ |
| **Graph (BFS/DFS)** | O(V + E) | 10⁴ - 10⁵ |
| **Backtracking** | O(2ⁿ) to O(n!) | 10 - 20 |
| **Dynamic Programming** | O(n) to O(n²) | 10³ - 10⁵ |

---

**Pro Tip:** Always check the constraints first! They immediately eliminate impossible approaches and guide you toward the correct complexity class.
