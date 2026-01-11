# Maximum Subarray (Kadane's Algorithm)

**Difficulty:** Medium  
**LeetCode Problem:** #53  
**Tags:** `Dynamic Programming`, `Kadane's Algorithm`, `Running State`, `Greedy`

---

## Problem Statement

Given an integer array `nums`, find the **subarray** with the largest sum, and return its sum.

**Constraints:**
- `1 <= nums.length <= 10^5`
- `-10^4 <= nums[i] <= 10^4`

**Example 1:**
```
Input: nums = [-2,1,-3,4,-1,2,1,-5,4]
Output: 6
Explanation: The subarray [4,-1,2,1] has the largest sum 6.
```

**Example 2:**
```
Input: nums = [1]
Output: 1
```

**Example 3:**
```
Input: nums = [5,4,-1,7,8]
Output: 23
```

---

## Core Concepts

### Kadane's Algorithm - Key Insight
```
At each position i, decide:
  1. Extend previous subarray: currentMax + nums[i]
  2. Start fresh from i: nums[i]

Choose whichever is larger.

currentMax = max(nums[i], currentMax + nums[i])
globalMax = max(globalMax, currentMax)
```

### Example Walkthrough

Input: `nums = [-2,1,-3,4,-1,2,1,-5,4]`

```
Position 0: nums=-2
  currentMax = -2
  globalMax = -2

Position 1: nums=1
  Extend: -2 + 1 = -1
  Fresh: 1
  currentMax = max(-1, 1) = 1
  globalMax = 1

Position 2: nums=-3
  Extend: 1 + (-3) = -2
  Fresh: -3
  currentMax = max(-2, -3) = -2
  globalMax = 1

Position 3: nums=4
  Extend: -2 + 4 = 2
  Fresh: 4
  currentMax = max(2, 4) = 4
  globalMax = 4

Position 4: nums=-1
  currentMax = max(-1, 4 + (-1)) = 3
  globalMax = 4

Position 5: nums=2
  currentMax = max(2, 3 + 2) = 5
  globalMax = 5

Position 6: nums=1
  currentMax = max(1, 5 + 1) = 6
  globalMax = 6 ✓

Position 7: nums=-5
  currentMax = max(-5, 6 + (-5)) = 1
  globalMax = 6

Position 8: nums=4
  currentMax = max(4, 1 + 4) = 5
  globalMax = 6

Result: 6 (subarray [4,-1,2,1])
```

---

## Step 4: Optimal Solution (Kadane's Algorithm)

```csharp
public class Solution {
    public int MaxSubArray(int[] nums) {
        int currentMax = nums[0];
        int globalMax = nums[0];
        
        for (int i = 1; i < nums.Length; i++) {
            // Extend previous or start fresh
            currentMax = Math.Max(nums[i], currentMax + nums[i]);
            
            // Update global max
            globalMax = Math.Max(globalMax, currentMax);
        }
        
        return globalMax;
    }
}
```

**Complexity:**
- Time: O(n) - single pass
- Space: O(1) - constant variables

**Why it works:**
```
currentMax tracks: "Best subarray ending at current position"
If adding current element makes it worse than starting fresh,
we start a new subarray.

globalMax tracks: "Best we've seen so far anywhere"
```

---

## Alternative: DP Array Formulation

```csharp
public class Solution {
    public int MaxSubArray(int[] nums) {
        int n = nums.Length;
        int[] dp = new int[n];
        dp[0] = nums[0];
        int maxSum = dp[0];
        
        for (int i = 1; i < n; i++) {
            dp[i] = Math.Max(nums[i], dp[i - 1] + nums[i]);
            maxSum = Math.Max(maxSum, dp[i]);
        }
        
        return maxSum;
    }
}
```

**DP Definition:**
```
dp[i] = maximum subarray sum ending at index i
```

**Complexity:**
- Time: O(n)
- Space: O(n) - can optimize to O(1)

---

## Divide and Conquer Approach (O(n log n))

```csharp
public class Solution {
    public int MaxSubArray(int[] nums) {
        return MaxSubArrayHelper(nums, 0, nums.Length - 1);
    }
    
    private int MaxSubArrayHelper(int[] nums, int left, int right) {
        if (left == right) return nums[left];
        
        int mid = left + (right - left) / 2;
        
        int leftMax = MaxSubArrayHelper(nums, left, mid);
        int rightMax = MaxSubArrayHelper(nums, mid + 1, right);
        int crossMax = MaxCrossingSum(nums, left, mid, right);
        
        return Math.Max(Math.Max(leftMax, rightMax), crossMax);
    }
    
    private int MaxCrossingSum(int[] nums, int left, int mid, int right) {
        int leftSum = int.MinValue;
        int sum = 0;
        for (int i = mid; i >= left; i--) {
            sum += nums[i];
            leftSum = Math.Max(leftSum, sum);
        }
        
        int rightSum = int.MinValue;
        sum = 0;
        for (int i = mid + 1; i <= right; i++) {
            sum += nums[i];
            rightSum = Math.Max(rightSum, sum);
        }
        
        return leftSum + rightSum;
    }
}
```

---

## Edge Cases

1. **All negative:** Return least negative number
2. **Single element:** Return that element
3. **All positive:** Return sum of entire array
4. **Mix of positive and negative:** Kadane's handles optimally

---

## Related Problems

1. **Maximum Product Subarray (LeetCode #152)** - Product instead of sum
2. **Maximum Sum Circular Subarray (LeetCode #918)** - Circular array
3. **Best Time to Buy and Sell Stock (LeetCode #121)** - Similar structure
4. **House Robber (LeetCode #198)** - Non-adjacent variant

---

## Tags

`#dynamic-programming` `#kadane` `#running-state` `#greedy` `#medium`
