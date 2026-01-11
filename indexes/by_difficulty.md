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
1. **[Maximum Sum Subarray of Size K](../variants/sliding_window/variant_2_max_sum_size_k.md)** - Fixed window, simple accumulation
   - Pattern: Fixed-size sliding window
   - Complexity: O(n)

### Two Pointers (Easy)
1. **[Two Sum II (Sorted Array)](../variants/two_pointers/variant_1_two_sum_sorted.md)** - LeetCode #167
   - Pattern: Opposite-direction pointers
   - Complexity: O(n)

2. **[Valid Palindrome](../variants/two_pointers/variant_2_valid_palindrome.md)** - LeetCode #125
   - Pattern: Compare from both ends
   - Complexity: O(n)

3. **[Remove Duplicates from Sorted Array](../variants/two_pointers/variant_3_remove_duplicates.md)** - LeetCode #26
   - Pattern: Fast & slow pointers
   - Complexity: O(n)

4. **[Move Zeros](../variants/two_pointers/variant_7_move_zeros.md)** - LeetCode #283
   - Pattern: In-place partition
   - Complexity: O(n)

### Fast & Slow Pointers (Easy)
1. **[Linked List Cycle](../variants/fast_slow_pointers/variant_1_linked_list_cycle.md)** - LeetCode #141 ⭐
   - Pattern: Floyd's cycle detection
   - Complexity: O(n)

2. **[Find Middle of Linked List](../variants/fast_slow_pointers/variant_3_find_middle.md)** - LeetCode #876
   - Pattern: Fast 2x, slow 1x
   - Complexity: O(n)

3. **[Palindrome Linked List](../variants/fast_slow_pointers/variant_4_palindrome_linked_list.md)** - LeetCode #234
   - Pattern: Find middle + reverse + compare
   - Complexity: O(n)

4. **[Happy Number](../variants/fast_slow_pointers/variant_5_happy_number.md)** - LeetCode #202
   - Pattern: Cycle detection in sequences
   - Complexity: O(log n)

### Hash Map (Easy)
1. **[Two Sum](../variants/hash_map/variant_1_two_sum.md)** - LeetCode #1 ⭐
   - Pattern: Complement lookup
   - Complexity: O(n)

2. **[Contains Duplicate II](../variants/hash_map/variant_5_contains_duplicate_ii.md)** - LeetCode #219
   - Pattern: Sliding hash map
   - Complexity: O(n)

### Binary Search (Easy)
1. **[Classic Binary Search](../variants/binary_search/variant_1_binary_search_classic.md)** - LeetCode #704
   - Pattern: Standard template
   - Complexity: O(log n)

2. **[Search Insert Position](../variants/binary_search/variant_2_search_insert_position.md)** - LeetCode #35
   - Pattern: Find insertion point
   - Complexity: O(log n)

### Prefix Sum (Easy)
1. **[Range Sum Query - Immutable](../variants/prefix_sum/variant_1_range_sum_query.md)** - LeetCode #303
   - Pattern: Precompute cumulative sums
   - Complexity: O(n) prep, O(1) query

2. **[Find Pivot Index](../variants/prefix_sum/variant_3_find_pivot_index.md)** - LeetCode #724
   - Pattern: Left sum = right sum
   - Complexity: O(n)

### Dynamic Programming (Easy)
1. **[Climbing Stairs](../variants/dp_1d/variant_1_climbing_stairs.md)** - LeetCode #70 ⭐
   - Pattern: Fibonacci recurrence
   - Complexity: O(n)

### Greedy (Easy)
1. **[Jump Game](../variants/greedy/variant_2_jump_game.md)** - LeetCode #55
   - Pattern: Track max reachable
   - Complexity: O(n)

---

## **🟡 Medium Problems (Intermediate)**

**Build on fundamentals with more complex pattern applications**

### Sliding Window (Medium)
1. **[Longest Substring Without Repeating](../variants/sliding_window/variant_1_longest_substring_no_repeat.md)** - LeetCode #3 ⭐
   - Pattern: Variable window + hash set
   - Complexity: O(n)

2. **[Subarray Product Less Than K](../variants/sliding_window/variant_4_subarray_product_less_k.md)** - LeetCode #713
   - Pattern: Shrinking window
   - Complexity: O(n)

3. **[Minimum Size Subarray Sum](../variants/sliding_window/variant_5_min_size_subarray_sum.md)** - LeetCode #209
   - Pattern: Grow-then-shrink
   - Complexity: O(n)

4. **[K Distinct Characters](../variants/sliding_window/variant_7_k_distinct_characters.md)** - LeetCode #340
   - Pattern: Variable window + frequency map
   - Complexity: O(n)

