# Pattern Recognition Guide: From Problem Statement to Solution

A systematic approach to identify which algorithm pattern applies to any given problem.

---

## **Quick Pattern Identification Framework**

### **Step 1: Identify the Data Structure in the Problem**

| What You See in Problem | Likely Pattern(s) |
|-------------------------|-------------------|
| **Array/String with "contiguous" or "consecutive"** | Sliding Window, Prefix Sum |
| **Array/String with "pairs" or "two elements"** | Two Pointers, Hash Map |
| **Sorted array** | Binary Search, Two Pointers |
| **Array of intervals/ranges** | Intervals Merging |
| **Tree or Graph mentioned** | Graph Traversal (BFS/DFS), Binary Tree |
| **Binary tree structure** | Binary Tree (DFS/BFS) |
| **"Generate all", "find all combinations"** | Backtracking |
| **Stack of elements where order matters** | Monotonic Stack |
| **"Top K", "Kth largest/smallest"** | Heap/Priority Queue |
| **Grid/Matrix exploration** | Graph Traversal (DFS/BFS) |

---

## **Step 2: Identify Key Signal Words**

### **🔵 Sliding Window Signals:**
```
✓ "substring", "subarray", "contiguous sequence"
✓ "maximum/minimum length" with constraint
✓ "longest/shortest" satisfying condition
✓ "all subarrays of size K"
✓ "characters without repeating"
✓ "sum ≥ target", "product < K"

Example: "Find the longest substring without repeating characters"
         → Variable window that shrinks when constraint violated
```

### **🟢 Two Pointers Signals:**
```
✓ "sorted array" + "find pair/triplet"
✓ "palindrome" (mirror comparison)
✓ "remove duplicates in-place"
✓ "partition array" based on condition
✓ "container/water" problems (area optimization)
✓ "move zeros", "sort colors"

Example: "Given a sorted array, find two numbers that sum to target"
         → Opposite direction pointers (left from start, right from end)
```

### **🔵 Fast & Slow Pointers Signals:**
```
✓ "cycle" in linked list or sequence
✓ "middle" of linked list
✓ "palindrome" linked list
✓ "happy number" or repeating transformation
✓ "intersection" of linked lists
✓ O(1) space for cycle detection
✓ Floyd's cycle detection
✓ Linked list traversal problems

Example: "Detect if linked list has a cycle"
         → Fast pointer (2x speed) will meet slow pointer if cycle exists
```

### **🟡 Hash Map/Frequency Counting Signals:**
```
✓ "count occurrences/frequency"
✓ "find duplicates"
✓ "group by" some property
✓ "anagram" problems
✓ "first unique/non-repeating"
✓ O(1) lookup required
✓ "two sum" style problems

Example: "Group anagrams together"
         → Hash map with sorted string as key
```

### **🟠 Prefix Sum Signals:**
```
✓ "sum of subarray" queries
✓ "range sum" between indices
✓ "subarray sum equals K"
✓ "product of subarray"
✓ Multiple queries on same array
✓ "continuous subarray" with sum condition

Example: "Find number of subarrays with sum equal to K"
         → Prefix sum + hash map to track sum frequencies
```

### **🔴 Binary Search Signals:**
```
✓ "sorted" array/matrix
✓ "find target" in O(log n)
✓ "rotated sorted array"
✓ "first/last occurrence"
✓ "search insert position"
✓ "minimize/maximize" with monotonic function
✓ "peak element"
✓ Answer space is bounded and sortable

Example: "Find minimum in rotated sorted array"
         → Modified binary search comparing with boundaries
```

### **🟣 Backtracking Signals:**
```
✓ "generate ALL" solutions
✓ "find ALL combinations/permutations"
✓ "subset", "powerset"
✓ "can you reach" (with choices at each step)
✓ "partition into" groups
✓ "N-Queens", "Sudoku"
✓ "word search" in grid
✓ Decision tree with multiple choices

Example: "Generate all subsets of a set"
         → Include/exclude decision at each element
```

