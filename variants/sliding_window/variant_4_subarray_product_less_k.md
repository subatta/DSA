# Variant: Subarray Product Less Than K

**Pattern:** Sliding Window  
**Difficulty:** Medium  
**LeetCode:** #713

---

## Problem Statement

Given an array of positive integers `nums` and an integer `k`, return the number of contiguous subarrays where the product of all elements is strictly less than `k`.

### Input/Output:
- Input: `nums = [10,5,2,6]`, `k = 100`
- Output: `8`
- Explanation: The 8 subarrays are: `[10], [5], [2], [6], [10,5], [5,2], [2,6], [5,2,6]`

---

## **Step 1: Derive Full State Space from Problem**

### **Problem Analysis:**
- **Count** all contiguous subarrays where product < k
- Must enumerate all possible subarrays and test each one
- Product grows multiplicatively, so large windows quickly exceed k

### **State Space Definition:**

**Reference:** See [All Contiguous Subarrays](../../state_spaces/all_contiguous_subarrays.md) for detailed mathematical derivation

- **Type**: All contiguous subarrays (includes single elements)
- **Structure**: Linear sequences `[start, end]` where `0 ≤ start ≤ end < n`
- **Cardinality**: n(n+1)/2 subarrays = **O(n²)**
- **Generation**: Nested loops - outer for start, inner for end

### **Full State Space Enumeration:**
For `nums = [10,5,2,6]` (n=4), there are 4×5/2 = **10 subarrays**:

```
Start=0: [10], [10,5], [10,5,2], [10,5,2,6]           (4 subarrays)
Start=1: [5], [5,2], [5,2,6]                          (3 subarrays)
Start=2: [2], [2,6]                                   (2 subarrays)
Start=3: [6]                                          (1 subarray)
Total: 10 subarrays
```

**With products:**
```
[10]→10,  [10,5]→50,  [10,5,2]→100,  [10,5,2,6]→600
[5]→5,    [5,2]→10,   [5,2,6]→60
[2]→2,    [2,6]→12
[6]→6
```

---

## **Step 2: Brute Force Solution with Value Tracing**

### **Brute Force Approach:**
1. Generate all contiguous subarrays using nested loops
2. For each subarray, calculate product
3. Count those with product < k

### **Brute Force Code:**
```csharp
int NumSubarrayProductLessThanKBruteForce(int[] nums, int k) 
{
    if (k <= 1) return 0; // Product can't be < 1 with positive integers
    
    int count = 0;
    
    // Generate all subarrays
    for (int start = 0; start < nums.Length; start++)
    {
        int product = 1;
        
        for (int end = start; end < nums.Length; end++)
        {
            product *= nums[end];
            
            if (product < k)
                count++;
            else
                break; // Product will only grow larger
        }
    }
    
    return count;
}
```

### **Brute Force Value Trace (nums = [10,5,2,6], k = 100):**

| start | end | subarray  | product calc | product | < 100? | count |
|-------|-----|-----------|--------------|---------|--------|-------|
| 0     | 0   | [10]      | 10           | 10      | ✅     | 1     |
| 0     | 1   | [10,5]    | 10×5         | 50      | ✅     | 2     |
| 0     | 2   | [10,5,2]  | 50×2         | 100     | ❌     | 2     |
| 1     | 1   | [5]       | 5            | 5       | ✅     | 3     |
| 1     | 2   | [5,2]     | 5×2          | 10      | ✅     | 4     |
| 1     | 3   | [5,2,6]   | 10×6         | 60      | ✅     | 5     |
| 2     | 2   | [2]       | 2            | 2       | ✅     | 6     |
| 2     | 3   | [2,6]     | 2×6          | 12      | ✅     | 7     |
| 3     | 3   | [6]       | 6            | 6       | ✅     | 8     |

**Result: 8**

### **Complexity:**
- **Time**: O(n²) to generate subarrays
- **Space**: O(1)

---

## **Step 3: Can We Prune the Brute Force?**

### **Key Observations:**

1. **Early Termination**: Already implemented - when product ≥ k, stop extending
   ```
   [10,5]→50 < 100 ✅
   [10,5,2]→100 ≥ 100 ❌ → stop, don't check [10,5,2,6]
   ```

2. **Overlapping Windows**: Many windows share elements
   ```
   [10,5] product = 10×5 = 50
   [5,2] product = 5×2 = 10 ← recalculated 5 unnecessarily
   ```

3. **Counting Insight**: When window `[left, right]` is valid, ALL subarrays ending at `right` within this window are valid
   ```
   Window [5,2,6] is valid → adds 3 subarrays: [6], [2,6], [5,2,6]
   That's (right - left + 1) subarrays!
   ```

