# Algorithm Patterns by Data Structure

This index organizes patterns by the **primary data structure** mentioned in the problem, helping you quickly identify applicable patterns based on what you're working with.

---

## **Array / List**

### When Problem Gives You: Array of integers/elements

**Primary Patterns:**

#### 1. **Sliding Window** (8 variants) - Contiguous sequences
- [Maximum Sum Subarray of Size K](../variants/sliding_window/variant_8_max_sum_subarray_size_k.md)
- [Longest Substring Without Repeating](../variants/sliding_window/variant_1_longest_substring_no_repeat.md)
- [Minimum Window Substring](../variants/sliding_window/variant_3_min_window_substring.md)
- [Subarray Product Less Than K](../variants/sliding_window/variant_2_subarray_product_less_k.md)
- [Minimum Size Subarray Sum](../variants/sliding_window/variant_5_min_size_subarray_sum.md)
- [Sliding Window Maximum](../variants/sliding_window/variant_6_sliding_window_maximum.md)
- [K Distinct Characters](../variants/sliding_window/variant_7_k_distinct_characters.md)

**Signal Words:** "subarray", "contiguous", "substring", "window"

#### 2. **Two Pointers** (8 variants) - Pairs/triplets, sorted arrays
- [Two Sum II (Sorted Array)](../variants/two_pointers/variant_1_two_sum_ii.md)
- [Container With Most Water](../variants/two_pointers/variant_3_container_with_most_water.md)
- [3Sum](../variants/two_pointers/variant_4_three_sum.md)
- [Trapping Rain Water](../variants/two_pointers/variant_6_trapping_rain_water.md)
- [Remove Duplicates](../variants/two_pointers/variant_7_remove_duplicates.md)
- [Move Zeros](../variants/two_pointers/variant_8_move_zeros.md)
- [Sort Colors](../variants/two_pointers/variant_5_sort_colors.md)

**Signal Words:** "two elements", "pair sum", "sorted", "palindrome"

#### 3. **Hash Map** (5 variants) - Counting, grouping
- [Two Sum](../variants/hash_map/variant_1_two_sum.md)
- [Subarray Sum Equals K](../variants/hash_map/variant_3_subarray_sum_k.md)
- [Longest Consecutive Sequence](../variants/hash_map/variant_4_longest_consecutive.md)
- [Duplicate Within K Distance](../variants/hash_map/variant_5_duplicate_within_k_distance.md)

**Signal Words:** "frequency", "count", "find pair", "group by"

#### 4. **Prefix Sum** (4 variants) - Range queries
- [Range Sum Query](../variants/prefix_sum/variant_1_range_sum_query.md)
- [Product of Array Except Self](../variants/prefix_sum/variant_2_product_except_self.md)
- [Find Pivot Index](../variants/prefix_sum/variant_3_find_pivot_index.md)
- [Continuous Subarray Sum](../variants/prefix_sum/variant_4_continuous_subarray_sum.md)

**Signal Words:** "subarray sum", "range query", "cumulative"

#### 5. **Monotonic Stack** (5 variants) - Next greater/smaller
- [Next Greater Element](../variants/monotonic_stack/variant_1_next_greater_element.md)
- [Daily Temperatures](../variants/monotonic_stack/variant_2_daily_temperatures.md)
- [Largest Rectangle in Histogram](../variants/monotonic_stack/variant_3_largest_rectangle_histogram.md)
- [Remove K Digits](../variants/monotonic_stack/variant_5_remove_k_digits.md)

**Signal Words:** "next greater", "next smaller", "span", "histogram"

#### 6. **Binary Search** (6 variants) - Sorted or monotonic
- [Classic Binary Search](../variants/binary_search/variant_1_classic_binary_search.md)
- [Search Insert Position](../variants/binary_search/variant_2_search_insert_position.md)
- [Search in Rotated Sorted Array](../variants/binary_search/variant_3_rotated_array.md)
- [Find First and Last Position](../variants/binary_search/variant_4_first_last_position.md)
- [Find Peak Element](../variants/binary_search/variant_5_find_peak_element.md)
- [Koko Eating Bananas](../variants/binary_search/variant_6_koko_eating_bananas.md)

**Signal Words:** "sorted", "find target", "monotonic", "search"

#### 7. **Dynamic Programming 1D** (8 variants) - Optimization
- [Climbing Stairs](../variants/dp_1d/variant_1_climbing_stairs.md)
- [House Robber](../variants/dp_1d/variant_2_house_robber.md)
- [Coin Change](../variants/dp_1d/variant_3_coin_change.md)
- [Longest Increasing Subsequence](../variants/dp_1d/variant_4_longest_increasing_subsequence.md)
- [Maximum Subarray](../variants/dp_1d/variant_5_maximum_subarray.md)
- [Word Break](../variants/dp_1d/variant_6_word_break.md)
- [Decode Ways](../variants/dp_1d/variant_7_decode_ways.md)
- [Jump Game II](../variants/dp_1d/variant_8_jump_game_ii.md)

**Signal Words:** "count ways", "maximum/minimum", "can you reach"