### **🟤 Monotonic Stack Signals:**
```
✓ "next greater/smaller element"
✓ "previous greater/smaller"
✓ "daily temperatures"
✓ "largest rectangle" in histogram
✓ "stock span" problem
✓ Looking backward/forward for comparison
✓ Need to maintain increasing/decreasing order

Example: "Find next greater element for each element in array"
         → Decreasing monotonic stack (pop smaller elements)
```

### **⚫ Top K Elements Signals:**
```
✓ "K largest/smallest elements"
✓ "Kth largest/smallest"
✓ "top K frequent"
✓ "K closest" (to origin, target, etc.)
✓ Don't need full sort
✓ K much smaller than N
✓ Streaming K elements

Example: "Find Kth largest element in array"
         → Min heap of size K (root = Kth largest)
```

### **🟣 Heap/Priority Queue Signals:**
```
✓ "merge K sorted" lists/arrays
✓ "median" from stream (two heaps)
✓ "scheduling" with priorities
✓ "task scheduler" with cooldown
✓ Need to repeatedly access min/max
✓ Dynamic ordering requirements

Example: "Find median from data stream"
         → Max heap (left half) + Min heap (right half)
```

### **🔵 Intervals/Merging Signals:**
```
✓ "intervals", "ranges"
✓ "merge overlapping"
✓ "insert interval"
✓ "meeting rooms"
✓ "minimum removals" to avoid overlap
✓ "intersection" of intervals
✓ Time-based scheduling

Example: "Merge all overlapping intervals"
         → Sort by start time, merge consecutive overlaps
```

### **🟢 Greedy Signals:**
```
✓ "minimum/maximum" with local choice
✓ "activity selection"
✓ "scheduling" problems
✓ "fractional" problems (can take parts)
✓ "jump game"
✓ Making locally optimal choice at each step
✓ Can prove optimal substructure

Example: "Jump game - can you reach the end?"
         → Track maximum reachable index greedily
```

### **🟡 Graph Traversal (BFS/DFS) Signals:**
```
✓ "connected components"
✓ "islands" in grid
✓ "shortest path" (unweighted → BFS)
✓ "all paths" (→ DFS)
✓ "cycle detection"
✓ "topological sort"
✓ "tree traversal"
✓ "word ladder", "maze solving"
✓ Exploring neighbors/adjacency

Example: "Number of islands in grid"
         → DFS/BFS to mark connected land cells
```

### **🟠 Dynamic Programming Signals:**
```
✓ "count number of ways"
✓ "minimum/maximum" with subproblems
✓ "longest" subsequence/substring
✓ "can you make" (coin change, subset sum)
✓ Overlapping subproblems
✓ Optimal substructure
✓ Decision affects future states
✓ "How many ways to..."

Example: "Count ways to climb stairs (1 or 2 steps)"
         → ways[i] = ways[i-1] + ways[i-2]
```

### **🌳 Binary Tree Signals:**
```
✓ "binary tree" structure (TreeNode with left/right)
✓ "depth" or "height" of tree
✓ "invert/flip/mirror" tree
✓ "diameter" (longest path between nodes)
✓ "path sum" from root to leaf
✓ "validate BST" (binary search tree)
✓ "level order" traversal
✓ "lowest common ancestor"
✓ "serialize/deserialize" tree
✓ "balanced tree" check
✓ "symmetric tree" check
✓ "same tree" comparison

Example: "Find maximum depth of binary tree"
         → Recursive DFS: 1 + max(left_depth, right_depth)
         
Example: "Validate if tree is a valid BST"
         → DFS with range tracking (min, max)
```

---

## **Step 3: Decision Tree for Pattern Selection**

### **Quick 3-Step Framework:**

```
┌──────────────────────────────────────────────────────────────┐
│  STEP 1: Output Goal  →  STEP 2: Data Structure  →  STEP 3: Constraint  │
└──────────────────────────────────────────────────────────────┘
```

### **Decision Tree (with Variants):**

