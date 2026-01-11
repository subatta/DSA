# State Space: All Permutations

**Type:** All possible orderings of n elements  
**Structure:** Sequences where order matters and all elements appear exactly once  
**Cardinality:** n! = **O(n!) permutations**

---

## Mathematical Foundation

### Factorial Formula
Number of permutations of n distinct elements:

$$n! = n \times (n-1) \times (n-2) \times ... \times 2 \times 1$$

**Why this formula?**
- First position: **n** choices
- Second position: **(n-1)** choices (one element already used)
- Third position: **(n-2)** choices
- ...
- Last position: **1** choice

Total: $n \times (n-1) \times ... \times 1 = n!$

---

## Visualization

### Small Example (n=3)
For array `[1, 2, 3]`:

```
Decision Tree (Position-by-Position):
                        []
         /              |              \
       [1]             [2]             [3]
      /   \           /   \           /   \
   [1,2] [1,3]     [2,1] [2,3]     [3,1] [3,2]
     |     |         |     |         |     |
  [1,2,3][1,3,2] [2,1,3][2,3,1] [3,1,2][3,2,1]

All 6 permutations (3! = 6):
  [1,2,3]
  [1,3,2]
  [2,1,3]
  [2,3,1]
  [3,1,2]
  [3,2,1]
```

**Key insight:** At each position, we have fewer choices:
- Position 0: 3 choices → 3 branches
- Position 1: 2 choices → 2 branches each
- Position 2: 1 choice → 1 branch each

Total leaves: 3 × 2 × 1 = 6

---

## Permutations vs. Subsets vs. Combinations

**Critical distinctions:**

| Aspect | Permutations | Subsets | Combinations |
|--------|--------------|---------|--------------|
| **Cardinality** | n! | 2^n | C(n,k) |
| **Order matters?** | ✅ Yes | ❌ No | ❌ No |
| **Size constraint?** | All n elements | Any size (0 to n) | Fixed size k |
| **Example (n=3)** | 6 orderings | 8 subsets | 3 pairs (if k=2) |
| **[1,2] vs [2,1]** | Different | Same | Same |
| **Generation** | Backtracking with used[] | Include/exclude | Combinations loop |

**Examples with `[1, 2, 3]`:**

```
Permutations (n! = 6):
  [1,2,3], [1,3,2], [2,1,3], [2,3,1], [3,1,2], [3,2,1]
  Order matters: [1,2,3] ≠ [3,2,1]

Subsets (2^n = 8):
  {}, {1}, {2}, {3}, {1,2}, {1,3}, {2,3}, {1,2,3}
  Order doesn't matter: {1,2} = {2,1}

Combinations of size 2 (C(3,2) = 3):
  {1,2}, {1,3}, {2,3}
  Order doesn't matter, fixed size k=2
```

---

## Generation Patterns

### Method 1: Backtracking with Used Array
```csharp
void GeneratePermutationsBacktracking<T>(T[] elements)
{
    var allPermutations = new List<List<T>>();
    var current = new List<T>();
    var used = new bool[elements.Length];
    
    void Backtrack()
    {
        // Base case: permutation complete
        if (current.Count == elements.Length)
        {
            allPermutations.Add(new List<T>(current));
            return;
        }
        
        // Try each unused element in current position
        for (int i = 0; i < elements.Length; i++)
        {
            if (used[i]) continue;  // Skip if already used
            
            current.Add(elements[i]);  // Choose
            used[i] = true;
            Backtrack();               // Explore
            current.RemoveAt(current.Count - 1);  // Unchoose
            used[i] = false;
        }
    }
    
    Backtrack();
    // allPermutations.Count = elements.Length!
}
```

**Time complexity:** O(n × n!)
- Generate n! permutations
- Each takes O(n) time to copy

**Space complexity:** O(n × n!)
- Store n! permutations
- Each has n elements

**Recursion depth:** O(n)
- Stack frames for n positions

---

### Method 2: Backtracking with Swapping
```csharp
void GeneratePermutationsSwapping<T>(T[] arr)
{
    var allPermutations = new List<List<T>>();
    
    void Backtrack(int start)
    {
        // Base case: completed permutation
        if (start == arr.Length)
        {
            allPermutations.Add(new List<T>(arr));
            return;
        }
        
        // Try each element from 'start' onward in position 'start'
        for (int i = start; i < arr.Length; i++)
        {
            // Swap element i into position start
            Swap(arr, start, i);
            
            // Recurse for remaining positions
            Backtrack(start + 1);
            
            // Backtrack: restore original order
            Swap(arr, start, i);
        }
    }
    
    void Swap(T[] array, int i, int j)
    {
        var temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }
    
    Backtrack(0);
}
```

**Advantages:**
- Generates permutations in-place (swaps original array)
- No need for `used[]` array
- Slightly more memory efficient

---

## Complexity Analysis

### Time Complexity: O(n × n!)
- **Generate n! permutations** (unavoidable if generating all)
- **Each permutation takes O(n)** time to construct/copy
- **Total: O(n × n!)**

### Space Complexity: O(n × n!)
- **Store n! permutations** (output requirement)
- **Each permutation has n elements**
- **Total: O(n × n!)**

### Recursion Depth: O(n)
- Decision tree has depth n (one level per position)
- Stack space: O(n)

