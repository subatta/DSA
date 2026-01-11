# Variant: Maximum Sum Subarray of Size K

**Pattern:** Sliding Window  
**Difficulty:** Easy  
**LeetCode:** Similar to #643 (Maximum Average Subarray I)

---

## Problem Statement

Given an array of integers `nums` and an integer `k`, find the maximum sum of any contiguous subarray of size `k`.

### Input/Output:
- Input: `nums = [2, 1, 5, 1, 3, 2]`, `k = 3`
- Output: `9` (subarray `[5,1,3]`)

---

##**Step 1: Derive Full State Space from Problem**

### **Problem Visualization:**
```
Input: nums = [2, 1, 5, 1, 3, 2], k = 3

All size-3 windows:

Window 1: [2, 1, 5] sum = 8
           ┃━━━━━┃
           
 Window 2:    [1, 5, 1] sum = 7
                 ┃━━━━━┃

  Window 3:       [5, 1, 3] sum = 9 ✓ (maximum)
                     ┃━━━━━┃

   Window 4:          [1, 3, 2] sum = 6
                         ┃━━━━━┃

Strategy: Slide window right, remove left element, add right element
```

### **Problem Analysis:**
- Find **maximum sum** among all **size-k contiguous subarrays**
- Fixed window size makes this simpler than variable-length problems
- Must check every possible size-k window

### **State Space Definition:**
- **Type**: All non-zero-length contiguous subarrays with **size = k**
- **Structure**: Linear sequences `[start, start+k-1]` where `0 ≤ start ≤ n-k`
- **Cardinality**: `n - k + 1` subarrays = **O(n)**
- **Generation**: Single loop with fixed window size

### **Full State Space Enumeration:**
For `nums = [2, 1, 5, 1, 3, 2]` (n=6), `k=3`, there are 6-3+1 = **4 subarrays**:

```
[0,2]: [2, 1, 5] → sum = 8
[1,3]: [1, 5, 1] → sum = 7
[2,4]: [5, 1, 3] → sum = 9 ✅
[3,5]: [1, 3, 2] → sum = 6
```

**Note:** Only 4 valid windows, not n(n+1)/2 = 21 total subarrays!

---

## **Step 2: Brute Force Solution with Value Tracing**

### **Brute Force Approach:**
1. For each starting position from `0` to `n-k`
2. Sum the k elements starting from that position
3. Track maximum sum

### **Brute Force Code:**
```csharp
int MaxSumSubarrayBruteForce(int[] nums, int k) 
{ 
    int maxSum = int.MinValue;
    
    // Generate all size-k subarrays
    for (int start = 0; start <= nums.Length - k; start++)
    {
        // Calculate sum of subarray [start, start+k-1]
        int sum = 0;
        for (int i = start; i < start + k; i++)
        {
            sum += nums[i];
        }
        
        maxSum = Math.Max(maxSum, sum);
    }
    
    return maxSum;
}
```

### **Brute Force Value Trace (nums = [2,1,5,1,3,2], k=3):**

| start | Window Range | Elements  | sum calculation | sum | maxSum |
|-------|--------------|-----------|-----------------|-----|--------|
| 0     | [0,2]        | [2,1,5]   | 2+1+5           | 8   | 8      |
| 1     | [1,3]        | [1,5,1]   | 1+5+1           | 7   | 8      |
| 2     | [2,4]        | [5,1,3]   | 5+1+3           | 9   | **9**  |
| 3     | [3,5]        | [1,3,2]   | 1+3+2           | 6   | 9      |

**Result: 9**

### **Complexity:**
- **Time**: O(n) windows × O(k) sum calculation = **O(n·k)**
- **Space**: O(1)

---

## **Step 3: Can We Prune the Brute Force?**

### **Key Observations:**

