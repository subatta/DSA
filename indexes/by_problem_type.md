# Algorithm Patterns by Problem Type

This index organizes patterns by the **goal or objective** of the problem, helping you identify the right approach based on what you're trying to achieve.

---

## **Optimization Problems (Min/Max)**

**Goal:** Find minimum or maximum value/count under constraints

### Primary Patterns:

#### 1. **Dynamic Programming 1D** (8 variants)
**Use when:** Overlapping subproblems, need to try multiple choices

- **[Climbing Stairs](../variants/dp_1d/variant_1_climbing_stairs.md)** - Count ways to reach top
- **[House Robber](../variants/dp_1d/variant_2_house_robber.md)** - Maximum sum (non-adjacent)
- **[Coin Change](../variants/dp_1d/variant_3_coin_change.md)** - Minimum coins to make amount
- **[Longest Increasing Subsequence](../variants/dp_1d/variant_4_longest_increasing_subsequence.md)** - Longest length
- **[Maximum Subarray (Kadane's)](../variants/dp_1d/variant_5_maximum_subarray.md)** - Maximum sum
- **[Word Break](../variants/dp_1d/variant_6_word_break.md)** - Can partition string?
- **[Decode Ways](../variants/dp_1d/variant_7_decode_ways.md)** - Count valid decodings
- **[Jump Game II](../variants/dp_1d/variant_8_jump_game_ii.md)** - Minimum jumps

**Signal Words:** "minimum cost", "maximum value", "count ways to"

#### 2. **Greedy** (6 variants)
**Use when:** Local optimal leads to global optimal

- **[Activity Selection](../variants/greedy/variant_1_activity_selection.md)** - Maximum non-overlapping activities
- **[Jump Game](../variants/greedy/variant_2_jump_game.md)** - Can reach end?
- **[Jump Game II](../variants/dp_1d/variant_8_jump_game_ii.md)** - Minimum jumps
- **[Gas Station](../variants/greedy/variant_3_gas_station.md)** - Find starting point
- **[Task Scheduler](../variants/greedy/variant_5_task_scheduler.md)** - Minimum idle time
- **[Partition Labels](../variants/greedy/variant_6_partition_labels.md)** - Maximum partition count

**Signal Words:** "minimum", "maximum", "optimal", "scheduling"

**How to decide DP vs Greedy:**
- **Greedy:** Can prove locally optimal → globally optimal (exchange argument)
- **DP:** Need to explore multiple choices, can't commit early

#### 3. **Sliding Window** (optimization variants)
- **[Maximum Sum Subarray of Size K](../variants/sliding_window/variant_8_max_sum_subarray_size_k.md)** - Maximum sum
- **[Minimum Size Subarray Sum](../variants/sliding_window/variant_5_min_size_subarray_sum.md)** - Minimum length
- **[Minimum Window Substring](../variants/sliding_window/variant_3_min_window_substring.md)** - Smallest window

**Use when:** Optimizing over contiguous sequences

---

## **Search Problems**

**Goal:** Find specific element, position, or boundary

### Primary Patterns:

#### 1. **Binary Search** (6 variants)
**Use when:** Search space is sorted or monotonic

- **[Classic Binary Search](../variants/binary_search/variant_1_classic_binary_search.md)** - Find exact value
- **[Search Insert Position](../variants/binary_search/variant_2_search_insert_position.md)** - Find insertion point
- **[Search in Rotated Sorted Array](../variants/binary_search/variant_3_rotated_array.md)** - Modified search
- **[Find First and Last Position](../variants/binary_search/variant_4_first_last_position.md)** - Find boundaries
- **[Find Peak Element](../variants/binary_search/variant_5_find_peak_element.md)** - Find local maximum
- **[Koko Eating Bananas](../variants/binary_search/variant_6_koko_eating_bananas.md)** - Binary search on answer

**Signal Words:** "sorted", "find", "search for", "locate"

#### 2. **Hash Map** (lookup variants)
- **[Two Sum](../variants/hash_map/variant_1_two_sum.md)** - Find pair with target sum
- **[Contains Duplicate II](../variants/hash_map/variant_5_contains_duplicate_ii.md)** - Find duplicate within distance
- **[Longest Consecutive Sequence](../variants/hash_map/variant_4_longest_consecutive.md)** - Find longest sequence

**Use when:** Need fast O(1) lookup

#### 3. **Graph Traversal (BFS)** (shortest path)
- **[Word Ladder](../variants/graph/variant_5_word_ladder.md)** - Shortest transformation
- **[Network Delay Time](../variants/graph/variant_6_network_delay_time.md)** - Shortest path (weighted)
- **[Number of Islands](../variants/graph/variant_1_number_of_islands.md)** - Find all components

**Use when:** Searching graphs, finding paths

---

## **Counting Problems**

**Goal:** Count number of ways, combinations, or occurrences

### Primary Patterns:

#### 1. **Dynamic Programming** (counting variants)
- **[Climbing Stairs](../variants/dp_1d/variant_1_climbing_stairs.md)** - Count ways to climb
- **[Decode Ways](../variants/dp_1d/variant_7_decode_ways.md)** - Count valid decodings
- **Coin Change II** - Count combinations

**Use when:** Need to count all valid ways

#### 2. **Backtracking** (generation + counting)
- **[Subsets](../variants/backtracking/variant_1_subsets.md)** - Count/generate all subsets (2ⁿ)
- **[Permutations](../variants/backtracking/variant_3_permutations.md)** - Count/generate all orderings (n!)
- **[Combination Sum](../variants/backtracking/variant_7_combination_sum.md)** - Count valid combinations
- **[Generate Parentheses](../variants/backtracking/variant_2_generate_parentheses.md)** - Count valid strings

**Use when:** Small n (≤ 20), need explicit generation or counting

#### 3. **Hash Map** (frequency counting)
- **[Group Anagrams](../variants/hash_map/variant_2_group_anagrams.md)** - Count groups
- **[Subarray Sum Equals K](../variants/hash_map/variant_3_subarray_sum_k.md)** - Count subarrays
- **[Top K Frequent Elements](../variants/top_k/variant_2_top_k_frequent.md)** - Count frequencies

**Use when:** Counting occurrences or frequencies

---

## **Generation Problems (Find All)**

**Goal:** Generate/list all possible solutions

### Primary Pattern: **Backtracking** (7 variants)

1. **[Subsets](../variants/backtracking/variant_1_subsets.md)** - Generate all subsets (power set)
2. **[Permutations](../variants/backtracking/variant_3_permutations.md)** - Generate all orderings
3. **[Combination Sum](../variants/backtracking/variant_7_combination_sum.md)** - Generate all combinations
4. **[N-Queens](../variants/backtracking/variant_4_n_queens.md)** - Generate all valid board placements
5. **[Palindrome Partitioning](../variants/backtracking/variant_5_palindrome_partitioning.md)** - Generate all palindrome partitions
6. **[Word Search](../variants/backtracking/variant_6_word_search.md)** - Find if word exists in grid
7. **[Generate Parentheses](../variants/backtracking/variant_2_generate_parentheses.md)** - Generate all valid parentheses

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

- **[Maximum Sum Subarray of Size K](../variants/sliding_window/variant_8_max_sum_subarray_size_k.md)** - Fixed window
- **[Longest Substring Without Repeating](../variants/sliding_window/variant_1_longest_substring_no_repeat.md)** - Variable window
- **[Minimum Window Substring](../variants/sliding_window/variant_3_min_window_substring.md)** - Shrinkable window
- **[Subarray Product Less Than K](../variants/sliding_window/variant_2_subarray_product_less_k.md)** - Constraint-based
- **[Sliding Window Maximum](../variants/sliding_window/variant_6_sliding_window_maximum.md)** - Track max in window

**Signal Words:** "subarray", "substring", "contiguous", "consecutive"

#### 2. **Prefix Sum** (4 variants)
**Use when:** Need sum of ranges efficiently

- **[Range Sum Query](../variants/prefix_sum/variant_1_range_sum_query.md)** - O(1) range sums
- **[Subarray Sum Equals K](../variants/hash_map/variant_3_subarray_sum_k.md)** - Count subarrays with sum
- **[Continuous Subarray Sum](../variants/prefix_sum/variant_4_continuous_subarray_sum.md)** - Multiple of k
- **[Find Pivot Index](../variants/prefix_sum/variant_3_find_pivot_index.md)** - Equal left/right sums

**Signal Words:** "range sum", "subarray sum", "cumulative"

#### 3. **Two Pointers**
- **[Container With Most Water](../variants/two_pointers/variant_3_container_with_most_water.md)** - Maximum area
- **[Trapping Rain Water](../variants/two_pointers/variant_6_trapping_rain_water.md)** - Water trapped
- **[3Sum](../variants/two_pointers/variant_4_three_sum.md)** - Find triplets

**Use when:** Can eliminate options by comparing

---

## **Sorting/Ordering Problems**

**Goal:** Arrange elements or find order

### Primary Patterns:

#### 1. **Intervals / Merging** (5 variants)
**Use when:** Dealing with ranges/intervals

- **[Merge Intervals](../variants/intervals/variant_1_merge_intervals.md)** - Collapse overlaps
- **[Insert Interval](../variants/intervals/variant_2_insert_interval.md)** - Maintain sorted intervals
- **[Non-Overlapping Intervals](../variants/intervals/variant_3_non_overlapping_intervals.md)** - Remove minimum
- **[Meeting Rooms II](../variants/intervals/variant_4_meeting_rooms_ii.md)** - Count overlaps
- **[Interval List Intersections](../variants/intervals/variant_5_interval_list_intersections.md)** - Find overlaps

**Key:** Sort by start time, then process

#### 2. **Graph Traversal** (topological sort)
- **[Course Schedule](../variants/graph/variant_3_course_schedule.md)** - Topological ordering
- **Course Schedule II** - Return valid order

**Use when:** Dependencies or ordering constraints

#### 3. **Two Pointers**
- **[Sort Colors](../variants/two_pointers/variant_5_sort_colors.md)** - In-place partitioning
- **[Remove Duplicates](../variants/two_pointers/variant_7_remove_duplicates.md)** - Maintain order

---

## **Selection/Ranking Problems**

**Goal:** Find top K, Kth element, or rank elements

### Primary Patterns:

#### 1. **Top K Elements** (4 variants)
**Use when:** Need K largest/smallest/frequent

- **[Kth Largest Element](../variants/top_k/variant_1_kth_largest_element.md)** - Find Kth largest
- **[Top K Frequent Elements](../variants/top_k/variant_2_top_k_frequent.md)** - K most frequent
- **[K Closest Points](../variants/top_k/variant_3_k_closest_points.md)** - K nearest to origin
- **[K Pairs with Smallest Sums](../variants/top_k/variant_4_k_pairs_smallest_sums.md)** - K smallest pairs

**Technique:** Heap of size K

#### 2. **Heap / Priority Queue** (5 variants)
**Use when:** Need to repeatedly access extreme

- **[Kth Largest in Stream](../variants/heap/variant_1_kth_largest_stream.md)** - Maintain Kth largest
- **[Find Median from Stream](../variants/heap/variant_2_find_median_stream.md)** - Two heaps
- **[Merge K Sorted Lists](../variants/heap/variant_3_merge_k_sorted_lists.md)** - Multi-way merge

**Signal Words:** "Kth", "top K", "largest", "smallest", "median"

---

## **Validation/Decision Problems**

**Goal:** Check if something is valid or possible

### Primary Patterns:

#### 1. **Fast & Slow Pointers** (cycle detection)
- **[Linked List Cycle](../variants/fast_slow_pointers/variant_1_linked_list_cycle.md)** - Has cycle?
- **[Happy Number](../variants/fast_slow_pointers/variant_5_happy_number.md)** - Reaches 1 or cycles?

**Use when:** Checking cycles or loops

#### 2. **Two Pointers** (validation)
- **[Valid Palindrome](../variants/two_pointers/variant_2_valid_palindrome.md)** - Is palindrome?

#### 3. **Graph Traversal** (reachability)
- **[Course Schedule](../variants/graph/variant_3_course_schedule.md)** - Is valid ordering possible?
- **[Number of Islands](../variants/graph/variant_1_number_of_islands.md)** - Count connected components

#### 4. **Dynamic Programming** (feasibility)
- **[Word Break](../variants/dp_1d/variant_6_word_break.md)** - Can segment string?
- **[Jump Game](../variants/greedy/variant_2_jump_game.md)** - Can reach end?

**Signal Words:** "can you", "is it possible", "valid", "feasible"

---

## **Matching/Pairing Problems**

**Goal:** Find pairs, matches, or relationships

### Primary Patterns:

#### 1. **Hash Map** (5 variants)
**Use when:** Need to find matching elements

- **[Two Sum](../variants/hash_map/variant_1_two_sum.md)** ⭐ - Find pair summing to target
- **[Group Anagrams](../variants/hash_map/variant_2_group_anagrams.md)** - Match anagrams together
- **[Longest Consecutive Sequence](../variants/hash_map/variant_4_longest_consecutive.md)** - Match consecutive numbers

#### 2. **Two Pointers**
- **[Two Sum II (Sorted)](../variants/two_pointers/variant_1_two_sum_ii.md)** - Find pair in sorted array
- **[3Sum](../variants/two_pointers/variant_4_three_sum.md)** - Find triplets
- **[Container With Most Water](../variants/two_pointers/variant_3_container_with_most_water.md)** - Find optimal pair

**Signal Words:** "find pair", "two elements", "match"

---

## **Connectivity/Reachability Problems**

**Goal:** Determine if nodes/cells are connected or reachable

### Primary Pattern: **Graph Traversal (BFS/DFS)** (6 variants)

1. **[Number of Islands](../variants/graph/variant_1_number_of_islands.md)** - Count connected components
2. **[Clone Graph](../variants/graph/variant_2_clone_graph.md)** - Traverse and copy
3. **[Course Schedule](../variants/graph/variant_3_course_schedule.md)** - Check if all reachable (no cycles)
4. **[Pacific Atlantic Water Flow](../variants/graph/variant_4_pacific_atlantic.md)** - Multi-source reachability
5. **[Word Ladder](../variants/graph/variant_5_word_ladder.md)** - Can transform word to target?
6. **[Network Delay Time](../variants/graph/variant_6_network_delay_time.md)** - Can signal reach all nodes?

**Choose BFS when:** Need shortest path
**Choose DFS when:** Need any path or full exploration

**Signal Words:** "connected", "reachable", "path", "components", "islands"

---

## **Transformation/Construction Problems**

**Goal:** Transform input to desired output or construct something

### Primary Patterns:

#### 1. **Dynamic Programming**
- **[Decode Ways](../variants/dp_1d/variant_7_decode_ways.md)** - Transform digit string to letters
- **[Word Break](../variants/dp_1d/variant_6_word_break.md)** - Transform string to words

#### 2. **Monotonic Stack**
- **[Remove K Digits](../variants/monotonic_stack/variant_5_remove_k_digits.md)** - Construct smallest number

#### 3. **Backtracking**
- **[Generate Parentheses](../variants/backtracking/variant_2_generate_parentheses.md)** - Construct valid strings
- **[N-Queens](../variants/backtracking/variant_4_n_queens.md)** - Construct valid board

---

## **Streaming/Online Problems**

**Goal:** Process elements one at a time without knowing future

### Primary Patterns:

#### 1. **Heap / Priority Queue**
- **[Kth Largest in Stream](../variants/heap/variant_1_kth_largest_stream.md)** - Maintain Kth as elements arrive
- **[Find Median from Stream](../variants/heap/variant_2_find_median_stream.md)** - Update median online

#### 2. **Hash Map**
- **[Contains Duplicate II](../variants/hash_map/variant_5_contains_duplicate_ii.md)** - Track recent elements
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
