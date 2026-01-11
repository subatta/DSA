# Variant: Minimum Size Subarray Sum

**Pattern:** Sliding Window  
**Difficulty:** Medium  
**LeetCode:** #209

> **⚠️ Important Constraint:** This variant works ONLY for **positive integers**. For arrays with negative integers, the sliding window approach fails because removing elements doesn't guarantee sum decrease. See [Variant 5b: With Negative Integers](variant_5b_min_size_subarray_sum_negative.md) for the Prefix Sum + Monotonic Deque solution.

---

## Problem Statement

Given an array of positive integers `nums` and a positive integer `target`, return the **minimal length** of a contiguous subarray whose sum is **greater than or equal to** `target`. If no such subarray exists, return `0`.

### Input/Output:
- Input: `nums = [2,3,1,2,4,3]`, `target = 7`
- Output: `2`
- Explanation: Subarray `[4,3]` has minimal length 2 with sum = 7

---

## **Step 1: Derive Full State Space from Problem**

### **Problem Analysis:**
- Find **minimum length** subarray where sum ≥ target
- Must consider all possible contiguous subarrays
- Need to track both length and sum

### **State Space Definition:**

**Reference:** See [All Contiguous Subarrays](../../state_spaces/all_contiguous_subarrays.md) for detailed mathematical derivation

- **Type**: All contiguous subarrays (includes single elements)
- **Structure**: Linear sequences `[start, end]` where `0 ≤ start ≤ end < n`
- **Cardinality**: n(n+1)/2 subarrays = **O(n²)**
- **Generation**: Nested loops - outer for start, inner for end

### **Full State Space Enumeration:**
For `nums = [2,3,1,2,4,3]` (n=6), there are 6×7/2 = **21 subarrays**:

```
Start=0: [2], [2,3], [2,3,1], [2,3,1,2], [2,3,1,2,4], [2,3,1,2,4,3]  (6)
Start=1: [3], [3,1], [3,1,2], [3,1,2,4], [3,1,2,4,3]                 (5)
Start=2: [1], [1,2], [1,2,4], [1,2,4,3]                              (4)
Start=3: [2], [2,4], [2,4,3]                                         (3)
Start=4: [4], [4,3]                                                  (2)
Start=5: [3]                                                         (1)
Total: 21 subarrays
```

**With sums and lengths:**
```
[2]→2 len=1, [2,3]→5 len=2, [2,3,1]→6 len=3, [2,3,1,2]→8 len=4 ✅, ...
[3]→3 len=1, [3,1]→4 len=2, [3,1,2]→6 len=3, [3,1,2,4]→10 len=4 ✅, ...
[1]→1 len=1, [1,2]→3 len=2, [1,2,4]→7 len=3 ✅, [1,2,4,3]→10 len=4 ✅
[2]→2 len=1, [2,4]→6 len=2, [2,4,3]→9 len=3 ✅
[4]→4 len=1, [4,3]→7 len=2 ✅ ← minimal!
[3]→3 len=1
```

---

## **Step 2: Brute Force Solution with Value Tracing**

### **Brute Force Approach:**
1. Generate all contiguous subarrays using nested loops
2. For each subarray, calculate sum
3. Track minimum length among those with sum ≥ target

### **Brute Force Code:**
```csharp
int MinSubArrayLenBruteForce(int target, int[] nums) 
{
    int minLen = int.MaxValue;
    
    // Generate all subarrays
    for (int start = 0; start < nums.Length; start++)
    {
        int sum = 0;
        
        for (int end = start; end < nums.Length; end++)
        {
            sum += nums[end];
            
            if (sum >= target)
            {
                int length = end - start + 1;
                minLen = Math.Min(minLen, length);
                break; // No need to extend further from this start
            }
        }
    }
    
    return minLen == int.MaxValue ? 0 : minLen;
}
```

### **Brute Force Value Trace (nums = [2,3,1,2,4,3], target = 7):**

