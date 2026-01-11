# Pattern: Dynamic Programming (1D)

## Pattern Description
**1D Dynamic Programming** is used when solutions can be built from smaller overlapping subproblems along a single dimension (array/sequence).

### What is it?
- **Maintain:** DP array `dp[i]` representing some property up to index `i`
- **Compute:** Each `dp[i]` from previous entries (`dp[i-1]`, `dp[i-2]`, etc.)
- **Optimize:** Space-optimize to O(1) by keeping only needed previous values

### Abstract Problems:
- **Counting paths:** Number of ways to reach position/sum
- **Optimization:** Max/min sum, cost, value with constraints
- **Sequence properties:** Subsequences, subarrays with specific properties
- **Coin/step problems:** Ways to form target with given options

### Key Characteristics:
- **Optimal substructure:** Solution contains optimal solutions to subproblems
- **Overlapping subproblems:** Same subproblems solved multiple times
- **State transition:** Clear recurrence relation
- **1D array:** State depends on single index

---

## Core Transition Patterns:
```csharp
// Pattern 1: Sum of previous k states (Climbing Stairs)
dp[i] = dp[i-1] + dp[i-2] + ... + dp[i-k]

// Pattern 2: Max/Min choice (House Robber)
dp[i] = Max(dp[i-1], dp[i-2] + val[i])

// Pattern 3: Kadane-style (extend or restart)
dp[i] = Max(nums[i], dp[i-1] + nums[i])

// Pattern 4: Counting ways to sum (Coin Change)
for each coin: dp[i] += dp[i - coin]
```

---

## Mental Model
Think of DP as **filling a table** where:
1. **Base cases** = known answers (dp[0], dp[1])
2. **Recurrence** = rule to compute dp[i] from previous values
3. **Final answer** = dp[n] or specific index
4. **Space optimization** = only keep last k values if recurrence uses dp[i-k]

---

<details>
<summary><b>Variant #1: Climbing Stairs</b></summary>

## Variant #1: Climbing Stairs

### Input/Output:
- **Input:** n = 5 (number of steps)
- **Output:** 8 (number of ways to climb 5 steps)
- **Rules:** Can climb 1 or 2 steps at a time

### Example:
```
Ways to climb n=3:
1. 1+1+1
2. 1+2
3. 2+1
Answer: 3
```

### Full State Space:
- **All possible sequences:** 2^n combinations of 1-step and 2-step moves
- **Example for n=5:**
  - [1,1,1,1,1]
  - [1,1,1,2]
  - [1,1,2,1]
  - [1,2,1,1]
  - [2,1,1,1]
  - [1,2,2]
  - [2,1,2]
  - [2,2,1]
  - And more...
- **Why exponential:** Each position can be reached by 2 choices → branching

```csharp
// Enumerate all sequences (exponential - DO NOT USE)
void GenerateAll(int remaining, List<int> path, List<List<int>> result)
{
    if (remaining == 0)
    {
        result.Add(new List<int>(path));
        return;
    }
    if (remaining < 0) return;
    
    path.Add(1);
    GenerateAll(remaining - 1, path, result);
    path.RemoveAt(path.Count - 1);
    
    path.Add(2);
    GenerateAll(remaining - 2, path, result);
    path.RemoveAt(path.Count - 1);
}
```

### Expected / Pruned State Space:
- **We don't need sequences** → only COUNT
- **Key insight:** To reach step i, must come from step (i-1) or (i-2)
- **Recurrence:** `dp[i] = dp[i-1] + dp[i-2]`
- **Pruning:** Instead of 2^n sequences → n subproblems
- **Space:** O(n) states → O(1) with optimization

### Brute Force Canonical Skeleton (Recursive):
```csharp
public class Solution
{
    // Exponential time - overlapping subproblems
    public int ClimbStairsBruteForce(int n)
    {
        if (n <= 1) return 1;
        
        return ClimbStairsBruteForce(n - 1) + ClimbStairsBruteForce(n - 2);
    }
}
```

### Brute Force Code Walkthrough / Variable Trace:
For n=5:

