# Pattern Title: Hash Map / Frequency Counting

## Pattern Description:
- What is it?  
  Hash Map / Frequency Counting is a technique that uses a hash map (dictionary) to count, track, or aggregate occurrences of elements while iterating through a collection, enabling O(1) lookups instead of repeated scans.

- What abstract problem does it solve?  
  It converts repeated scanning or pairwise comparison problems (often O(n²) or O(n!)) into single-pass O(n) solutions by trading space for time, maintaining element frequencies or mappings for constant-time access.

- Real-world problem variants (simplest first):  
  1. [Two Sum](variants/hash_map/variant_1_two_sum.md) - LeetCode #1, Easy
  2. [Contains Duplicate II](variants/hash_map/variant_5_contains_duplicate_ii.md) - LeetCode #219, Easy
  3. [Group Anagrams](variants/hash_map/variant_2_group_anagrams.md) - LeetCode #49, Medium
  4. [Longest Consecutive Sequence](variants/hash_map/variant_4_longest_consecutive_sequence.md) - LeetCode #128, Medium
  5. [Subarray Sum Equals K](variants/hash_map/variant_3_subarray_sum_equals_k.md) - LeetCode #560, Medium

## Canonical Code Skeleton:

```csharp
Dictionary<T, int> FrequencyCountTemplate(IEnumerable<T> input) {
    var map = new Dictionary<T, int>();
    
    // Build frequency map
    foreach (var item in input)
    {
        map[item] = map.GetValueOrDefault(item) + 1;
    }
    
    // Use map for O(1) lookups
    // Second pass or logic using map
    
    return map;
}
```

## Variant Deep Dives

Each variant demonstrates the Hash Map pattern with a 4-step analysis framework:

### Basic Lookup Variants
- [Variant #1: Two Sum](variants/hash_map/variant_1_two_sum.md) - O(n²) → O(n) via complement lookup
- [Variant #5: Contains Duplicate II](variants/hash_map/variant_5_contains_duplicate_ii.md) - Track recent indices within distance k

### Grouping / Frequency Variants
- [Variant #2: Group Anagrams](variants/hash_map/variant_2_group_anagrams.md) - Group by character frequency signature
- [Variant #4: Longest Consecutive Sequence](variants/hash_map/variant_4_longest_consecutive_sequence.md) - Hash set for O(1) existence checks

### Prefix Sum + Hash Map Variants
- [Variant #3: Subarray Sum Equals K](variants/hash_map/variant_3_subarray_sum_equals_k.md) - Count subarrays via prefix sum frequency

---

**Note:** The embedded variant content has been moved to individual files for better organization. Each file contains:
1. State Space Derivation (cardinality, structure, generation)
2. Brute Force with Value Tracing
3. Pruning Analysis (can we do better?)
4. Optimal Solution with Skeleton Transformation