### **Can We Do Better?**
**YES!** Use sliding window with product tracking and counting formula

---

## **Step 4: Optimal Solution - Sliding Window**

### **Key Insight:**
Maintain a **sliding window** where product < k:
- **Expand** right pointer to include new elements
- **Contract** left pointer when product ≥ k (divide out left elements)
- **Count**: When window `[left, right]` is valid, it contributes `right - left + 1` subarrays (all ending at `right`)

### **Why Counting Works:**
```
Valid window [5, 2, 6]:
- Subarray [6] ends at right ✅
- Subarray [2, 6] ends at right ✅
- Subarray [5, 2, 6] ends at right ✅
Total: 3 = right(3) - left(1) + 1
```

### **Canonical Skeleton Applied:**
```csharp
int SlidingWindowTemplate(int[] nums, int kOrCondition) {
    int left = 0;
    int result = 0;
    var windowState = new Dictionary<int,int>();

    for (int right = 0; right < nums.Length; right++) {
        // Update windowState with nums[right]
        
        // Shrink window if condition violated
        while (ConditionViolated(windowState)) {
            // Update windowState by removing nums[left]
            left++;
        }

        // Update result based on current window
        result = UpdateResult(result, windowState);
    }

    return result;
}
```

### **Optimized Solution:**
```csharp
int NumSubarrayProductLessThanK(int[] nums, int k) 
{
    if (k <= 1) return 0;
    
    int product = 1, left = 0, count = 0;
    
    for (int right = 0; right < nums.Length; right++)
    {
        // Expand window: multiply by new element
        product *= nums[right];
        
        // Shrink window while product >= k
        while (product >= k)
        {
            product /= nums[left];
            left++;
        }
        
        // Count all subarrays ending at right
        count += right - left + 1;
    }
    
    return count;
}
```

### **Changes from Skeleton:**
1. **windowState** → `product` (single integer tracking multiplicative property)
2. **ConditionViolated** → `product >= k` (need to shrink)
3. **UpdateResult** → `count += right - left + 1` (mathematical counting formula)
4. **Early exit**: Check `k <= 1` upfront

### **Optimized Value Trace (nums = [10,5,2,6], k = 100):**

| Step | right | nums[right] | product (after) | left | product ≥ k? | Shrink | product (after shrink) | right-left+1 | count |
|------|-------|-------------|-----------------|------|--------------|--------|------------------------|--------------|-------|
| 1    | 0     | 10          | 10              | 0    | ❌           | -      | 10                     | 1            | 1     |
| 2    | 1     | 5           | 50              | 0    | ❌           | -      | 50                     | 2            | 3     |
| 3    | 2     | 2           | 100             | 0    | ✅           | YES    | -                      | -            | 3     |
|      |       |             | 10 (÷10)        | 1    | ❌           | -      | 10                     | 2            | 5     |
| 4    | 3     | 6           | 60              | 1    | ❌           | -      | 60                     | 3            | **8** |

**Detailed Step 3 Shrinking:**
```
right=2, product=100, left=0
  product(100) >= k(100)? YES → divide by nums[0]=10 → product=10, left=1
  product(10) >= k(100)? NO → stop shrinking
  Add right-left+1 = 2-1+1 = 2 subarrays: [5,2], [2]
```

**Result: 8**

### **State Space Comparison:**

| Approach | Subarrays Explored | Calculations | Total Operations |
|----------|-------------------|--------------|------------------|
| Brute Force | 10 subarrays | Multiply per element | O(n²) ≈ 16 ops |
| Sliding Window | 4 windows | 1 multiply, few divides | O(n) ≈ 8 ops |

### **Big-O Analysis:**
- **Time**: O(n) - each element enters/exits window at most once
- **Space**: O(1)
- **Optimization**: O(n²) → **O(n)** ✅

### **Key Takeaway:**
**Sliding Window with mathematical counting** avoids explicit enumeration. The formula `right - left + 1` counts all subarrays ending at `right` within a valid window - a powerful technique for **counting problems** where we don't need to list all solutions!

---

## **Related Problems:**
- [Maximum Sum Subarray of Size K](variant_2_max_sum_size_k.md) - Track sum instead of product
- [Minimum Size Subarray Sum](variant_5_min_size_subarray_sum.md) - Find minimum length instead of count
- Subarrays with Bounded Maximum - Similar counting technique

## **Tags:**
`sliding-window` `array` `counting` `O(n²)-state-space` `variable-window` `medium`
