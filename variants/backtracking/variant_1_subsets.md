# Subsets

**Difficulty:** Medium  
**LeetCode Problem:** #78  
**Tags:** `Backtracking`, `DFS`, `Bit Manipulation`, `Combinations`

---

## Problem Statement

Given an integer array `nums` of **unique** elements, return all possible subsets (the power set).

The solution set **must not** contain duplicate subsets. Return the solution in **any order**.

**Constraints:**
- `1 <= nums.length <= 10`
- `-10 <= nums[i] <= 10`
- All the numbers of `nums` are **unique**.

**Example 1:**
```
Input: nums = [1,2,3]
Output: [[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
```

**Example 2:**
```
Input: nums = [0]
Output: [[],[0]]
```

---

## Step 1: State Space

### Problem Visualization

Input: `nums = [1,2,3]`

```
Power set (all subsets):
  {}          ← empty set
  {1}
  {2}
  {3}
  {1,2}
  {1,3}
  {2,3}
  {1,2,3}

Total: 2³ = 8 subsets
```

**Decision tree (backtracking):**

```
                    []
           /         |         \
       [1]          [2]         [3]
      /   \          |
   [1,2] [1,3]     [2,3]
     |
  [1,2,3]

At each element, choose: include it or skip it

Level 0: Consider element 1
  Branch 1: Include 1 → [1]
  Branch 2: Skip 1 → []

Level 1: Consider element 2
  From [1]: Include 2 → [1,2], Skip 2 → [1]
  From []: Include 2 → [2], Skip 2 → []

Level 2: Consider element 3
  From [1,2]: Include 3 → [1,2,3], Skip 3 → [1,2]
  From [1]: Include 3 → [1,3], Skip 3 → [1]
  From [2]: Include 3 → [2,3], Skip 3 → [2]
  From []: Include 3 → [3], Skip 3 → []

All paths lead to a valid subset!
```

**Alternative view - iterative building:**

```
Start: [[]]

Add 1: [[], [1]]

Add 2: [[], [1], [2], [1,2]]

Add 3: [[], [1], [2], [1,2], [3], [1,3], [2,3], [1,2,3]]
```

### Core Question

**How do we generate all possible subsets of a set?**

Backtracking explores all include/exclude decisions for each element.

### Deriving from First Principles

**Observation 1: Subset count**
```
For n elements, there are 2ⁿ subsets
Each element: included or not included (2 choices)
Total: 2 × 2 × ... × 2 (n times) = 2ⁿ
```

**Observation 2: Backtracking approach**
```
For each element:
  1. Include it in current subset, recurse
  2. Backtrack (remove it), skip it, recurse

This explores all 2ⁿ combinations.
```

**Observation 3: Start index optimization**
```
To avoid duplicates like [1,2] and [2,1]:
  Use startIndex parameter
  Only consider elements from startIndex onward
  
This ensures we generate combinations, not permutations.
Example: After choosing 1, only consider 2,3 (not 0)
```

**Observation 4: When to record subsets**
```
Option 1: Record at leaf nodes only (when startIndex == n)
Option 2: Record at every node (every state is a valid subset)

For subsets, Option 2 is correct!
Every recursive call represents a valid subset.
```

**Observation 5: Time complexity**
```
Number of subsets: 2ⁿ
Each subset takes O(n) to copy
Total: O(n × 2ⁿ)

Space: O(n) for recursion depth
       O(n × 2ⁿ) for output
```

**Formula (Backtracking):**
```
result = []

Backtrack(current, startIndex):
  result.add(copy of current)  // Every state is valid
  
  for i from startIndex to n-1:
    current.add(nums[i])       // Choose
    Backtrack(current, i+1)    // Explore
    current.removeLast()       // Unchoose

Backtrack([], 0)
return result
```

### State Space Structure

**Type:** Complete enumeration via backtracking decision tree.

**Structure:**
- Binary tree of depth n
- Each level: decide on one element (include/skip)
- All 2ⁿ leaf paths represent subsets

**Cardinality:**
- 2ⁿ subsets to generate
- O(n × 2ⁿ) total time (including copy)
- O(n) recursion depth
- O(n × 2ⁿ) output space

**Key Property:** Backtracking with startIndex ensures no duplicate subsets.

### Example Computation

Input: `nums = [1,2,3]`

```
Backtrack([], 0):
  Add [] to result
  
  i=0 (element 1):
    current = [1]
    Backtrack([1], 1):
      Add [1] to result
      
      i=1 (element 2):
        current = [1,2]
        Backtrack([1,2], 2):
          Add [1,2] to result
          
          i=2 (element 3):
            current = [1,2,3]
            Backtrack([1,2,3], 3):
              Add [1,2,3] to result
              (no more elements)
            current = [1,2]  // backtrack
        current = [1]  // backtrack
      
      i=2 (element 3):
        current = [1,3]
        Backtrack([1,3], 3):
          Add [1,3] to result
        current = [1]  // backtrack
    current = []  // backtrack
  
  i=1 (element 2):
    current = [2]
    Backtrack([2], 2):
      Add [2] to result
      
      i=2 (element 3):
        current = [2,3]
        Backtrack([2,3], 3):
          Add [2,3] to result
        current = [2]  // backtrack
    current = []  // backtrack
  
  i=2 (element 3):
    current = [3]
    Backtrack([3], 3):
      Add [3] to result
    current = []  // backtrack

Result: [[], [1], [1,2], [1,2,3], [1,3], [2], [2,3], [3]]
```

