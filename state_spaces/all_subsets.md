# State Space: All Subsets (Power Set)

**Type:** All possible combinations of elements (with or without each element)  
**Structure:** Binary choice for each element - include or exclude  
**Cardinality:** 2^n = **O(2^n) subsets**

---

## Mathematical Foundation

### Exponential Formula
Number of subsets of a set with n elements:

$$2^n$$

**Why this formula?**
- Each element has 2 choices: **include** or **exclude**
- n independent binary decisions
- Total combinations: 2 × 2 × 2 × ... (n times) = **2^n**

### Includes Empty Set and Full Set
- Empty set {} is always a subset
- Full set {all elements} is always a subset
- All intermediate combinations in between

---

## Visualization

### Small Example (n=3)
For set `{a, b, c}`:

```
Decision Tree (Binary Choices):
                        {}
                  /           \
           include a         exclude a
              {a}               {}
            /     \           /     \
         +b      -b        +b      -b
       {a,b}    {a}       {b}      {}
       /  \     /  \      /  \     /  \
      +c  -c   +c  -c    +c  -c   +c  -c
   {a,b,c}{a,b}{a,c}{a} {b,c}{b} {c} {}

All 8 subsets (2³ = 8):
  {}         (empty set)
  {a}
  {b}
  {c}
  {a,b}
  {a,c}
  {b,c}
  {a,b,c}    (full set)
```

### Binary Representation
Each subset corresponds to a binary number (0 to 2^n - 1):

```
For {a, b, c} (n=3):

Binary  Include?  Subset
000     ---       {}
001     --c       {c}
010     -b-       {b}
011     -bc       {b,c}
100     a--       {a}
101     a-c       {a,c}
110     ab-       {a,b}
111     abc       {a,b,c}
```

**Bit i = 1 means include element i**

---

## Generation Patterns

### Method 1: Backtracking (Recursive)
```csharp
void GenerateSubsetsBacktracking<T>(T[] elements)
{
    var allSubsets = new List<List<T>>();
    var current = new List<T>();
    
    void Backtrack(int index)
    {
        // Every state is a valid subset
        allSubsets.Add(new List<T>(current));
        
        // Try including each remaining element
        for (int i = index; i < elements.Length; i++)
        {
            current.Add(elements[i]);      // Include
            Backtrack(i + 1);               // Recurse
            current.RemoveAt(current.Count - 1);  // Exclude (backtrack)
        }
    }
    
    Backtrack(0);
    // allSubsets.Count = 2^elements.Length
}
```

### Method 2: Bit Manipulation (Iterative)
```csharp
List<List<T>> GenerateSubsetsBitwise<T>(T[] elements)
{
    int n = elements.Length;
    int totalSubsets = 1 << n;  // 2^n
    var allSubsets = new List<List<T>>();
    
    // Iterate through all binary numbers from 0 to 2^n - 1
    for (int mask = 0; mask < totalSubsets; mask++)
    {
        var subset = new List<T>();
        
        // Check each bit position
        for (int i = 0; i < n; i++)
        {
            // If bit i is set, include element i
            if ((mask & (1 << i)) != 0)
            {
                subset.Add(elements[i]);
            }
        }
        
        allSubsets.Add(subset);
    }
    
    return allSubsets;
}
```

---

## Complexity Analysis

### Time Complexity: O(n × 2^n)
- Generate 2^n subsets
- Each subset takes O(n) time to construct/copy
- Total: O(n × 2^n)

### Space Complexity: O(n × 2^n)
- Store 2^n subsets
- Average subset size: n/2
- Total: O(n × 2^n)

### Recursion Depth: O(n)
- Decision tree has depth n
- Stack space: O(n)

---

## Growth Rate Table

| n | Subsets (2^n) | Growth Factor |
|---|---------------|---------------|
| 0 | 1 | base |
| 1 | 2 | ×2 |
| 2 | 4 | ×2 |
| 3 | 8 | ×2 |
| 4 | 16 | ×2 |
| 5 | 32 | ×2 |
| 10 | 1,024 | - |
| 15 | 32,768 | - |
| 20 | 1,048,576 | ~1 million |
| 25 | 33,554,432 | ~33 million |
| 30 | 1,073,741,824 | ~1 billion |

