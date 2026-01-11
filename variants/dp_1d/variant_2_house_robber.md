# House Robber

**Difficulty:** Medium  
**LeetCode Problem:** #198  
**Tags:** `Dynamic Programming`, `Non-Adjacent Selection`, `State Machine`, `Optimization`

---

## Problem Statement

You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed, the only constraint stopping you from robbing each of them is that adjacent houses have security systems connected and **it will automatically contact the police if two adjacent houses were broken into on the same night**.

Given an integer array `nums` representing the amount of money of each house, return the maximum amount of money you can rob tonight **without alerting the police**.

**Constraints:**
- `1 <= nums.length <= 100`
- `0 <= nums[i] <= 400`

**Example 1:**
```
Input: nums = [1,2,3,1]
Output: 4
Explanation: Rob house 1 (money = 1) and then rob house 3 (money = 3).
Total amount you can rob = 1 + 3 = 4.
```

**Example 2:**
```
Input: nums = [2,7,9,3,1]
Output: 12
Explanation: Rob house 1 (money = 2), rob house 3 (money = 9) and rob house 5 (money = 1).
Total amount you can rob = 2 + 9 + 1 = 12.
```

---

## Step 1: State Space

### Problem Visualization

Input: `nums = [2,7,9,3,1]`

```
Houses:     [2,  7,  9,  3,  1]
Index:       0   1   2   3   4

Constraint: Cannot rob adjacent houses

Valid robbery sequences:
  Rob houses {0, 2, 4}: 2 + 9 + 1 = 12 ✓ (max)
  Rob houses {0, 2}: 2 + 9 = 11
  Rob houses {0, 3}: 2 + 3 = 5
  Rob houses {0, 4}: 2 + 1 = 3
  Rob houses {1, 3}: 7 + 3 = 10
  Rob houses {1, 4}: 7 + 1 = 8
  Rob houses {2, 4}: 9 + 1 = 10
  Rob houses {0}: 2
  Rob houses {1}: 7
  Rob houses {2}: 9
  Rob houses {3}: 3
  Rob houses {4}: 1

Invalid (adjacent):
  Rob houses {0, 1}: Adjacent!
  Rob houses {1, 2}: Adjacent!
  Rob houses {2, 3}: Adjacent!
```

**Decision tree for [2,7,9,3,1]:**

```
At each house i, two choices:
  1. Rob it: add nums[i], can't rob i-1
  2. Skip it: keep max from i-1

                    House 0 (2)
              /                    \
         Rob (2)                Skip (0)
           |                        |
      House 1 (7)              House 1 (7)
      /        \                /        \
   Skip       Rob            Skip      Rob
   (2)      (can't)          (0)       (7)
    |                         |         |
House 2 (9)              House 2 (9) House 2 (9)
 /      \                 /      \      /      \
Rob    Skip            Rob    Skip   Skip    Rob
(11)   (2)             (9)    (0)    (7)   (can't)
 |      |               |      |      |
...    ...             ...    ...    ...

Max at each level gets carried forward.
```

### Core Question

**What is the maximum sum we can obtain by selecting non-adjacent elements?**

This is a classic DP problem with choice: rob current house or skip it.

### Deriving from First Principles

**Observation 1: Decision at each house**
```
At house i, two options:

Option 1: Rob house i
  - Get nums[i] money
  - Cannot rob house i-1
  - Must use max from houses 0..i-2
  - Total: nums[i] + dp[i-2]

Option 2: Skip house i
  - Get 0 from this house
  - Keep best from houses 0..i-1
  - Total: dp[i-1]

Choose max of both options.
```

**Observation 2: State definition**
```
dp[i] = maximum money robbed from houses 0..i

dp[i] represents:
  "What's the best I can do considering houses up to i?"

Not:
  "How much if I rob house i?"
  (That would be a different formulation)
```

**Observation 3: Recurrence relation**
```
dp[i] = max(
  nums[i] + dp[i-2],  // Rob house i
  dp[i-1]              // Skip house i
)

Base cases:
  dp[0] = nums[0]  (only one house, rob it)
  dp[1] = max(nums[0], nums[1])  (rob better of first two)
```

**Observation 4: Why this works**
```
If we rob house i:
  We get nums[i]
  Plus best from houses 0..i-2 (can't use i-1)

If we skip house i:
  We get best from houses 0..i-1
  (which already considered all valid combinations)

Taking max ensures optimal substructure.
```

