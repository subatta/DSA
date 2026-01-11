# State Space: All Pairs of Elements

**Type:** Combinatorial - selecting 2 elements from n elements  
**Structure:** Pairs (i, j) where `0 ≤ i < j < n`  
**Cardinality:** n(n-1)/2 = **O(n²) pairs**

---

## Mathematical Foundation

### Combination Formula
Selecting 2 items from n items where **order doesn't matter**:

$$C(n, 2) = \binom{n}{2} = \frac{n!}{2!(n-2)!} = \frac{n(n-1)}{2}$$

### Why This Formula?
- First element: n choices
- Second element: n-1 remaining choices
- Total ordered pairs: n × (n-1)
- Divide by 2 because (i, j) and (j, i) represent the same unordered pair
- Result: **n(n-1)/2 unique pairs**

---

## Visualization

### Small Example (n=4)
For array of 4 elements `[a, b, c, d]`:

```
All pairs (visualized as grid, excluding diagonal and upper triangle):
     0   1   2   3
   +---+---+---+---+
0  | X | ✓ | ✓ | ✓ |   (0,1) (0,2) (0,3)
   +---+---+---+---+
1  |   | X | ✓ | ✓ |   (1,2) (1,3)
   +---+---+---+---+
2  |   |   | X | ✓ |   (2,3)
   +---+---+---+---+
3  |   |   |   | X |
   +---+---+---+---+

Total pairs: 3 + 2 + 1 = 6 = 4×3/2
```

### Enumeration
```
(0,1): elements[0], elements[1]
(0,2): elements[0], elements[2]
(0,3): elements[0], elements[3]
(1,2): elements[1], elements[2]
(1,3): elements[1], elements[3]
(2,3): elements[2], elements[3]
```

---

## Generation Pattern

### Nested Loop Structure
```csharp
void GenerateAllPairs<T>(T[] elements)
{
    var pairs = new List<(int i, int j, T first, T second)>();
    
    for (int i = 0; i < elements.Length; i++)
    {
        for (int j = i + 1; j < elements.Length; j++)
        {
            pairs.Add((i, j, elements[i], elements[j]));
        }
    }
    
    // pairs.Count = elements.Length * (elements.Length - 1) / 2
}
```

**Key insight:** `j = i + 1` ensures we only generate pairs where `i < j`, avoiding duplicates and self-pairs.

---

## Complexity Analysis

### Time Complexity: O(n²)
- Outer loop: n iterations
- Inner loop: (n-1) + (n-2) + ... + 1 = n(n-1)/2 iterations total
- Growth: Quadratic

### Space Complexity
- **If generating explicitly:** O(n²) to store all pairs
- **If processing on-the-fly:** O(1) if we just check each pair without storing

---

## Growth Rate Table

| n | Pairs | Formula Check |
|---|-------|---------------|
| 2 | 1 | 2×1/2 = 1 |
| 3 | 3 | 3×2/2 = 3 |
| 4 | 6 | 4×3/2 = 6 |
| 5 | 10 | 5×4/2 = 10 |
| 10 | 45 | 10×9/2 = 45 |
| 100 | 4,950 | 100×99/2 = 4,950 |
| 1,000 | 499,500 | 1000×999/2 = 499,500 |

**Observation:** Grows quadratically - doubling n roughly quadruples the pairs.

---

## Common Problems Using This State Space

### Direct Enumeration (Brute Force)
Problems that check all pairs before optimization:
- **Two Sum** (unsorted) → O(n²) brute force, O(n) with hash map
- **Two Sum II** (sorted) → O(n²) brute force, O(n) with two pointers
- **Container With Most Water** → O(n²) brute force, O(n) with two pointers
- **3Sum** → O(n³) brute force (n choices × n² pairs), O(n²) optimized
- **Contains Duplicate II** → O(n²) brute force, O(n) with hash map

### Pair Comparison
- Finding closest pair
- Finding pair with maximum/minimum sum
- Counting pairs satisfying a condition

---

## Optimization Strategies

Different problems use different techniques to avoid checking all O(n²) pairs:

### 1. Hash Map (O(n))
**When:** Need to find complementary element
- Transform: "Does pair (a, b) exist?" → "Does complement exist?"
- **Example:** Two Sum

### 2. Two Pointers (O(n))
**When:** Array is sorted or has monotonic property
- Use sorted order to eliminate entire ranges of pairs
- **Example:** Two Sum II, Container With Most Water

### 3. Sorting + Early Termination (O(n log n))
**When:** Sorting enables pruning
- Sort first, then use sorted property to skip impossible pairs
- **Example:** 3Sum

### 4. Advanced Data Structures
**When:** Need specific pair properties
- Binary search, heaps, etc.
- **Example:** Finding k closest pairs

---

## Related State Spaces

- **All Triplets:** n(n-1)(n-2)/6 = O(n³)
- **All k-tuples:** C(n, k) = O(n^k)
- **All Subsets:** 2^n = O(2^n)
- **All Permutation Pairs:** n(n-1) = O(n²) (ordered pairs)

---

## When to Use This Model

✅ **Use when:**
- Need to consider relationships between **two distinct elements**
- Order of selection doesn't matter (i,j) same as (j,i)
- Cannot use same element twice
- Starting from brute force solution before optimization

❌ **Don't use when:**
- Elements can be reused (use n² ordered pairs)
- Need more than 2 elements (use different combination formula)
- Order matters (use permutations instead)
- Problem has structure that avoids pair enumeration entirely