```
START: Read Problem Statement
│
├─ 🎯 OUTPUT GOAL?
│  │
│  ├─ "Generate ALL solutions" ──────────────────────────────► ✅ BACKTRACKING
│  │                                                             ├─ Subsets/Power Set
│  │                                                             ├─ Permutations
│  │                                                             ├─ Combination Sum
│  │                                                             ├─ Letter Combinations
│  │                                                             ├─ Palindrome Partitioning
│  │                                                             ├─ Word Search (2D)
│  │                                                             ├─ N-Queens
│  │                                                             └─ Sudoku Solver
│  │
│  ├─ "Count ways" ──────────────────────────────────────────► ✅ DYNAMIC PROGRAMMING
│  │                                                             ├─ Climbing Stairs
│  │                                                             ├─ House Robber
│  │                                                             ├─ Min Cost Climbing
│  │                                                             ├─ Max Subarray (Kadane)
│  │                                                             ├─ Coin Change (min)
│  │                                                             ├─ Coin Change II (count)
│  │                                                             ├─ Target Sum (+/-)
│  │                                                             └─ Longest Increasing Subseq
│  │
│  └─ "Find/Optimize single result" ────► Continue to STEP 2 ▼
│
├─ 📊 DATA STRUCTURE?
│  │
│  ├─ LINKED LIST ──► "Cycle/middle/palindrome?" ────────────► ✅ FAST & SLOW POINTERS
│  │                                                             ├─ Middle of List
│  │                                                             ├─ Cycle Detection
│  │                                                             ├─ Cycle Start
│  │                                                             ├─ Happy Number
│  │                                                             ├─ Palindrome List
│  │                                                             ├─ Cycle Length
│  │                                                             ├─ Reorder List
│  │                                                             └─ Intersection of Lists
│  │
│  ├─ GRAPH/TREE/GRID ──┬─ "Shortest path?" ─────────────────► ✅ BFS (GRAPH TRAVERSAL)
│  │                    │                                        └─ Word Ladder, Maze
│  │                    ├─ "All paths/cycles/components?" ────► ✅ DFS (GRAPH TRAVERSAL)
│  │                    │                                        ├─ Number of Islands
│  │                    │                                        ├─ Tree Traversals
│  │                    │                                        ├─ Connected Components
│  │                    │                                        ├─ Cycle Detection
│  │                    │                                        └─ Topological Sort
│  │                    ├─ "Binary tree properties?" ──────────► ✅ BINARY TREE (DFS)
│  │                    │                                        ├─ Max Depth
│  │                    │                                        ├─ Invert Tree
│  │                    │                                        ├─ Diameter
│  │                    │                                        ├─ Path Sum
│  │                    │                                        ├─ Validate BST
│  │                    │                                        ├─ Level Order (BFS)
│  │                    │                                        ├─ Lowest Common Ancestor
│  │                    │                                        └─ Serialize/Deserialize
│  │                    └─ "Topological/general?" ────────────► ✅ GRAPH TRAVERSAL
│  │
│  ├─ INTERVALS/RANGES ──► "Merge/schedule?" ────────────────► ✅ INTERVALS MERGING
│  │                                                             ├─ Merge Intervals
│  │                                                             ├─ Insert Interval
│  │                                                             ├─ Meeting Rooms I/II
│  │                                                             ├─ Non-overlapping Intervals
│  │                                                             ├─ Interval Intersection
│  │                                                             ├─ Minimum Arrows
│  │                                                             └─ Employee Free Time
│  │
│  └─ ARRAY/STRING ────────────────────────────────────────────► Continue to STEP 3 ▼
│
└─ 🔍 CONSTRAINT/PATTERN?
   │
   ├─ "Sorted array/list" ──┬─ "Find target/position?" ──────► ✅ BINARY SEARCH
   │                        │                                    ├─ Classic Search
   │                        │                                    ├─ First/Last Occurrence
   │                        │                                    ├─ Search Insert Position
   │                        │                                    ├─ Peak Element
   │                        │                                    ├─ Rotated Array Search
   │                        │                                    ├─ Binary Search on Answer
   │                        │                                    ├─ Search 2D Matrix
   │                        │                                    └─ Min in Rotated Array
   │                        │
   │                        ├─ "Pair/triplet sum?" ────────────► ✅ TWO POINTERS
   │                        │                                    ├─ Two Sum II
   │                        │                                    ├─ Valid Palindrome
   │                        │                                    ├─ Remove Duplicates
   │                        │                                    ├─ Container With Water
   │                        │                                    ├─ 3Sum
   │                        │                                    ├─ Trapping Rain Water*
   │                        │                                    ├─ Move Zeros
   │                        │                                    └─ Sort Colors
   │                        │
   │                        └─ "K closest to target?" ─────────► ✅ TOP K ELEMENTS
   │                                                              └─ K Closest in Sorted Array
   │
   ├─ "Contiguous subarray/substring" ─┬─ "With constraint?" ─► ✅ SLIDING WINDOW
   │                                    │                        ├─ Longest Substring No Repeat
   │                                    │                        ├─ Max Sum Size K
   │                                    │                        ├─ Min Window Substring
   │                                    │                        ├─ Subarray Product < K
   │                                    │                        ├─ Min Size Sum ≥ S
   │                                    │                        ├─ Sliding Window Max* (+ Deque)
   │                                    │                        └─ K Distinct Characters*
   │                                    │
   │                                    └─ "Sum queries?" ──────► ✅ PREFIX SUM
   │                                                              ├─ Range Sum Query
   │                                                              └─ Subarray Sum = K* (+ Hash Map)
   │
   ├─ "K largest/smallest/frequent/Kth" ────────────────────────► ✅ TOP K ELEMENTS
   │                                                              ├─ Kth Largest in Array
   │                                                              ├─ K Largest Elements
   │                                                              ├─ Kth Largest in Stream
   │                                                              ├─ Top K Frequent* (+ Hash Map)
   │                                                              ├─ K Closest Points
   │                                                              ├─ K Closest in Array
   │                                                              ├─ Kth in Matrix
   │                                                              └─ K Pairs Smallest Sums
   │
   ├─ "Median / Merge K lists / Scheduling" ────────────────────► ✅ HEAP/PRIORITY QUEUE
   │                                                              ├─ Kth Largest in Stream
   │                                                              ├─ Merge K Sorted Lists
   │                                                              ├─ Top K Frequent*
   │                                                              ├─ Find Median Stream
   │                                                              ├─ Task Scheduler* (+ Greedy)
   │                                                              ├─ Reorganize String* (+ Greedy)
   │                                                              └─ Sliding Window Median*
   │
   ├─ "Next/previous greater/smaller" ──────────────────────────► ✅ MONOTONIC STACK
   │                                                              ├─ Next Greater Element
   │                                                              ├─ Daily Temperatures
   │                                                              ├─ Stock Span
   │                                                              ├─ Largest Rectangle Histogram
   │                                                              ├─ Trapping Rain Water*
   │                                                              ├─ Sum Subarray Minimums
   │                                                              ├─ Remove K Digits
   │                                                              └─ Next Greater Circular
   │
   ├─ "Frequency/count/group/anagram" ──────────────────────────► ✅ HASH MAP
   │                                                              ├─ Valid Anagram
   │                                                              ├─ Two Sum
   │                                                              ├─ Group Anagrams
   │                                                              ├─ First Non-Repeating Char
   │                                                              ├─ Subarray Sum = K*
   │                                                              ├─ Longest K Distinct*
   │                                                              └─ Top K Frequent*
   │
   ├─ "Range sum queries" (multiple) ────────────────────────────► ✅ PREFIX SUM
   │                                                              ├─ Range Sum Query
   │                                                              └─ Subarray Sum = K*
   │
   ├─ "Min/max optimization" ──┬─ "Local choice works?" ────────► ✅ GREEDY
   │                            │                                 ├─ Activity Selection
   │                            │                                 ├─ Jump Game
   │                            │                                 ├─ Fractional Knapsack
   │                            │                                 ├─ Min Coins (canonical)
   │                            │                                 ├─ Task Scheduling
   │                            │                                 ├─ Reorganize String*
   │                            │                                 ├─ Gas Station
   │                            │                                 └─ Huffman Encoding
   │                            │
   │                            └─ "Need all choices?" ───────────► ✅ DYNAMIC PROGRAMMING
   │                                                               (See "Count ways" above)
   │
   └─ "Doesn't fit above?" ──────────────────────────────────────► 🔀 HYBRID PATTERN
                                                                   * = Hybrid (uses 2+ patterns)
```