---

## Growth Rate Table

| n | Permutations (n!) | Comparison to 2^n |
|---|-------------------|-------------------|
| 0 | 1 | 2^0 = 1 |
| 1 | 1 | 2^1 = 2 |
| 2 | 2 | 2^2 = 4 |
| 3 | 6 | 2^3 = 8 |
| 4 | 24 | 2^4 = 16 |
| 5 | 120 | 2^5 = 32 |
| 6 | 720 | 2^6 = 64 |
| 7 | 5,040 | 2^7 = 128 |
| 8 | 40,320 | 2^8 = 256 |
| 9 | 362,880 | 2^9 = 512 |
| 10 | 3,628,800 | 2^10 = 1,024 |
| 11 | 39,916,800 | 2^11 = 2,048 |
| 12 | 479,001,600 | 2^12 = 4,096 |

**Observations:**
- **n ≤ 4:** n! < 2^n (factorial grows slower initially)
- **n ≥ 5:** n! > 2^n (factorial overtakes exponential!)
- **n = 10:** 3.6 million permutations (practical limit)
- **n = 12:** 479 million permutations (feasible but slow)
- **n = 13+:** Billions of permutations (impractical to generate all)

**Growth comparison:**
```
Polynomial: n² = 100 for n=10
Exponential: 2^n = 1,024 for n=10
Factorial: n! = 3,628,800 for n=10

Factorial grows MUCH faster than exponential!
```

---

## Common Problems Using This State Space

### Direct Permutation Generation
- **[Permutations](../variants/backtracking/variant_2_permutations.md)** - LeetCode #46 ⭐
  - Generate all permutations of array
  - Core backtracking template for permutations

### With Constraints/Optimization
- **N-Queens** - Place n queens on chessboard
  - n! potential arrangements, but constraint checking prunes heavily
  - Backtracking with early validation
  
- **Traveling Salesman Problem (TSP)** - Visit all cities minimizing distance
  - n! possible routes
  - Dynamic Programming + Bitmask → O(2^n × n²)
  
- **String Permutations** - Generate all anagrams
  - n! permutations, but duplicates if repeated characters
  - Use frequency map for optimization

---

## Optimization Strategies

### 1. Early Termination (Pruning)
**When:** Can detect invalid permutations early
- **Technique:** Check constraints before completing permutation
- **Example:** N-Queens - stop if queen conflicts with previous queens
- **Reduction:** Still O(n!) worst case, but drastically fewer in practice

### 2. Avoiding Duplicates
**When:** Input has repeated elements
- **Problem:** [1, 1, 2] generates duplicates: [1₁, 1₂, 2] vs [1₂, 1₁, 2]
- **Technique:** Sort input, skip duplicates at same recursion level
- **Code:**
  ```csharp
  if (i > start && arr[i] == arr[i-1]) continue;  // Skip duplicate
  ```
- **Reduction:** Fewer permutations generated (but still factorial order)

### 3. Next Permutation (Iterative)
**When:** Need permutations in lexicographic order
- **Technique:** Find next permutation without recursion
- **Algorithm:**
  1. Find rightmost pair where arr[i] < arr[i+1]
  2. Swap arr[i] with smallest element > arr[i] in arr[i+1:]
  3. Reverse arr[i+1:]
- **Complexity:** O(n) per permutation, O(n × n!) total

### 4. Dynamic Programming (Not for Generation)
**When:** Need to count/decide, not generate all
- **Example:** Traveling Salesman - count shortest path, not all paths
- **Technique:** DP with bitmask to track visited cities
- **Complexity:** O(2^n × n²) instead of O(n!)

---

## When NOT to Generate All Permutations

Many problems **appear** to need all permutations but have better solutions:

### Problem Type → Better Approach

| Problem | Naive | Optimized | Technique |
|---------|-------|-----------|-----------|
| Find Minimum/Maximum | O(n!) | O(n log n) | Sort + Greedy |
| Anagram Check | O(n!) | O(n) | Frequency Map |
| Next Permutation | O(n!) | O(n) | In-place algorithm |
| Permutation Parity (Even/Odd) | O(n!) | O(n) | Inversion count |

---

## When to Use This Model

✅ **Use when:**
- Problem says "generate all permutations", "all orderings", "all arrangements"
- Need to explore every possible ordering (e.g., exhaustive search)
- Order of elements explicitly matters
- No better optimization exists (e.g., N-Queens with constraints)

❌ **Don't use when:**
- Only need to count permutations (use combinatorics: n!)
- Only need one specific permutation (use greedy/sorting)
- Order doesn't matter (use combinations or subsets instead)
- Can solve with DP (e.g., TSP with bitmask DP)
- n > 12 (impractical to generate billions of permutations)

---

## Key Insights

1. **Factorial growth is faster than exponential** for n ≥ 5
2. **Practical limit: n ≤ 12** for generating all permutations
3. **Most permutation problems DON'T need to generate all** - look for optimization
4. **Constraint checking dramatically reduces search space** (e.g., N-Queens)
5. **Duplicates require special handling** - sort and skip at same level
6. **Two main templates:** Used array (explicit) vs Swapping (in-place)
7. **Order matters:** Distinguishes permutations from combinations/subsets