| Call | n | Returns | Computation |
|------|---|---------|-------------|
| ClimbStairs(5) | 5 | 8 | ClimbStairs(4) + ClimbStairs(3) |
| ClimbStairs(4) | 4 | 5 | ClimbStairs(3) + ClimbStairs(2) |
| ClimbStairs(3) | 3 | 3 | ClimbStairs(2) + ClimbStairs(1) |
| ClimbStairs(2) | 2 | 2 | ClimbStairs(1) + ClimbStairs(0) |
| ClimbStairs(1) | 1 | 1 | Base case |
| ClimbStairs(0) | 0 | 1 | Base case |

**Note:** ClimbStairs(3) called multiple times → overlapping subproblems!

### Optimized Solution (DP with Space Optimization):
```csharp
public class Solution
{
    // Bottom-up DP with O(1) space
    public int ClimbStairs(int n)
    {
        if (n <= 1) return 1;
        
        int prev2 = 1;  // dp[i-2]
        int prev1 = 1;  // dp[i-1]
        
        for (int i = 2; i <= n; i++)
        {
            int current = prev1 + prev2;  // dp[i] = dp[i-1] + dp[i-2]
            prev2 = prev1;
            prev1 = current;
        }
        
        return prev1;
    }
}
```

### Explanation of Pruning:
- **Brute force:** Recalculates same subproblems → O(2^n)
- **Memoization:** Store results → O(n) time, O(n) space
- **Bottom-up DP:** Build from base cases → O(n) time, O(n) space
- **Space optimization:** Only need last 2 values → O(n) time, O(1) space
- **Key insight:** Answer for i only depends on (i-1) and (i-2)

### Optimized Solution Code Walkthrough / Variable Trace:
For n=5:

| i | prev2 | prev1 | current | Action |
|---|-------|-------|---------|--------|
| - | 1 | 1 | - | Initialize (dp[0]=1, dp[1]=1) |
| 2 | 1 | 1 | 1+1=2 | dp[2]=2 |
| - | 1 | 2 | - | Update pointers |
| 3 | 1 | 2 | 1+2=3 | dp[3]=3 |
| - | 2 | 3 | - | Update pointers |
| 4 | 2 | 3 | 2+3=5 | dp[4]=5 |
| - | 3 | 5 | - | Update pointers |
| 5 | 3 | 5 | 3+5=8 | dp[5]=8 |

**Result:** 8 ways to climb 5 steps

### Big-O Analysis:
- **Brute Force Recursive:**
  - Time: O(2^n) → exponential branching
  - Space: O(n) → recursion stack depth
  
- **DP with Array:**
  - Time: O(n) → single pass
  - Space: O(n) → dp array
  
- **DP Space-Optimized:**
  - Time: O(n) → single pass
  - Space: O(1) → only 2 variables
  
- **Comparison:** 2^30 = 1 billion operations vs 30 operations!

</details>

---

<details>
<summary><b>Variant #2: House Robber (Max Non-Adjacent Sum)</b></summary>

## Variant #2: House Robber

### Input/Output:
- **Input:** nums = [2,7,9,3,1]
- **Output:** 12 (rob houses at indices 0,2,4 → 2+9+1=12)
- **Constraint:** Cannot rob two adjacent houses

### Example:
```
Houses: [2, 7, 9, 3, 1]
         0  1  2  3  4

Option 1: Rob 0,2,4 → 2+9+1 = 12 ✓
Option 2: Rob 1,3 → 7+3 = 10
Option 3: Rob 1,4 → 7+1 = 8
```

### Full State Space:
- **All subsets without adjacent elements:** exponential
- **Example subsets:**
  - {}
  - {0}, {1}, {2}, {3}, {4}
  - {0,2}, {0,3}, {0,4}, {1,3}, {1,4}, {2,4}
  - {0,2,4}, {0,3}, {1,3}, {1,4}
  - And more...
- **Count:** ~Fibonacci(n) valid subsets

