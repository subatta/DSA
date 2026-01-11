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

## Variant Deep Dives

Each variant below demonstrates the Two Pointers pattern with a 4-step analysis framework:

### Opposite Ends Sub-pattern
- [Variant #1: Two Sum II (Sorted Array)](variants/two_pointers/variant_1_two_sum_sorted.md) - LeetCode #167, Easy
- [Variant #2: Valid Palindrome](variants/two_pointers/variant_2_valid_palindrome.md) - LeetCode #125, Easy
- [Variant #4: Container With Most Water](variants/two_pointers/variant_4_container_with_most_water.md) - LeetCode #11, Medium
- [Variant #5: 3Sum](variants/two_pointers/variant_5_3sum.md) - LeetCode #15, Medium
- [Variant #6: Trapping Rain Water](variants/two_pointers/variant_6_trapping_rain_water.md) - LeetCode #42, Hard

### Fast & Slow Sub-pattern
- [Variant #3: Remove Duplicates from Sorted Array](variants/two_pointers/variant_3_remove_duplicates.md) - LeetCode #26, Easy
- [Variant #7: Move Zeros](variants/two_pointers/variant_7_move_zeros.md) - LeetCode #283, Easy

### Three-way Partition Sub-pattern
- [Variant #8: Sort Colors (Dutch National Flag)](variants/two_pointers/variant_8_sort_colors.md) - LeetCode #75, Medium

---

**Note:** The embedded variant content has been moved to individual files for better organization. Each file contains:
1. State Space Derivation (cardinality, structure, generation)
2. Brute Force with Value Tracing
3. Pruning Analysis (can we do better?)
4. Optimal Solution with Skeleton Transformation
