# Jump Game II

**Difficulty:** Medium  
**LeetCode Problem:** #45  
**Tags:** `Greedy`, `Array`, `BFS`, `Dynamic Programming`

---

## Problem Statement

You are given a 0-indexed array of integers `nums` of length `n`. You are initially positioned at `nums[0]`.

Each element `nums[i]` represents the maximum length of a forward jump from index `i`. In other words, if you are at `nums[i]`, you can jump to any `nums[i + j]` where:
- `0 <= j <= nums[i]`
- `i + j < n`

Return the minimum number of jumps to reach `nums[n - 1]`. The test cases are generated such that you can reach `nums[n - 1]`.

**Constraints:**
- `1 <= nums.length <= 10^4`
- `0 <= nums[i] <= 1000`
- It's guaranteed that you can reach `nums[n-1]`

**Example 1:**
```
Input: nums = [2,3,1,1,4]
Output: 2
Explanation: Minimum path: 0 → 1 → 4 (2 jumps)
```

**Example 2:**
```
Input: nums = [2,3,0,1,4]
Output: 2
Explanation: Minimum path: 0 → 1 → 4 or 0 → 3 → 4
```

---

## Step 1: State Space

### Problem Visualization

For `nums = [2,3,1,1,4]`:

```
Index:     0  1  2  3  4
Value:     2  3  1  1  4

Jump levels (BFS view):
Level 0: [0]           (start)
Level 1: [1,2]         (from 0, can reach 1-2)
Level 2: [3,4]         (from 1,2, can reach 3-4)

Minimum jumps to reach 4: 2
```

Greedy view:
```
Current range: [0,0] (positions reachable with current jumps)
Next reach: Track furthest position reachable from current range

Jump 0: At position 0
  Current range: [0,0]
  From positions in [0,0], can reach up to: 0+2=2
  Next range: [1,2]

Jump 1: Process range [1,2]
  From position 1: can reach 1+3=4 ✓
  From position 2: can reach 2+1=3
  Next range: [3,4] (includes target 4)

Answer: 2 jumps
```

### Core Question

**What is the minimum number of jumps needed to reach the last index?**

This is a shortest path problem, which can be solved with BFS or greedy approach.

### Deriving from First Principles

**Observation 1: BFS perspective**
Think of the array as a graph where position i connects to positions [i+1, i+nums[i]].
Minimum jumps = shortest path = BFS level.

**Observation 2: Level-by-level expansion**
Instead of exploring individual positions, explore by "levels":
- Level 0: position 0
- Level 1: all positions reachable with 1 jump
- Level 2: all positions reachable with 2 jumps
- Continue until we reach the end

**Observation 3: Greedy range expansion**
For each level (number of jumps), track:
- Current range: [start, end] of positions reachable with current jumps
- Next furthest: Maximum position reachable from current range
- When we exhaust current range, increment jumps and expand range

**Observation 4: Two pointers for range**
```
currentEnd = 0 (end of current level)
farthest = 0 (furthest we can reach from current level)
jumps = 0

For each position i:
  Update farthest = max(farthest, i + nums[i])
  
  If i == currentEnd: (exhausted current level)
    jumps++
    currentEnd = farthest (expand to next level)
    
    If currentEnd >= n-1: (reached end)
      break
```

**Observation 5: Why greedy works**
We don't need to explore all paths. Simply tracking the furthest reachable position at each jump level gives minimum jumps.

**Formula:**
```
jumps = 0
currentEnd = 0  // End of current jump range
farthest = 0    // Furthest reachable from current range

for i = 0 to n-2:  // Don't process last position
  farthest = max(farthest, i + nums[i])
  
  if i == currentEnd:  // Completed current range
    jumps++
    currentEnd = farthest
    
return jumps
```

### State Space Structure

**Type:** Greedy BFS-style level expansion.

**Structure:**
- Three variables: jumps, currentEnd, farthest
- Scan array left to right
- Expand range when current range exhausted

**Cardinality:**
- O(n) iterations
- O(1) space

**Key Property:** Each jump level expands to furthest reachable positions; guaranteed minimum jumps.

### Example Computation

Input: `nums = [2,3,1,1,4]`

```
jumps = 0, currentEnd = 0, farthest = 0

i=0, nums[0]=2:
  farthest = max(0, 0+2) = 2
  i == currentEnd (0 == 0):
    jumps = 1
    currentEnd = 2

i=1, nums[1]=3:
  farthest = max(2, 1+3) = 4
  i != currentEnd (1 != 2)

i=2, nums[2]=1:
  farthest = max(4, 2+1) = 4
  i == currentEnd (2 == 2):
    jumps = 2
    currentEnd = 4
    currentEnd >= 4 (can reach end)

i=3, 4: Skip (i >= n-1 or currentEnd >= n-1)

Return jumps = 2
```