| start | end | subarray    | sum calc  | sum | ≥ 7? | length | minLen |
|-------|-----|-------------|-----------|-----|------|--------|--------|
| 0     | 0   | [2]         | 2         | 2   | ❌   | -      | ∞      |
| 0     | 1   | [2,3]       | 2+3       | 5   | ❌   | -      | ∞      |
| 0     | 2   | [2,3,1]     | 5+1       | 6   | ❌   | -      | ∞      |
| 0     | 3   | [2,3,1,2]   | 6+2       | 8   | ✅   | 4      | 4      |
| 1     | 0   | [3]         | 3         | 3   | ❌   | -      | 4      |
| 1     | 1   | [3,1]       | 3+1       | 4   | ❌   | -      | 4      |
| 1     | 2   | [3,1,2]     | 4+2       | 6   | ❌   | -      | 4      |
| 1     | 3   | [3,1,2,4]   | 6+4       | 10  | ✅   | 4      | 4      |
| 2     | 0   | [1]         | 1         | 1   | ❌   | -      | 4      |
| 2     | 1   | [1,2]       | 1+2       | 3   | ❌   | -      | 4      |
| 2     | 2   | [1,2,4]     | 3+4       | 7   | ✅   | 3      | 3      |
| 3     | 0   | [2]         | 2         | 2   | ❌   | -      | 3      |
| 3     | 1   | [2,4]       | 2+4       | 6   | ❌   | -      | 3      |
| 3     | 2   | [2,4,3]     | 6+3       | 9   | ✅   | 3      | 3      |
| 4     | 0   | [4]         | 4         | 4   | ❌   | -      | 3      |
| 4     | 1   | [4,3]       | 4+3       | 7   | ✅   | 2      | **2**  |
| 5     | 0   | [3]         | 3         | 3   | ❌   | -      | 2      |

**Result: 2** (subarray `[4,3]`)

### **Complexity:**
- **Time**: O(n²) to generate and check all subarrays
- **Space**: O(1)

---

## **Step 3: Can We Prune the Brute Force?**

### **Key Observations:**

1. **Early Termination**: Already implemented - when sum ≥ target, stop extending
   ```
   [2,3,1]→6 < 7 ❌
   [2,3,1,2]→8 ≥ 7 ✅ → found valid, stop extending from start=0
   ```

2. **Monotonic Property**: Since all numbers are positive, adding elements only increases sum
   - Once sum ≥ target, all further extensions are valid but longer (not optimal)

3. **Overlapping Windows**: Recalculating sums from scratch wastes work
   ```
   [2,3,1] sum = 6
   [3,1,2] sum recalculated ← could reuse partial work
   ```

4. **Shrinking Opportunity**: When we have a valid window, we should try to shrink it from the left
   ```
   [2,3,1,2]→8 ≥ 7 ✅ len=4
   Try removing left: [3,1,2]→6 < 7 ❌ → can't shrink
   ```

### **Can We Do Better?**
**YES!** Use sliding window to avoid recalculating sums and intelligently shrink valid windows

---

## **Step 4: Optimal Solution - Sliding Window**

### **Key Insight:**
Maintain a **sliding window** with running sum:
- **Expand** right pointer to increase sum until sum ≥ target
- **Contract** left pointer while sum still ≥ target (find minimal window)
- **Track** minimum length encountered

This is the **"grow-then-shrink"** pattern - opposite of typical sliding window!

### **Why This Works:**
```
Window [2,3,1,2] sum=8 ≥ 7 ✅ len=4
  Try shrink: [3,1,2] sum=6 < 7 ❌ → can't shrink further
  
Window [1,2,4] sum=7 ≥ 7 ✅ len=3
  Try shrink: [2,4] sum=6 < 7 ❌ → can't shrink
  
Window [4,3] sum=7 ≥ 7 ✅ len=2
  Try shrink: [3] sum=3 < 7 ❌ → can't shrink
  
Result: minimum is 2
```

### **Canonical Skeleton Applied:**
```csharp
int SlidingWindowTemplate(int[] nums, int targetOrCondition) {
    int left = 0;
    int result = InitialValue;
    var windowState = new DataStructure();

    for (int right = 0; right < nums.Length; right++) {
        // Update windowState with nums[right]
        
        // Shrink window while condition is met
        while (ConditionMet(windowState)) {
            result = UpdateResult(result, windowState);
            // Update windowState by removing nums[left]
            left++;
        }
    }

    return result;
}
```

### **Optimized Solution:**
```csharp
int MinSubArrayLen(int target, int[] nums) 
{
    int minLen = int.MaxValue;
    int sum = 0, left = 0;
    
    for (int right = 0; right < nums.Length; right++)
    {
        // Expand window: add new element
        sum += nums[right];
        
        // Shrink window while sum >= target
        while (sum >= target)
        {
            // Update minimum length
            minLen = Math.Min(minLen, right - left + 1);
            
            // Try to shrink from left
            sum -= nums[left];
            left++;
        }
    }
    
    return minLen == int.MaxValue ? 0 : minLen;
}
```