### **🔀 Common Hybrid Patterns:**
Problems marked with `*` require combining multiple techniques:

| Problem | Primary Pattern | Secondary Pattern(s) |
|---------|----------------|---------------------|
| **Subarray Sum = K** | Prefix Sum | + Hash Map (store prefix sums) |
| **Longest K Distinct** | Sliding Window | + Hash Map (track frequencies) |
| **Top K Frequent** | Heap/Top K | + Hash Map (count frequencies first) |
| **Sliding Window Max** | Sliding Window | + Monotonic Deque (track maximums) |
| **Trapping Rain Water** | Two Pointers | OR Monotonic Stack (both work) |
| **Task Scheduler** | Greedy | + Heap (greedy selection with priorities) |
| **Reorganize String** | Greedy | + Heap (pick most frequent repeatedly) |
| **Sliding Window Median** | Sliding Window | + Two Heaps (maintain median dynamically) |

### **Key Insights:**
1. Most problems can be identified in **3 questions or less**: Output Goal → Data Structure → Constraint
2. **Pure patterns** have dedicated variants that use a single technique
3. **Hybrid patterns** combine 2+ patterns - recognize this early to avoid confusion
4. When stuck, check if problem needs **frequency counting** (Hash Map), **ordering** (Heap), or **range tracking** (Sliding Window)