```csharp
// Enumerate all valid subsets (exponential - DO NOT USE)
void GenerateSubsets(int[] nums, int idx, List<int> current, 
                     List<List<int>> result)
{
    result.Add(new List<int>(current));
    
    for (int i = idx; i < nums.Length; i++)
    {
        current.Add(i);
        GenerateSubsets(nums, i + 2, current, result);  // Skip adjacent
        current.RemoveAt(current.Count - 1);
    }
}
```

### Expected / Pruned State Space:
- **Don't need subsets** → only MAX sum
- **Key decision at each house:** Rob it (skip previous) OR skip it
- **State:** `dp[i]` = max money from houses 0...i
- **Recurrence:** `dp[i] = max(dp[i-1], dp[i-2] + nums[i])`
  - `dp[i-1]`: Skip current house
  - `dp[i-2] + nums[i]`: Rob current house (must skip i-1)
- **Pruning:** n subproblems instead of exponential subsets

### Brute Force Canonical Skeleton (Recursive):
```csharp
public class Solution
{
    public int RobBruteForce(int[] nums)
    {
        return RobFrom(nums, nums.Length - 1);
    }
    
    private int RobFrom(int[] nums, int i)
    {
        if (i < 0) return 0;
        
        // Choose to rob current house or skip it
        int robCurrent = RobFrom(nums, i - 2) + nums[i];
        int skipCurrent = RobFrom(nums, i - 1);
        
        return Math.Max(robCurrent, skipCurrent);
    }
}
```

### Brute Force Code Walkthrough / Variable Trace:
For nums = [2,7,9,3,1]:

| Call | i | nums[i] | robCurrent | skipCurrent | Returns |
|------|---|---------|------------|-------------|---------|
| RobFrom(4) | 4 | 1 | RobFrom(2)+1 | RobFrom(3) | max(12,11)=12 |
| RobFrom(3) | 3 | 3 | RobFrom(1)+3 | RobFrom(2) | max(10,11)=11 |
| RobFrom(2) | 2 | 9 | RobFrom(0)+9 | RobFrom(1) | max(11,7)=11 |
| RobFrom(1) | 1 | 7 | RobFrom(-1)+7 | RobFrom(0) | max(7,2)=7 |
| RobFrom(0) | 0 | 2 | RobFrom(-2)+2 | RobFrom(-1) | max(2,0)=2 |
| RobFrom(-1) | -1 | - | - | - | 0 (base) |

**Result:** 12

### Optimized Solution (DP with Space Optimization):
```csharp
public class Solution
{
    public int Rob(int[] nums)
    {
        if (nums.Length == 0) return 0;
        if (nums.Length == 1) return nums[0];
        
        int prev2 = 0;           // dp[i-2]
        int prev1 = nums[0];     // dp[i-1]
        
        for (int i = 1; i < nums.Length; i++)
        {
            int robCurrent = prev2 + nums[i];     // Rob current house
            int skipCurrent = prev1;              // Skip current house
            int current = Math.Max(robCurrent, skipCurrent);
            
            prev2 = prev1;
            prev1 = current;
        }
        
        return prev1;
    }
}
```

### Explanation of Pruning:
- **Brute force:** Try all valid subsets → exponential
- **DP insight:** At each house, only 2 choices matter
  - Rob it → get nums[i] + best from i-2
  - Skip it → get best from i-1
- **State reduction:** Only need last 2 results → O(1) space
- **Pruning:** From ~Fib(n) subsets to n decisions

### Optimized Solution Code Walkthrough / Variable Trace:
For nums = [2,7,9,3,1]:

| i | nums[i] | prev2 | prev1 | robCurrent | skipCurrent | current | Decision |
|---|---------|-------|-------|------------|-------------|---------|----------|
| - | - | 0 | 2 | - | - | - | Initialize |
| 1 | 7 | 0 | 2 | 0+7=7 | 2 | max(7,2)=7 | Rob house 1 |
| - | - | 2 | 7 | - | - | - | Update |
| 2 | 9 | 2 | 7 | 2+9=11 | 7 | max(11,7)=11 | Rob house 2 |
| - | - | 7 | 11 | - | - | - | Update |
| 3 | 3 | 7 | 11 | 7+3=10 | 11 | max(10,11)=11 | Skip house 3 |
| - | - | 11 | 11 | - | - | - | Update |
| 4 | 1 | 11 | 11 | 11+1=12 | 11 | max(12,11)=12 | Rob house 4 |

