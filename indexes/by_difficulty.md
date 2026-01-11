# Algorithm Patterns by Difficulty Level

This index organizes all 83 variants by difficulty, helping you create an effective learning progression from beginner to advanced levels.

---

## **Difficulty Rating Guide**

- **🟢 Easy:** Straightforward pattern application, single technique, clear state space
- **🟡 Medium:** Requires insight, pattern combination, or optimization
- **🔴 Hard:** Multiple techniques, complex state space, or advanced optimization

---

## **🟢 Easy Problems (Beginner-Friendly)**

**Start here to build pattern recognition fundamentals**

### Sliding Window (Easy)
1. **Maximum Sum Subarray of Size K** - Fixed window, simple accumulation
   - Pattern: Fixed-size sliding window
   - Complexity: O(n)

### Two Pointers (Easy)
1. **Two Sum II (Sorted Array)** - LeetCode #167
   - Pattern: Opposite-direction pointers
   - Complexity: O(n)

2. **Valid Palindrome** - LeetCode #125
   - Pattern: Compare from both ends
   - Complexity: O(n)

3. **Remove Duplicates from Sorted Array** - LeetCode #26
   - Pattern: Fast & slow pointers
   - Complexity: O(n)

4. **Move Zeros** - LeetCode #283
   - Pattern: In-place partition
   - Complexity: O(n)

### Fast & Slow Pointers (Easy)
1. **Linked List Cycle** - LeetCode #141 ⭐
   - Pattern: Floyd's cycle detection
   - Complexity: O(n)

2. **Find Middle of Linked List** - LeetCode #876
   - Pattern: Fast 2x, slow 1x
   - Complexity: O(n)

3. **Palindrome Linked List** - LeetCode #234
   - Pattern: Find middle + reverse + compare
   - Complexity: O(n)

4. **Happy Number** - LeetCode #202
   - Pattern: Cycle detection in sequences
   - Complexity: O(log n)

### Hash Map (Easy)
1. **Two Sum** - LeetCode #1 ⭐
   - Pattern: Complement lookup
   - Complexity: O(n)

2. **Contains Duplicate II** - LeetCode #219
   - Pattern: Sliding hash map
   - Complexity: O(n)

### Binary Search (Easy)
1. **Classic Binary Search** - LeetCode #704
   - Pattern: Standard template
   - Complexity: O(log n)

2. **Search Insert Position** - LeetCode #35
   - Pattern: Find insertion point
   - Complexity: O(log n)

### Prefix Sum (Easy)
1. **Range Sum Query - Immutable** - LeetCode #303
   - Pattern: Precompute cumulative sums
   - Complexity: O(n) prep, O(1) query

2. **Find Pivot Index** - LeetCode #724
   - Pattern: Left sum = right sum
   - Complexity: O(n)

### Dynamic Programming (Easy)
1. **Climbing Stairs** - LeetCode #70 ⭐
   - Pattern: Fibonacci recurrence
   - Complexity: O(n)

### Greedy (Easy)
1. **Jump Game** - LeetCode #55
   - Pattern: Track max reachable
   - Complexity: O(n)

---

## **🟡 Medium Problems (Intermediate)**

**Build on fundamentals with more complex pattern applications**

### Sliding Window (Medium)
1. **Longest Substring Without Repeating** - LeetCode #3 ⭐
   - Pattern: Variable window + hash set
   - Complexity: O(n)

2. **Subarray Product Less Than K** - LeetCode #713
   - Pattern: Shrinking window
   - Complexity: O(n)

3. **Minimum Size Subarray Sum** - LeetCode #209
   - Pattern: Grow-then-shrink
   - Complexity: O(n)

4. **K Distinct Characters** - LeetCode #340
   - Pattern: Variable window + frequency map
   - Complexity: O(n)

### Two Pointers (Medium)
1. **Container With Most Water** - LeetCode #11
   - Pattern: Maximize area, greedy movement
   - Complexity: O(n)

2. **3Sum** - LeetCode #15 ⭐
   - Pattern: Fix one + two pointers
   - Complexity: O(n²)

3. **Sort Colors (Dutch National Flag)** - LeetCode #75
   - Pattern: Three-way partition
   - Complexity: O(n)

### Fast & Slow Pointers (Medium)
1. **Linked List Cycle II** - LeetCode #142
   - Pattern: Find cycle start (two phases)
   - Complexity: O(n)

2. **Remove Nth Node From End** - LeetCode #19
   - Pattern: Gap-based two pointers
   - Complexity: O(n)

### Hash Map (Medium)
1. **Group Anagrams** - LeetCode #49 ⭐
   - Pattern: Group by sorted key
   - Complexity: O(n × k log k)

