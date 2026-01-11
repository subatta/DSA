# Jump Game

**Difficulty:** Medium  
**LeetCode Problem:** #55  
**Tags:** `Greedy`, `Array`, `Dynamic Programming`

---

## Problem Statement

You are given an integer array `nums`. You are initially positioned at the array's first index, and each element in the array represents your maximum jump length at that position.

Return `true` if you can reach the last index, or `false` otherwise.

**Constraints:**
- `1 <= nums.length <= 10^4`
- `0 <= nums[i] <= 10^5`

**Example 1:**
```
Input: nums = [2,3,1,1,4]
Output: true
Explanation: Jump 1 step from index 0 to 1, then 3 steps to the last index
```

**Example 2:**
```
Input: nums = [3,2,1,0,4]
Output: false
Explanation: You will always arrive at index 3, which has value 0 (cannot jump)
```

---

## Step 1: State Space

### Problem Visualization

For `nums = [2,3,1,1,4]`:

```
Index:     0  1  2  3  4
Value:     2  3  1  1  4
           ↓
From 0: Can reach 1,2 (jump 1 or 2)
From 1: Can reach 2,3,4 (jump 1,2,3)

Reachability:
  0: ✓ (start)
  1: ✓ (from 0)
  2: ✓ (from 0 or 1)
  3: ✓ (from 1)
  4: ✓ (from 1)

Can reach end: YES
```

For `nums = [3,2,1,0,4]`:

```
Index:     0  1  2  3  4
Value:     3  2  1  0  4
           ↓
From 0: Can reach 1,2,3
From 1: Can reach 2,3
From 2: Can reach 3
From 3: Can reach 3 (stuck! value=0)

Maximum reach = 3
Target = 4
Cannot reach end: NO
```

Greedy view:
```
Track furthest reachable position:
  Start: maxReach = 0
  At 0 (val=2): maxReach = max(0, 0+2) = 2
  At 1 (val=3): maxReach = max(2, 1+3) = 4 ≥ 4 ✓
```

### Core Question

**Can we reach the last index by making a series of jumps, where each jump is at most nums[i] steps from position i?**

Equivalently: **Does there exist a path from index 0 to index n-1?**

### Deriving from First Principles

**Observation 1: Reachability propagates**
If we can reach position i, and nums[i] = k, then we can reach all positions in range [i+1, i+k].

**Observation 2: Track maximum reach**
Instead of tracking all reachable positions, track the furthest position we can reach:
```
maxReach = initially 0
For each position i we can reach (i <= maxReach):
  Update maxReach = max(maxReach, i + nums[i])
```

**Observation 3: Early termination**
- If maxReach >= n-1: We can reach the end, return true
- If we're at position i and i > maxReach: We're stuck, return false

**Observation 4: Greedy is sufficient**
We don't need to find the actual path. Just knowing if the end is reachable is enough.
Greedy approach: Always update the maximum reachable position.

**Observation 5: Single pass algorithm**
```
maxReach = 0
for i = 0 to n-1:
  if i > maxReach:
    return false  // Cannot reach position i
  maxReach = max(maxReach, i + nums[i])
  if maxReach >= n-1:
    return true  // Can reach end
return true
```

**Formula:**
```
maxReach = 0
for each position i in [0, n-1]:
  if i > maxReach:
    return false
  maxReach = max(maxReach, i + nums[i])
return maxReach >= n-1
```

### State Space Structure

**Type:** Greedy tracking of maximum reachable position.

**Structure:**
- Single variable: maxReach
- Scan array left to right
- Update maxReach at each position

**Cardinality:**
- O(n) positions to check
- O(1) space

**Key Property:** Greedy update of maxReach is sufficient; no need to track all reachable positions.

### Example Computation

Input: `nums = [2,3,1,1,4]`

```
maxReach = 0, n = 5, target = 4

i=0, nums[0]=2:
  0 <= 0 (reachable)
  maxReach = max(0, 0+2) = 2

i=1, nums[1]=3:
  1 <= 2 (reachable)
  maxReach = max(2, 1+3) = 4
  4 >= 4 → Can reach end!
  Return true
```