**Result:** 12 (houses 0,2,4 → 2+9+1)

### Big-O Analysis:
- **Brute Force:**
  - Time: O(2^n) → try all subsets
  - Space: O(n) → recursion depth
  
- **DP with Array:**
  - Time: O(n) → single pass
  - Space: O(n) → dp array
  
- **DP Space-Optimized:**
  - Time: O(n) → single pass
  - Space: O(1) → only 2 variables

</details>

---

<details>
<summary><b>Variant #3: Min Cost Climbing Stairs</b></summary>

## Variant #3: Min Cost Climbing Stairs

### Input/Output:
- **Input:** cost = [10,15,20] (cost to leave each step)
- **Output:** 15 (start at step 1, pay 15, jump to top)
- **Rules:** Can climb 1 or 2 steps; pay cost[i] to leave step i

### Approach:
```csharp
public int MinCostClimbingStairs(int[] cost)
{
    int n = cost.Length;
    int prev2 = 0;  // Cost to reach step i-2
    int prev1 = 0;  // Cost to reach step i-1
    
    for (int i = 2; i <= n; i++)
    {
        int current = Math.Min(prev1 + cost[i-1], prev2 + cost[i-2]);
        prev2 = prev1;
        prev1 = current;
    }
    
    return prev1;
}
```

### Recurrence:
`dp[i] = min(dp[i-1] + cost[i-1], dp[i-2] + cost[i-2])`

### Big-O: O(n) time, O(1) space

</details>

---

<details>
<summary><b>Variant #4: Maximum Subarray Sum (Kadane's Algorithm)</b></summary>

## Variant #4: Maximum Subarray Sum (Kadane)

### Input/Output:
- **Input:** nums = [-2,1,-3,4,-1,2,1,-5,4]
- **Output:** 6 (subarray [4,-1,2,1])

### Key Insight:
At each position, decide: **extend current subarray** OR **start new subarray**

### Optimized Solution:
```csharp
public int MaxSubArray(int[] nums)
{
    int maxSoFar = nums[0];
    int maxEndingHere = nums[0];
    
    for (int i = 1; i < nums.Length; i++)
    {
        // Extend or restart
        maxEndingHere = Math.Max(nums[i], maxEndingHere + nums[i]);
        maxSoFar = Math.Max(maxSoFar, maxEndingHere);
    }
    
    return maxSoFar;
}
```

### Variable Trace:
For nums = [-2,1,-3,4,-1,2,1,-5,4]:

| i | nums[i] | maxEndingHere | maxSoFar | Decision |
|---|---------|---------------|----------|----------|
| 0 | -2 | -2 | -2 | Start |
| 1 | 1 | max(1, -2+1)=1 | 1 | Restart |
| 2 | -3 | max(-3, 1-3)=-2 | 1 | Extend |
| 3 | 4 | max(4, -2+4)=4 | 4 | Restart |
| 4 | -1 | max(-1, 4-1)=3 | 4 | Extend |
| 5 | 2 | max(2, 3+2)=5 | 5 | Extend |
| 6 | 1 | max(1, 5+1)=6 | 6 | Extend |
| 7 | -5 | max(-5, 6-5)=1 | 6 | Extend |
| 8 | 4 | max(4, 1+4)=5 | 6 | Extend |

**Result:** 6

### Big-O: O(n) time, O(1) space

</details>

---

<details>
<summary><b>Variant #5: Coin Change (Minimum Coins)</b></summary>

## Variant #5: Coin Change

### Input/Output:
- **Input:** coins = [1,2,5], amount = 11
- **Output:** 3 (11 = 5+5+1)

