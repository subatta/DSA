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
1. **[Classic Binary Search](../variants/binary_search/variant_1_binary_search_classic.md)** - O(log n)
2. **[Search Insert Position](../variants/binary_search/variant_2_search_insert_position.md)** - O(log n)
3. **[Search in Rotated Sorted Array](../variants/binary_search/variant_3_rotated_sorted_array.md)** - O(log n)
4. **[Find First and Last Position](../variants/binary_search/variant_4_find_first_last_position.md)** - O(log n)
5. **[Find Peak Element](../variants/binary_search/variant_5_find_peak_element.md)** - O(log n)
6. **[Koko Eating Bananas](../variants/binary_search/variant_6_koko_eating_bananas.md)** - O(n log m) where m = max value

**Key Insight:** Monotonic search space allows halving

---

## **O(n) - Linear Time**

**Patterns:** Sliding Window, Two Pointers, Hash Map, Prefix Sum, Monotonic Stack, Fast & Slow Pointers

### Sliding Window Pattern (8 variants)
1. **[Maximum Sum Subarray of Size K](../variants/sliding_window/variant_2_max_sum_size_k.md)** - O(n)
2. **[Longest Substring Without Repeating](../variants/sliding_window/variant_1_longest_substring_no_repeat.md)** - O(n)
3. **[Minimum Window Substring](../variants/sliding_window/variant_3_min_window_substring.md)** - O(n)
4. **[Subarray Product Less Than K](../variants/sliding_window/variant_4_subarray_product_less_k.md)** - O(n)
5. **[Minimum Size Subarray Sum](../variants/sliding_window/variant_5_min_size_subarray_sum.md)** - O(n)
6. **[Sliding Window Maximum](../variants/sliding_window/variant_6_sliding_window_maximum.md)** - O(n)
7. **[K Distinct Characters](../variants/sliding_window/variant_7_k_distinct_characters.md)** - O(n)

**Key Insight:** Single pass with window state maintenance

### Hash Map Pattern (5 variants)
1. **[Two Sum](../variants/hash_map/variant_1_two_sum.md)** - O(n)
2. **[Group Anagrams](../variants/hash_map/variant_2_group_anagrams.md)** - O(n × k) where k = string length
3. **[Subarray Sum Equals K](../variants/hash_map/variant_3_subarray_sum_equals_k.md)** - O(n)
4. **[Longest Consecutive Sequence](../variants/hash_map/variant_4_longest_consecutive_sequence.md)** - O(n)
5. **[Duplicate Within K Distance](../variants/hash_map/variant_5_duplicate_within_k_distance.md)** - O(n)

**Key Insight:** O(1) lookup eliminates nested loops

### Prefix Sum Pattern (4 variants)
1. **[Range Sum Query](../variants/prefix_sum/variant_1_range_sum_query.md)** - O(n) preprocessing, O(1) query
2. **[Product of Array Except Self](../variants/prefix_sum/variant_2_product_except_self.md)** - O(n)
3. **[Find Pivot Index](../variants/prefix_sum/variant_3_find_pivot_index.md)** - O(n)
4. **[Continuous Subarray Sum](../variants/prefix_sum/variant_4_continuous_subarray_sum.md)** - O(n)

**Key Insight:** Precompute cumulative values for fast queries

### Monotonic Stack Pattern (5 variants)
1. **[Next Greater Element](../variants/monotonic_stack/variant_1_next_greater_element.md)** - O(n)
2. **[Daily Temperatures](../variants/monotonic_stack/variant_2_daily_temperatures.md)** - O(n)
3. **[Largest Rectangle in Histogram](../variants/monotonic_stack/variant_3_largest_rectangle_histogram.md)** - O(n)
4. **[Next Greater Element II](../variants/monotonic_stack/variant_4_next_greater_element_ii.md)** - O(n)
5. **[Remove K Digits](../variants/monotonic_stack/variant_5_remove_k_digits.md)** - O(n)

**Key Insight:** Each element pushed/popped once (amortized)

### Fast & Slow Pointers Pattern (6 variants)
1. **[Linked List Cycle](../variants/fast_slow_pointers/variant_1_linked_list_cycle.md)** - O(n)
2. **[Linked List Cycle II](../variants/fast_slow_pointers/variant_2_linked_list_cycle_ii.md)** - O(n)
3. **[Find Middle of Linked List](../variants/fast_slow_pointers/variant_3_find_middle.md)** - O(n)
4. **[Palindrome Linked List](../variants/fast_slow_pointers/variant_4_palindrome_linked_list.md)** - O(n)
5. **[Happy Number](../variants/fast_slow_pointers/variant_5_happy_number.md)** - O(log n) for sum of squares
6. **[Remove Nth Node From End](../variants/fast_slow_pointers/variant_6_remove_nth_from_end.md)** - O(n)

