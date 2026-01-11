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

## Pattern Variants (8 Total)

### 🟢 Easy (1 variant)
**Master the fundamentals of fixed-size windows**

1. **[Maximum Sum Subarray of Size K](variants/sliding_window/variant_8_max_sum_subarray_size_k.md)** - Fixed window
   - **Concept:** Slide fixed-size window, maintain sum incrementally
   - **Complexity:** O(n) time, O(1) space
   - **Why Easy:** Straightforward add/remove, no shrinking logic

### 🟡 Medium (4 variants)
**Learn variable-size windows and hybrid techniques**

2. **[Longest Substring Without Repeating Characters](variants/sliding_window/variant_1_longest_substring_no_repeat.md)** - LeetCode #3 ⭐
   - **Concept:** Expand-while-valid, shrink when duplicate found
   - **Complexity:** O(n) time, O(k) space (k = distinct chars)
   - **Why Medium:** Variable window + hash set for tracking

3. **[Subarray Product Less Than K](variants/sliding_window/variant_2_subarray_product_less_k.md)** - LeetCode #713
   - **Concept:** Shrink when product ≥ k, count with formula
   - **Complexity:** O(n) time, O(1) space
   - **Why Medium:** Counting formula insight: `right - left + 1`

4. **[Minimum Size Subarray Sum](variants/sliding_window/variant_5_min_size_subarray_sum.md)** - LeetCode #209
   - **Concept:** Grow until sum ≥ target, then shrink to minimize
   - **Complexity:** O(n) time, O(1) space
   - **Why Medium:** Shrink-while-valid minimization strategy

5. **[Longest Substring with K Distinct Characters](variants/sliding_window/variant_7_k_distinct_characters.md)** - LeetCode #340
   - **Concept:** Hash map frequency tracking with distinct count
   - **Complexity:** O(n) time, O(k) space
   - **Why Medium:** Hybrid - sliding window + hash map

### 🔴 Hard (3 variants)
**Master complex hybrid techniques and edge cases**

6. **[Minimum Window Substring](variants/sliding_window/variant_3_min_window_substring.md)** - LeetCode #76 ⭐⭐
   - **Concept:** Expand until valid, shrink while maintaining validity
   - **Complexity:** O(n + m) time, O(m) space
   - **Why Hard:** Contract-while-valid strategy, complex frequency matching

7. **[Sliding Window Maximum](variants/sliding_window/variant_6_sliding_window_maximum.md)** - LeetCode #239 ⭐⭐
   - **Concept:** Monotonic deque removes dominated elements
   - **Complexity:** O(n) time, O(k) space
   - **Why Hard:** Non-intuitive data structure choice (deque)

8. **[Find All Anagrams in String](variants/sliding_window/variant_4_find_all_anagrams.md)** - LeetCode #438
   - **Concept:** Fixed-size window with frequency matching
   - **Complexity:** O(n) time, O(1) space (26 letters)
   - **Why Hard:** Efficient frequency comparison technique

### Practice Progression
1. Start with **Maximum Sum** (#1) - learn fixed windows
2. Master **Longest Substring** (#2) - variable window basics
3. Practice **Subarray Product** (#3) and **Min Size** (#4) - shrinking strategies
4. Learn hybrid with **K Distinct** (#5)
5. Tackle **Minimum Window** (#6) - hardest, most common in interviews
6. Complete **Sliding Window Maximum** (#7) - advanced data structure

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