### Approach:
```csharp
public int CoinChange(int[] coins, int amount)
{
    int[] dp = new int[amount + 1];
    Array.Fill(dp, amount + 1);  // Impossible value
    dp[0] = 0;
    
    for (int i = 1; i <= amount; i++)
    {
        foreach (int coin in coins)
        {
            if (i >= coin)
            {
                dp[i] = Math.Min(dp[i], dp[i - coin] + 1);
            }
        }
    }
    
    return dp[amount] > amount ? -1 : dp[amount];
}
```

### Recurrence:
`dp[i] = min(dp[i], dp[i - coin] + 1)` for each coin

### Big-O: O(amount × coins) time, O(amount) space

</details>

---

<details>
<summary><b>Variant #6: Coin Change II (Count Ways)</b></summary>

## Variant #6: Coin Change II (Count Combinations)

### Input/Output:
- **Input:** amount = 5, coins = [1,2,5]
- **Output:** 4 ways ([1,1,1,1,1], [1,1,1,2], [1,2,2], [5])

### Approach:
```csharp
public int Change(int amount, int[] coins)
{
    int[] dp = new int[amount + 1];
    dp[0] = 1;  // One way to make 0
    
    foreach (int coin in coins)
    {
        for (int i = coin; i <= amount; i++)
        {
            dp[i] += dp[i - coin];
        }
    }
    
    return dp[amount];
}
```

### Recurrence:
For each coin: `dp[i] += dp[i - coin]`

### Big-O: O(amount × coins) time, O(amount) space

</details>

---

<details>
<summary><b>Variant #7: Target Sum (Count Ways with +/−)</b></summary>

## Variant #7: Target Sum

### Input/Output:
- **Input:** nums = [1,1,1,1,1], target = 3
- **Output:** 5 (ways to assign +/− to get 3)

### Key Insight:
Convert to subset sum: find subset P where sum(P) - sum(rest) = target

### Approach:
```csharp
public int FindTargetSumWays(int[] nums, int target)
{
    int sum = nums.Sum();
    if (Math.Abs(target) > sum || (sum + target) % 2 != 0) return 0;
    
    int subsetSum = (sum + target) / 2;
    
    int[] dp = new int[subsetSum + 1];
    dp[0] = 1;
    
    foreach (int num in nums)
    {
        for (int i = subsetSum; i >= num; i--)
        {
            dp[i] += dp[i - num];
        }
    }
    
    return dp[subsetSum];
}
```

### Big-O: O(n × sum) time, O(sum) space

</details>

---

<details>
<summary><b>Variant #8: Longest Increasing Subsequence (LIS)</b></summary>

## Variant #8: Longest Increasing Subsequence

### Input/Output:
- **Input:** nums = [10,9,2,5,3,7,101,18]
- **Output:** 4 (LIS: [2,3,7,101] or [2,3,7,18])

### DP Approach:
```csharp
public int LengthOfLIS(int[] nums)
{
    int[] dp = new int[nums.Length];
    Array.Fill(dp, 1);  // Each element is LIS of length 1
    
    for (int i = 1; i < nums.Length; i++)
    {
        for (int j = 0; j < i; j++)
        {
            if (nums[j] < nums[i])
            {
                dp[i] = Math.Max(dp[i], dp[j] + 1);
            }
        }
    }
    
    return dp.Max();
}
```

### Recurrence:
`dp[i] = max(dp[j] + 1)` for all j < i where nums[j] < nums[i]

### Big-O: O(n²) time, O(n) space
(Binary search optimization → O(n log n))

</details>

---

# Key Takeaways

## Common DP Patterns

### 1. Fibonacci-Style (Climbing Stairs)
- **Recurrence:** `dp[i] = dp[i-1] + dp[i-2]`
- **Space optimization:** Keep only last k values
- **Example:** Climbing Stairs, Fibonacci, Tribonacci

### 2. Max/Min Choice (House Robber)
- **Recurrence:** `dp[i] = max(take_i, skip_i)`
- **Constraint handling:** Skip adjacent elements
- **Example:** House Robber, Max Non-Adjacent Sum