**Observation:** Exponential growth - each additional element doubles the number of subsets!

---

## Common Problems Using This State Space

### Direct Subset Generation
- **[Subsets](../variants/backtracking/variant_1_subsets.md)** - LeetCode #78 ⭐
  - Generate all subsets (power set)
  - Core backtracking template

### With Constraints
- **Combination Sum** variants - Sum equals target
- **Partition Equal Subset Sum** - DP optimization
- **Word Break** - String partitioning

### Implicit Subset Enumeration
- Dynamic Programming problems with subset choices
- 0/1 Knapsack (implicit subset selection)
- Subset sum problems

---

## Optimization Strategies

Most problems **cannot avoid exponential complexity** for generating all subsets, but some use optimizations:

### 1. Early Termination (Pruning)
**When:** Can detect invalid subsets early
- **Technique:** Prune branches that can't lead to valid solution
- **Example:** Combination sum - stop when sum exceeds target
- **Reduction:** Still O(2^n) worst case, but fewer in practice

### 2. Dynamic Programming (Avoid Regeneration)
**When:** Need to count or find specific subsets
- **Technique:** Build solution bottom-up, reuse previous results
- **Example:** Subset sum - dp[i][sum] = can make sum with first i elements
- **Complexity:** O(n × sum) instead of O(2^n)

### 3. Memoization (Avoid Recomputation)
**When:** Overlapping subproblems in recursive solution
- **Technique:** Cache results of subproblems
- **Example:** Fibonacci-like subset problems
- **Reduction:** Exponential → Polynomial in many cases

### 4. Bit Manipulation (Faster Generation)
**When:** Just need to generate all subsets
- **Technique:** Iterate through 0 to 2^n-1, use bits as inclusion mask
- **Benefit:** Simpler code, slightly faster constant factors
- **Complexity:** Still O(n × 2^n)

---

## When NOT to Generate All Subsets

Many problems **appear** to need all subsets but have better solutions:

### Problem Type → Better Approach
| Problem | Naive | Optimized | Technique |
|---------|-------|-----------|-----------|
| Subset Sum | O(2^n) | O(n × sum) | DP |
| Longest Increasing Subsequence | O(2^n) | O(n log n) | DP + Binary Search |
| Partition Problem | O(2^n) | O(n × sum) | DP |
| Knapsack | O(2^n) | O(n × W) | DP |

---

## Subset vs. Subarray

**Critical distinction:**

| Aspect | Subsets (Power Set) | Subarrays (Contiguous) |
|--------|---------------------|------------------------|
| **Cardinality** | 2^n (exponential) | n(n+1)/2 (quadratic) |
| **Elements** | Any combination | Must be contiguous |
| **Order** | Doesn't matter | Position matters |
| **Example (n=3)** | 8 subsets | 6 subarrays |
| **Generation** | Binary choices | Nested loops |

---

## Related State Spaces

- **All Permutations:** n! = O(n!) - order matters
- **All Combinations of size k:** C(n,k) - fixed subset size
- **All Subarrays:** n(n+1)/2 = O(n²) - must be contiguous
- **All Subsequences:** 2^n = O(2^n) - maintains relative order

---

## When to Use This Model

✅ **Use when:**
- Problem says "generate all subsets", "power set", "all combinations"
- Each element has binary choice (include/exclude)
- No constraint on size or contiguity
- Backtracking/exhaustive search required

❌ **Don't use when:**
- Problem has polynomial solution (check for DP first!)
- Only need specific subsets (not all)
- Elements must be contiguous (use subarrays)
- Order of selection matters (use permutations)

---

## Key Insights

1. **Exponential growth is unavoidable** when generating ALL subsets
2. **Many subset problems have DP solutions** - don't generate all!
3. **Backtracking is the standard template** for generation
4. **Bit manipulation is faster** for simple generation
5. **Pruning can help** but won't change O(2^n) worst case
6. **n ≤ 20 is practical limit** for brute force subset generation
