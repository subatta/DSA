# Permutations

**Difficulty:** Medium  
**LeetCode Problem:** #46  
**Tags:** `Backtracking`, `DFS`, `Permutations`, `Swap Technique`

---

## Problem Statement

Given an array `nums` of **distinct** integers, return all the possible permutations. You can return the answer in **any order**.

**Constraints:**
- `1 <= nums.length <= 6`
- `-10 <= nums[i] <= 10`
- All the integers of `nums` are **unique**.

**Example 1:**
```
Input: nums = [1,2,3]
Output: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

**Example 2:**
```
Input: nums = [0,1]
Output: [[0,1],[1,0]]
```

**Example 3:**
```
Input: nums = [1]
Output: [[1]]
```

---

## Step 1: State Space

### Problem Analysis

**Input:** Array `nums` of `n` unique elements  
**Output:** All possible orderings (permutations)

### State Space Definition

**Reference:** See [All Permutations](../../state_spaces/all_permutations.md) for detailed mathematical derivation

- **Type**: All possible orderings of n elements
- **Structure**: Sequences where order matters, all elements appear exactly once
- **Cardinality**: n! permutations = **O(n!)**

### Full State Space Enumeration

Input: `nums = [1,2,3]`

```
All permutations (3! = 6):
  [1,2,3]
  [1,3,2]
  [2,1,3]
  [2,3,1]
  [3,1,2]
  [3,2,1]

Key difference from subsets:
  Subsets: {1,2} == {2,1} (same subset)
  Permutations: [1,2] ≠ [2,1] (different permutations)
  
  Order matters!
```

**Decision tree (backtracking):**

```
                      []
         /            |            \
       [1]           [2]           [3]
      /   \         /   \         /   \
   [1,2] [1,3]   [2,1] [2,3]   [3,1] [3,2]
     |     |       |     |       |     |
  [1,2,3][1,3,2][2,1,3][2,3,1][3,1,2][3,2,1]

Level 0: Choose first position (3 choices)
Level 1: Choose second position (2 remaining choices)
Level 2: Choose third position (1 remaining choice)

Total: 3 × 2 × 1 = 3! = 6 permutations
```

**Path example:**
```
Start: []
  ↓ Choose 1 for position 0
[1]
  ↓ Choose 3 for position 1 (skip 1, already used)
[1,3]
  ↓ Choose 2 for position 2 (skip 1,3, already used)
[1,3,2] ← Complete permutation! Add to result.

Backtrack to [1,3], try next choice
Backtrack to [1], try next choice (2)
[1,2]
  ↓ Choose 3
[1,2,3] ← Add to result

Continue...
```

### Core Question

**How do we generate all possible orderings (permutations) of a sequence?**

Backtracking explores all ways to fill each position with unused elements.

### Deriving from First Principles

**Observation 1: Permutation count**
```
For n distinct elements: n! permutations
Position 0: n choices
Position 1: n-1 choices (one used)
Position 2: n-2 choices
...
Total: n × (n-1) × (n-2) × ... × 1 = n!
```

**Observation 2: Need to track used elements**
```
Unlike subsets, we can't use startIndex
  Why? In subsets, [1,2] and [2,1] are the same
       In permutations, they're different!

Solution: Use a "used" boolean array
  Track which elements are already in current permutation
```

**Observation 3: When to record**
```
Record only when current.length == n
This is a complete permutation (leaf node)

Unlike subsets, intermediate states aren't valid results.
```

**Observation 4: Backtracking structure**
```
For each position:
  Try every unused element
    Mark as used
    Add to current permutation
    Recurse
    Remove from permutation (backtrack)
    Mark as unused
```

**Observation 5: Optimization with swap**
```
Alternative: Swap elements in-place
  Maintain a "fixed" prefix
  Permute remaining elements
  
  [1,2,3,4] with index=2:
    Fixed: [1,2]
    Permute: [3,4]
  
  No "used" array needed!
  Just swap and backtrack swaps.
```

**Formula (Backtracking with used array):**
```
result = []
used = boolean[n]