#### 8. **Greedy** (6 variants) - Local optimal choices
- [Activity Selection](../variants/greedy/variant_1_activity_selection.md)
- [Jump Game](../variants/greedy/variant_2_jump_game.md)
- [Gas Station](../variants/greedy/variant_3_gas_station.md)
- [Task Scheduler](../variants/greedy/variant_5_task_scheduler.md)
- [Partition Labels](../variants/greedy/variant_6_partition_labels.md)

**Signal Words:** "minimum/maximum", "scheduling", "optimal"

---

## **String**

### When Problem Gives You: String or array of characters

**Applicable Patterns:**

#### 1. **Sliding Window** (string variants)
- [Longest Substring Without Repeating Characters](../variants/sliding_window/variant_1_longest_substring_no_repeat.md) ⭐
- [Minimum Window Substring](../variants/sliding_window/variant_3_min_window_substring.md) ⭐
- [K Distinct Characters](../variants/sliding_window/variant_7_k_distinct_characters.md)
- [Find All Anagrams](../variants/sliding_window/variant_4_find_all_anagrams.md)

**Use when:** "substring", "contiguous characters", "window"

#### 2. **Two Pointers** (string variants)
- [Valid Palindrome](../variants/two_pointers/variant_2_valid_palindrome.md)
- [3Sum (after sorting)](../variants/two_pointers/variant_4_three_sum.md)

**Use when:** "palindrome", "reverse", "compare"

#### 3. **Hash Map** (string variants)
- [Group Anagrams](../variants/hash_map/variant_2_group_anagrams.md) ⭐
- First Unique Character
- Longest Substring with At Most K Distinct

**Use when:** "anagram", "frequency", "character count"

#### 4. **Dynamic Programming** (string variants)
- [Word Break](../variants/dp_1d/variant_6_word_break.md) ⭐
- [Decode Ways](../variants/dp_1d/variant_7_decode_ways.md) ⭐
- [Longest Increasing Subsequence (with sorting)](../variants/dp_1d/variant_4_longest_increasing_subsequence.md)

**Use when:** "partition string", "decode", "count ways"

#### 5. **Backtracking** (string variants)
- [Palindrome Partitioning](../variants/backtracking/variant_5_palindrome_partitioning.md)
- [Generate Parentheses](../variants/backtracking/variant_2_generate_parentheses.md)
- [Word Search (if in grid)](../variants/backtracking/variant_6_word_search.md)

**Use when:** "all partitions", "generate all", "valid combinations"

---

## **Linked List**

### When Problem Gives You: Singly/Doubly Linked List

**Primary Pattern: Fast & Slow Pointers** (6 variants)

1. **[Linked List Cycle](../variants/fast_slow_pointers/variant_1_linked_list_cycle.md)** ⭐ - Detect cycle
2. **[Linked List Cycle II](../variants/fast_slow_pointers/variant_2_linked_list_cycle_ii.md)** - Find cycle start
3. **[Find Middle of Linked List](../variants/fast_slow_pointers/variant_3_middle_of_linked_list.md)** ⭐ - One-pass middle
4. **[Palindrome Linked List](../variants/fast_slow_pointers/variant_4_palindrome_linked_list.md)** - Check palindrome
5. **[Happy Number](../variants/fast_slow_pointers/variant_5_happy_number.md)** - Cycle detection in sequences
6. **[Remove Nth Node From End](../variants/fast_slow_pointers/variant_6_remove_nth_from_end.md)** - Two-pointer gap

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

**Primary Pattern: Binary Tree (DFS/BFS)** (8 variants)

1. **[Maximum Depth of Binary Tree](../variants/binary_tree/variant_1_max_depth.md)** ⭐ - DFS (post-order)
2. **[Invert Binary Tree](../variants/binary_tree/variant_2_invert_tree.md)** ⭐ - DFS (pre-order swap)
3. **[Diameter of Binary Tree](../variants/binary_tree/variant_3_diameter.md)** - DFS with global state
4. **[Path Sum](../variants/binary_tree/variant_4_path_sum.md)** - DFS with accumulator
5. **[Validate Binary Search Tree](../variants/binary_tree/variant_5_validate_bst.md)** ⭐⭐ - DFS with range tracking
6. **[Binary Tree Level Order Traversal](../variants/binary_tree/variant_6_level_order.md)** ⭐ - BFS with queue
7. **[Lowest Common Ancestor](../variants/binary_tree/variant_7_lowest_common_ancestor.md)** ⭐⭐ - DFS (post-order)
8. **[Serialize and Deserialize Binary Tree](../variants/binary_tree/variant_8_serialize_deserialize.md)** ⭐⭐ - DFS/BFS

**Key Technique:**
- **DFS (Recursive):** For tree properties (depth, diameter, validation)
  - **Pre-order:** Process root before children (invert, serialize)
  - **In-order:** Left → Root → Right (BST validation, sorted output)
  - **Post-order:** Process children before root (depth, diameter, LCA)
- **BFS (Iterative):** For level-by-level processing (level order traversal)