---

## **Step 4: Common Problem-to-Pattern Mappings**

| Problem Type | Pattern | Why? |
|--------------|---------|------|
| **"Longest substring without repeating"** | Sliding Window | Variable window shrinks on duplicate |
| **"Two sum in sorted array"** | Two Pointers | Opposite direction optimization |
| **"Linked list cycle detection"** | Fast & Slow Pointers | Floyd's algorithm with speed differential |
| **"Group anagrams"** | Hash Map | Group by sorted string key |
| **"Subarray sum equals K"** | Prefix Sum + Hash Map | Track cumulative sums |
| **"Find target in rotated sorted array"** | Binary Search | Modified binary search |
| **"Generate all subsets"** | Backtracking | Include/exclude decision tree |
| **"Next greater element"** | Monotonic Stack | Track decreasing sequence |
| **"Kth largest element"** | Top K Elements | Min heap of size K |
| **"Merge intervals"** | Intervals Merging | Sort + merge overlapping |
| **"Jump game"** | Greedy | Track max reachable |
| **"Number of islands"** | Graph Traversal (DFS) | Connected components in grid |
| **"Coin change - minimum coins"** | Dynamic Programming | Optimal substructure with overlaps |
| **"Valid parentheses"** | Monotonic Stack | Match opening/closing pairs |
| **"Meeting rooms conflict"** | Intervals Merging | Sort + check overlaps |
| **"Container with most water"** | Two Pointers | Opposite direction with greedy choice |
| **"Trapping rain water"** | Monotonic Stack or Two Pointers | Track boundaries |
| **"Top K frequent elements"** | Top K Elements + Hash Map | Count frequencies + min heap |
| **"Word ladder"** | Graph Traversal (BFS) | Shortest transformation path |
| **"House robber"** | Dynamic Programming | Max non-adjacent sum |
| **"Minimum window substring"** | Sliding Window + Hash Map | Variable window + frequency tracking |
| **"Happy number"** | Fast & Slow Pointers | Detect cycle in transformation |
| **"Palindrome linked list"** | Fast & Slow Pointers | Find middle + reverse + compare |
| **"Find median from stream"** | Heap/Priority Queue | Two heaps (max + min) |

---

## **Step 5: Quick Checklist Questions**

When you read a problem, ask yourself:

### **1. Data Structure:**
- [ ] Array/List?
- [ ] String?
- [ ] Tree?
- [ ] Graph/Grid?
- [ ] Intervals/Ranges?
- [ ] Stream of data?