Backtrack(current):
  if current.length == n:
    result.add(copy of current)
    return
  
  for i from 0 to n-1:
    if used[i]:
      continue
    
    used[i] = true
    current.add(nums[i])
    Backtrack(current)
    current.removeLast()
    used[i] = false

Backtrack([])
return result
```

**Formula (Backtracking with swap):**
```
result = []

Backtrack(index):
  if index == n:
    result.add(copy of nums)
    return
  
  for i from index to n-1:
    swap(nums[index], nums[i])    // Choose
    Backtrack(index + 1)           // Explore
    swap(nums[index], nums[i])    // Unchoose (restore)

Backtrack(0)
return result
```

### State Space Structure

**Type:** Complete enumeration via backtracking decision tree.

**Structure:**
- n-ary tree of depth n
- Each level: choose one unused element (n, n-1, n-2, ... choices)
- n! leaf nodes (complete permutations)

**Cardinality:**
- n! permutations to generate
- O(n × n!) total time (including copy)
- O(n) recursion depth
- O(n × n!) output space

**Key Property:** Track "used" elements or use swap technique to generate all orderings.

### Example Computation

Input: `nums = [1,2,3]`

```
Backtrack([]):
  
  i=0 (try 1):
    used[0] = true, current = [1]
    Backtrack([1]):
      
      i=0: used[0]=true, skip
      i=1 (try 2):
        used[1] = true, current = [1,2]
        Backtrack([1,2]):
          
          i=0: skip
          i=1: skip
          i=2 (try 3):
            used[2] = true, current = [1,2,3]
            Backtrack([1,2,3]):
              length == 3, add [1,2,3] to result ✓
            current = [1,2], used[2] = false
        current = [1], used[1] = false
      
      i=2 (try 3):
        used[2] = true, current = [1,3]
        Backtrack([1,3]):
          
          i=0: skip
          i=1 (try 2):
            used[1] = true, current = [1,3,2]
            Backtrack([1,3,2]):
              length == 3, add [1,3,2] to result ✓
            current = [1,3], used[1] = false
          i=2: skip
        current = [1], used[2] = false
    current = [], used[0] = false
  
  i=1 (try 2):
    used[1] = true, current = [2]
    Backtrack([2]):
      
      i=0 (try 1):
        used[0] = true, current = [2,1]
        Backtrack([2,1]):
          
          i=0: skip
          i=1: skip
          i=2 (try 3):
            used[2] = true, current = [2,1,3]
            Backtrack([2,1,3]):
              length == 3, add [2,1,3] to result ✓
            ...
      
      i=2 (try 3):
        used[2] = true, current = [2,3]
        Backtrack([2,3]):
          
          i=0 (try 1):
            used[0] = true, current = [2,3,1]
            Backtrack([2,3,1]):
              length == 3, add [2,3,1] to result ✓
            ...
    current = [], used[1] = false
  
  i=2 (try 3):
    ... [3,1,2] and [3,2,1]

Result: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

### Generation Pattern

**Brute force (same as optimal):**
```csharp
// Backtracking is the standard approach
// Time: O(n × n!)
```

---

## Step 2: Approaches

### Approach 1: Backtracking with Used Array
Track used elements with boolean array

### Approach 2: Backtracking with Swap
Swap elements in-place, no extra space

### Approach 3: Iterative
Build permutations by inserting each element in all positions

---

## Step 3: Optimization Ideas

### Key Insights
1. **Track used elements:** Boolean array or swap technique
2. **Record at leaves:** Only complete permutations (length == n)
3. **No pruning:** All n! permutations are valid

---

## Step 4: Optimal Solution (Backtracking with Used Array)