Input: `nums = [2,3,0,1,4]`

```
jumps = 0, currentEnd = 0, farthest = 0

i=0:
  farthest = max(0, 0+2) = 2
  jumps = 1, currentEnd = 2

i=1:
  farthest = max(2, 1+3) = 4
  
i=2:
  farthest = max(4, 2+0) = 4
  i == currentEnd:
    jumps = 2, currentEnd = 4

Return 2
```

### Generation Pattern

**Dynamic Programming:**
```csharp
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
// Time: O(n × max(nums)), Space: O(n)
```

---

## Step 2: Brute Force

Try all paths using BFS or DP: O(n × max(nums)) time.

---

## Step 3: Optimization Ideas

### Key Insights
1. **BFS by levels:** Process positions level by level (jumps)
2. **Greedy range expansion:** Track furthest reach for each level
3. **No need for queue:** Use two pointers (currentEnd, farthest)
4. **One pass:** O(n) time

### Why Greedy Works
At each jump level, we want to reach the furthest possible position to minimize total jumps. Greedy choice of maximum reach at each level is optimal.

---

## Step 4: Optimal Solution

```csharp
public int Jump(int[] nums) {
    int n = nums.Length;
    if (n == 1) return 0;
    
    int jumps = 0;
    int currentEnd = 0;   // End of current jump level
    int farthest = 0;     // Furthest position reachable
    
    // Don't process last position (already there if we reach it)
    for (int i = 0; i < n - 1; i++) {
        // Update furthest position reachable from positions in current level
        farthest = Math.Max(farthest, i + nums[i]);
        
        // If we've reached end of current level
        if (i == currentEnd) {
            jumps++;
            currentEnd = farthest;
            
            // If we can reach the end, done
            if (currentEnd >= n - 1) {
                break;
            }
        }
    }
    
    return jumps;
}
```

**Complexity:**
- Time: O(n) - single pass
- Space: O(1)

**Key Mechanics:**
1. **currentEnd:** Marks boundary of current jump level
2. **farthest:** Tracks furthest reach from current level
3. **When i reaches currentEnd:** Complete current level, increment jumps, expand to farthest
4. **Loop to n-2:** Don't need to process last position

**Alternative BFS (explicit):**
```csharp
public int Jump(int[] nums) {
    int n = nums.Length;
    if (n == 1) return 0;
    
    int jumps = 0;
    int currentLevelEnd = 0;
    int nextLevelEnd = 0;
    int i = 0;
    
    while (currentLevelEnd < n - 1) {
        jumps++;
        
        // Explore all positions in current level
        while (i <= currentLevelEnd) {
            nextLevelEnd = Math.Max(nextLevelEnd, i + nums[i]);
            i++;
        }
        
        currentLevelEnd = nextLevelEnd;
    }
    
    return jumps;
}
```

---

## Edge Cases

1. **Single element:** `[0]` → `0` (already at end)
2. **Two elements:** `[1,1]` → `1`
3. **Can reach end in one jump:** `[5,1,1,1,1]` → `1`
4. **Must use all jumps:** `[1,1,1,1]` → `3`
5. **Large jumps available:** `[10,9,8,7,6,5,4,3,2,1,1,0]` → `2`

---

## BFS vs Greedy Comparison

**BFS (explicit queue):**
- Time: O(n), Space: O(n) for queue
- More intuitive but uses extra space

**Greedy (implicit BFS):**
- Time: O(n), Space: O(1)
- Tracks level boundaries without queue
- More efficient

Both give same answer (minimum jumps), but greedy is more space-efficient.

---

## Related Problems

1. **Jump Game (LeetCode #55)** - Check if reachable
2. **Jump Game III (LeetCode #1306)** - Jump forward/backward
3. **Minimum Number of Taps (LeetCode #1326)** - Interval covering
4. **Video Stitching (LeetCode #1024)** - Minimum clips to cover

---

## Pattern Recognition

**Problem Asks For:**
- Minimum jumps/steps
- Shortest path in implicit graph
- Minimum resources to reach target

**This Suggests:**
- BFS for shortest path
- Greedy level expansion
- Track range boundaries
- → **Greedy + BFS pattern**

**Key Indicators:**
- "Minimum jumps"
- Guaranteed reachability
- Jump based on array values

---

## Tags

`#greedy` `#bfs` `#array` `#jump-game` `#minimum-jumps` `#range-expansion` `#shortest-path`