Input: `nums = [3,2,1,0,4]`

```
maxReach = 0, n = 5, target = 4

i=0, nums[0]=3:
  0 <= 0 (reachable)
  maxReach = max(0, 0+3) = 3

i=1, nums[1]=2:
  1 <= 3 (reachable)
  maxReach = max(3, 1+2) = 3

i=2, nums[2]=1:
  2 <= 3 (reachable)
  maxReach = max(3, 2+1) = 3

i=3, nums[3]=0:
  3 <= 3 (reachable)
  maxReach = max(3, 3+0) = 3

i=4:
  4 > 3 (unreachable!)
  Return false
```

### Generation Pattern

**Dynamic Programming (bottom-up):**
```csharp
public bool CanJump(int[] nums) {
    int n = nums.Length;
    bool[] canReach = new bool[n];
    canReach[0] = true;
    
    for (int i = 0; i < n; i++) {
        if (!canReach[i]) continue;
        
        for (int j = 1; j <= nums[i] && i + j < n; j++) {
            canReach[i + j] = true;
        }
    }
    
    return canReach[n - 1];
}
// Time: O(n × max(nums)), Space: O(n)
```

---

## Step 2: Brute Force

Try all possible jump combinations using recursion or DP: O(n × max(nums)) or exponential.

---

## Step 3: Optimization Ideas

### Key Insights
1. **Don't need actual path:** Just reachability
2. **Track maximum reach:** One variable suffices
3. **Greedy is optimal:** Update max reach at each step
4. **Single pass:** O(n) time, O(1) space

### Why Greedy Works
If we can reach position i, it doesn't matter how we got there. What matters is the furthest we can go from i.

---

## Step 4: Optimal Solution

```csharp
public bool CanJump(int[] nums) {
    int maxReach = 0;
    
    for (int i = 0; i < nums.Length; i++) {
        // If current position is beyond max reachable, stuck
        if (i > maxReach) {
            return false;
        }
        
        // Update furthest position we can reach
        maxReach = Math.Max(maxReach, i + nums[i]);
        
        // Early exit if we can reach the end
        if (maxReach >= nums.Length - 1) {
            return true;
        }
    }
    
    return true;
}
```

**Complexity:**
- Time: O(n) - single pass
- Space: O(1)

**Alternative (backward greedy):**
```csharp
public bool CanJump(int[] nums) {
    int lastGoodIndex = nums.Length - 1;
    
    // Work backward: can we reach lastGoodIndex?
    for (int i = nums.Length - 2; i >= 0; i--) {
        if (i + nums[i] >= lastGoodIndex) {
            lastGoodIndex = i;
        }
    }
    
    return lastGoodIndex == 0;
}
```

This checks if position 0 can reach the "last good index" (closest position that can reach the end).

---

## Edge Cases

1. **Single element:** `[0]` → `true` (already at end)
2. **Large jump at start:** `[5,1,1,1,1]` → `true`
3. **Zero at end:** `[1,1,1,0]` → `true` (reach before 0)
4. **Zero barrier:** `[1,0,1]` → `false`
5. **All zeros except first:** `[0,0,0]` → `false` (n>1)

---

## Related Problems

1. **Jump Game II (LeetCode #45)** - Minimum jumps to reach end
2. **Jump Game III (LeetCode #1306)** - Jump forward or backward
3. **Jump Game VII (LeetCode #1871)** - With constraints
4. **Minimum Number of Taps (LeetCode #1326)** - Similar greedy

---

## Pattern Recognition

**Problem Asks For:**
- Can reach target position
- Reachability in array
- Jump based on values

**This Suggests:**
- Track maximum reach greedily
- Single pass left to right
- Don't need actual path
- → **Greedy pattern**

**Key Indicators:**
- "Can reach" or "reachability"
- Jump length based on array values
- Don't need to count or find path

---

## Tags

`#greedy` `#array` `#reachability` `#jump-game` `#max-reach` `#single-pass`