### Two Pointers (Medium)
1. **[Container With Most Water](../variants/two_pointers/variant_4_container_with_most_water.md)** - LeetCode #11
   - Pattern: Maximize area, greedy movement
   - Complexity: O(n)

2. **[3Sum](../variants/two_pointers/variant_5_3sum.md)** - LeetCode #15 ⭐
   - Pattern: Fix one + two pointers
   - Complexity: O(n²)

3. **[Sort Colors (Dutch National Flag)](../variants/two_pointers/variant_8_sort_colors.md)** - LeetCode #75
   - Pattern: Three-way partition
   - Complexity: O(n)

### Fast & Slow Pointers (Medium)
1. **[Linked List Cycle II](../variants/fast_slow_pointers/variant_2_linked_list_cycle_ii.md)** - LeetCode #142
   - Pattern: Find cycle start (two phases)
   - Complexity: O(n)

2. **[Remove Nth Node From End](../variants/fast_slow_pointers/variant_6_remove_nth_from_end.md)** - LeetCode #19
   - Pattern: Gap-based two pointers
   - Complexity: O(n)

### Hash Map (Medium)
1. **[Group Anagrams](../variants/hash_map/variant_2_group_anagrams.md)** - LeetCode #49 ⭐
   - Pattern: Group by sorted key
   - Complexity: O(n × k log k)

2. **[Subarray Sum Equals K](../variants/hash_map/variant_3_subarray_sum_equals_k.md)** - LeetCode #560 ⭐
   - Pattern: Prefix sum + frequency map
   - Complexity: O(n)

3. **[Longest Consecutive Sequence](../variants/hash_map/variant_4_longest_consecutive_sequence.md)** - LeetCode #128
   - Pattern: Hash set for O(1) lookup
   - Complexity: O(n)

### Binary Search (Medium)
1. **[Search in Rotated Sorted Array](../variants/binary_search/variant_3_rotated_sorted_array.md)** - LeetCode #33
   - Pattern: Modified binary search
   - Complexity: O(log n)

2. **[Find First and Last Position](../variants/binary_search/variant_4_find_first_last_position.md)** - LeetCode #34
   - Pattern: Two binary searches (boundaries)
   - Complexity: O(log n)

3. **[Find Peak Element](../variants/binary_search/variant_5_find_peak_element.md)** - LeetCode #162
   - Pattern: Binary search for local max
   - Complexity: O(log n)

### Monotonic Stack (Medium)
1. **[Next Greater Element](../variants/monotonic_stack/variant_1_next_greater_element.md)** - LeetCode #496
   - Pattern: Decreasing stack
   - Complexity: O(n)

2. **[Daily Temperatures](../variants/monotonic_stack/variant_2_daily_temperatures.md)** - LeetCode #739 ⭐
   - Pattern: Distance to next greater
   - Complexity: O(n)

3. **[Next Greater Element II](../variants/monotonic_stack/variant_4_next_greater_element_ii.md)** - LeetCode #503
   - Pattern: Circular array (2x pass)
   - Complexity: O(n)

### Intervals (Medium)
1. **[Merge Intervals](../variants/intervals/variant_1_merge_intervals.md)** - LeetCode #56 ⭐
   - Pattern: Sort + sweep
   - Complexity: O(n log n)

2. **[Insert Interval](../variants/intervals/variant_2_insert_interval.md)** - LeetCode #57
   - Pattern: Three-phase insertion
   - Complexity: O(n)

3. **[Non-Overlapping Intervals](../variants/intervals/variant_3_non_overlapping_intervals.md)** - LeetCode #435
   - Pattern: Greedy (earliest end)
   - Complexity: O(n log n)

4. **[Interval List Intersections](../variants/intervals/variant_5_interval_list_intersections.md)** - LeetCode #986
   - Pattern: Two-pointer merge
   - Complexity: O(n + m)

### Heap / Priority Queue (Medium)
1. **[Kth Largest in Stream](../variants/heap/variant_1_kth_largest_stream.md)** - LeetCode #703
   - Pattern: Min-heap of size K
   - Complexity: O(log k) per insert

2. **[Task Scheduler](../variants/heap/variant_4_task_scheduler.md)** - LeetCode #621
   - Pattern: Greedy with heap
   - Complexity: O(n log 26)

3. **[K Closest Points](../variants/heap/variant_5_k_closest_points.md)** - LeetCode #973
   - Pattern: Min-heap of size K
   - Complexity: O(n log k)

### Top K Elements (Medium)
1. **[Top K Frequent Elements](../variants/top_k/variant_2_top_k_frequent_elements.md)** - LeetCode #347 ⭐
   - Pattern: Frequency + heap
   - Complexity: O(n log k)

