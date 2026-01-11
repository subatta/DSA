# Variant #2: Product of Array Except Self

**LeetCode #238 - Medium**

## Problem Statement
Given an integer array `nums`, return an array `answer` such that `answer[i]` is equal to the product of all elements of `nums` except `nums[i]`.

You must write an algorithm that runs in **O(n)** time and without using the division operation.

**Follow-up:** Can you solve it in O(1) extra space? (Output array doesn't count)

**Example:** `nums = [1,2,3,4]` → `[24,12,8,6]`

## 4-Step Analysis Framework

### Step 1: State Space Derivation

### **Problem Visualization:**
```
Input: nums = [1, 2, 3, 4]

Goal: For each position i, product of ALL OTHER elements

Position 0: product of [2,3,4] = 2×3×4 = 24
Position 1: product of [1,3,4] = 1×3×4 = 12
Position 2: product of [1,2,4] = 1×2×4 = 8
Position 3: product of [1,2,3] = 1×2×3 = 6

Result: [24, 12, 8, 6]

Key insight: Split into LEFT and RIGHT products
  answer[i] = (product of all elements LEFT of i) × (product of all elements RIGHT of i)

Visual breakdown:
Position:  0  1  2  3
nums:      1  2  3  4

For position 2:
  Left product:  1×2 = 2      (elements before index 2)
  Right product: 4 = 4         (elements after index 2)
  answer[2] = 2 × 4 = 8

Prefix (left) products:  [1,  1,  2,  6]
                          ↑   ↑   ↑   ↑
                          1  1×1 1×2 1×2×3
                          
Suffix (right) products: [24, 24, 12, 4]
                          ↑   ↑   ↑   ↑
                       2×3×4 3×4  4   1
                       
answer[i] = prefix[i] × suffix[i]
```

### **Core Question:**
How to compute product of all elements except current without division?

### **Deriving from First Principles:**

**Observation 1:** Naive approach with division
- Compute total product: P = nums[0] × nums[1] × ... × nums[n-1]
- For each i: answer[i] = P / nums[i]
- Problem: Division not allowed!
- Also breaks with zeros: P = 0

**Observation 2:** Decompose the product
```
For position i, we need product of:
  [nums[0], nums[1], ..., nums[i-1], nums[i+1], ..., nums[n-1]]
  
Split into two parts:
  Left part:  nums[0] × nums[1] × ... × nums[i-1]
  Right part: nums[i+1] × nums[i+2] × ... × nums[n-1]
  
answer[i] = left[i] × right[i]
```

**Observation 3:** Prefix and suffix products
- **Prefix product:** Product of all elements to the LEFT
  - prefix[i] = nums[0] × nums[1] × ... × nums[i-1]
  - prefix[0] = 1 (no elements to left)
- **Suffix product:** Product of all elements to the RIGHT
  - suffix[i] = nums[i+1] × nums[i+2] × ... × nums[n-1]
  - suffix[n-1] = 1 (no elements to right)

**Observation 4:** Two-pass computation
```
Pass 1 (left to right): Build prefix products
  prefix[0] = 1
  prefix[i] = prefix[i-1] × nums[i-1]

Pass 2 (right to left): Build suffix products
  suffix[n-1] = 1
  suffix[i] = suffix[i+1] × nums[i+1]

Pass 3: Combine
  answer[i] = prefix[i] × suffix[i]
```

**Observation 5:** Space optimization (O(1) extra space)
- Instead of storing prefix and suffix arrays
- Use output array to store prefix products first
- Then multiply by suffix products in-place using a single variable
- Only O(1) extra space (one variable for running suffix product)

### **State Space Structure:**

**Type:** For each position, compute product of all other elements  
**Structure:** Need left products and right products for all n positions  
**Cardinality:** n positions, each needs 2 values (left product, right product)  
**Answer generation:** Pairwise multiplication of prefix and suffix arrays

**Example Computation:**
For `nums = [1, 2, 3, 4]`:

```
Step 1: Build prefix products (left to right)
prefix[0] = 1           (no elements to left of index 0)
prefix[1] = 1 × 1 = 1   (product of [1])
prefix[2] = 1 × 2 = 2   (product of [1,2])
prefix[3] = 2 × 3 = 6   (product of [1,2,3])

Result: prefix = [1, 1, 2, 6]

Step 2: Build suffix products (right to left)
suffix[3] = 1           (no elements to right of index 3)
suffix[2] = 4 × 1 = 4   (product of [4])
suffix[1] = 3 × 4 = 12  (product of [3,4])
suffix[0] = 2 × 12 = 24 (product of [2,3,4])

Result: suffix = [24, 12, 4, 1]

Step 3: Combine
answer[0] = prefix[0] × suffix[0] = 1 × 24 = 24
answer[1] = prefix[1] × suffix[1] = 1 × 12 = 12
answer[2] = prefix[2] × suffix[2] = 2 × 4 = 8
answer[3] = prefix[3] × suffix[3] = 6 × 1 = 6

Result: [24, 12, 8, 6]
```

### **Generation Pattern (Naive with Division):**
```csharp
int[] ProductExceptSelfNaive(int[] nums)
{
    int n = nums.Length;
    int[] answer = new int[n];
    
    // For each position
    for (int i = 0; i < n; i++)
    {
        int product = 1;
        // Multiply all elements except nums[i]
        for (int j = 0; j < n; j++)
        {
            if (i != j)
            {
                product *= nums[j];
            }
        }
        answer[i] = product;
    }
    
    return answer;
}
// Time: O(n²), Space: O(1) extra
```

---

### Step 2: Brute Force with Value Tracing

**Approach:** For each position, multiply all other elements

**Value Trace for:** `nums = [1, 2, 3, 4]`

**Computing answer[0]** (skip index 0):
| j | nums[j] | product |
|---|---------|---------|
| 1 | 2 | 2 |
| 2 | 3 | 6 |
| 3 | 4 | 24 |

answer[0] = 24

**Computing answer[1]** (skip index 1):
| j | nums[j] | product |
|---|---------|---------|
| 0 | 1 | 1 |
| 2 | 3 | 3 |
| 3 | 4 | 12 |

answer[1] = 12

**Computing answer[2]** (skip index 2):
| j | nums[j] | product |
|---|---------|---------|
| 0 | 1 | 1 |
| 1 | 2 | 2 |
| 3 | 4 | 8 |

answer[2] = 8

**Computing answer[3]** (skip index 3):
| j | nums[j] | product |
|---|---------|---------|
| 0 | 1 | 1 |
| 1 | 2 | 2 |
| 2 | 3 | 6 |

answer[3] = 6

**Complexity:**
- Time: O(n²) - for each of n positions, iterate n elements
- Space: O(1) extra space

---

### Step 3: Pruning Analysis - Can We Do Better?

**Key Insight:** Use prefix and suffix products - each position computed once!

**Why it works:**
```
Product except self = (product of elements before) × (product of elements after)

For nums = [a, b, c, d, e] at position 2 (element c):
  Left product:  a × b
  Right product: d × e
  answer[2] = (a × b) × (d × e)
  
No need to recalculate a × b for each position!
```

**Transformation:** O(n²) → O(n)
- Build prefix array: O(n)
- Build suffix array: O(n)
- Multiply arrays: O(n)
- Total: O(n) with O(n) extra space

**Further optimization:** O(n) space → O(1) space
- Use output array for prefix products
- Compute suffix products on-the-fly with running variable
- Only O(1) extra space!

---

### Step 4: Optimal Solution

**Skeleton Transformation:**

```csharp
// BEFORE: Naive O(n²) approach
int[] ProductExceptSelf_BruteForce(int[] nums)
{
    int n = nums.Length;
    int[] answer = new int[n];
    
    for (int i = 0; i < n; i++)  // O(n)
    {
        int product = 1;
        for (int j = 0; j < n; j++)  // O(n) - nested loop!
        {
            if (i != j)
                product *= nums[j];
        }
        answer[i] = product;
    }
    
    return answer;
}

// AFTER: Prefix/Suffix approach O(n) time, O(n) space
int[] ProductExceptSelf_TwoArrays(int[] nums)
{
    int n = nums.Length;
    int[] prefix = new int[n];
    int[] suffix = new int[n];
    int[] answer = new int[n];
    
    // Build prefix products (left to right)
    prefix[0] = 1;
    for (int i = 1; i < n; i++)
    {
        prefix[i] = prefix[i - 1] * nums[i - 1];
    }
    
    // Build suffix products (right to left)
    suffix[n - 1] = 1;
    for (int i = n - 2; i >= 0; i--)
    {
        suffix[i] = suffix[i + 1] * nums[i + 1];
    }
    
    // Combine
    for (int i = 0; i < n; i++)
    {
        answer[i] = prefix[i] * suffix[i];
    }
    
    return answer;
}

// OPTIMAL: O(n) time, O(1) extra space (output doesn't count)
int[] ProductExceptSelf(int[] nums)
{
    int n = nums.Length;
    int[] answer = new int[n];
    
    // Use answer array to store prefix products
    answer[0] = 1;
    for (int i = 1; i < n; i++)
    {
        answer[i] = answer[i - 1] * nums[i - 1];
    }
    
    // Multiply by suffix products on-the-fly
    int suffixProduct = 1;
    for (int i = n - 1; i >= 0; i--)
    {
        answer[i] *= suffixProduct;  // prefix * suffix
        suffixProduct *= nums[i];     // Update running suffix
    }
    
    return answer;
}
```

**Complexity:**
- **Time:** O(n) - two passes through array
- **Space:** O(1) extra (output array doesn't count as extra space)

**Execution Trace for [1,2,3,4]:**

Pass 1 (prefix products):
```
answer = [1, 1, 2, 6]
```

Pass 2 (multiply by suffix):
```
i=3: answer[3] = 6 × 1 = 6,  suffixProduct = 4
i=2: answer[2] = 2 × 4 = 8,  suffixProduct = 12
i=1: answer[1] = 1 × 12 = 12, suffixProduct = 24
i=0: answer[0] = 1 × 24 = 24, suffixProduct = 24

Final: [24, 12, 8, 6]
```

---

## Edge Cases

1. **Contains zero:** `[1, 0, 3]` → `[0, 3, 0]`
   - Prefix/suffix handles naturally

2. **Multiple zeros:** `[0, 0, 3]` → `[0, 0, 0]`
   - All products become 0

3. **Single element:** `[5]` → `[1]`
   - Empty product = 1

4. **Two elements:** `[2, 3]` → `[3, 2]`

5. **Negative numbers:** `[-1, 2, -3]` → `[-6, 3, -2]`

---

## Related Problems

- **LeetCode #152:** Maximum Product Subarray
- **LeetCode #628:** Maximum Product of Three Numbers
- **LeetCode #1464:** Maximum Product of Two Elements in an Array

---

## Tags
`#prefix-sum` `#suffix-product` `#array` `#no-division` `#medium` `#space-optimization`
