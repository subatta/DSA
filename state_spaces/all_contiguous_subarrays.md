# State Space: All Contiguous Subarrays

**Type:** Contiguous sequences of elements  
**Structure:** Subarrays [start, end] where `0 ≤ start ≤ end < n`  
**Cardinality:** n(n+1)/2 = **O(n²) subarrays**

---

## Mathematical Foundation

### Triangular Number Formula
Number of contiguous subarrays in an array of length n:

$$\sum_{i=1}^{n} i = \frac{n(n+1)}{2}$$

**Why this formula?**
- From position 0: n subarrays (ending at 0, 1, 2, ..., n-1)
- From position 1: n-1 subarrays (ending at 1, 2, ..., n-1)
- From position 2: n-2 subarrays
- ...
- From position n-1: 1 subarray

Total: n + (n-1) + (n-2) + ... + 1 = **n(n+1)/2**

### Critical Difference from "All Pairs"

| Aspect | All Pairs | All Subarrays |
|--------|-----------|---------------|
| **Formula** | n(n-1)/2 | n(n+1)/2 |
| **Single element valid?** | ❌ No | ✅ Yes |
| **Loop condition** | `end = start + 1` | `end = start` |
| **Meaning** | Two **different** elements | **Any** contiguous sequence |
| **Example (n=3)** | 3 pairs | 6 subarrays |

---

## Visualization

### Small Example (n=4)
For array `[a, b, c, d]`:

```
Subarrays organized by starting position:
     Start at 0       Start at 1     Start at 2   Start at 3
   ┌─────────────┐  ┌───────────┐  ┌─────────┐  ┌─────┐
   │ [a]         │  │ [b]       │  │ [c]     │  │ [d] │
   │ [a,b]       │  │ [b,c]     │  │ [c,d]   │  └─────┘
   │ [a,b,c]     │  │ [b,c,d]   │  └─────────┘
   │ [a,b,c,d]   │  └───────────┘
   └─────────────┘

Subarrays by length:
  Length 1: [a], [b], [c], [d]                → 4 subarrays
  Length 2: [a,b], [b,c], [c,d]               → 3 subarrays
  Length 3: [a,b,c], [b,c,d]                  → 2 subarrays
  Length 4: [a,b,c,d]                         → 1 subarray
  
Total: 4 + 3 + 2 + 1 = 10 = 4×5/2
```

### Grid Representation
```
Subarrays as (start, end) pairs (inclusive):
     0   1   2   3
   +---+---+---+---+
0  | ✓ | ✓ | ✓ | ✓ |   [0,0] [0,1] [0,2] [0,3]  (4 subarrays)
   +---+---+---+---+
1  |   | ✓ | ✓ | ✓ |   [1,1] [1,2] [1,3]        (3 subarrays)
   +---+---+---+---+
2  |   |   | ✓ | ✓ |   [2,2] [2,3]              (2 subarrays)
   +---+---+---+---+
3  |   |   |   | ✓ |   [3,3]                    (1 subarray)
   +---+---+---+---+

Note: Includes diagonal (start = end) for single-element subarrays
Total: 4 + 3 + 2 + 1 = 10
```

---

## Generation Pattern

### Nested Loop Structure
```csharp
void GenerateAllSubarrays<T>(T[] elements)
{
    var subarrays = new List<(int start, int end, T[] subarray)>();
    
    for (int start = 0; start < elements.Length; start++)
    {
        for (int end = start; end < elements.Length; end++)
        {
            // Extract subarray from start to end (inclusive)
            var subarray = elements[start..(end + 1)];
            subarrays.Add((start, end, subarray));
        }
    }
    
    // subarrays.Count = elements.Length * (elements.Length + 1) / 2
}
```

**Key insight:** `end = start` (not `start + 1`) ensures we include single-element subarrays.

---

## Complexity Analysis

### Time Complexity: O(n²)
- Outer loop: n iterations (each starting position)
- Inner loop: n, n-1, n-2, ..., 1 iterations
- Total: 1 + 2 + 3 + ... + n = n(n+1)/2 ≈ n²/2

### Space Complexity
- **If generating explicitly:** O(n³) to store all subarrays with their contents
  - n²/2 subarrays × average length n/2
- **If just storing indices:** O(n²) for start/end pairs
- **If processing on-the-fly:** O(1) with nested loops

---

## Growth Rate Table