### **2. Constraints:**
- [ ] Is it sorted?
- [ ] Fixed size window?
- [ ] Contiguous elements?
- [ ] In-place modification allowed?
- [ ] Time/Space complexity hints?

### **3. Goal:**
- [ ] Find one solution?
- [ ] Find all solutions?
- [ ] Count number of solutions?
- [ ] Optimize (min/max)?
- [ ] Check existence (true/false)?

### **4. Query Type:**
- [ ] Single query?
- [ ] Multiple queries (preprocessing needed)?
- [ ] Online/streaming data?

### **5. Keywords Present:**
- [ ] "All", "generate", "find all"?
- [ ] "K largest/smallest"?
- [ ] "Contiguous", "substring", "subarray"?
- [ ] "Sorted"?
- [ ] "Next/previous greater/smaller"?
- [ ] "Frequency", "count", "group"?
- [ ] "Shortest path", "connected", "islands"?
- [ ] "Ways to", "count ways"?

---

## **Pattern Combination Examples**

Some problems require **multiple patterns**:

| Problem | Patterns Combined | Why? |
|---------|-------------------|------|
| **Subarray sum equals K** | Prefix Sum + Hash Map | Sum tracking + O(1) lookup |
| **Longest substring with K distinct** | Sliding Window + Hash Map | Window management + frequency tracking |
| **Top K frequent elements** | Hash Map + Top K Elements | Count frequencies + min heap of size K |
| **Meeting Rooms II** | Intervals + Heap | Overlap detection + concurrent tracking |
| **Sliding Window Maximum** | Sliding Window + Monotonic Deque | Fixed window + efficient max tracking |
| **Subarrays with K different integers** | Sliding Window (2x) + Hash Map | Transform to "at most K" problem |
| **Merge K sorted lists** | Heap + Linked List | Priority selection across K sources |
| **Word Search II** | Backtracking + Trie | DFS with optimized prefix lookup |
| **Trapping Rain Water** | Two Pointers or Monotonic Stack | Boundary tracking or decreasing stack |
| **Serialize/Deserialize Tree** | BFS/DFS + String Manipulation | Traversal + encoding |

---

## **Advanced Pattern Recognition: Time Complexity Hints**

Sometimes the **expected time complexity** hints at the pattern:

| Expected Complexity | Likely Pattern(s) | Example |
|---------------------|-------------------|---------|
| **O(n)** | Sliding Window, Two Pointers, Hash Map, Monotonic Stack | Single pass optimization |
| **O(n log n)** | Binary Search, Heap, Sorting | Divide & conquer or priority operations |
| **O(n log k)** | Heap of size K | Top K problems |
| **O(n²)** → optimize to O(n) | Hash Map, Prefix Sum | Two-sum, subarray sum |
| **O(2ⁿ)** | Backtracking, DP | Generate all subsets |
| **O(V + E)** | Graph Traversal | BFS/DFS |
| **O(n × m)** | 2D DP | Grid problems |

---

## **Mental Model Summary**

```
┌─────────────────────────────────────────────┐
│ PATTERN RECOGNITION MENTAL MODEL            │
├─────────────────────────────────────────────┤
│                                             │
│ 1. READ → Identify data structure          │
│ 2. KEYWORDS → Match signal words           │
│ 3. CONSTRAINT → Sorted? Contiguous? K?     │
│ 4. GOAL → One? All? Count? Optimize?       │
│ 5. DECIDE → Use decision tree              │
│ 6. VERIFY → Does pattern fit all signals?  │
│ 7. COMBINE → Multiple patterns if needed   │
│                                             │
└─────────────────────────────────────────────┘
```

---

## **Practice Exercise: Identify the Pattern**

Try identifying patterns for these problems:

1. **"Find all anagrams of a string in another string"**
   - Keywords: "all", "anagrams", "substring"
   - Pattern: **Sliding Window + Hash Map** (fixed window, frequency tracking)

2. **"Find the median from a data stream"**
   - Keywords: "median", "stream"
   - Pattern: **Two Heaps** (max heap for lower half, min heap for upper half)

3. **"Longest increasing subsequence"**
   - Keywords: "longest", "subsequence", optimization
   - Pattern: **Dynamic Programming** (or DP + Binary Search for O(n log n))