2. **[K Closest Points](../variants/top_k/variant_3_k_closest_points.md)** - LeetCode #973
   - Pattern: Distance + heap
   - Complexity: O(n log k)

### Greedy (Medium)
1. **[Jump Game II](../variants/greedy/variant_3_jump_game_ii.md)** - LeetCode #45
   - Pattern: BFS levels (greedy)
   - Complexity: O(n)

2. **[Gas Station](../variants/greedy/variant_4_gas_station.md)** - LeetCode #134
   - Pattern: Single pass with total/current
   - Complexity: O(n)

3. **[Partition Labels](../variants/greedy/variant_6_partition_labels.md)** - LeetCode #763
   - Pattern: Greedy boundary extension
   - Complexity: O(n)

### Graph (Medium)
1. **[Clone Graph](../variants/graph/variant_2_clone_graph.md)** - LeetCode #133
   - Pattern: DFS with map
   - Complexity: O(V + E)

2. **[Course Schedule](../variants/graph/variant_3_course_schedule.md)** - LeetCode #207 ⭐
   - Pattern: Topological sort (cycle detection)
   - Complexity: O(V + E)

3. **[Pacific Atlantic Water Flow](../variants/graph/variant_4_pacific_atlantic_water_flow.md)** - LeetCode #417
   - Pattern: Multi-source DFS
   - Complexity: O(m × n)

### Dynamic Programming (Medium)
1. **[House Robber](../variants/dp_1d/variant_2_house_robber.md)** - LeetCode #198 ⭐
   - Pattern: Binary choice (rob or skip)
   - Complexity: O(n)

2. **[Coin Change](../variants/dp_1d/variant_3_coin_change.md)** - LeetCode #322 ⭐
   - Pattern: Unbounded knapsack
   - Complexity: O(n × m)

