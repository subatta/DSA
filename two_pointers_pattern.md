# Pattern Title: Two Pointers

## Pattern Description:
- What is it?  
  Two Pointers is a technique where two indices traverse a data structure (usually an array or string) in a coordinated way, often moving toward each other, in the same direction, or at different speeds to solve problems efficiently.

- What abstract problem does it solve?  
  It reduces a large combinatorial or pairwise state space (often O(n²) or O(n³)) by exploiting ordering, symmetry, or monotonicity, collapsing it into a linear or near-linear traversal.

- Real-world problem variants (simplest first):  
  1. [Two Sum II (sorted array)](variants/two_pointers/variant_1_two_sum_sorted.md)
  2. [Valid Palindrome](variants/two_pointers/variant_2_valid_palindrome.md)
  3. [Remove Duplicates from Sorted Array](variants/two_pointers/variant_3_remove_duplicates.md)
  4. [Container With Most Water](variants/two_pointers/variant_4_container_with_most_water.md)
  5. [3Sum](variants/two_pointers/variant_5_3sum.md)
  6. [Trapping Rain Water](variants/two_pointers/variant_6_trapping_rain_water.md)
  7. [Partition / In-place rearrangement (Move Zeros)](variants/two_pointers/variant_7_move_zeros.md)
  8. [Sort Colors (Dutch National Flag)](variants/two_pointers/variant_8_sort_colors.md)

## Canonical Code Skeleton:

```csharp
int TwoPointersTemplate(int[] nums) {
    int left = 0, right = nums.Length - 1;
    int result = 0; // or initialize depending on problem
    
    while (left < right) {
        // Process current pair (nums[left], nums[right])
        
        if (ConditionMet(nums[left], nums[right])) {
            // Record/update result
            left++;
            right--;
        }
        else if (NeedSmaller()) {
            right--;
        }
        else {
            left++;
        }
    }
    
    return result;
}
```

## Pattern Variants (8 Total)

### 🟢 Easy (4 variants)
**Master basic pointer movement patterns**

1. **[Two Sum II (Sorted Array)](variants/two_pointers/variant_1_two_sum_ii.md)** - LeetCode #167 ⭐
   - **Concept:** Opposite-direction pointers, move based on sum comparison
   - **Complexity:** O(n) time, O(1) space
   - **Why Easy:** Sorted array, clear decision rule for pointer movement

2. **[Valid Palindrome](variants/two_pointers/variant_2_valid_palindrome.md)** - LeetCode #125
   - **Concept:** Compare characters from both ends moving inward
   - **Complexity:** O(n) time, O(1) space
   - **Why Easy:** Simple comparison, straightforward pointer movement

3. **[Remove Duplicates from Sorted Array](variants/two_pointers/variant_7_remove_duplicates.md)** - LeetCode #26
   - **Concept:** Fast & slow pointers, slow tracks unique position
   - **Complexity:** O(n) time, O(1) space
   - **Why Easy:** Single-pass in-place modification

4. **[Move Zeros](variants/two_pointers/variant_8_move_zeros.md)** - LeetCode #283
   - **Concept:** Slow pointer tracks non-zero position, swap when found
   - **Complexity:** O(n) time, O(1) space
   - **Why Easy:** Partition variant, simple swap logic

### 🟡 Medium (3 variants)
**Handle multiple pointers and complex logic**

5. **[Container With Most Water](variants/two_pointers/variant_3_container_with_most_water.md)** - LeetCode #11
   - **Concept:** Move pointer at shorter height (greedy elimination)
   - **Complexity:** O(n) time, O(1) space
   - **Why Medium:** Non-obvious greedy decision, area calculation

6. **[3Sum](variants/two_pointers/variant_4_three_sum.md)** - LeetCode #15 ⭐⭐
   - **Concept:** Fix one element, two pointers for remaining two
   - **Complexity:** O(n²) time, O(1) space (excluding output)
   - **Why Medium:** Nested loop + two pointers, duplicate handling

7. **[Sort Colors (Dutch National Flag)](variants/two_pointers/variant_5_sort_colors.md)** - LeetCode #75
   - **Concept:** Three-way partition with low/mid/high pointers
   - **Complexity:** O(n) time, O(1) space
   - **Why Medium:** Three pointers, careful case handling

### 🔴 Hard (1 variant)
**Master advanced pointer techniques**

8. **[Trapping Rain Water](variants/two_pointers/variant_6_trapping_rain_water.md)** - LeetCode #42 ⭐⭐
   - **Concept:** Track left_max and right_max, move based on comparison
   - **Complexity:** O(n) time, O(1) space
   - **Why Hard:** Non-intuitive optimization, requires insight about water levels

### Practice Progression
1. Start with **Valid Palindrome** (#2) - simplest pattern
2. Master **Two Sum II** (#1) - core opposite-direction technique
3. Practice **Remove Duplicates** (#3) and **Move Zeros** (#4) - fast & slow
4. Advance to **Container** (#5) - greedy elimination
5. Tackle **3Sum** (#6) - most common interview problem
6. Learn **Sort Colors** (#7) - three-way partition
7. Complete **Trapping Rain Water** (#8) - hardest optimization

---

**Note:** The embedded variant content has been moved to individual files for better organization. Each file contains:
1. State Space Derivation (cardinality, structure, generation)
2. Brute Force with Value Tracing
3. Pruning Analysis (can we do better?)
4. Optimal Solution with Skeleton Transformation