| n | Subarrays | Formula Check | vs Pairs |
|---|-----------|---------------|----------|
| 1 | 1 | 1×2/2 = 1 | same |
| 2 | 3 | 2×3/2 = 3 | +2 (vs 1 pair) |
| 3 | 6 | 3×4/2 = 6 | +3 (vs 3 pairs) |
| 4 | 10 | 4×5/2 = 10 | +4 (vs 6 pairs) |
| 5 | 15 | 5×6/2 = 15 | +5 (vs 10 pairs) |
| 10 | 55 | 10×11/2 = 55 | +10 (vs 45 pairs) |
| 100 | 5,050 | 100×101/2 = 5,050 | +100 (vs 4,950 pairs) |

**Observation:** Always n more subarrays than pairs (for single-element subarrays).

---

## Common Problems Using This State Space

### Sliding Window Optimization
Problems that start with O(n²) subarray enumeration but optimize to O(n):
- **[Longest Substring Without Repeating](../variants/sliding_window/variant_1_longest_substring_no_repeat.md)** - LeetCode #3
- **[Minimum Window Substring](../variants/sliding_window/variant_3_min_window_substring.md)** - LeetCode #76
- **[Subarray Product Less Than K](../variants/sliding_window/variant_4_subarray_product_less_k.md)** - LeetCode #713
- **[Minimum Size Subarray Sum](../variants/sliding_window/variant_5_min_size_subarray_sum.md)** - LeetCode #209
- **[K Distinct Characters](../variants/sliding_window/variant_7_k_distinct_characters.md)** - LeetCode #340

### Prefix Sum Optimization
- **[Subarray Sum Equals K](../variants/hash_map/variant_3_subarray_sum_equals_k.md)** - LeetCode #560
- **[Continuous Subarray Sum](../variants/prefix_sum/variant_4_continuous_subarray_sum.md)** - LeetCode #523

### Fixed-Size Window
- **[Maximum Sum Subarray of Size K](../variants/sliding_window/variant_2_max_sum_size_k.md)** - Fixed window
- **[Sliding Window Maximum](../variants/sliding_window/variant_6_sliding_window_maximum.md)** - LeetCode #239

---

## Optimization Strategies

Different problems use different techniques to avoid checking all O(n²) subarrays:

### 1. Sliding Window (O(n))
**When:** Monotonic property allows incremental window adjustment
- **Technique:** Maintain window state, expand/contract based on condition
- **Key:** Can determine validity incrementally without recalculating from scratch
- **Example:** Longest substring without repeating characters

### 2. Prefix Sum + Hash Map (O(n))
**When:** Need subarray sums or cumulative properties
- **Technique:** Compute prefix sums, use hash map to find complement
- **Transform:** "Subarray [i, j] has sum k" → "prefix[j] - prefix[i-1] = k"
- **Example:** Subarray sum equals K

### 3. Fixed Window Sliding (O(n))
**When:** All subarrays must be exactly size k
- **Reduces:** From n(n+1)/2 to just (n-k+1) windows
- **Technique:** Slide fixed-size window, maintain window state
- **Example:** Maximum sum of subarray of size K

### 4. Monotonic Stack/Deque (O(n))
**When:** Need to track min/max in sliding window
- **Technique:** Maintain monotonic order, remove dominated elements
- **Example:** Sliding window maximum

### 5. Two Pointers (O(n))
**When:** Can shrink window when condition violated
- **Technique:** Expand right, contract left based on condition
- **Example:** Minimum window substring

---

## Related State Spaces

- **All Pairs:** n(n-1)/2 = O(n²) - excludes single elements
- **All Subsequences (non-contiguous):** 2^n = O(2^n) - exponential
- **All Subarrays of Fixed Length k:** n-k+1 = O(n) - linear
- **All Substrings:** Same as subarrays but for strings

---

## When to Use This Model

✅ **Use when:**
- Problem mentions "subarray", "substring", "contiguous sequence"
- Need to consider all possible contiguous segments
- Starting from brute force before optimization
- Window-based problems with variable size

❌ **Don't use when:**
- Problem requires non-contiguous elements (use subsequences)
- Only need fixed-size windows (use specialized formula)
- Problem has two distinct elements (use all pairs)
- Can solve directly without enumeration

---

## Key Insights

1. **Single-element subarrays are valid** - This is the key difference from "all pairs"
2. **Diagonal included** - Grid representation includes start = end
3. **Always n more than pairs** - Due to n single-element subarrays
4. **Most problems optimize away** - O(n²) is rarely the final complexity
5. **Sliding window is the primary optimization** - Maintains incremental state
