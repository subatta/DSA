# Algorithm Patterns by Data Structure

This index organizes patterns by the **primary data structure** mentioned in the problem, helping you quickly identify applicable patterns based on what you're working with.

---

## **Array / List**

### When Problem Gives You: Array of integers/elements

**Primary Patterns:**

#### 1. **Sliding Window** (8 variants) - Contiguous sequences
- Maximum Sum Subarray of Size K
- Longest Substring Without Repeating
- Minimum Window Substring
- Subarray Product Less Than K
- Minimum Size Subarray Sum
- Sliding Window Maximum
- K Distinct Characters

**Signal Words:** "subarray", "contiguous", "substring", "window"

#### 2. **Two Pointers** (8 variants) - Pairs/triplets, sorted arrays
- Two Sum II (Sorted Array)
- Container With Most Water
- 3Sum
- Trapping Rain Water
- Remove Duplicates
- Move Zeros
- Sort Colors

**Signal Words:** "two elements", "pair sum", "sorted", "palindrome"

#### 3. **Hash Map** (5 variants) - Counting, grouping
- Two Sum
- Subarray Sum Equals K
- Longest Consecutive Sequence
- Contains Duplicate II

**Signal Words:** "frequency", "count", "find pair", "group by"

#### 4. **Prefix Sum** (4 variants) - Range queries
- Range Sum Query
- Product of Array Except Self
- Find Pivot Index
- Continuous Subarray Sum

**Signal Words:** "subarray sum", "range query", "cumulative"

#### 5. **Monotonic Stack** (5 variants) - Next greater/smaller
- Next Greater Element
- Daily Temperatures
- Largest Rectangle in Histogram
- Remove K Digits

**Signal Words:** "next greater", "next smaller", "span", "histogram"

#### 6. **Binary Search** (6 variants) - Sorted or monotonic
- Classic Binary Search
- Search Insert Position
- Search in Rotated Sorted Array
- Find First and Last Position
- Find Peak Element
- Koko Eating Bananas

**Signal Words:** "sorted", "find target", "monotonic", "search"

#### 7. **Dynamic Programming 1D** (8 variants) - Optimization
- Climbing Stairs
- House Robber
- Coin Change
- Longest Increasing Subsequence
- Maximum Subarray
- Word Break
- Decode Ways
- Jump Game II

**Signal Words:** "count ways", "maximum/minimum", "can you reach"

#### 8. **Greedy** (6 variants) - Local optimal choices
- Activity Selection
- Jump Game
- Gas Station
- Task Scheduler
- Partition Labels

**Signal Words:** "minimum/maximum", "scheduling", "optimal"

---

## **String**

### When Problem Gives You: String or array of characters

**Applicable Patterns:**

#### 1. **Sliding Window** (string variants)
- Longest Substring Without Repeating Characters ⭐
- Minimum Window Substring ⭐
- K Distinct Characters
- Find All Anagrams

**Use when:** "substring", "contiguous characters", "window"

#### 2. **Two Pointers** (string variants)
- Valid Palindrome
- 3Sum (after sorting)

**Use when:** "palindrome", "reverse", "compare"

#### 3. **Hash Map** (string variants)
- Group Anagrams ⭐
- First Unique Character
- Longest Substring with At Most K Distinct

**Use when:** "anagram", "frequency", "character count"

#### 4. **Dynamic Programming** (string variants)
- Word Break ⭐
- Decode Ways ⭐
- Longest Increasing Subsequence (with sorting)

**Use when:** "partition string", "decode", "count ways"

#### 5. **Backtracking** (string variants)
- Palindrome Partitioning
- Generate Parentheses
- Word Search (if in grid)

**Use when:** "all partitions", "generate all", "valid combinations"

---

## **Linked List**

### When Problem Gives You: Singly/Doubly Linked List

**Primary Pattern: Fast & Slow Pointers** (6 variants)

1. **Linked List Cycle** ⭐ - Detect cycle
2. **Linked List Cycle II** - Find cycle start
3. **Find Middle of Linked List** ⭐ - One-pass middle
4. **Palindrome Linked List** - Check palindrome
5. **Happy Number** - Cycle detection in sequences
6. **Remove Nth Node From End** - Two-pointer gap

