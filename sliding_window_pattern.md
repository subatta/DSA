# Pattern Title: Sliding Window

## Pattern Description:
- **What is it?**  
  Sliding Window is a technique used to process contiguous sequences (subarrays/substrings) of a given size or condition efficiently, often using two pointers to maintain a "window" of elements and updating results incrementally.  

- **What abstract problem does it solve?**  
  Efficiently finding subarrays/substrings that satisfy conditions (sum, distinct elements, max/min values, etc.) without recomputing from scratch every time.  

- **Core Optimization:**  
  Transforms O(n²) or O(n³) brute force enumeration of all subarrays into O(n) by maintaining a window that slides through the array, updating incrementally instead of recalculating from scratch.

## State Space Context:
- **Primary State Space**: All Contiguous Subarrays - O(n²) or O(n) for fixed-size windows
- See [State Space Index](../indexes/by_state_space.md#category-2-all-contiguous-subarrays-on²) for full analysis
- **Key Insight**: Most variants start with O(n²) state space but optimize to O(n) traversal through incremental window updates

## Canonical Code Skeleton:

```csharp
int left = 0;
// Declare and define window state as required
// Declare and define result data structure

for (int right = 0; right < arr.Length; right++) 
{
    // Use nums[right]

    // Shrink window from left if condition violated
    while (window invalid) {
        // Use nums[left] to update window state
        left++;
    }

    // Update result based on window state
}

// return result;
```

## Pattern Variants:

This pattern has 8 main variants organized by window type and auxiliary data structures:

### Fixed-Size Window:
1. **[Maximum Sum Subarray of Size K](variants/sliding_window/variant_2_max_sum_size_k.md)** - Easy
   - Find maximum sum in fixed window of size k
   - State Space: O(n) windows (n-k+1)
   - Incremental update: O(1) per slide vs O(k) recalculation

### Variable-Size Window (Pure Sliding Window):
2. **[Longest Substring Without Repeating Characters](variants/sliding_window/variant_1_longest_substring_no_repeat.md)** - LeetCode #3, Medium
   - Find longest substring with all distinct characters
   - State Space: O(n²) substrings → O(n) with hash set tracking
   
3. **[Subarray Product Less Than K](variants/sliding_window/variant_4_subarray_product_less_k.md)** - LeetCode #713, Medium
   - Count subarrays where product < k
   - Uses counting formula: `right - left + 1`
   
4. **[Minimum Size Subarray Sum](variants/sliding_window/variant_5_min_size_subarray_sum.md)** - LeetCode #209, Medium
   - Find shortest subarray with sum ≥ target (positive integers only)
   - "Grow-then-shrink" minimization pattern

5. **[Minimum Size Subarray Sum (With Negative Integers)](variants/sliding_window/variant_5b_min_size_subarray_sum_negative.md)** - Hard
   - Same problem but handles negative integers
   - **Hybrid**: Prefix Sum + Monotonic Deque (sliding window fails!)
   - Shows when sliding window doesn't work

### Hybrid Patterns (Sliding Window + Auxiliary Data Structure):
6. **[Minimum Window Substring](variants/sliding_window/variant_3_min_window_substring.md)** ⭐ - LeetCode #76, Hard
   - Shortest substring containing all target characters
   - **Hybrid**: Sliding Window + Hash Map (frequency tracking)
   - Contract-while-valid strategy
   
7. **[Sliding Window Maximum](variants/sliding_window/variant_6_sliding_window_maximum.md)** ⭐ - LeetCode #239, Hard
   - Maximum value in each fixed-size window
   - **Hybrid**: Sliding Window + Monotonic Deque
   - O(n×k) → O(n) via dominated element removal
   
8. **[Longest Substring with K Distinct Characters](variants/sliding_window/variant_7_k_distinct_characters.md)** ⭐ - LeetCode #340, Medium
   - Longest substring with ≤ k distinct characters
   - **Hybrid**: Sliding Window + Hash Map (frequency counting)

⭐ = Hybrid pattern requiring auxiliary data structure

---

## Pattern Recognition:

**Use Sliding Window when:**
- Problem involves **contiguous subarrays/substrings**
- Brute force would enumerate O(n²) sequences
- Can maintain window state incrementally (sum, count, max, distinct chars, etc.)
- Monotonic property: extending/shrinking window has predictable effect on constraint

**Window Types:**
- **Fixed-size**: Window size k is given → Simple slide with add/remove
- **Variable-size**: Condition-based (sum ≥ target, distinct ≤ k) → Grow/shrink dynamically

**Optimization Patterns:**
- **Pure Sliding Window**: Track simple property (sum, count) with O(1) update
- **+ Hash Map**: Track frequencies or distinct elements
- **+ Monotonic Deque**: Track min/max with dominated element removal
- **+ Two Pointers**: When shrinking is deterministic based on condition

---

## Related Patterns:
- [Two Pointers](two_pointers_pattern.md) - Similar idea but windows may not be contiguous
- [Prefix Sum](prefix_sum_pattern.md) - Alternative for sum-based queries without updates
- [Monotonic Stack](monotonic_stack_pattern.md) - Related to monotonic deque in variant #6

## Practice Progression:
1. Start with **Variant #1** (fixed window, easiest)
2. Progress to **Variants #2, #3, #4** (variable window, core pattern)
3. Master **Variants #6, #7, #8** (hybrid patterns, interview favorites)
4. Study **Variant #5** (edge case showing when sliding window fails)

---

Each variant linked above contains comprehensive 4-step analysis:
1. **State Space Derivation** - Full enumeration with cardinality calculation
2. **Brute Force Solution** - Canonical implementation with value tracing
3. **Pruning Analysis** - Optimization opportunities and decision making
4. **Optimal Solution** - Skeleton transformation with complexity analysis

---

## When to Use This Pattern:

✅ **Use Sliding Window when:**
- Problem involves contiguous subarrays/substrings
- Need to find optimal window satisfying some condition
- Can maintain window state incrementally (sum, count, frequencies)
- Brute force would be O(n²) or O(n³)

❌ **Don't use when:**
- Need non-contiguous subsequences (use Dynamic Programming)
- Looking for all pairs/combinations (use Two Pointers or Backtracking)
- Window state cannot be maintained incrementally

## Common Mistakes:
1. **Forgetting window validity check** - Always validate window before updating result
2. **Wrong shrinking condition** - Understand when to expand vs. shrink
3. **Off-by-one errors** - Window size is `right - left + 1`, not `right - left`
4. **Not handling edge cases** - Empty array, k=0, k>n, etc.
