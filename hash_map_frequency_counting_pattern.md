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

## Pattern Variants (5 Total)

### 🟢 Easy (2 variants)
**Master O(1) lookup and basic frequency tracking**

1. **[Two Sum](variants/hash_map/variant_1_two_sum.md)** - LeetCode #1 ⭐⭐
   - **Concept:** Store complement, check if current value exists
   - **Complexity:** O(n) time, O(n) space
   - **Why Easy:** Single-pass, simple lookup, most famous interview problem

2. **[Contains Duplicate II](variants/hash_map/variant_5_contains_duplicate_ii.md)** - LeetCode #219
   - **Concept:** Track most recent index of each value
   - **Complexity:** O(n) time, O(n) space
   - **Why Easy:** Simple index tracking, distance check

### 🟡 Medium (3 variants)
**Learn grouping, frequency patterns, and advanced techniques**

3. **[Group Anagrams](variants/hash_map/variant_2_group_anagrams.md)** - LeetCode #49 ⭐
   - **Concept:** Use sorted string or frequency array as key
   - **Complexity:** O(n × k log k) time, O(n × k) space (k = string length)
   - **Why Medium:** Choosing right key representation

4. **[Subarray Sum Equals K](variants/hash_map/variant_3_subarray_sum_k.md)** - LeetCode #560 ⭐⭐
   - **Concept:** Prefix sum + hash map for O(n) counting
   - **Complexity:** O(n) time, O(n) space
   - **Why Medium:** Non-obvious prefix sum insight, frequency counting

5. **[Longest Consecutive Sequence](variants/hash_map/variant_4_longest_consecutive.md)** - LeetCode #128
   - **Concept:** Hash set for O(1) existence, only start from sequence beginnings
   - **Complexity:** O(n) time, O(n) space
   - **Why Medium:** Optimization to avoid redundant checks

### Practice Progression
1. Start with **Two Sum** (#1) - most fundamental hash map problem
2. Practice **Contains Duplicate II** (#2) - index tracking
3. Learn **Group Anagrams** (#3) - key design pattern
4. Master **Subarray Sum** (#4) - prefix sum technique (hardest)
5. Complete **Longest Consecutive** (#5) - sequence building

---

**Note:** The embedded variant content has been moved to individual files for better organization. Each file contains:
1. State Space Derivation (cardinality, structure, generation)
2. Brute Force with Value Tracing
3. Pruning Analysis (can we do better?)
4. Optimal Solution with Skeleton Transformation