2. **Subarray Sum Equals K** - LeetCode #560 ⭐
   - Pattern: Prefix sum + frequency map
   - Complexity: O(n)

3. **Longest Consecutive Sequence** - LeetCode #128
   - Pattern: Hash set for O(1) lookup
   - Complexity: O(n)

### Binary Search (Medium)
1. **Search in Rotated Sorted Array** - LeetCode #33
   - Pattern: Modified binary search
   - Complexity: O(log n)

2. **Find First and Last Position** - LeetCode #34
   - Pattern: Two binary searches (boundaries)
   - Complexity: O(log n)

3. **Find Peak Element** - LeetCode #162
   - Pattern: Binary search for local max
   - Complexity: O(log n)

### Monotonic Stack (Medium)
1. **Next Greater Element** - LeetCode #496
   - Pattern: Decreasing stack
   - Complexity: O(n)

2. **Daily Temperatures** - LeetCode #739 ⭐
   - Pattern: Distance to next greater
   - Complexity: O(n)

3. **Next Greater Element II** - LeetCode #503
   - Pattern: Circular array (2x pass)
   - Complexity: O(n)

### Intervals (Medium)
1. **Merge Intervals** - LeetCode #56 ⭐
   - Pattern: Sort + sweep
   - Complexity: O(n log n)

2. **Insert Interval** - LeetCode #57
   - Pattern: Three-phase insertion
   - Complexity: O(n)

3. **Non-Overlapping Intervals** - LeetCode #435
   - Pattern: Greedy (earliest end)
   - Complexity: O(n log n)

4. **Interval List Intersections** - LeetCode #986
   - Pattern: Two-pointer merge
   - Complexity: O(n + m)

### Heap / Priority Queue (Medium)
1. **Kth Largest in Stream** - LeetCode #703
   - Pattern: Min-heap of size K
   - Complexity: O(log k) per insert

2. **Task Scheduler** - LeetCode #621
   - Pattern: Greedy with heap
   - Complexity: O(n log 26)

3. **K Closest Points** - LeetCode #973
   - Pattern: Min-heap of size K
   - Complexity: O(n log k)

### Top K Elements (Medium)
1. **Top K Frequent Elements** - LeetCode #347 ⭐
   - Pattern: Frequency + heap
   - Complexity: O(n log k)

2. **K Closest Points** - LeetCode #973
   - Pattern: Distance + heap
   - Complexity: O(n log k)

### Greedy (Medium)
1. **Jump Game II** - LeetCode #45
   - Pattern: BFS levels (greedy)
   - Complexity: O(n)

2. **Gas Station** - LeetCode #134
   - Pattern: Single pass with total/current
   - Complexity: O(n)

3. **Partition Labels** - LeetCode #763
   - Pattern: Greedy boundary extension
   - Complexity: O(n)

### Graph (Medium)
1. **Clone Graph** - LeetCode #133
   - Pattern: DFS with map
   - Complexity: O(V + E)

2. **Course Schedule** - LeetCode #207 ⭐
   - Pattern: Topological sort (cycle detection)
   - Complexity: O(V + E)

3. **Pacific Atlantic Water Flow** - LeetCode #417
   - Pattern: Multi-source DFS
   - Complexity: O(m × n)

### Dynamic Programming (Medium)
1. **House Robber** - LeetCode #198 ⭐
   - Pattern: Binary choice (rob or skip)
   - Complexity: O(n)

2. **Coin Change** - LeetCode #322 ⭐
   - Pattern: Unbounded knapsack
   - Complexity: O(n × m)