**Key Insight:** Two-pointer traversal in one pass

### Two Pointers (some variants)
1. **[Valid Palindrome](../variants/two_pointers/variant_2_valid_palindrome.md)** - O(n)
2. **[Remove Duplicates](../variants/two_pointers/variant_3_remove_duplicates.md)** - O(n)
3. **[Move Zeros](../variants/two_pointers/variant_7_move_zeros.md)** - O(n)

**Key Insight:** Linear scan with coordinated pointers

### Binary Tree Pattern (8 variants)
1. **[Maximum Depth of Binary Tree](../variants/binary_tree/variant_1_max_depth.md)** - O(n)
2. **[Invert Binary Tree](../variants/binary_tree/variant_2_invert_tree.md)** - O(n)
3. **[Diameter of Binary Tree](../variants/binary_tree/variant_3_diameter.md)** - O(n)
4. **[Path Sum](../variants/binary_tree/variant_4_path_sum.md)** - O(n)
5. **[Validate Binary Search Tree](../variants/binary_tree/variant_5_validate_bst.md)** - O(n)
6. **[Binary Tree Level Order Traversal](../variants/binary_tree/variant_6_level_order.md)** - O(n)
7. **[Lowest Common Ancestor](../variants/binary_tree/variant_7_lowest_common_ancestor.md)** - O(n)
8. **[Serialize and Deserialize Binary Tree](../variants/binary_tree/variant_8_serialize_deserialize.md)** - O(n)

**Key Insight:** Visit each node exactly once (DFS or BFS)

