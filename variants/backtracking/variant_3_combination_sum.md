# Combination Sum

**Difficulty:** Medium  
**LeetCode Problem:** #39  
**Tags:** `Backtracking`, `DFS`, `Combinations`, `Unlimited Reuse`

---

## Problem Statement

Given an array of **distinct** integers `candidates` and a target integer `target`, return a list of all **unique combinations** of `candidates` where the chosen numbers sum to `target`. You may return the combinations in **any order**.

The **same** number may be chosen from `candidates` an **unlimited number of times**. Two combinations are unique if the frequency of at least one of the chosen numbers is different.

**Constraints:**
- `1 <= candidates.length <= 30`
- `2 <= candidates[i] <= 40`
- All elements of `candidates` are **distinct**.
- `1 <= target <= 40`

**Example 1:**
```
Input: candidates = [2,3,6,7], target = 7
Output: [[2,2,3],[7]]
Explanation:
  2 and 3 are candidates, and 2 + 2 + 3 = 7
  7 is a candidate, and 7 = 7
  These are the only two combinations.
```

**Example 2:**
```
Input: candidates = [2,3,5], target = 8
Output: [[2,2,2,2],[2,3,3],[3,5]]
```

**Example 3:**
```
Input: candidates = [2], target = 1
Output: []
```

---

## Step 1: State Space

### Problem Visualization

Input: `candidates = [2,3,6,7]`, `target = 7`

```
Valid combinations (sum = 7):
  [2,2,3]     → 2+2+3 = 7 ✓
  [7]         → 7 = 7 ✓

Invalid examples:
  [3,2,2]     → Same as [2,2,3] (order doesn't matter)
  [2,5]       → 5 not in candidates
  [2,2,2,2]   → Sum = 8 ≠ 7
```

**Decision tree (backtracking):**

```
                        [] (remain=7)
           /      |       |           \
        [2]     [3]     [6]           [7] ✓
     (rem=5)  (rem=4) (rem=1)       (rem=0)
    /  |  \    / |                
 [2,2][2,3]...[3,3]               
 (3) (2)      (rem=-2,prune)     
  |    |                          
[2,2,3]✓[2,3,?]                   
(rem=0)                           

Key insight: Can reuse same element!
  After choosing 2, can choose 2 again
  But to avoid duplicates, use startIndex
    After [2], only try [2,3,6,7], not [2,3,6,7] again from start
```

**Why startIndex matters:**
```
Without startIndex (wrong):
  [2,3] and [3,2] both generated → duplicates

With startIndex (correct):
  After choosing 2, startIndex = 0 (can choose 2 again)
  After choosing 3, startIndex = 1 (can't go back to 2)
  Result: [2,3] but not [3,2] ✓
```

### Core Question

**How do we find all combinations that sum to target, allowing reuse?**

Backtracking with:
1. **Remaining target tracking** (prune when exceed)
2. **StartIndex** (avoid duplicate combinations)
3. **Allow reuse** (startIndex stays same when picking same element again)

### Deriving from First Principles

**Observation 1: Unlimited reuse**
```
Each element can be used 0, 1, 2, ... times
Example: [2] with target=8 → [2,2,2,2]

In backtracking:
  After choosing element at index i,
  recurse with startIndex = i (not i+1)
  This allows picking i again.
```

**Observation 2: Avoid duplicates**
```
[2,3] and [3,2] are the same combination
To avoid:
  After choosing candidates[i],
  only consider candidates[i..n-1] in next recursion
  Never go back to candidates[0..i-1]
  
This ensures combinations, not permutations.
```

**Observation 3: Pruning strategy**
```
If remaining < 0: impossible to reach target
  Prune (return immediately)

If remaining == 0: found valid combination
  Add to result

If remaining > 0: continue exploring
```

**Observation 4: Early termination**
```
Optimization: Sort candidates first
If candidates[i] > remaining:
  All subsequent candidates are also too large
  Break early (no need to try them)
```