**DFS Template:**
```csharp
int DFS(TreeNode root) {
    if (root == null) return baseCase;
    
    int left = DFS(root.left);
    int right = DFS(root.right);
    
    return Combine(left, right, root.val);
}
```

**BFS Template:**
```csharp
var queue = new Queue<TreeNode>();
queue.Enqueue(root);

while (queue.Count > 0) {
    int levelSize = queue.Count;
    for (int i = 0; i < levelSize; i++) {
        var node = queue.Dequeue();
        // Process node
        if (node.left != null) queue.Enqueue(node.left);
        if (node.right != null) queue.Enqueue(node.right);
    }
}
```

**Dynamic Programming** can also apply:
- House Robber III (LeetCode #337) - Tree version
- Binary Tree Maximum Path Sum (LeetCode #124)

**Signal Words:** "binary tree", "traverse", "level", "depth", "path", "ancestor", "BST", "valid"

---

## **Graph / Matrix (Grid)**

### When Problem Gives You: Adjacency list, adjacency matrix, or 2D grid

**Primary Pattern: Graph Traversal (BFS/DFS)** (6 variants)

1. **[Number of Islands](../variants/graph/variant_1_number_of_islands.md)** ⭐ - Grid DFS/BFS
2. **[Clone Graph](../variants/graph/variant_2_clone_graph.md)** - DFS with map
3. **[Course Schedule](../variants/graph/variant_3_course_schedule.md)** ⭐ - Topological sort (cycle detection)
4. **[Pacific Atlantic Water Flow](../variants/graph/variant_4_pacific_atlantic.md)** - Multi-source DFS
5. **[Word Ladder](../variants/graph/variant_5_word_ladder.md)** - BFS shortest path
6. **[Network Delay Time](../variants/graph/variant_6_network_delay_time.md)** - Dijkstra's algorithm

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

1. **[Merge Intervals](../variants/intervals/variant_1_merge_intervals.md)** ⭐ - Collapse overlaps
2. **[Insert Interval](../variants/intervals/variant_2_insert_interval.md)** - Merge into sorted list
3. **[Non-Overlapping Intervals](../variants/intervals/variant_3_non_overlapping_intervals.md)** - Minimum removals
4. **[Meeting Rooms II](../variants/intervals/variant_4_meeting_rooms_ii.md)** ⭐ - Count concurrent
5. **[Interval List Intersections](../variants/intervals/variant_5_interval_list_intersections.md)** - Find overlaps

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
- [Kth Largest in Stream](../variants/heap/variant_1_kth_largest_stream.md)
- [Find Median from Stream](../variants/heap/variant_2_find_median_stream.md) ⭐
- [Merge K Sorted Lists](../variants/heap/variant_3_merge_k_sorted_lists.md) ⭐
- [Task Scheduler](../variants/greedy/variant_5_task_scheduler.md)
- [K Closest Points](../variants/top_k/variant_3_k_closest_points.md)

**Use heap when:**
- Dynamic ordering (elements added/removed)
- Need min/max repeatedly
- Don't need full sort

#### 2. **Top K Elements** (4 variants)
- [Kth Largest Element](../variants/top_k/variant_1_kth_largest_element.md) ⭐
- [Top K Frequent Elements](../variants/top_k/variant_2_top_k_frequent.md) ⭐
- [K Closest Points](../variants/top_k/variant_3_k_closest_points.md)
- [K Pairs with Smallest Sums](../variants/top_k/variant_4_k_pairs_smallest_sums.md)

**Technique:**
- **Min-heap of size K** for K largest
- **Max-heap of size K** for K smallest

**Signal Words:** "K largest", "K smallest", "median", "merge K", "Kth"

---

## **Set / Dictionary (Implicit)**

### When You Need: Fast lookup, uniqueness, counting

**Primary Pattern: Hash Map / Frequency Counting** (5 variants)

1. **[Two Sum](../variants/hash_map/variant_1_two_sum.md)** ⭐ - Complement lookup
2. **[Group Anagrams](../variants/hash_map/variant_2_group_anagrams.md)** ⭐ - Group by key
3. **[Subarray Sum Equals K](../variants/hash_map/variant_3_subarray_sum_k.md)** - Prefix sum frequency
4. **[Longest Consecutive Sequence](../variants/hash_map/variant_4_longest_consecutive.md)** - O(1) existence check
5. **[Duplicate Within K Distance](../variants/hash_map/variant_5_duplicate_within_k_distance.md)** - Recent index tracking

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

1. **[Next Greater Element](../variants/monotonic_stack/variant_1_next_greater_element.md)** ⭐
2. **[Daily Temperatures](../variants/monotonic_stack/variant_2_daily_temperatures.md)** ⭐
3. **[Largest Rectangle in Histogram](../variants/monotonic_stack/variant_3_largest_rectangle_histogram.md)** ⭐
4. **[Next Greater Element II](../variants/monotonic_stack/variant_4_next_greater_element_ii.md)**
5. **[Remove K Digits](../variants/monotonic_stack/variant_5_remove_k_digits.md)**

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