**Key Technique:** 
- **Slow pointer:** moves 1 step
- **Fast pointer:** moves 2 steps
- **Benefit:** O(1) space vs O(n) with hash set

**Secondary Patterns:**
- **Two Pointers** - Merge sorted lists, partition
- **Heap** - Merge K sorted lists

**Signal Words:** "cycle", "middle", "nth from end", "palindrome"

---

## **Binary Tree / Tree**

### When Problem Gives You: TreeNode, binary tree, N-ary tree

**Primary Pattern: Graph Traversal (DFS/BFS)** (tree-specific)

#### DFS Traversals:
- **Preorder:** Root → Left → Right
- **Inorder:** Left → Root → Right (BST gives sorted)
- **Postorder:** Left → Right → Root

#### BFS Traversal:
- **Level-order:** Process level by level with queue

**Common Tree Problems:**
- Maximum depth → DFS
- Level-order traversal → BFS
- Path sum → DFS with backtracking
- Lowest common ancestor → DFS
- Serialize/Deserialize → BFS or DFS

**Dynamic Programming** can also apply:
- House Robber III (tree version)
- Binary Tree Maximum Path Sum

**Signal Words:** "traverse", "level", "depth", "path", "ancestor"

---

## **Graph / Matrix (Grid)**

### When Problem Gives You: Adjacency list, adjacency matrix, or 2D grid

**Primary Pattern: Graph Traversal (BFS/DFS)** (6 variants)

1. **Number of Islands** ⭐ - Grid DFS/BFS
2. **Clone Graph** - DFS with map
3. **Course Schedule** ⭐ - Topological sort (cycle detection)
4. **Pacific Atlantic Water Flow** - Multi-source DFS
5. **Word Ladder** - BFS shortest path
6. **Network Delay Time** - Dijkstra's algorithm

**Choose BFS when:**
- Shortest path needed (unweighted)
- Level-by-level processing
- Minimum steps/distance

**Choose DFS when:**
- Any path works
- Detect cycles
- Connected components
- Exhaustive search

**Secondary Patterns:**
- **Backtracking** - Word Search in grid
- **Dynamic Programming** - Unique paths, minimum path sum

**Signal Words:** "connected", "shortest path", "reachable", "islands", "graph"

---

## **Intervals / Ranges**

### When Problem Gives You: Array of [start, end] pairs

**Primary Pattern: Intervals / Merging** (5 variants)

1. **Merge Intervals** ⭐ - Collapse overlaps
2. **Insert Interval** - Merge into sorted list
3. **Non-Overlapping Intervals** - Minimum removals
4. **Meeting Rooms II** ⭐ - Count concurrent
5. **Interval List Intersections** - Find overlaps

**Key Algorithm:**
1. Sort by start time - O(n log n)
2. Sweep through intervals - O(n)
3. Merge when `current.end >= next.start`

**Secondary Pattern:**
- **Greedy** - Activity selection, scheduling
- **Heap** - Meeting rooms (track end times)

**Signal Words:** "intervals", "ranges", "overlapping", "merge", "meeting rooms"

---

## **Heap / Priority Queue**

### When You Need: Repeatedly access min/max element

**Primary Patterns:**

#### 1. **Heap / Priority Queue** (5 variants)
- Kth Largest in Stream
- Find Median from Stream ⭐
- Merge K Sorted Lists ⭐
- Task Scheduler
- K Closest Points

**Use heap when:**
- Dynamic ordering (elements added/removed)
- Need min/max repeatedly
- Don't need full sort

#### 2. **Top K Elements** (4 variants)
- Kth Largest Element ⭐
- Top K Frequent Elements ⭐
- K Closest Points
- K Pairs with Smallest Sums

**Technique:**
- **Min-heap of size K** for K largest
- **Max-heap of size K** for K smallest

**Signal Words:** "K largest", "K smallest", "median", "merge K", "Kth"

---

## **Set / Dictionary (Implicit)**

### When You Need: Fast lookup, uniqueness, counting

**Primary Pattern: Hash Map / Frequency Counting** (5 variants)

