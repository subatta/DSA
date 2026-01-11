# Variant #1: Range Sum Query (Immutable)

**LeetCode #303 - Easy**

## Problem Statement
Given an integer array `nums`, handle multiple queries to calculate the sum of the elements of `nums` between indices `left` and `right` inclusive where `left <= right`.

Implement the NumArray class:
- `NumArray(int[] nums)` Initializes with the integer array `nums`.
- `int sumRange(int left, int right)` Returns the sum of elements between indices `left` and `right` inclusive.

**Example:** `nums = [1,2,3,4,5]`, `sumRange(1,3)` returns `2+3+4 = 9`

## 4-Step Analysis Framework

### Step 1: State Space Derivation

### **Problem Visualization:**
```
Input: nums = [1, 2, 3, 4, 5]

Query examples:
sumRange(0, 2) = 1 + 2 + 3 = 6
sumRange(1, 3) = 2 + 3 + 4 = 9
sumRange(2, 4) = 3 + 4 + 5 = 12

Naive approach: Sum elements for each query
  sumRange(1, 3): nums[1] + nums[2] + nums[3] = O(n) per query
  q queries → O(q × n) total

Prefix sum approach:
  Precompute cumulative sums:
  
  Index:    0  1  2  3  4  5
  nums:     -  1  2  3  4  5
  prefix:   0  1  3  6 10 15
            ↑  ↑  ↑  ↑  ↑  ↑
          sum sum sum sum sum sum
          [0:0][0:1][0:2][0:3][0:4][0:5]
  
  sumRange(1, 3) = prefix[4] - prefix[1]
                 = 10 - 1 = 9 ✓
                 
  Any range [L, R] = prefix[R+1] - prefix[L]
  Query time: O(1)!
```

### **Core Question:**
How to answer range sum queries in O(1) time after preprocessing?

### **Deriving from First Principles:**

**Observation 1:** Naive per-query calculation
- For each query [left, right], iterate and sum
- Time per query: O(right - left + 1) = O(n) worst case
- Total for q queries: O(q × n)
- Inefficient for many queries

**Observation 2:** Overlapping work
- sumRange(0, 5) = 1+2+3+4+5 = 15
- sumRange(0, 3) = 1+2+3+4 = 10
- Notice: sumRange(0, 5) includes sumRange(0, 3)
- We're recalculating the same prefix sums repeatedly!

**Observation 3:** Prefix sum array
- Define prefix[i] = sum of nums[0...i-1]
- prefix[0] = 0 (empty range)
- prefix[i] = prefix[i-1] + nums[i-1]
- Precomputation: O(n) time, O(n) space

**Observation 4:** Range sum formula
```
sum(nums[L...R]) = sum(nums[0...R]) - sum(nums[0...L-1])
                 = prefix[R+1] - prefix[L]

Example: nums = [1,2,3,4,5], find sum(nums[1...3])
  prefix[4] = 1+2+3+4 = 10
  prefix[1] = 1
  sum = 10 - 1 = 9 = (2+3+4) ✓

Why R+1 and not R?
  prefix[i] stores sum of first i elements
  So prefix[R+1] includes element at index R
```

**Observation 5:** Trade-off analysis
- **Naive:** 
  - Preprocessing: O(1)
  - Query: O(n)
  - Space: O(1)
- **Prefix Sum:**
  - Preprocessing: O(n)
  - Query: O(1)
  - Space: O(n)
- Prefix sum wins when q ≥ 1 (any queries!)

### **State Space Structure:**

**Type:** Precomputed cumulative sums for all prefixes  
**Structure:** Array prefix[0...n] where prefix[i] = sum(nums[0...i-1])  
**Cardinality:** n+1 prefix values (including empty prefix)  
**Query Space:** All possible ranges [L, R] where 0 ≤ L ≤ R < n = O(n²) possible queries

**Example Computation:**
For `nums = [1, 2, 3, 4, 5]`:

```
Build prefix array:
prefix[0] = 0
prefix[1] = prefix[0] + nums[0] = 0 + 1 = 1
prefix[2] = prefix[1] + nums[1] = 1 + 2 = 3
prefix[3] = prefix[2] + nums[2] = 3 + 3 = 6
prefix[4] = prefix[3] + nums[3] = 6 + 4 = 10
prefix[5] = prefix[4] + nums[4] = 10 + 5 = 15

Query examples:
sumRange(1, 3) = prefix[4] - prefix[1] = 10 - 1 = 9
sumRange(0, 4) = prefix[5] - prefix[0] = 15 - 0 = 15
sumRange(2, 2) = prefix[3] - prefix[2] = 6 - 3 = 3
```

