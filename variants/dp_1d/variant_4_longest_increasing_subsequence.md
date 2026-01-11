# Longest Increasing Subsequence

**Difficulty:** Medium  
**LeetCode Problem:** #300  
**Tags:** `Dynamic Programming`, `Binary Search`, `LIS`, `Subsequence`

---

## Problem Statement

Given an integer array `nums`, return the length of the longest **strictly increasing subsequence**.

A **subsequence** is an array that can be derived from another array by deleting some or no elements without changing the order of the remaining elements.

**Constraints:**
- `1 <= nums.length <= 2500`
- `-10^4 <= nums[i] <= 10^4`

**Example 1:**
```
Input: nums = [10,9,2,5,3,7,101,18]
Output: 4
Explanation: The longest increasing subsequence is [2,3,7,101], length = 4.
```

**Example 2:**
```
Input: nums = [0,1,0,3,2,3]
Output: 4
```

---

## Core Concepts

### State Definition
```
dp[i] = length of longest increasing subsequence ending at index i

Must include nums[i] in the subsequence.
```

### Recurrence Relation
```
dp[i] = max over all j < i where nums[j] < nums[i] {
  dp[j] + 1
}

Base case: dp[i] = 1 (single element is a subsequence)
```

### Example Computation

Input: `nums = [10,9,2,5,3,7,101,18]`

```
DP Table:
Index:  0   1  2  3  4  5   6    7
nums:  10   9  2  5  3  7  101  18
dp:     1   1  1  2  2  3   4    4

At i=3 (nums[3]=5):
  Check j=0: nums[0]=10 > 5, skip
  Check j=1: nums[1]=9 > 5, skip
  Check j=2: nums[2]=2 < 5, dp[3] = dp[2] + 1 = 2
  
At i=6 (nums[6]=101):
  Check all j < 6 where nums[j] < 101
  Best: dp[5]=3 (ending at 7)
  dp[6] = 3 + 1 = 4

Result: max(dp) = 4
```

---

## Step 4: Optimal Solution (DP Approach)

```csharp
public class Solution {
    public int LengthOfLIS(int[] nums) {
        int n = nums.Length;
        int[] dp = new int[n];
        Array.Fill(dp, 1);  // Each element is a subsequence of length 1
        
        int maxLen = 1;
        
        for (int i = 1; i < n; i++) {
            for (int j = 0; j < i; j++) {
                if (nums[j] < nums[i]) {
                    dp[i] = Math.Max(dp[i], dp[j] + 1);
                }
            }
            maxLen = Math.Max(maxLen, dp[i]);
        }
        
        return maxLen;
    }
}
```

**Complexity:**
- Time: O(n²)
- Space: O(n)

---

## Alternative: Binary Search + Greedy (O(n log n))

```csharp
public class Solution {
    public int LengthOfLIS(int[] nums) {
        var tails = new List<int>();
        
        foreach (int num in nums) {
            // Binary search for position
            int left = 0, right = tails.Count;
            
            while (left < right) {
                int mid = left + (right - left) / 2;
                if (tails[mid] < num) {
                    left = mid + 1;
                } else {
                    right = mid;
                }
            }
            
            // Replace or append
            if (left == tails.Count) {
                tails.Add(num);
            } else {
                tails[left] = num;
            }
        }
        
        return tails.Count;
    }
}
```

**Key Idea:**
- Maintain array `tails` where `tails[i]` = smallest ending value of increasing subsequence of length i+1
- For each num, find position using binary search
- Replace or extend

**Complexity:**
- Time: O(n log n)
- Space: O(n)

---

## Related Problems

1. **Number of LIS (LeetCode #673)** - Count longest increasing subsequences
2. **Russian Doll Envelopes (LeetCode #354)** - 2D LIS
3. **Maximum Length of Pair Chain (LeetCode #646)** - Similar structure
4. **Longest Consecutive Sequence (LeetCode #128)** - Different approach

---

## Tags

`#dynamic-programming` `#binary-search` `#lis` `#subsequence` `#medium`
