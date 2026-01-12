# Pattern Title: Prefix Sum / Running Sum

## Pattern Description:
- What is it?  
  Prefix Sum (also called Running Sum or Cumulative Sum) precomputes cumulative aggregates so that range queries or subarray calculations can be answered in constant time.

- What abstract problem does it solve?  
  It collapses repeated summation over overlapping ranges into a single pass, transforming O(n²) subarray enumeration problems into O(n) or O(n log n).

- Real-world problem variants (simplest first):  
  1. [Range Sum Query - Immutable](variants/prefix_sum/variant_1_range_sum_query.md)
  2. [Product of Array Except Self](variants/prefix_sum/variant_2_product_except_self.md)
  3. [Find Pivot Index](variants/prefix_sum/variant_3_find_pivot_index.md)
  4. [Continuous Subarray Sum](variants/prefix_sum/variant_4_continuous_subarray_sum.md)

## Canonical Code Skeleton:

```csharp
int SubarraySumTemplate(int[] nums, int k) {
    int prefixSum = 0, count = 0;
    var map = new Dictionary<int, int>();
    map[0] = 1; // base case for subarrays starting at index 0

    foreach (var num in nums)
    {
        prefixSum += num;

        int need = prefixSum - k;
        if (map.ContainsKey(need))
            count += map[need];

        map[prefixSum] = map.GetValueOrDefault(prefixSum, 0) + 1;
    }
    
    return count;
}
```

## Pattern Variants (4 Total)

### 🟢 Easy (2 variants)
**Master basic prefix sum mechanics**

1. **[Range Sum Query - Immutable](variants/prefix_sum/variant_1_range_sum_query.md)** - LeetCode #303
   - **Concept:** Precompute cumulative sums for O(1) range queries
   - **Complexity:** O(n) preprocessing, O(1) per query, O(n) space
   - **Why Easy:** Direct application of prefix sum array

2. **[Find Pivot Index](variants/prefix_sum/variant_3_find_pivot_index.md)** - LeetCode #724
   - **Concept:** Left sum equals right sum using prefix sum
   - **Complexity:** O(n) time, O(1) space
   - **Why Easy:** Single pass with running sum comparison

### 🟡 Medium (2 variants)
**Apply prefix sum with hash maps and modulo arithmetic**

3. **[Product of Array Except Self](variants/prefix_sum/variant_2_product_except_self.md)** - LeetCode #238 ⭐
   - **Concept:** Prefix products from left and right (no division)
   - **Complexity:** O(n) time, O(1) space (excluding output)
   - **Why Medium:** Two-pass approach, space optimization trick

4. **[Continuous Subarray Sum](variants/prefix_sum/variant_4_continuous_subarray_sum.md)** - LeetCode #523 ⭐
   - **Concept:** Prefix sum modulo k with hash map
   - **Complexity:** O(n) time, O(min(n,k)) space
   - **Why Medium:** Modulo arithmetic, edge case handling (k=0, multiple of k)

### Practice Progression
1. Start with **Range Sum Query** (#1) - understand basic prefix sum
2. Practice **Find Pivot Index** (#2) - apply prefix sum for balance point
3. Tackle **Product Except Self** (#3) - prefix products without division
4. Master **Continuous Subarray Sum** (#4) - prefix sum with modulo and hash map

---

## Key Pattern Takeaways

### Core Concepts
- **Prefix sum converts range problems into difference lookups**: `sum(l, r) = prefix[r+1] - prefix[l]`
- **Hash map tracks frequency**: Count how many times a prefix sum (or its remainder) has occurred
- **Initialize with base case**: Always set `map[0] = 1` to handle subarrays starting at index 0

### When to Use This Pattern
✅ **Use when you see:**
- "Subarray sum equals K"
- "Range sum queries"
- "Count subarrays with property"
- "Product except self"
- "Continuous subarray sum"

❌ **Don't use when:**
- Need to find maximum/minimum subarray (use Kadane's or Sliding Window)
- Array can be modified (consider Segment Tree or Binary Indexed Tree)
- Need to handle updates efficiently (static prefix sum won't work)

### Common Variations

**1. Simple Prefix Sum (Array)**
```csharp
int[] prefix = new int[n + 1];
for (int i = 0; i < n; i++) {
    prefix[i + 1] = prefix[i] + nums[i];
}
// Range sum: prefix[r+1] - prefix[l]
```

**2. Prefix Sum with Hash Map (Count Subarrays)**
```csharp
var map = new Dictionary<int, int> { {0, 1} };
int prefixSum = 0, count = 0;

foreach (var num in nums) {
    prefixSum += num;
    if (map.ContainsKey(prefixSum - k)) {
        count += map[prefixSum - k];
    }
    map[prefixSum] = map.GetValueOrDefault(prefixSum, 0) + 1;
}
```

**3. Prefix Product**
```csharp
int[] prefix = new int[n];
prefix[0] = nums[0];
for (int i = 1; i < n; i++) {
    prefix[i] = prefix[i - 1] * nums[i];
}
```

**4. Prefix Sum with Modulo**
```csharp
var map = new Dictionary<int, int> { {0, -1} };
int prefixSum = 0;

for (int i = 0; i < n; i++) {
    prefixSum += nums[i];
    int remainder = prefixSum % k;
    if (remainder < 0) remainder += k; // Handle negatives
    
    if (map.ContainsKey(remainder)) {
        // Found subarray
    }
    map[remainder] = i;
}
```

---

**Note:** The embedded variant content has been moved to individual files for better organization. Each file contains:
1. State Space Derivation (cardinality, structure, generation)
2. Brute Force with Value Tracing
3. Pruning Analysis (can we do better?)
4. Optimal Solution with Skeleton Transformation