4. **"Detect cycle in linked list"**
   - Keywords: "cycle", "linked list"
   - Pattern: **Two Pointers** (fast & slow)

5. **"Find all paths from root to leaf in binary tree"**
   - Keywords: "all paths", "tree"
   - Pattern: **Backtracking** (DFS with path tracking)

6. **"Daily temperatures - days until warmer"**
   - Keywords: "next greater", "temperatures"
   - Pattern: **Monotonic Stack** (decreasing stack)

7. **"Minimum number of meeting rooms required"**
   - Keywords: "intervals", "minimum", "concurrent"
   - Pattern: **Intervals + Heap** (sort by start, heap tracks end times)

8. **"Product of array except self"**
   - Keywords: "array", "product", O(n) expected
   - Pattern: **Prefix/Suffix Product** (variant of prefix sum)

9. **"Word break - can string be segmented"**
   - Keywords: "can", "ways to", overlapping subproblems
   - Pattern: **Dynamic Programming** (dp[i] = can segment up to i)

10. **"Implement LRU Cache"**
    - Keywords: "cache", "least recently used", O(1) operations
    - Pattern: **Hash Map + Doubly Linked List**

---

## **Common Pitfalls in Pattern Recognition**

### ❌ **Mistake: Jumping to conclusion too quickly**
- **Example:** Seeing "sorted array" → immediately think binary search
- **Reality:** Could be two pointers (two sum), sliding window (contiguous), or prefix sum
- **Fix:** Read the full problem, check what the question asks

### ❌ **Mistake: Ignoring constraint clues**
- **Example:** "Generate all subsets" but n ≤ 10
- **Reality:** Small n means O(2ⁿ) is acceptable → backtracking
- **Fix:** Always check input size constraints

### ❌ **Mistake: Missing pattern combinations**
- **Example:** Trying pure sliding window when frequency tracking is needed
- **Reality:** Many problems need 2+ patterns combined
- **Fix:** Ask "What auxiliary data structure helps?"

### ❌ **Mistake: Confusing similar patterns**
- **Example:** Greedy vs DP - both optimize, when to use which?
- **Reality:** Greedy = local optimal leads to global; DP = need to try all
- **Fix:** Check if greedy proof exists (exchange argument, cut-and-paste)

---

## **Quick Reference: Pattern → Core Operation**

| Pattern | Core Operation | Key Question |
|---------|----------------|--------------|
| **Sliding Window** | Expand right, shrink left | "What's optimal contiguous sequence?" |
| **Two Pointers** | Move left/right based on condition | "Can I eliminate options by comparing?" |
| **Hash Map** | Store key-value for O(1) lookup | "What property uniquely identifies?" |
| **Prefix Sum** | Cumulative sum array | "Can I reduce range query to O(1)?" |
| **Binary Search** | Divide space in half | "Is there monotonic decision boundary?" |
| **Backtracking** | Try, recurse, undo | "What choices do I have at each step?" |
| **Monotonic Stack** | Maintain increasing/decreasing order | "What's next greater/smaller element?" |
| **Heap** | Extract min/max efficiently | "Do I need repeatedly access extreme?" |
| **Intervals** | Sort + sweep/merge | "How do ranges overlap?" |
| **Greedy** | Make locally optimal choice | "Does local optimal lead to global?" |
| **Graph Traversal** | Visit nodes systematically | "How are elements connected?" |
| **Dynamic Programming** | Solve subproblems, build up | "Can I reuse computed results?" |

---

## **Final Tip: The 30-Second Test**

Before coding, spend 30 seconds:

1. **Underline keywords** in problem statement
2. **Circle constraints** (sorted? size? range?)
3. **Box the goal** (find one? all? count? optimize?)
4. **Match to decision tree** (which branch?)
5. **Verify pattern fit** (do ALL signals align?)

If unsure between 2 patterns → **start with simpler one** or **try combining both**.

---

**Remember:** Pattern recognition is a skill that improves with practice. The more problems you solve, the faster you'll identify patterns automatically!