**Observation 5: Time complexity**
```
Worst case: Each element can be chosen 0 to target/min times

Example: candidates=[1], target=30
  Combinations: [1]*0, [1]*1, ..., [1]*30
  Total: O(2^target) in extreme case

More practically: O(target / min × n^(target/min))
Where min = smallest candidate

Better bound: O(N^(T/M)) where:
  N = len(candidates)
  T = target
  M = min(candidates)
```

**Formula (Backtracking):**
```
result = []

Backtrack(current, startIndex, remaining):
  if remaining == 0:
    result.add(copy of current)
    return
  
  if remaining < 0:
    return  // Prune
  
  for i from startIndex to n-1:
    current.add(candidates[i])
    // Note: startIndex stays i (allow reuse)
    Backtrack(current, i, remaining - candidates[i])
    current.removeLast()

Backtrack([], 0, target)
return result
```

### State Space Structure

**Type:** Pruned backtracking tree with unlimited element reuse.

**Structure:**
- Tree depth: up to target / min(candidates)
- Branching factor: varies, pruned by remaining < 0
- Each path: sequence of indices with startIndex constraint

**Cardinality:**
- Exponential in worst case
- Heavily pruned by target constraint
- Space: O(target / min) recursion depth

**Key Property:** 
- **Reuse allowed:** startIndex = i (not i+1)
- **No duplicates:** Only consider i onwards
- **Pruning:** remaining < 0

### Example Computation

Input: `candidates = [2,3,6,7]`, `target = 7`

```
Backtrack([], 0, 7):
  
  i=0 (try 2):
    current = [2]
    Backtrack([2], 0, 5):
      
      i=0 (try 2 again):
        current = [2,2]
        Backtrack([2,2], 0, 3):
          
          i=0 (try 2 again):
            current = [2,2,2]
            Backtrack([2,2,2], 0, 1):
              
              i=0 (try 2): remaining = -1 < 0, prune
              i=1 (try 3): remaining = -2 < 0, prune
              i=2 (try 6): remaining = -5 < 0, prune
              i=3 (try 7): remaining = -6 < 0, prune
            current = [2,2]
          
          i=1 (try 3):
            current = [2,2,3]
            Backtrack([2,2,3], 1, 0):
              remaining == 0, add [2,2,3] to result ✓
            current = [2,2]
          
          i=2 (try 6): remaining = -3 < 0, prune
          i=3 (try 7): remaining = -4 < 0, prune
        current = [2]
      
      i=1 (try 3):
        current = [2,3]
        Backtrack([2,3], 1, 2):
          
          i=1 (try 3): remaining = -1 < 0, prune
          i=2 (try 6): remaining = -4 < 0, prune
          i=3 (try 7): remaining = -5 < 0, prune
        current = [2]
      
      i=2 (try 6): remaining = -1 < 0, prune
      i=3 (try 7): remaining = -2 < 0, prune
    current = []
  
  i=1 (try 3):
    current = [3]
    Backtrack([3], 1, 4):
      
      i=1 (try 3 again):
        current = [3,3]
        Backtrack([3,3], 1, 1):
          All candidates too large, no solution
        current = [3]
      
      i=2 (try 6): remaining = -2 < 0, prune
      i=3 (try 7): remaining = -3 < 0, prune
    current = []
  
  i=2 (try 6):
    current = [6]
    Backtrack([6], 2, 1):
      All candidates too large, no solution
    current = []
  
  i=3 (try 7):
    current = [7]
    Backtrack([7], 3, 0):
      remaining == 0, add [7] to result ✓
    current = []

Result: [[2,2,3], [7]]
```

### Generation Pattern

**Brute force (exponential):**
```csharp
// Try all possible combinations with reuse
// Time: O(2^target) in worst case
```

**Optimized (with pruning):**
```csharp
// Prune when remaining < 0
// Sort for early break
// Time: Much better in practice
```

---

## Step 2: Approaches

### Approach 1: Backtracking (Standard)
Track remaining, use startIndex, allow reuse

### Approach 2: Dynamic Programming
Count ways to reach target (different problem variant)

---

## Step 3: Optimization Ideas

### Key Insights
1. **Allow reuse:** startIndex = i (not i+1)
2. **Prune early:** Stop when remaining < 0
3. **Sort first:** Break when candidate > remaining
4. **No "used" array:** Can pick same element multiple times

