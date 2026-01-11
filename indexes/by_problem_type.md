# Algorithm Patterns by Problem Type

This index organizes patterns by the **goal or objective** of the problem, helping you identify the right approach based on what you're trying to achieve.

---

## **Optimization Problems (Min/Max)**

**Goal:** Find minimum or maximum value/count under constraints

### Primary Patterns:

#### 1. **Dynamic Programming 1D** (8 variants)
**Use when:** Overlapping subproblems, need to try multiple choices

- **Climbing Stairs** - Count ways to reach top
- **House Robber** - Maximum sum (non-adjacent)
- **Coin Change** - Minimum coins to make amount
- **Longest Increasing Subsequence** - Longest length
- **Maximum Subarray (Kadane's)** - Maximum sum
- **Word Break** - Can partition string?
- **Decode Ways** - Count valid decodings
- **Jump Game II** - Minimum jumps

**Signal Words:** "minimum cost", "maximum value", "count ways to"

#### 2. **Greedy** (6 variants)
**Use when:** Local optimal leads to global optimal

- **Activity Selection** - Maximum non-overlapping activities
- **Jump Game** - Can reach end?
- **Jump Game II** - Minimum jumps
- **Gas Station** - Find starting point
- **Task Scheduler** - Minimum idle time
- **Partition Labels** - Maximum partition count

**Signal Words:** "minimum", "maximum", "optimal", "scheduling"

**How to decide DP vs Greedy:**
- **Greedy:** Can prove locally optimal → globally optimal (exchange argument)
- **DP:** Need to explore multiple choices, can't commit early

#### 3. **Sliding Window** (optimization variants)
- **Maximum Sum Subarray of Size K** - Maximum sum
- **Minimum Size Subarray Sum** - Minimum length
- **Minimum Window Substring** - Smallest window

**Use when:** Optimizing over contiguous sequences

---

## **Search Problems**

**Goal:** Find specific element, position, or boundary

### Primary Patterns:

#### 1. **Binary Search** (6 variants)
**Use when:** Search space is sorted or monotonic

- **Classic Binary Search** - Find exact value
- **Search Insert Position** - Find insertion point
- **Search in Rotated Sorted Array** - Modified search
- **Find First and Last Position** - Find boundaries
- **Find Peak Element** - Find local maximum
- **Koko Eating Bananas** - Binary search on answer

**Signal Words:** "sorted", "find", "search for", "locate"

#### 2. **Hash Map** (lookup variants)
- **Two Sum** - Find pair with target sum
- **Contains Duplicate II** - Find duplicate within distance
- **Longest Consecutive Sequence** - Find longest sequence

**Use when:** Need fast O(1) lookup

#### 3. **Graph Traversal (BFS)** (shortest path)
- **Word Ladder** - Shortest transformation
- **Network Delay Time** - Shortest path (weighted)
- **Number of Islands** - Find all components

**Use when:** Searching graphs, finding paths

---

## **Counting Problems**

**Goal:** Count number of ways, combinations, or occurrences

### Primary Patterns:

#### 1. **Dynamic Programming** (counting variants)
- **Climbing Stairs** - Count ways to climb
- **Decode Ways** - Count valid decodings
- **Coin Change II** - Count combinations

**Use when:** Need to count all valid ways

#### 2. **Backtracking** (generation + counting)
- **Subsets** - Count/generate all subsets (2ⁿ)
- **Permutations** - Count/generate all orderings (n!)
- **Combination Sum** - Count valid combinations
- **Generate Parentheses** - Count valid strings

**Use when:** Small n (≤ 20), need explicit generation or counting

#### 3. **Hash Map** (frequency counting)
- **Group Anagrams** - Count groups
- **Subarray Sum Equals K** - Count subarrays
- **Top K Frequent Elements** - Count frequencies

**Use when:** Counting occurrences or frequencies

---

## **Generation Problems (Find All)**

**Goal:** Generate/list all possible solutions

### Primary Pattern: **Backtracking** (7 variants)

1. **Subsets** - Generate all subsets (power set)
2. **Permutations** - Generate all orderings
3. **Combination Sum** - Generate all combinations
4. **N-Queens** - Generate all valid board placements
5. **Palindrome Partitioning** - Generate all palindrome partitions
6. **Word Search** - Find if word exists in grid
7. **Generate Parentheses** - Generate all valid parentheses

**Characteristics:**
- Exponential time: O(2ⁿ) to O(n!)
- Only feasible for small n (typically ≤ 20)
- Explores decision tree with backtracking

**Signal Words:** "generate all", "find all", "list all", "all possible", "all combinations"

**Template:**
```csharp
void Backtrack(state, startIndex) {
    if (IsComplete(state)) {
        result.Add(Copy(state));
        return;
    }
    for (int i = startIndex; i < n; i++) {
        MakeChoice(state, i);
        Backtrack(state, i + 1); // or i for reuse
        UndoChoice(state, i);
    }
}
```

---

## **Sequence/Range Problems**

**Goal:** Find properties of contiguous sequences

### Primary Patterns:

#### 1. **Sliding Window** (8 variants)
**Use when:** Need to process contiguous subarray/substring

- **Maximum Sum Subarray of Size K** - Fixed window
- **Longest Substring Without Repeating** - Variable window
- **Minimum Window Substring** - Shrinkable window
- **Subarray Product Less Than K** - Constraint-based
- **Sliding Window Maximum** - Track max in window

**Signal Words:** "subarray", "substring", "contiguous", "consecutive"

#### 2. **Prefix Sum** (4 variants)
**Use when:** Need sum of ranges efficiently

- **Range Sum Query** - O(1) range sums
- **Subarray Sum Equals K** - Count subarrays with sum
- **Continuous Subarray Sum** - Multiple of k
- **Find Pivot Index** - Equal left/right sums

**Signal Words:** "range sum", "subarray sum", "cumulative"

#### 3. **Two Pointers**
- **Container With Most Water** - Maximum area
- **Trapping Rain Water** - Water trapped
- **3Sum** - Find triplets

**Use when:** Can eliminate options by comparing

---

## **Sorting/Ordering Problems**

**Goal:** Arrange elements or find order

### Primary Patterns:

#### 1. **Intervals / Merging** (5 variants)
**Use when:** Dealing with ranges/intervals

- **Merge Intervals** - Collapse overlaps
- **Insert Interval** - Maintain sorted intervals
- **Non-Overlapping Intervals** - Remove minimum
- **Meeting Rooms II** - Count overlaps
- **Interval List Intersections** - Find overlaps

**Key:** Sort by start time, then process

#### 2. **Graph Traversal** (topological sort)
- **Course Schedule** - Topological ordering
- **Course Schedule II** - Return valid order

**Use when:** Dependencies or ordering constraints

#### 3. **Two Pointers**
- **Sort Colors** - In-place partitioning
- **Remove Duplicates** - Maintain order

---

## **Selection/Ranking Problems**

**Goal:** Find top K, Kth element, or rank elements

### Primary Patterns:

#### 1. **Top K Elements** (4 variants)
**Use when:** Need K largest/smallest/frequent

- **Kth Largest Element** - Find Kth largest
- **Top K Frequent Elements** - K most frequent
- **K Closest Points** - K nearest to origin
- **K Pairs with Smallest Sums** - K smallest pairs

**Technique:** Heap of size K

#### 2. **Heap / Priority Queue** (5 variants)
**Use when:** Need to repeatedly access extreme

- **Kth Largest in Stream** - Maintain Kth largest
- **Find Median from Stream** - Two heaps
- **Merge K Sorted Lists** - Multi-way merge

**Signal Words:** "Kth", "top K", "largest", "smallest", "median"

---

## **Validation/Decision Problems**

**Goal:** Check if something is valid or possible

### Primary Patterns:

#### 1. **Fast & Slow Pointers** (cycle detection)
- **Linked List Cycle** - Has cycle?
- **Happy Number** - Reaches 1 or cycles?

**Use when:** Checking cycles or loops

#### 2. **Two Pointers** (validation)
- **Valid Palindrome** - Is palindrome?

#### 3. **Graph Traversal** (reachability)
- **Course Schedule** - Is valid ordering possible?
- **Number of Islands** - Count connected components

#### 4. **Dynamic Programming** (feasibility)
- **Word Break** - Can segment string?
- **Jump Game** - Can reach end?

**Signal Words:** "can you", "is it possible", "valid", "feasible"

---

## **Matching/Pairing Problems**

**Goal:** Find pairs, matches, or relationships

### Primary Patterns:

#### 1. **Hash Map** (5 variants)
**Use when:** Need to find matching elements

- **Two Sum** ⭐ - Find pair summing to target
- **Group Anagrams** - Match anagrams together
- **Longest Consecutive Sequence** - Match consecutive numbers

#### 2. **Two Pointers**
- **Two Sum II (Sorted)** - Find pair in sorted array
- **3Sum** - Find triplets
- **Container With Most Water** - Find optimal pair

**Signal Words:** "find pair", "two elements", "match"

---

## **Connectivity/Reachability Problems**

**Goal:** Determine if nodes/cells are connected or reachable

### Primary Pattern: **Graph Traversal (BFS/DFS)** (6 variants)

1. **Number of Islands** - Count connected components
2. **Clone Graph** - Traverse and copy
3. **Course Schedule** - Check if all reachable (no cycles)
4. **Pacific Atlantic Water Flow** - Multi-source reachability
5. **Word Ladder** - Can transform word to target?
6. **Network Delay Time** - Can signal reach all nodes?

**Choose BFS when:** Need shortest path
**Choose DFS when:** Need any path or full exploration

**Signal Words:** "connected", "reachable", "path", "components", "islands"

---

## **Transformation/Construction Problems**

**Goal:** Transform input to desired output or construct something

### Primary Patterns:

#### 1. **Dynamic Programming**
- **Decode Ways** - Transform digit string to letters
- **Word Break** - Transform string to words

#### 2. **Monotonic Stack**
- **Remove K Digits** - Construct smallest number

#### 3. **Backtracking**
- **Generate Parentheses** - Construct valid strings
- **N-Queens** - Construct valid board

---

## **Streaming/Online Problems**

**Goal:** Process elements one at a time without knowing future

### Primary Patterns:

#### 1. **Heap / Priority Queue**
- **Kth Largest in Stream** - Maintain Kth as elements arrive
- **Find Median from Stream** - Update median online

#### 2. **Hash Map**
- **Contains Duplicate II** - Track recent elements
- **LRU Cache** - Maintain recent accesses

**Signal Words:** "stream", "online", "as elements arrive", "maintain"

---

## **Problem Type Decision Tree**

```
What is the goal?
│
├─ Find minimum/maximum
│   ├─ Can prove greedy works? → Greedy
│   ├─ Overlapping subproblems? → Dynamic Programming
│   └─ Contiguous sequence? → Sliding Window
│
├─ Search for element/position
│   ├─ Sorted data? → Binary Search
│   ├─ Need O(1) lookup? → Hash Map
│   └─ Graph/tree? → BFS/DFS
│
├─ Count ways/occurrences
│   ├─ Small n (≤20)? → Backtracking
│   ├─ Overlapping subproblems? → Dynamic Programming
│   └─ Frequency counting? → Hash Map
│
├─ Generate all solutions
│   └─ n ≤ 20? → Backtracking
│
├─ Process sequence/range
│   ├─ Contiguous? → Sliding Window
│   ├─ Range queries? → Prefix Sum
│   └─ Need order? → Two Pointers
│
├─ Find top K / Kth element
│   ├─ Dynamic K? → Heap (stream)
│   └─ Static K? → Top K (heap)
│
├─ Check validity/feasibility
│   ├─ Cycle detection? → Fast & Slow Pointers
│   ├─ Palindrome? → Two Pointers
│   └─ Reachability? → Graph Traversal
│
├─ Find pairs/matches
│   ├─ Sorted array? → Two Pointers
│   └─ Unsorted? → Hash Map
│
└─ Connectivity/reachability
    └─ Graph/grid? → BFS/DFS
```

---

## **Pattern Combinations**

Some problems require combining multiple patterns:

### DP + Binary Search:
- Longest Increasing Subsequence (O(n log n))

### Sliding Window + Hash Map:
- Minimum Window Substring
- Longest Substring with K Distinct

### Greedy + Sorting:
- Activity Selection
- Non-Overlapping Intervals

### BFS + Hash Map:
- Word Ladder (shortest path + visited tracking)

### Prefix Sum + Hash Map:
- Subarray Sum Equals K

---

**Pro Tip:** The problem type (what you're trying to achieve) is often more important than the data structure for pattern selection. Focus on the **verb** in the problem statement: find, count, generate, check, optimize, etc.