3. **[Maximum Subarray (Kadane's)](../variants/dp_1d/variant_5_maximum_subarray.md)** - LeetCode #53 ⭐
   - Pattern: Extend or restart
   - Complexity: O(n)

4. **[Word Break](../variants/dp_1d/variant_6_word_break.md)** - LeetCode #139 ⭐
   - Pattern: String DP
   - Complexity: O(n²)

5. **[Decode Ways](../variants/dp_1d/variant_7_decode_ways.md)** - LeetCode #91
   - Pattern: Count ways (1-digit + 2-digit)
   - Complexity: O(n)

6. **[Jump Game II](../variants/dp_1d/variant_8_jump_game_ii.md)** - LeetCode #45
   - Pattern: DP or greedy (BFS levels)
   - Complexity: O(n)

### Backtracking (Medium)
1. **[Subsets](../variants/backtracking/variant_1_subsets.md)** - LeetCode #78 ⭐
   - Pattern: Include/exclude decisions
   - Complexity: O(n × 2ⁿ)

2. **[Permutations](../variants/backtracking/variant_2_permutations.md)** - LeetCode #46 ⭐
   - Pattern: Used tracking or swap
   - Complexity: O(n × n!)

3. **[Combination Sum](../variants/backtracking/variant_3_combination_sum.md)** - LeetCode #39
   - Pattern: Unlimited reuse, target pruning
   - Complexity: O(N^(T/M))

4. **[Palindrome Partitioning](../variants/backtracking/variant_5_palindrome_partitioning.md)** - LeetCode #131
   - Pattern: Try all cuts, validate palindromes
   - Complexity: O(n × 2ⁿ)

5. **[Generate Parentheses](../variants/backtracking/variant_7_generate_parentheses.md)** - LeetCode #22
   - Pattern: Constrained generation (open/close counts)
   - Complexity: O(4ⁿ / √n) - Catalan

### Prefix Sum (Medium)
1. **[Product of Array Except Self](../variants/prefix_sum/variant_2_product_except_self.md)** - LeetCode #238
   - Pattern: Left × right products
   - Complexity: O(n)

2. **[Continuous Subarray Sum](../variants/prefix_sum/variant_4_continuous_subarray_sum.md)** - LeetCode #523
   - Pattern: Prefix sum mod k
   - Complexity: O(n)

---

## **🔴 Hard Problems (Advanced)**

**Master complex techniques and optimizations**

### Sliding Window (Hard)
1. **[Minimum Window Substring](../variants/sliding_window/variant_3_min_window_substring.md)** - LeetCode #76 ⭐⭐
   - Pattern: Shrink-while-valid + frequency map
   - Complexity: O(n + m)
   - **Why Hard:** Contract-while-valid strategy, complex state tracking

2. **[Sliding Window Maximum](../variants/sliding_window/variant_6_sliding_window_maximum.md)** - LeetCode #239 ⭐⭐
   - Pattern: Monotonic deque
   - Complexity: O(n)
   - **Why Hard:** Requires deque, not intuitive optimization

3. **[Minimum Size Subarray Sum (Negative)](../variants/sliding_window/variant_5b_min_size_subarray_sum_negative.md)** - Hard variant
   - Pattern: Prefix sum + monotonic deque (SW fails!)
   - Complexity: O(n)
   - **Why Hard:** Shows when sliding window breaks, hybrid approach

### Two Pointers (Hard)
1. **[Trapping Rain Water](../variants/two_pointers/variant_6_trapping_rain_water.md)** - LeetCode #42 ⭐⭐
   - Pattern: Two pointers with left/right max tracking
   - Complexity: O(n)
   - **Why Hard:** Non-obvious optimization, needs insight about water level

### Binary Search (Hard)
1. **[Koko Eating Bananas](../variants/binary_search/variant_6_koko_eating_bananas.md)** - LeetCode #875
   - Pattern: Binary search on answer space
   - Complexity: O(n log m)
   - **Why Hard:** Not searching array, searching answer space

### Monotonic Stack (Hard)
1. **[Largest Rectangle in Histogram](../variants/monotonic_stack/variant_3_largest_rectangle_histogram.md)** - LeetCode #84 ⭐⭐
   - Pattern: Find left/right boundaries with stack
   - Complexity: O(n)
   - **Why Hard:** Complex boundary logic, sentinel values

2. **[Remove K Digits](../variants/monotonic_stack/variant_5_remove_k_digits.md)** - LeetCode #402
   - Pattern: Increasing stack for smallest number
   - Complexity: O(n)
   - **Why Hard:** Edge cases (leading zeros, k > n)

### Intervals (Hard)
1. **[Meeting Rooms II](../variants/intervals/variant_4_meeting_rooms_ii.md)** - LeetCode #253
   - Pattern: Heap (sweep line) or sort start/end separately
   - Complexity: O(n log n)
   - **Why Hard:** Need to track concurrent intervals, heap management

### Heap / Priority Queue (Hard)
1. **[Find Median from Stream](../variants/heap/variant_2_find_median_stream.md)** - LeetCode #295 ⭐⭐
   - Pattern: Two heaps (max-heap + min-heap)
   - Complexity: O(log n) per insert, O(1) get median
   - **Why Hard:** Balance two heaps, non-obvious data structure choice

2. **[Merge K Sorted Lists](../variants/heap/variant_3_merge_k_sorted_lists.md)** - LeetCode #23 ⭐
   - Pattern: Min-heap with list heads
   - Complexity: O(n log k)
   - **Why Hard:** Multi-way merge, heap management

### Top K Elements (Hard)
1. **[K Pairs with Smallest Sums](../variants/top_k/variant_4_k_pairs_smallest_sums.md)** - LeetCode #373
   - Pattern: Min-heap with candidate generation
   - Complexity: O(k log k)
   - **Why Hard:** Avoid generating all pairs, smart candidate management

### Graph (Hard)
1. **[Word Ladder](../variants/graph/variant_5_word_ladder.md)** - LeetCode #127 ⭐⭐
   - Pattern: BFS shortest path
   - Complexity: O(n × m × 26) where m = word length
   - **Why Hard:** Building graph on the fly, bidirectional BFS optimization

2. **[Network Delay Time (Dijkstra)](../variants/graph/variant_6_network_delay_time.md)** - LeetCode #743
   - Pattern: Dijkstra's shortest path
   - Complexity: O(E log V)
   - **Why Hard:** Requires understanding of Dijkstra's algorithm

### Dynamic Programming (Hard)
1. **[Longest Increasing Subsequence](../variants/dp_1d/variant_4_longest_increasing_subsequence.md)** - LeetCode #300 ⭐
   - Pattern: O(n²) DP or O(n log n) with binary search
   - Complexity: O(n log n) optimal
   - **Why Hard:** Non-obvious binary search optimization

### Backtracking (Hard)
1. **[N-Queens](../variants/backtracking/variant_4_n_queens.md)** - LeetCode #51 ⭐⭐
   - Pattern: Constraint satisfaction, diagonal tracking
   - Complexity: O(n!)
   - **Why Hard:** Complex constraint checking, optimizations needed

2. **[Word Search](../variants/backtracking/variant_6_word_search.md)** - LeetCode #79
   - Pattern: Grid DFS with in-place visited marking
   - Complexity: O(m × n × 4^L)
   - **Why Hard:** 4-directional search, backtrack visited state

### Greedy (Hard)
1. **[Task Scheduler](../variants/greedy/variant_5_task_scheduler.md)** - LeetCode #621
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