1. **Redundant Recalculation**: Windows overlap heavily!
   ```
   Window [0,2]: sum(2, 1, 5) = 8
   Window [1,3]: sum(1, 5, 1) = 7
                  ↑  ↑
                  Already calculated in previous window!
   ```

2. **Overlapping Elements**: Adjacent windows share k-1 elements
   - Window 1: `[2, 1, 5]`
   - Window 2: `[1, 5, 1]` ← shares `[1, 5]` with Window 1
   - We recompute sum of `1+5` unnecessarily

3. **Incremental Update**: When sliding from `[a,b,c]` to `[b,c,d]`:
   ```
   New sum = Old sum - a + d
   ```
   Only 2 operations instead of k!

### **Can We Do Better?**
**YES!** Sliding window with incremental updates: O(n) instead of O(n·k)

---

## **Step 4: Optimal Solution - Sliding Window**

### **Key Insight:**
Maintain a **running sum** and update it incrementally:
- **Remove** leftmost element when sliding right
- **Add** new rightmost element
- Each window updated in O(1) instead of O(k)

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
int MaxSumSubarray(int[] nums, int k) 
{
    int maxSum = 0, windowSum = 0;
    
    // Build initial window [0, k-1]
    for (int i = 0; i < k; i++)
    {
        windowSum += nums[i];
    }
    maxSum = windowSum;
    
    // Slide window: remove left, add right
    for (int right = k; right < nums.Length; right++)
    {
        int left = right - k; // Element leaving window
        windowSum = windowSum - nums[left] + nums[right];
        maxSum = Math.Max(maxSum, windowSum);
    }
    
    return maxSum;
}
```

### **Changes from Skeleton:**
1. **Fixed window size**: No while loop needed - window always size k
2. **windowState** → `windowSum` (single integer, not dictionary)
3. **Initial window**: Build first window separately
4. **Sliding logic**: `windowSum - nums[left] + nums[right]`
5. **UpdateResult** → `Math.Max(maxSum, windowSum)`

### **Optimized Value Trace (nums = [2,1,5,1,3,2], k=3):**

**Phase 1: Build Initial Window**
| i   | nums[i] | windowSum | maxSum |
|-----|---------|-----------|--------|
| 0   | 2       | 2         | -      |
| 1   | 1       | 3         | -      |
| 2   | 5       | 8         | 8      |

**Phase 2: Slide Window**
| right | nums[right] | left | nums[left] | Operation | windowSum | maxSum |
|-------|-------------|------|------------|-----------|-----------|--------|
| 3     | 1           | 0    | 2          | 8-2+1     | 7         | 8      |
| 4     | 3           | 1    | 1          | 7-1+3     | 9         | **9**  |
| 5     | 2           | 2    | 5          | 9-5+2     | 6         | 9      |

**Result: 9**

### **State Space Comparison:**

| Approach | Windows Processed | Operations per Window | Total Operations |
|----------|-------------------|----------------------|------------------|
| Brute Force | 4 windows | O(k) = 3 adds | O(n·k) = 12 ops |
| Sliding Window | 4 windows | O(1) = 1 subtract + 1 add | O(n) = 2+6 = 8 ops |

### **Big-O Analysis:**
- **Time**: O(k) initial window + O(n-k) slides = **O(n)**
- **Space**: O(1)
- **Optimization**: O(n·k) → **O(n)** ✅

### **Key Takeaway:**
**Fixed-size sliding window** eliminates redundant recalculation by maintaining running state (sum) and updating incrementally. Perfect for problems with **constant window constraints**!

---

## **Related Problems:**
- [Longest Substring Without Repeating Characters](variant_1_longest_substring_no_repeat.md) - Variable window size
- [Minimum Size Subarray Sum](variant_5_min_size_subarray_sum.md) - Variable window with sum constraint
- [Sliding Window Maximum](variant_6_sliding_window_maximum.md) - Track max instead of sum

## **Tags:**
`sliding-window` `array` `fixed-window` `O(n)-state-space` `easy`