1. **Two Sum** ⭐ - Complement lookup
2. **Group Anagrams** ⭐ - Group by key
3. **Subarray Sum Equals K** - Prefix sum frequency
4. **Longest Consecutive Sequence** - O(1) existence check
5. **Contains Duplicate II** - Recent index tracking

**Use hash map when:**
- Need O(1) lookup
- Counting frequencies
- Grouping by property
- Checking existence

**Data Structure Choice:**
- `Dictionary<TKey, TValue>` - Key-value mapping
- `HashSet<T>` - Uniqueness, existence checks
- `Dictionary<T, int>` - Frequency counting

**Signal Words:** "frequency", "count", "unique", "duplicate", "anagram", "pair"

---

## **Stack**

### When You Need: LIFO (Last In First Out) access

**Primary Pattern: Monotonic Stack** (5 variants)

1. **Next Greater Element** ⭐
2. **Daily Temperatures** ⭐
3. **Largest Rectangle in Histogram** ⭐
4. **Next Greater Element II**
5. **Remove K Digits**

**Monotonic Property:**
- **Increasing stack:** Pop when current < top (find smaller)
- **Decreasing stack:** Pop when current > top (find greater)

**Use stack when:**
- Need "next greater/smaller"
- Span problems
- Matching pairs (parentheses)
- Histogram problems

**Signal Words:** "next", "previous", "greater", "smaller", "span", "histogram"

---

## **Queue**

### When You Need: FIFO (First In First Out) access

**Primary Use: BFS in Graph Traversal**

**Problems requiring queue:**
- Level-order tree traversal
- Shortest path (BFS)
- Multi-source BFS
- Sliding window (deque variant)

**Special: Deque (Double-ended Queue)**
- **Sliding Window Maximum** - Monotonic deque
- Can add/remove from both ends

---

## **Data Structure Decision Tree**

```
Given Problem Input:
│
├─ Array/List of integers
│   ├─ Need subarray/substring → Sliding Window
│   ├─ Sorted array → Binary Search, Two Pointers
│   ├─ Find pair/triplet → Two Pointers, Hash Map
│   ├─ Range queries → Prefix Sum
│   ├─ Next greater/smaller → Monotonic Stack
│   └─ Optimization → DP, Greedy
│
├─ String
│   ├─ Substring problems → Sliding Window
│   ├─ Palindrome → Two Pointers
│   ├─ Anagram/frequency → Hash Map
│   └─ Partition/decode → DP, Backtracking
│
├─ Linked List
│   ├─ Cycle detection → Fast & Slow Pointers
│   ├─ Middle/Nth from end → Fast & Slow Pointers
│   └─ Merge lists → Heap, Two Pointers
│
├─ Tree
│   ├─ Traversal → DFS (recursion) or BFS (queue)
│   ├─ Path problems → DFS with backtracking
│   └─ Level-order → BFS
│
├─ Graph/Grid
│   ├─ Shortest path → BFS
│   ├─ Connected components → DFS
│   ├─ Cycle detection → DFS with colors
│   └─ Topological sort → DFS or BFS
│
├─ Intervals
│   ├─ Overlapping → Sort + Merge
│   ├─ Scheduling → Greedy or Heap
│   └─ Concurrent count → Heap (sweep line)
│
└─ Need K elements
    ├─ K largest/smallest → Top K (Heap)
    ├─ Kth element → Heap or Quickselect
    └─ Median → Two Heaps
```

---

## **Multi-Structure Problems**

Some problems combine multiple data structures:

### Array + Hash Map:
- Two Sum
- Subarray Sum Equals K
- Group Anagrams

### Array + Stack:
- Daily Temperatures
- Next Greater Element
- Largest Rectangle

### Array + Heap:
- Sliding Window Median
- K Closest Points
- Top K Frequent

### Graph + Queue (BFS):
- Word Ladder
- Shortest Path
- Level-order Traversal

### Graph + Stack (DFS):
- Number of Islands
- Course Schedule
- Clone Graph

### Linked List + Hash Map:
- Copy List with Random Pointer
- LRU Cache

---

**Pro Tip:** The data structure in the problem statement is your first clue, but the **operation you need to perform** (search, count, find next, etc.) determines the pattern!