**Observation 5: Overlapping subproblems**
```
Computing dp[5] needs dp[4] and dp[3]
Computing dp[4] needs dp[3] and dp[2]

dp[3] used twice → overlapping subproblems
Perfect for DP!
```

**Observation 6: Space optimization**
```
Only need last 2 values:
  dp[i-2] and dp[i-1]

Can use two variables instead of array:
  Space: O(n) → O(1)
```

**Observation 7: Complexity**
```
Time: O(n)
  Single pass through houses
  Constant work per house

Space: O(n) with array
       O(1) with variables
```

**Formula (Bottom-up DP):**
```
if n == 1: return nums[0]

dp[0] = nums[0]
dp[1] = max(nums[0], nums[1])

for i from 2 to n-1:
  dp[i] = max(nums[i] + dp[i-2], dp[i-1])

return dp[n-1]
```

**Formula (Space-optimized):**
```
if n == 1: return nums[0]

prev2 = nums[0]
prev1 = max(nums[0], nums[1])

for i from 2 to n-1:
  current = max(nums[i] + prev2, prev1)
  prev2 = prev1
  prev1 = current

return prev1
```

### State Space Structure

**Type:** Linear 1D DP with choice (rob or skip).

**Structure:**
- States: 0 to n-1 (houses)
- Each state: depends on previous 2 states
- Binary choice at each state

**Cardinality:**
- States: n
- Time: O(n)
- Space: O(n) or O(1)

**Key Property:** Non-adjacent selection with optimal substructure.

### Example Computation

Input: `nums = [2,7,9,3,1]`, n = 5

```
Initialize:
  dp[0] = nums[0] = 2
  dp[1] = max(nums[0], nums[1]) = max(2, 7) = 7

Compute:

i=2 (house with 9):
  Rob: nums[2] + dp[0] = 9 + 2 = 11
  Skip: dp[1] = 7
  dp[2] = max(11, 7) = 11
  (Rob houses 0 and 2)

i=3 (house with 3):
  Rob: nums[3] + dp[1] = 3 + 7 = 10
  Skip: dp[2] = 11
  dp[3] = max(10, 11) = 11
  (Skip house 3, keep houses 0 and 2)

i=4 (house with 1):
  Rob: nums[4] + dp[3] = 1 + 11 = 12
  Skip: dp[3] = 11
  dp[4] = max(12, 11) = 12
  (Rob houses 0, 2, and 4)

Result: dp[4] = 12

DP Table:
House:  0   1   2   3   4
Value:  2   7   9   3   1
DP:     2   7  11  11  12
```

**Trace with space optimization:**

```
Initial:
  prev2 = nums[0] = 2
  prev1 = max(2, 7) = 7

i=2:
  current = max(9 + prev2, prev1)
          = max(9 + 2, 7)
          = max(11, 7) = 11
  prev2 = prev1 = 7
  prev1 = current = 11

i=3:
  current = max(3 + prev2, prev1)
          = max(3 + 7, 11)
          = max(10, 11) = 11
  prev2 = prev1 = 11
  prev1 = current = 11

i=4:
  current = max(1 + prev2, prev1)
          = max(1 + 11, 11)
          = max(12, 11) = 12
  prev2 = prev1 = 11
  prev1 = current = 12

Result: prev1 = 12
```

### Generation Pattern

**Brute force (recursive):**
```csharp
int Rob(int[] nums, int i) {
    if (i < 0) return 0;
    if (i == 0) return nums[0];
    return max(nums[i] + Rob(nums, i-2), Rob(nums, i-1));
}
// Time: O(2^n) - exponential
```

**Optimized (DP):**
```csharp
// Time: O(n), Space: O(1)
```

---

## Step 2: Approaches

### Approach 1: Recursive (Brute Force)
Try all non-adjacent combinations

### Approach 2: Top-Down DP (Memoization)
Cache results to avoid recomputation

### Approach 3: Bottom-Up DP with Array
Build solution iteratively

### Approach 4: Bottom-Up with Space Optimization
Use two variables

---

## Step 3: Optimization Ideas

### Key Insights
1. **Binary choice:** Rob or skip each house
2. **Optimal substructure:** Max at i depends on max at i-1 and i-2
3. **Only need last 2:** Space O(n) → O(1)
4. **Base cases:** Handle n=1 and n=2 separately

---

## Step 4: Optimal Solution (Space-Optimized DP)