### 3. Kadane-Style (Extend or Restart)
- **Recurrence:** `dp[i] = max(nums[i], dp[i-1] + nums[i])`
- **Use when:** Finding optimal subarray/substring
- **Example:** Maximum Subarray, Buy/Sell Stock

### 4. Subset Sum (Coin Change)
- **Recurrence:** `dp[i] += dp[i - x]` for each element x
- **Use when:** Counting/optimizing combinations
- **Example:** Coin Change, Target Sum, Partition Equal Subset Sum

### 5. LIS-Style (Longest Increasing Subsequence)
- **Recurrence:** `dp[i] = max(dp[j] + 1)` for valid j < i
- **Use when:** Finding optimal subsequence with property
- **Example:** LIS, Russian Doll Envelopes

## Space Optimization Techniques

### When dp[i] depends on dp[i-1], dp[i-2]:
```csharp
// Instead of: int[] dp = new int[n];
int prev2 = base_case;
int prev1 = base_case;

for (int i = 2; i <= n; i++)
{
    int current = f(prev1, prev2);
    prev2 = prev1;
    prev1 = current;
}
```

### When dp[i] depends on dp[i - coin]:
```csharp
// Use 1D array, iterate carefully
int[] dp = new int[amount + 1];
dp[0] = base;

foreach (coin in coins)
{
    for (int i = coin; i <= amount; i++)
    {
        dp[i] = f(dp[i], dp[i - coin]);
    }
}
```

## Problem Identification Checklist

### DP applies when:
- [ ] **Overlapping subproblems:** Same calculations repeated
- [ ] **Optimal substructure:** Optimal solution contains optimal subsolutions
- [ ] **Counting/optimization:** Count ways OR find max/min
- [ ] **Sequential decisions:** Process elements one by one
- [ ] **Small constraints:** n ≤ 10^4 (indicates O(n) or O(n²) is acceptable)

### DP doesn't apply when:
- ❌ **No overlapping subproblems:** Divide and conquer better (merge sort)
- ❌ **Greedy works:** Local optimal = global optimal
- ❌ **Graph structure:** Use BFS/DFS instead

## Core Templates

### Bottom-Up Template:
```csharp
public int DPSolution(int[] input, int n)
{
    // 1. Create DP array
    int[] dp = new int[n + 1];
    
    // 2. Initialize base cases
    dp[0] = base_case_0;
    dp[1] = base_case_1;
    
    // 3. Fill table using recurrence
    for (int i = 2; i <= n; i++)
    {
        dp[i] = f(dp[i-1], dp[i-2], input[i]);
    }
    
    // 4. Return final answer
    return dp[n];
}
```

### Space-Optimized Template:
```csharp
public int DPOptimized(int[] input, int n)
{
    int prev2 = base_case_0;
    int prev1 = base_case_1;
    
    for (int i = 2; i <= n; i++)
    {
        int current = f(prev1, prev2, input[i]);
        prev2 = prev1;
        prev1 = current;
    }
    
    return prev1;
}
```

## Big-O Patterns

| Pattern | Time | Space | Space-Optimized |
|---------|------|-------|-----------------|
| **Fibonacci-style** | O(n) | O(n) | O(1) |
| **House Robber** | O(n) | O(n) | O(1) |
| **Kadane** | O(n) | O(1) | O(1) |
| **Coin Change** | O(n × m) | O(n) | O(n) |
| **LIS** | O(n²) | O(n) | O(n) |

---

# Mental Checklist for 1D DP

- [ ] **Is there optimal substructure?** → Can I build solution from subproblems?
- [ ] **Are subproblems overlapping?** → Same calculations repeated?
- [ ] **What's the recurrence relation?** → How does dp[i] relate to previous?
- [ ] **What are base cases?** → dp[0], dp[1] = ?
- [ ] **Can I space-optimize?** → Does dp[i] only need last k values?
- [ ] **Is there a greedy alternative?** → Check before implementing DP
- [ ] **What's the DP array meaning?** → dp[i] represents ___?
- [ ] **Is it counting or optimization?** → Sum or max/min?

**If building solutions from smaller subproblems → 1D DP applies.**