### Generation Pattern

**Brute force (same as optimal):**
```csharp
// Backtracking is already the standard approach
// Time: O(n × 2ⁿ)
```

---

## Step 2: Approaches

### Approach 1: Backtracking (Standard)
Time: O(n × 2ⁿ), Space: O(n) recursion

### Approach 2: Iterative
Start with [[]], for each num, add it to all existing subsets

### Approach 3: Bit Manipulation
Each subset maps to a binary number 0 to 2ⁿ-1

---

## Step 3: Optimization Ideas

### Key Insights
1. **Every state is valid:** Record at every recursive call
2. **Start index prevents duplicates:** Only consider elements ahead
3. **No pruning needed:** All 2ⁿ subsets are valid

---

## Step 4: Optimal Solution (Backtracking)

```csharp
public class Solution {
    public IList<IList<int>> Subsets(int[] nums) {
        var result = new List<IList<int>>();
        Backtrack(nums, new List<int>(), 0, result);
        return result;
    }
    
    private void Backtrack(int[] nums, List<int> current, int start, IList<IList<int>> result) {
        // Add current subset to result
        result.Add(new List<int>(current));
        
        // Explore subsets including elements from start onward
        for (int i = start; i < nums.Length; i++) {
            // Choose: include nums[i]
            current.Add(nums[i]);
            
            // Explore: generate subsets with nums[i] included
            Backtrack(nums, current, i + 1, result);
            
            // Unchoose: backtrack
            current.RemoveAt(current.Count - 1);
        }
    }
}
```

**Complexity:**
- Time: O(n × 2ⁿ)
  - 2ⁿ subsets to generate
  - O(n) to copy each subset
- Space: O(n) - recursion stack depth
  - O(n × 2ⁿ) for output (not counted in space complexity)

---

## Alternative: Iterative Approach

```csharp
public IList<IList<int>> Subsets(int[] nums) {
    var result = new List<IList<int>> { new List<int>() };
    
    foreach (int num in nums) {
        int count = result.Count;
        for (int i = 0; i < count; i++) {
            var newSubset = new List<int>(result[i]);
            newSubset.Add(num);
            result.Add(newSubset);
        }
    }
    
    return result;
}
```

**How it works:**
```
Start: [[]]
Add 1: [[], [1]]
Add 2: [[], [1], [2], [1,2]]
Add 3: [[], [1], [2], [1,2], [3], [1,3], [2,3], [1,2,3]]
```

**Complexity:** Same O(n × 2ⁿ)

---

## Alternative: Bit Manipulation

```csharp
public IList<IList<int>> Subsets(int[] nums) {
    var result = new List<IList<int>>();
    int n = nums.Length;
    int totalSubsets = 1 << n;  // 2^n
    
    for (int mask = 0; mask < totalSubsets; mask++) {
        var subset = new List<int>();
        
        for (int i = 0; i < n; i++) {
            // Check if i-th bit is set
            if ((mask & (1 << i)) != 0) {
                subset.Add(nums[i]);
            }
        }
        
        result.Add(subset);
    }
    
    return result;
}
```

**How it works:**
```
For nums = [1,2,3]:
  mask=0 (000): []
  mask=1 (001): [1]
  mask=2 (010): [2]
  mask=3 (011): [1,2]
  mask=4 (100): [3]
  mask=5 (101): [1,3]
  mask=6 (110): [2,3]
  mask=7 (111): [1,2,3]
```

**Complexity:** Same O(n × 2ⁿ)

---

## Edge Cases

1. **Single element:** `[1]` → `[[],[1]]`
2. **Empty array:** (Not possible per constraints, but would be `[[]]`)
3. **All negative:** Works the same
4. **Maximum size:** n=10 → 1024 subsets

---

## Visualization

```
Backtracking tree for [1,2,3]:

                        []
                        ↓ (add to result)
          ┌─────────────┼─────────────┐
         [1]           [2]            [3]
          ↓             ↓              ↓
    ┌─────┴─────┐      [2,3]        (end)
  [1,2]      [1,3]      ↓
    ↓          ↓     (add each)
 [1,2,3]    (end)
    ↓
  (end)

Result: [[], [1], [1,2], [1,2,3], [1,3], [2], [2,3], [3]]
```

---

## Related Problems

1. **Subsets II (LeetCode #90)** - With duplicate elements
2. **Permutations (LeetCode #46)** - Order matters
3. **Combinations (LeetCode #77)** - Choose k elements
4. **Power Set** - Same problem, different name
5. **Combination Sum (LeetCode #39)** - With target sum

---

## Pattern Recognition

**Problem Asks For:**
- All possible subsets
- Power set enumeration
- Include/exclude decisions

**This Suggests:**
- Backtracking for complete enumeration
- Binary decision per element
- No pruning (all valid)
- → **Subsets Backtracking Pattern**

**Key Indicators:**
- "All possible subsets/combinations"
- No specific target or constraint
- Exponential output (2ⁿ)

---

## Tags

`#backtracking` `#dfs` `#subsets` `#power-set` `#combinations` `#bit-manipulation`