---

## Step 4: Optimal Solution (Backtracking)

```csharp
public class Solution {
    public IList<IList<int>> CombinationSum(int[] candidates, int target) {
        var result = new List<IList<int>>();
        Array.Sort(candidates);  // For early termination
        Backtrack(candidates, target, new List<int>(), 0, result);
        return result;
    }
    
    private void Backtrack(int[] candidates, int remaining, List<int> current, int start, IList<IList<int>> result) {
        // Base case: found valid combination
        if (remaining == 0) {
            result.Add(new List<int>(current));
            return;
        }
        
        // Base case: exceeded target
        if (remaining < 0) {
            return;
        }
        
        // Try each candidate from start onwards
        for (int i = start; i < candidates.Length; i++) {
            // Early termination (requires sorted array)
            if (candidates[i] > remaining) {
                break;
            }
            
            // Choose: add candidates[i]
            current.Add(candidates[i]);
            
            // Explore: allow reuse (start = i, not i+1)
            Backtrack(candidates, remaining - candidates[i], current, i, result);
            
            // Unchoose: backtrack
            current.RemoveAt(current.Count - 1);
        }
    }
}
```

**Complexity:**
- Time: O(N^(T/M)) where N = len(candidates), T = target, M = min(candidates)
  - In practice, much better due to pruning
- Space: O(T/M) - recursion stack depth
  - Output space not counted

**Why `start = i` not `i+1`?**
```
start = i   → allows reuse: [2] → [2,2] → [2,2,2]
start = i+1 → no reuse: [2] → [3] or [6] or [7], never [2,2]
```

---

## Without Sorting (Slightly Slower)

```csharp
private void Backtrack(int[] candidates, int remaining, List<int> current, int start, IList<IList<int>> result) {
    if (remaining == 0) {
        result.Add(new List<int>(current));
        return;
    }
    
    if (remaining < 0) {
        return;  // Prune
    }
    
    for (int i = start; i < candidates.Length; i++) {
        current.Add(candidates[i]);
        Backtrack(candidates, remaining - candidates[i], current, i, result);
        current.RemoveAt(current.Count - 1);
    }
}
```

**Trade-off:** No sorting overhead, but no early break optimization.

---

## Edge Cases

1. **No solution:** `candidates=[2], target=1` → `[]`
2. **Single element:** `candidates=[7], target=7` → `[[7]]`
3. **Multiple uses:** `candidates=[2], target=8` → `[[2,2,2,2]]`
4. **Large target:** Requires significant recursion depth
5. **All candidates > target:** Returns `[]` immediately

---

## Visualization

```
Tree for candidates=[2,3,6,7], target=7:

                    [] (rem=7)
        ┌────┬──────┴────┬──────┐
       [2]  [3]         [6]    [7] ✓
      (5)  (4)          (1)   (rem=0)
    ┌──┼───┐  ├──┐       X
   [2,2][2,3]... [3,3]
    (3)  (2)     (-2) X
    ┌┼───┐
 [2,2,2][2,2,3] ✓
   (1)   (rem=0)
    X

✓ = Valid (remaining = 0)
X = Pruned (remaining < 0 or too small)
```

---

## Related Problems

1. **Combination Sum II (LeetCode #40)** - Each element used once
2. **Combination Sum III (LeetCode #216)** - Exactly k numbers, use 1-9
3. **Combination Sum IV (LeetCode #377)** - Count combinations (DP problem)
4. **Target Sum (LeetCode #494)** - +/- operators
5. **Coin Change (LeetCode #322)** - Minimum coins (DP problem)

---

## Pattern Recognition

**Problem Asks For:**
- All combinations that sum to target
- Elements can be reused unlimited times
- Order doesn't matter

**This Suggests:**
- Backtracking with reuse (startIndex = i)
- Pruning when exceed target
- Sort for early termination
- → **Combination Sum with Reuse Pattern**

**Key Indicators:**
- "Combinations that sum to X"
- "May reuse elements" or "unlimited times"
- "Unique combinations" (order doesn't matter)

---

## Tags

`#backtracking` `#dfs` `#combination-sum` `#reuse` `#pruning` `#target-sum`