### **Changes from Skeleton:**
1. **windowState** → `sum` (single integer tracking additive property)
2. **ConditionMet** → `sum >= target` (we have valid window)
3. **UpdateResult** → Update `minLen` **before** shrinking (critical!)
4. **InitialValue** → `int.MaxValue` (looking for minimum)
5. **Return** → Check if `minLen` was ever updated

### **Optimized Value Trace (nums = [2,3,1,2,4,3], target = 7):**

| Step | right | nums[right] | sum  | left | sum ≥ 7? | Action                    | minLen | Final sum |
|------|-------|-------------|------|------|----------|---------------------------|--------|-----------|
| 1    | 0     | 2           | 2    | 0    | ❌       | -                         | ∞      | 2         |
| 2    | 1     | 3           | 5    | 0    | ❌       | -                         | ∞      | 5         |
| 3    | 2     | 1           | 6    | 0    | ❌       | -                         | ∞      | 6         |
| 4    | 3     | 2           | 8    | 0    | ✅       | Shrink loop starts        | ∞      | 8         |
|      |       |             |      |      |          | minLen = min(∞, 4) = 4    | 4      | 8         |
|      |       |             |      |      |          | Remove nums[0]=2, left=1  | 4      | 6         |
|      |       |             | 6    | 1    | ❌       | Exit shrink               | 4      | 6         |
| 5    | 4     | 4           | 10   | 1    | ✅       | Shrink loop starts        | 4      | 10        |
|      |       |             |      |      |          | minLen = min(4, 4) = 4    | 4      | 10        |
|      |       |             |      |      |          | Remove nums[1]=3, left=2  | 4      | 7         |
|      |       |             | 7    | 2    | ✅       | Continue shrinking        | 4      | 7         |
|      |       |             |      |      |          | minLen = min(4, 3) = 3    | 3      | 7         |
|      |       |             |      |      |          | Remove nums[2]=1, left=3  | 3      | 6         |
|      |       |             | 6    | 3    | ❌       | Exit shrink               | 3      | 6         |
| 6    | 5     | 3           | 9    | 3    | ✅       | Shrink loop starts        | 3      | 9         |
|      |       |             |      |      |          | minLen = min(3, 3) = 3    | 3      | 9         |
|      |       |             |      |      |          | Remove nums[3]=2, left=4  | 3      | 7         |
|      |       |             | 7    | 4    | ✅       | Continue shrinking        | 3      | 7         |
|      |       |             |      |      |          | minLen = min(3, 2) = **2**| **2**  | 7         |
|      |       |             |      |      |          | Remove nums[4]=4, left=5  | 2      | 3         |
|      |       |             | 3    | 5    | ❌       | Exit shrink               | 2      | 3         |

**Result: 2** (window `[4,3]` at step 6)

### **Critical Observation - Step 5 Double Shrink:**
```
right=4, sum=10 (window [3,1,2,4]), left=1
  sum(10) ≥ 7? YES → minLen = 4, shrink → sum=7 (window [1,2,4]), left=2
  sum(7) ≥ 7? YES → minLen = 3, shrink → sum=6 (window [2,4]), left=3
  sum(6) ≥ 7? NO → stop shrinking
```

The while loop shrinks **multiple times** in one iteration - this is the optimization!

### **State Space Comparison:**

| Approach        | Subarrays Explored | Calculations      | Total Operations |
|-----------------|-------------------|-------------------|------------------|
| Brute Force     | 21 subarrays      | 21 sum calcs      | O(n²) ≈ 36 ops   |
| Sliding Window  | 6 windows         | Each element +/- once | O(n) ≈ 12 ops |

### **Big-O Analysis:**
- **Time**: O(n) - each element enters/exits window at most once
- **Space**: O(1)
- **Optimization**: O(n²) → **O(n)** ✅

### **Key Takeaway:**
This is **minimization sliding window** - we shrink **aggressively** while condition holds, updating the answer before each shrink. The pattern is:
1. Expand to meet condition
2. Shrink greedily while maintaining condition
3. Update result during shrinking phase

---

## **Related Problems:**
- [Subarray Product Less Than K](variant_4_subarray_product_less_k.md) - Counting variant with product
- [Longest Substring Without Repeating Characters](variant_1_longest_substring_no_repeat.md) - Maximization instead of minimization
- [Minimum Size Subarray Sum (With Negative Integers)](variant_5b_min_size_subarray_sum_negative.md) - Requires Prefix Sum + Monotonic Deque
- Maximum Size Subarray Sum Equals K - Hash map approach for exact sum

## **Tags:**
`sliding-window` `array` `minimization` `O(n²)-state-space` `variable-window` `medium`