### **Generation Pattern (Naive):**
```csharp
class NumArrayNaive
{
    private int[] nums;
    
    public NumArrayNaive(int[] nums)
    {
        this.nums = nums;
    }
    
    public int SumRange(int left, int right)
    {
        int sum = 0;
        for (int i = left; i <= right; i++)
        {
            sum += nums[i];
        }
        return sum;
    }
}
// Constructor: O(1), Query: O(n)
```

---

### Step 2: Brute Force with Value Tracing

**Approach:** Sum elements from left to right for each query

**Value Trace for:** `nums = [1,2,3,4,5]`

**Query 1:** sumRange(1, 3)
| i | nums[i] | sum |
|---|---------|-----|
| 1 | 2 | 2 |
| 2 | 3 | 5 |
| 3 | 4 | 9 |

Result: 9

**Query 2:** sumRange(0, 2)
| i | nums[i] | sum |
|---|---------|-----|
| 0 | 1 | 1 |
| 1 | 2 | 3 |
| 2 | 3 | 6 |

Result: 6

**Complexity:**
- Constructor: O(1) time, O(1) space
- Query: O(R - L + 1) = O(n) time worst case
- For q queries: O(q × n) total

---

### Step 3: Pruning Analysis - Can We Do Better?

**Key Insight:** Precompute prefix sums to answer any query in O(1)

**Why it works:**
- Sum from 0 to R is a "building block"
- Sum from 0 to L-1 is another "building block"
- Range sum = (Sum 0 to R) - (Sum 0 to L-1)

**Mathematical foundation:**
```
Σ(i=L to R) nums[i] = Σ(i=0 to R) nums[i] - Σ(i=0 to L-1) nums[i]
```

**Space-time tradeoff:**
- Invest O(n) space and O(n) preprocessing
- Get O(1) query time
- Amortizes perfectly for multiple queries

---

### Step 4: Optimal Solution

**Skeleton Transformation:**

```csharp
// BEFORE: Naive O(n) per query
class NumArrayBruteForce
{
    private int[] nums;
    
    public NumArrayBruteForce(int[] nums)
    {
        this.nums = nums;
    }
    
    public int SumRange(int left, int right)
    {
        int sum = 0;
        for (int i = left; i <= right; i++)  // O(n) work per query
            sum += nums[i];
        return sum;
    }
}

// AFTER: Prefix sum O(1) per query
class NumArray
{
    private int[] prefix;  // Key: Precomputed sums
    
    public NumArray(int[] nums)
    {
        int n = nums.Length;
        prefix = new int[n + 1];  // prefix[i] = sum(nums[0..i-1])
        
        // O(n) preprocessing
        for (int i = 0; i < n; i++)
        {
            prefix[i + 1] = prefix[i] + nums[i];
        }
    }
    
    public int SumRange(int left, int right)
    {
        // O(1) query using difference
        return prefix[right + 1] - prefix[left];
    }
}
```

**Complexity:**
- **Constructor:** O(n) time, O(n) space
- **Query:** O(1) time
- **Total for q queries:** O(n + q) vs O(q × n) naive

**Why prefix[right + 1] instead of prefix[right]?**
```
prefix[i] stores sum of FIRST i elements (indices 0 to i-1)
To include element at index right, we need prefix[right + 1]

Example: nums = [1,2,3], get sum of indices [1,2]
  prefix[3] = 1+2+3 = 6
  prefix[1] = 1
  sum = 6 - 1 = 5 = (2+3) ✓
```

---

## Edge Cases

1. **Single element range:** `sumRange(2, 2)`
   - prefix[3] - prefix[2] = single element ✓

2. **Full array:** `sumRange(0, n-1)`
   - prefix[n] - prefix[0] = total sum ✓

3. **Empty array:** Handle with prefix = [0]

4. **Negative numbers:** Works fine, prefix sum handles them

---

## Related Problems

- **LeetCode #304:** Range Sum Query 2D (Immutable) - 2D prefix sums
- **LeetCode #307:** Range Sum Query (Mutable) - Requires Segment Tree/Fenwick Tree
- **LeetCode #560:** Subarray Sum Equals K - Prefix sum + hash map

---

## Tags
`#prefix-sum` `#array` `#preprocessing` `#range-query` `#cumulative-sum` `#easy`