```csharp
public class Solution {
    public int Rob(int[] nums) {
        int n = nums.Length;
        
        // Base case
        if (n == 1) return nums[0];
        
        // Initialize with first two houses
        int prev2 = nums[0];                      // dp[i-2]
        int prev1 = Math.Max(nums[0], nums[1]);   // dp[i-1]
        
        // Process remaining houses
        for (int i = 2; i < n; i++) {
            // Choose: rob current + prev2, or skip (keep prev1)
            int current = Math.Max(nums[i] + prev2, prev1);
            
            // Slide the window
            prev2 = prev1;
            prev1 = current;
        }
        
        return prev1;
    }
}
```

**Complexity:**
- Time: O(n) - single pass
- Space: O(1) - two variables

**Why this works:**
```
At each house i, we only care about:
  - Best result if we robbed up to i-2 (so we can rob i)
  - Best result if we robbed up to i-1 (skip i)

Don't need full history, just last 2 values.
```

---

## Alternative: Bottom-Up DP with Array

```csharp
public class Solution {
    public int Rob(int[] nums) {
        int n = nums.Length;
        if (n == 1) return nums[0];
        
        int[] dp = new int[n];
        dp[0] = nums[0];
        dp[1] = Math.Max(nums[0], nums[1]);
        
        for (int i = 2; i < n; i++) {
            dp[i] = Math.Max(nums[i] + dp[i - 2], dp[i - 1]);
        }
        
        return dp[n - 1];
    }
}
```

**Complexity:**
- Time: O(n)
- Space: O(n)

**Trade-off:** More intuitive for understanding DP table.

---

## Alternative: Top-Down DP (Memoization)

```csharp
public class Solution {
    private int[] memo;
    
    public int Rob(int[] nums) {
        memo = new int[nums.Length];
        Array.Fill(memo, -1);
        return RobHelper(nums, nums.Length - 1);
    }
    
    private int RobHelper(int[] nums, int i) {
        // Base cases
        if (i < 0) return 0;
        if (i == 0) return nums[0];
        
        // Check memo
        if (memo[i] != -1) return memo[i];
        
        // Compute: rob current or skip
        int rob = nums[i] + RobHelper(nums, i - 2);
        int skip = RobHelper(nums, i - 1);
        
        memo[i] = Math.Max(rob, skip);
        return memo[i];
    }
}
```

**Complexity:**
- Time: O(n)
- Space: O(n) - recursion + memo

---

## Edge Cases

1. **Single house:** `[10]` → 10
2. **Two houses:** `[1,2]` → 2 (rob better one)
3. **All same:** `[5,5,5,5]` → 10 (rob alternating)
4. **Decreasing:** `[9,6,3,1]` → 12 (rob 0 and 2)
5. **One large value:** `[1,100,1,1]` → 100 (rob house 1)

---

## Visualization

```
DP Table for [2,7,9,3,1]:

House i:    0    1    2    3    4
nums[i]:    2    7    9    3    1
dp[i]:      2    7   11   11   12
            ↑    ↑    ↑
           base max  rob 0+2

Decision at i=2:
  Rob 2: 9 + dp[0] = 9 + 2 = 11
  Skip 2: dp[1] = 7
  Choose: max(11, 7) = 11

Decision at i=4:
  Rob 4: 1 + dp[2] = 1 + 11 = 12 ✓
  Skip 4: dp[3] = 11
  Choose: max(12, 11) = 12
```

---

## Related Problems

1. **House Robber II (LeetCode #213)** - Houses in a circle
2. **House Robber III (LeetCode #337)** - Houses in binary tree
3. **Delete and Earn (LeetCode #740)** - Similar non-adjacent selection
4. **Maximum Alternating Subsequence Sum (LeetCode #1911)** - Sign alternating
5. **Coin Change (LeetCode #322)** - Different DP structure

---

## Pattern Recognition

**Problem Asks For:**
- Maximum sum/value
- Non-adjacent selection constraint
- Optimization problem

**This Suggests:**
- Dynamic Programming
- Binary choice at each step
- State: max value up to index i
- → **Non-Adjacent Selection DP Pattern**

**Key Indicators:**
- "Cannot select adjacent items"
- "Maximize sum/value"
- Linear arrangement
- Each element contributes independently

---

## Tags

`#dynamic-programming` `#1d-dp` `#non-adjacent` `#optimization` `#state-machine` `#medium`