### Dynamic Programming 1D (linear variants)
1. **[Climbing Stairs](../variants/dp_1d/variant_1_climbing_stairs.md)** - O(n)
2. **[House Robber](../variants/dp_1d/variant_2_house_robber.md)** - O(n)
3. **[Maximum Subarray (Kadane's)](../variants/dp_1d/variant_5_maximum_subarray.md)** - O(n)
4. **[Decode Ways](../variants/dp_1d/variant_7_decode_ways.md)** - O(n)

**Key Insight:** Each state computed once in sequence

---

## **O(n log n) - Linearithmic Time**

**Patterns:** Intervals, Greedy (with sorting), Heap, Top K, Some DP variants

### Intervals Pattern (5 variants)
1. **[Merge Intervals](../variants/intervals/variant_1_merge_intervals.md)** - O(n log n) - sorting
2. **[Insert Interval](../variants/intervals/variant_2_insert_interval.md)** - O(n) if sorted, O(n log n) if not
3. **[Non-Overlapping Intervals](../variants/intervals/variant_3_non_overlapping_intervals.md)** - O(n log n)
4. **[Meeting Rooms II](../variants/intervals/variant_4_meeting_rooms_ii.md)** - O(n log n)
5. **[Interval List Intersections](../variants/intervals/variant_5_interval_list_intersections.md)** - O(n + m) if both sorted

**Key Insight:** Sort by start time, then linear merge

### Greedy Pattern (6 variants)
1. **[Activity Selection](../variants/greedy/variant_1_activity_selection.md)** - O(n log n) - sort by end time
2. **[Jump Game](../variants/greedy/variant_2_jump_game.md)** - O(n)
3. **[Jump Game II](../variants/greedy/variant_3_jump_game_ii.md)** - O(n)
4. **[Gas Station](../variants/greedy/variant_4_gas_station.md)** - O(n)
5. **[Task Scheduler](../variants/greedy/variant_5_task_scheduler.md)** - O(n log n)
6. **[Partition Labels](../variants/greedy/variant_6_partition_labels.md)** - O(n)

**Key Insight:** Sorting enables greedy choice validation

### Heap / Priority Queue Pattern (5 variants)
1. **[Kth Largest in Stream](../variants/heap/variant_1_kth_largest_stream.md)** - O(log k) per insertion
2. **[Find Median from Stream](../variants/heap/variant_2_find_median_stream.md)** - O(log n) per insertion
3. **[Merge K Sorted Lists](../variants/heap/variant_3_merge_k_sorted_lists.md)** - O(n log k) where n = total elements
4. **[Task Scheduler](../variants/heap/variant_4_task_scheduler.md)** - O(n log n)
5. **[K Closest Points](../variants/heap/variant_5_k_closest_points.md)** - O(n log k)

**Key Insight:** Heap operations are O(log n)

### Top K Elements Pattern (4 variants)
1. **[Kth Largest Element](../variants/top_k/variant_1_kth_largest_element.md)** - O(n log k) with heap, O(n) with quickselect
2. **[Top K Frequent Elements](../variants/top_k/variant_2_top_k_frequent_elements.md)** - O(n log k)
3. **[K Closest Points](../variants/top_k/variant_3_k_closest_points.md)** - O(n log k)
4. **[K Pairs with Smallest Sums](../variants/top_k/variant_4_k_pairs_smallest_sums.md)** - O(k log k)

**Key Insight:** Maintain heap of size k

### Dynamic Programming (with sorting)
1. **[Longest Increasing Subsequence](../variants/dp_1d/variant_4_longest_increasing_subsequence.md)** - O(n log n) with binary search

**Key Insight:** Binary search optimization on DP state

---

## **O(n²) - Quadratic Time**

**Patterns:** Two Pointers (nested), Dynamic Programming 1D, Graph (adjacency matrix)

### Two Pointers Pattern (quadratic variants)
1. **[Two Sum II (Sorted)](../variants/two_pointers/variant_1_two_sum_sorted.md)** - O(n) but brute force O(n²)
2. **[Container With Most Water](../variants/two_pointers/variant_4_container_with_most_water.md)** - O(n) optimized from O(n²)
3. **[3Sum](../variants/two_pointers/variant_5_3sum.md)** - O(n²) - outer loop + two pointers
4. **[Trapping Rain Water](../variants/two_pointers/variant_6_trapping_rain_water.md)** - O(n) optimized from O(n²)
5. **[Sort Colors](../variants/two_pointers/variant_8_sort_colors.md)** - O(n)

**Key Insight:** Fixing one element, scanning with two pointers

### Dynamic Programming 1D (quadratic variants)
1. **[Coin Change](../variants/dp_1d/variant_3_coin_change.md)** - O(n × m) where n = amount, m = coins
2. **[Longest Increasing Subsequence](../variants/dp_1d/variant_4_longest_increasing_subsequence.md)** - O(n²) with DP table
3. **[Word Break](../variants/dp_1d/variant_6_word_break.md)** - O(n² × L) where L = avg word length
4. **[Jump Game II](../variants/dp_1d/variant_8_jump_game_ii.md)** - O(n) greedy, O(n²) with DP

**Key Insight:** Nested loops for state transitions

### Graph Traversal Pattern (6 variants)
1. **[Number of Islands](../variants/graph/variant_1_number_of_islands.md)** - O(m × n) for grid
2. **[Clone Graph](../variants/graph/variant_2_clone_graph.md)** - O(V + E)
3. **[Course Schedule](../variants/graph/variant_3_course_schedule.md)** - O(V + E)
4. **[Pacific Atlantic Water Flow](../variants/graph/variant_4_pacific_atlantic_water_flow.md)** - O(m × n)
5. **[Word Ladder](../variants/graph/variant_5_word_ladder.md)** - O(n × m × 26) where n = words, m = length
6. **[Network Delay Time (Dijkstra)](../variants/graph/variant_6_network_delay_time.md)** - O(E log V)

**Key Insight:** Visit each node/edge once

---

## **O(2ⁿ) - Exponential Time**

**Patterns:** Backtracking (generate all solutions)

### Backtracking Pattern (7 variants)
1. **[Subsets](../variants/backtracking/variant_1_subsets.md)** - O(n × 2ⁿ) - power set generation
2. **[Permutations](../variants/backtracking/variant_2_permutations.md)** - O(n × n!) 
3. **[Combination Sum](../variants/backtracking/variant_3_combination_sum.md)** - O(2ⁿ) average, depends on target
4. **[N-Queens](../variants/backtracking/variant_4_n_queens.md)** - O(n!)
5. **[Palindrome Partitioning](../variants/backtracking/variant_5_palindrome_partitioning.md)** - O(n × 2ⁿ)
6. **[Word Search](../variants/backtracking/variant_6_word_search.md)** - O(m × n × 4^L) where L = word length
7. **[Generate Parentheses](../variants/backtracking/variant_7_generate_parentheses.md)** - O(4ⁿ / √n) - Catalan number

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
