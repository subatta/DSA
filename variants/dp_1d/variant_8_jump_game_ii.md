# Jump Game II (Minimum Jumps)

**Difficulty:** Medium  
**LeetCode Problem:** #45  
**Tags:** `Dynamic Programming`, `Greedy`, `BFS`, `Optimization`

---

## Problem Statement

You are given a **0-indexed** array of integers `nums` of length `n`. You are initially positioned at `nums[0]`.

Each element `nums[i]` represents the maximum length of a forward jump from index `i`. In other words, if you are at `nums[i]`, you can jump to any `nums[i + j]` where:
- `0 <= j <= nums[i]`
- `i + j < n`

Return the **minimum number of jumps** to reach `nums[n - 1]`. The test cases are generated such that you can reach `nums[n - 1]`.

**Constraints:**
- `1 <= nums.length <= 10^4`
- `0 <= nums[i] <= 1000`
- It's guaranteed that you can reach `nums[n - 1]`.

**Example 1:**
```
Input: nums = [2,3,1,1,4]
Output: 2
Explanation: Jump 1 step from index 0 to 1, then 3 steps to the last index.
```

**Example 2:**
```
Input: nums = [2,3,0,1,4]
Output: 2
```

---

## Core Concepts

### Greedy Approach (Optimal - O(n))

**Key Insight:**
```
Use BFS-like level traversal
Track: current jump's range [currentStart, currentEnd]
For each jump level, find the farthest position reachable

No need to try all positions - just track farthest reach.
```

### Algorithm
```
jumps = 0
currentEnd = 0 (end of current jump range)
farthest = 0 (farthest reachable in next jump)

for i from 0 to n-2:
  farthest = max(farthest, i + nums[i])
  
  if i == currentEnd:
    jumps++
    currentEnd = farthest
    
    if currentEnd >= n-1:
      break

return jumps
```

### Example Walkthrough

Input: `nums = [2,3,1,1,4]`

```
Index:     0  1  2  3  4
nums:      2  3  1  1  4
           ↑
         start

Jump 0 (implicit, at index 0):
  currentEnd = 0
  farthest = 0
  
i=0:
  farthest = max(0, 0+2) = 2
  i == currentEnd (0):
    jumps = 1
    currentEnd = 2
    
i=1:
  farthest = max(2, 1+3) = 4
  i != currentEnd
  
i=2:
  farthest = max(4, 2+1) = 4
  i == currentEnd (2):
    jumps = 2
    currentEnd = 4 >= n-1, done!

Result: 2 jumps
Path: 0 → 1 → 4 (jump to index 1, then to index 4)
```

---

## Step 4: Optimal Solution (Greedy)

```csharp
public class Solution {
    public int Jump(int[] nums) {
        int n = nums.Length;
        if (n == 1) return 0;
        
        int jumps = 0;
        int currentEnd = 0;
        int farthest = 0;
        
        for (int i = 0; i < n - 1; i++) {
            // Update farthest reachable from current position
            farthest = Math.Max(farthest, i + nums[i]);
            
            // Reached end of current jump range
            if (i == currentEnd) {
                jumps++;
                currentEnd = farthest;
                
                // Early exit if can reach end
                if (currentEnd >= n - 1) {
                    break;
                }
            }
        }
        
        return jumps;
    }
}
```

**Complexity:**
- Time: O(n) - single pass
- Space: O(1) - constant variables

**Why this works:**
```
Think of it as BFS levels:
  Level 0: positions reachable in 0 jumps (just start)
  Level 1: positions reachable in 1 jump
  Level 2: positions reachable in 2 jumps
  ...

We don't need to track all positions, just:
  - Current level's boundary (currentEnd)
  - Next level's farthest reach (farthest)
```

---

## Alternative: DP Approach (O(n²))

```csharp
public class Solution {
    public int Jump(int[] nums) {
        int n = nums.Length;
        int[] dp = new int[n];
        Array.Fill(dp, int.MaxValue);
        dp[0] = 0;
        
        for (int i = 0; i < n; i++) {
            for (int j = 1; j <= nums[i] && i + j < n; j++) {
                dp[i + j] = Math.Min(dp[i + j], dp[i] + 1);
            }
        }
        
        return dp[n - 1];
    }
}
```

**DP Definition:**
```
dp[i] = minimum jumps to reach index i
```

**Complexity:**
- Time: O(n × max(nums[i])) ≈ O(n²) in worst case
- Space: O(n)

**Trade-off:** DP is more intuitive but slower than greedy.

---

## Alternative: BFS (Explicit Levels)

```csharp
public class Solution {
    public int Jump(int[] nums) {
        int n = nums.Length;
        if (n == 1) return 0;
        
        int jumps = 0;
        int currentLevelEnd = 0;
        int nextLevelEnd = 0;
        
        for (int i = 0; i <= currentLevelEnd; i++) {
            nextLevelEnd = Math.Max(nextLevelEnd, i + nums[i]);
            
            if (nextLevelEnd >= n - 1) {
                return jumps + 1;
            }
            
            if (i == currentLevelEnd) {
                jumps++;
                currentLevelEnd = nextLevelEnd;
            }
        }
        
        return jumps;
    }
}
```

**Same complexity as greedy, more explicit BFS structure.**

---

## Visualization

```
nums = [2,3,1,1,4]

Level 0:  [0]           (start)
          ↓ can reach 0+2=2
          
Level 1:  [1,2]         (jump 1)
          ↓ from 1: reach 1+3=4
          
Level 2:  [3,4]         (jump 2)
          ↓ reached end!

Minimum jumps: 2

Path: 0 → 1 → 4
```

---

## Key Insights

1. **Greedy works:** Always jumping to position that extends farthest reach is optimal
2. **BFS structure:** Think in terms of jump levels
3. **No backtracking:** Once we've jumped to a range, don't revisit earlier positions
4. **Linear time possible:** No need to explore all O(n²) transitions

---

## Related Problems

1. **Jump Game (LeetCode #55)** - Check if can reach end (easier)
2. **Jump Game III (LeetCode #1306)** - Jump forward/backward
3. **Jump Game VII (LeetCode #1871)** - With forbidden positions
4. **Minimum Number of Taps (LeetCode #1326)** - Similar greedy interval

---

## Tags

`#dynamic-programming` `#greedy` `#bfs` `#optimization` `#medium`