```csharp
public class Solution {
    public IList<IList<int>> Permute(int[] nums) {
        var result = new List<IList<int>>();
        bool[] used = new bool[nums.Length];
        Backtrack(nums, new List<int>(), used, result);
        return result;
    }
    
    private void Backtrack(int[] nums, List<int> current, bool[] used, IList<IList<int>> result) {
        // Base case: complete permutation
        if (current.Count == nums.Length) {
            result.Add(new List<int>(current));
            return;
        }
        
        // Try each unused element in current position
        for (int i = 0; i < nums.Length; i++) {
            if (used[i]) continue;
            
            // Choose: add nums[i]
            current.Add(nums[i]);
            used[i] = true;
            
            // Explore: build rest of permutation
            Backtrack(nums, current, used, result);
            
            // Unchoose: backtrack
            current.RemoveAt(current.Count - 1);
            used[i] = false;
        }
    }
}
```

**Complexity:**
- Time: O(n × n!)
  - n! permutations to generate
  - O(n) to copy each permutation
- Space: O(n) - recursion stack + used array
  - O(n × n!) for output (not counted)

---

## Alternative: Backtracking with Swap (Space Optimized)

```csharp
public class Solution {
    public IList<IList<int>> Permute(int[] nums) {
        var result = new List<IList<int>>();
        Backtrack(nums, 0, result);
        return result;
    }
    
    private void Backtrack(int[] nums, int index, IList<IList<int>> result) {
        // Base case: complete permutation
        if (index == nums.Length) {
            result.Add(new List<int>(nums));
            return;
        }
        
        // Try each element from index onwards in position index
        for (int i = index; i < nums.Length; i++) {
            // Choose: swap to put nums[i] at position index
            Swap(nums, index, i);
            
            // Explore: permute remaining elements
            Backtrack(nums, index + 1, result);
            
            // Unchoose: restore array
            Swap(nums, index, i);
        }
    }
    
    private void Swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```

**How it works:**
```
nums = [1,2,3], index = 0

Swap(0,0): [1,2,3]
  Permute [2,3] at index 1
    Swap(1,1): [1,2,3]
      Permute [3] at index 2
        → [1,2,3] ✓
    Swap(1,2): [1,3,2]
      Permute [2] at index 2
        → [1,3,2] ✓

Swap(0,1): [2,1,3]
  Permute [1,3] at index 1
    → [2,1,3], [2,3,1] ✓

Swap(0,2): [3,2,1]
  Permute [2,1] at index 1
    → [3,2,1], [3,1,2] ✓
```

**Complexity:** Same time, O(1) extra space (no used array)

---

## Edge Cases

1. **Single element:** `[1]` → `[[1]]`
2. **Two elements:** `[1,2]` → `[[1,2],[2,1]]`
3. **All same (but constraints say unique):** Not applicable
4. **Maximum size:** n=6 → 720 permutations

---

## Visualization

```
Backtracking tree for [1,2,3]:

                    []
        ┌────────────┼────────────┐
       [1]          [2]          [3]
      ┌─┴─┐        ┌─┴─┐        ┌─┴─┐
   [1,2][1,3]   [2,1][2,3]   [3,1][3,2]
     |    |        |    |        |    |
  [1,2,3][1,3,2][2,1,3][2,3,1][3,1,2][3,2,1]
     ✓    ✓        ✓    ✓        ✓    ✓

6 leaf nodes = 3! permutations
```

---

## Related Problems

1. **Permutations II (LeetCode #47)** - With duplicate elements
2. **Next Permutation (LeetCode #31)** - Find lexicographically next
3. **Permutation Sequence (LeetCode #60)** - Find k-th permutation
4. **Subsets (LeetCode #78)** - Order doesn't matter
5. **Letter Case Permutation (LeetCode #784)** - Toggle case

---

## Pattern Recognition

**Problem Asks For:**
- All possible permutations/orderings
- All arrangements where order matters
- Generate all n! sequences

**This Suggests:**
- Backtracking with used tracking
- Or swap technique
- Record at leaf nodes (complete permutations)
- → **Permutations Backtracking Pattern**

**Key Indicators:**
- "All permutations"
- "All arrangements"
- Order matters (unlike subsets/combinations)

---

## Tags

`#backtracking` `#dfs` `#permutations` `#swap-technique` `#ordering`