3. **Maximum Subarray (Kadane's)** - LeetCode #53 ⭐
   - Pattern: Extend or restart
   - Complexity: O(n)

4. **Word Break** - LeetCode #139 ⭐
   - Pattern: String DP
   - Complexity: O(n²)

5. **Decode Ways** - LeetCode #91
   - Pattern: Count ways (1-digit + 2-digit)
   - Complexity: O(n)

6. **Jump Game II** - LeetCode #45
   - Pattern: DP or greedy (BFS levels)
   - Complexity: O(n)

### Backtracking (Medium)
1. **Subsets** - LeetCode #78 ⭐
   - Pattern: Include/exclude decisions
   - Complexity: O(n × 2ⁿ)

2. **Permutations** - LeetCode #46 ⭐
   - Pattern: Used tracking or swap
   - Complexity: O(n × n!)

3. **Combination Sum** - LeetCode #39
   - Pattern: Unlimited reuse, target pruning
   - Complexity: O(N^(T/M))

4. **Palindrome Partitioning** - LeetCode #131
   - Pattern: Try all cuts, validate palindromes
   - Complexity: O(n × 2ⁿ)

5. **Generate Parentheses** - LeetCode #22
   - Pattern: Constrained generation (open/close counts)
   - Complexity: O(4ⁿ / √n) - Catalan

### Prefix Sum (Medium)
1. **Product of Array Except Self** - LeetCode #238
   - Pattern: Left × right products
   - Complexity: O(n)

2. **Continuous Subarray Sum** - LeetCode #523
   - Pattern: Prefix sum mod k
   - Complexity: O(n)

---

## **🔴 Hard Problems (Advanced)**

**Master complex techniques and optimizations**

### Sliding Window (Hard)
1. **Minimum Window Substring** - LeetCode #76 ⭐⭐
   - Pattern: Shrink-while-valid + frequency map
   - Complexity: O(n + m)
   - **Why Hard:** Contract-while-valid strategy, complex state tracking

2. **Sliding Window Maximum** - LeetCode #239 ⭐⭐
   - Pattern: Monotonic deque
   - Complexity: O(n)
   - **Why Hard:** Requires deque, not intuitive optimization

3. **Minimum Size Subarray Sum (Negative)** - Hard variant
   - Pattern: Prefix sum + monotonic deque (SW fails!)
   - Complexity: O(n)
   - **Why Hard:** Shows when sliding window breaks, hybrid approach

### Two Pointers (Hard)
1. **Trapping Rain Water** - LeetCode #42 ⭐⭐
   - Pattern: Two pointers with left/right max tracking
   - Complexity: O(n)
   - **Why Hard:** Non-obvious optimization, needs insight about water level

### Binary Search (Hard)
1. **Koko Eating Bananas** - LeetCode #875
   - Pattern: Binary search on answer space
   - Complexity: O(n log m)
   - **Why Hard:** Not searching array, searching answer space

### Monotonic Stack (Hard)
1. **Largest Rectangle in Histogram** - LeetCode #84 ⭐⭐
   - Pattern: Find left/right boundaries with stack
   - Complexity: O(n)
   - **Why Hard:** Complex boundary logic, sentinel values

2. **Remove K Digits** - LeetCode #402
   - Pattern: Increasing stack for smallest number
   - Complexity: O(n)
   - **Why Hard:** Edge cases (leading zeros, k > n)

### Intervals (Hard)
1. **Meeting Rooms II** - LeetCode #253
   - Pattern: Heap (sweep line) or sort start/end separately
   - Complexity: O(n log n)
   - **Why Hard:** Need to track concurrent intervals, heap management

### Heap / Priority Queue (Hard)
1. **Find Median from Stream** - LeetCode #295 ⭐⭐
   - Pattern: Two heaps (max-heap + min-heap)
   - Complexity: O(log n) per insert, O(1) get median
   - **Why Hard:** Balance two heaps, non-obvious data structure choice

2. **Merge K Sorted Lists** - LeetCode #23 ⭐
   - Pattern: Min-heap with list heads
   - Complexity: O(n log k)
   - **Why Hard:** Multi-way merge, heap management

### Top K Elements (Hard)
1. **K Pairs with Smallest Sums** - LeetCode #373
   - Pattern: Min-heap with candidate generation
   - Complexity: O(k log k)
   - **Why Hard:** Avoid generating all pairs, smart candidate management

### Graph (Hard)
1. **Word Ladder** - LeetCode #127 ⭐⭐
   - Pattern: BFS shortest path
   - Complexity: O(n × m × 26) where m = word length
   - **Why Hard:** Building graph on the fly, bidirectional BFS optimization

2. **Network Delay Time (Dijkstra)** - LeetCode #743
   - Pattern: Dijkstra's shortest path
   - Complexity: O(E log V)
   - **Why Hard:** Requires understanding of Dijkstra's algorithm

### Dynamic Programming (Hard)
1. **Longest Increasing Subsequence** - LeetCode #300 ⭐
   - Pattern: O(n²) DP or O(n log n) with binary search
   - Complexity: O(n log n) optimal
   - **Why Hard:** Non-obvious binary search optimization

### Backtracking (Hard)
1. **N-Queens** - LeetCode #51 ⭐⭐
   - Pattern: Constraint satisfaction, diagonal tracking
   - Complexity: O(n!)
   - **Why Hard:** Complex constraint checking, optimizations needed

2. **Word Search** - LeetCode #79
   - Pattern: Grid DFS with in-place visited marking
   - Complexity: O(m × n × 4^L)
   - **Why Hard:** 4-directional search, backtrack visited state

### Greedy (Hard)
1. **Task Scheduler** - LeetCode #621
   - Pattern: Greedy with formula or heap
   - Complexity: O(n)
   - **Why Hard:** Mathematical insight needed, or complex heap logic

---

## **Learning Progression Paths**

### Path 1: Hash Map Focus (Week 1)
🟢 Two Sum → 🟢 Contains Duplicate II → 🟡 Group Anagrams → 🟡 Subarray Sum Equals K → 🟡 Longest Consecutive Sequence

### Path 2: Two Pointers (Week 1-2)
🟢 Valid Palindrome → 🟢 Two Sum II → 🟢 Remove Duplicates → 🟡 Container With Most Water → 🟡 3Sum → 🔴 Trapping Rain Water

### Path 3: Sliding Window (Week 2)
🟢 Max Sum Size K → 🟡 Longest Substring Without Repeating → 🟡 Min Size Subarray Sum → 🔴 Minimum Window Substring

### Path 4: Binary Search (Week 2)
🟢 Classic Binary Search → 🟢 Search Insert Position → 🟡 Search Rotated Array → 🟡 Find First/Last → 🔴 Koko Bananas

### Path 5: Stack (Week 3)
🟡 Next Greater Element → 🟡 Daily Temperatures → 🔴 Largest Rectangle → 🔴 Remove K Digits

### Path 6: Linked List (Week 3)
🟢 Linked List Cycle → 🟢 Find Middle → 🟡 Cycle II → 🟢 Happy Number → 🟡 Remove Nth From End

### Path 7: Heap & Top K (Week 4)
🟡 Kth Largest → 🟡 Top K Frequent → 🟡 K Closest Points → 🔴 Find Median → 🔴 Merge K Lists

### Path 8: Intervals (Week 4)
🟡 Merge Intervals → 🟡 Insert Interval → 🟡 Non-Overlapping → 🔴 Meeting Rooms II

### Path 9: Graph (Week 5)
🟡 Number of Islands → 🟡 Clone Graph → 🟡 Course Schedule → 🟡 Pacific Atlantic → 🔴 Word Ladder

### Path 10: Dynamic Programming (Week 6-7)
🟢 Climbing Stairs → 🟡 House Robber → 🟡 Coin Change → 🟡 Max Subarray → 🟡 Word Break → 🔴 LIS (O(n log n))

### Path 11: Backtracking (Week 7-8)
🟡 Subsets → 🟡 Permutations → 🟡 Combination Sum → 🟡 Palindrome Partitioning → 🔴 N-Queens → 🔴 Word Search

### Path 12: Greedy (Week 5-6)
🟢 Jump Game → 🟡 Jump Game II → 🟡 Gas Station → 🟡 Partition Labels → 🔴 Task Scheduler

---

## **Difficulty Statistics**

### By Pattern:
| Pattern | Easy | Medium | Hard | Total |
|---------|------|--------|------|-------|
| Sliding Window | 1 | 4 | 3 | 8 |
| Two Pointers | 4 | 3 | 1 | 8 |
| Fast & Slow Pointers | 4 | 2 | 0 | 6 |
| Hash Map | 2 | 3 | 0 | 5 |
| Prefix Sum | 2 | 2 | 0 | 4 |
| Binary Search | 2 | 3 | 1 | 6 |
| Monotonic Stack | 0 | 3 | 2 | 5 |
| Top K Elements | 0 | 3 | 1 | 4 |
| Heap / Priority Queue | 0 | 3 | 2 | 5 |
| Intervals | 0 | 4 | 1 | 5 |
| Greedy | 1 | 4 | 1 | 6 |
| Graph | 0 | 5 | 2 | 7 |
| Dynamic Programming | 1 | 6 | 1 | 8 |
| Backtracking | 0 | 5 | 2 | 7 |
| **TOTAL** | **17** | **50** | **17** | **83** |

### Overall Distribution:
- **🟢 Easy:** 17 problems (20%)
- **🟡 Medium:** 49 problems (59%)
- **🔴 Hard:** 17 problems (20%)

---

## **Interview Prep Strategy by Level**

### Beginner (First 2 weeks):
- Focus on **Easy + foundational Medium** problems
- Master: Hash Map, Two Pointers, Sliding Window basics
- Goal: 20-25 problems

### Intermediate (Weeks 3-6):
- Focus on **Medium** problems across all patterns
- Master: DP 1D, Graph, Backtracking, Heap
- Goal: 40-50 problems total

### Advanced (Weeks 7-8):
- Focus on **Hard** problems and optimizations
- Master: Advanced DP, complex backtracking, Dijkstra
- Goal: All 83 variants

### Pre-Interview Polish (Week before):
- Review **starred (⭐) problems** - most common in interviews
- Practice **Pattern Recognition** guide
- Time yourself: 15-20 mins for Easy, 25-35 for Medium, 40+ for Hard

---

**Pro Tip:** Don't jump to Hard too early! Master Easy and Medium thoroughly first. Understanding **why** a solution works is more important than memorizing solutions.
