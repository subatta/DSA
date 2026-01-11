# Next Greater Element II (Circular Array)

**Difficulty:** Medium  
**LeetCode Problem:** #503  
**Tags:** `Monotonic Stack`, `Array`, `Circular Array`, `Next Greater`

---

## Problem Statement

Given a circular integer array `nums` (i.e., the next element of `nums[nums.length - 1]` is `nums[0]`), return the next greater number for every element in `nums`.

The next greater number of a number `x` is the first greater number to its traversing-order next in the array, which means you could search circularly to find its next greater number. If it doesn't exist, return `-1` for this number.

**Constraints:**
- `1 <= nums.length <= 10^4`
- `-10^9 <= nums[i] <= 10^9`

**Example 1:**
```
Input: nums = [1,2,1]
Output: [2,-1,2]
Explanation: 
- First 1's next greater is 2
- 2 has no next greater (it's the largest), so -1
- Second 1's next greater is 2 (circularly, wraps to index 0)
```

**Example 2:**
```
Input: nums = [1,2,3,4,3]
Output: [2,3,4,-1,4]
```

---

## Step 1: State Space

### Problem Visualization

For `nums = [1,2,1]`:

```
Circular array (conceptually doubled):
[1, 2, 1, 1, 2, 1]
 0  1  2  0  1  2  (indices mod 3)

For index 0 (value 1): scan right → 2 (found at index 1)
For index 1 (value 2): scan right → 1, 1, 2, 1 (no greater, return -1)
For index 2 (value 1): scan right → wraps to 1, 2 (found 2 at index 1)

Result: [2, -1, 2]
```

Visual representation:
```
    1 ←---+
   ↙      |
  2       | (circular)
   ↖      |
    1 ----+
```

### Core Question

**For each element, what is the first greater element encountered when traversing circularly to the right?**

Circular means: after reaching the end, continue from the beginning (but stop before completing a full cycle back to the element itself).

### Deriving from First Principles

**Observation 1: Circular traversal**
To handle circular array, we can conceptually "double" the array:
- Process indices `[0, 2n)` 
- Use `i % n` to map to actual array positions
- Stop before processing each element twice

**Observation 2: Similar to Next Greater Element I**
The core pattern is the same: monotonic decreasing stack.
- Difference: We traverse the array twice (circular)

**Observation 3: Why traverse twice?**
Example: `[3, 2, 1]`
- After first pass, stack = [3, 2, 1] (all decreasing)
- Second pass: 3 is greater than 2 and 1
- So 2's next greater is 3, 1's next greater is 3

**Observation 4: Stack stores indices**
We need to store indices (not values) because:
- Result array needs to be populated by index
- Same value can appear multiple times

**Observation 5: Process 2n elements**
Traverse range `[0, 2n)`:
- First n elements: build initial stack and find some answers
- Second n elements: complete remaining answers using circular property
- Use `i % n` to access array values

**Formula:**
```
For each element at index i (in range [0, 2n)):
  While stack not empty AND nums[stack.top()] < nums[i % n]:
    Pop index j
    result[j] = nums[i % n]  (found next greater for j)
  If i < n:
    Push i to stack  (only push each index once)
```

### State Space Structure

**Type:** Array of next greater elements for circular traversal.

**Structure:** 
- Result array of size n
- Stack of indices in decreasing value order
- Process 2n iterations using modulo

**Cardinality:** n elements, each processed twice, O(n) total stack operations.

**Key Property:** Monotonic decreasing stack with circular traversal via doubling.

### Example Computation

Input: `nums = [1,2,1]`, n=3

```
Initialize: result = [-1, -1, -1], stack = []

i=0, nums[0]=1:
  Stack empty, push 0
  Stack: [0]

i=1, nums[1]=2:
  Stack top: 0 (nums[0]=1) < 2, pop
    result[0] = 2
  Push 1
  Stack: [1]

i=2, nums[2]=1:
  Stack top: 1 (nums[1]=2) > 1, don't pop
  Push 2
  Stack: [1, 2]

--- Second pass (circular) ---

i=3, nums[3%3]=nums[0]=1:
  Stack top: 2 (nums[2]=1) = 1, don't pop
  Don't push (i >= n)
  Stack: [1, 2]

i=4, nums[4%3]=nums[1]=2:
  Stack top: 2 (nums[2]=1) < 2, pop
    result[2] = 2
  Stack top: 1 (nums[1]=2) = 2, don't pop
  Don't push (i >= n)
  Stack: [1]

i=5, nums[5%3]=nums[2]=1:
  Stack top: 1 (nums[1]=2) > 1, don't pop
  Don't push (i >= n)
  Stack: [1]

Result: [2, -1, 2]
```

### Generation Pattern

**Brute Force:**
```csharp
public int[] NextGreaterElements(int[] nums) {
    int n = nums.Length;
    int[] result = new int[n];
    
    for (int i = 0; i < n; i++) {
        result[i] = -1;
        // Search circularly
        for (int j = 1; j < n; j++) {
            int nextIdx = (i + j) % n;
            if (nums[nextIdx] > nums[i]) {
                result[i] = nums[nextIdx];
                break;
            }
        }
    }
    
    return result;
}
// Time: O(n²), Space: O(1)
```

---

## Step 2: Brute Force

```csharp
public int[] NextGreaterElements(int[] nums) {
    int n = nums.Length;
    int[] result = new int[n];
    
    for (int i = 0; i < n; i++) {
        result[i] = -1;
        
        // Search next n-1 positions circularly
        for (int j = 1; j < n; j++) {
            int nextIdx = (i + j) % n;
            if (nums[nextIdx] > nums[i]) {
                result[i] = nums[nextIdx];
                break;
            }
        }
    }
    
    return result;
}
```

**Complexity:**
- Time: O(n²) - for each element, scan up to n-1 positions
- Space: O(1) excluding output

---

## Step 3: Optimization Ideas

### Key Insights
1. **Circular = Double traversal:** Process array twice
2. **Monotonic stack pattern:** Same as Next Greater Element I
3. **Index modulo:** Use `i % n` to map to actual positions

### Optimization Strategy
Use monotonic decreasing stack, but traverse `2n` indices with modulo arithmetic.

---

## Step 4: Optimal Solution

```csharp
public int[] NextGreaterElements(int[] nums) {
    int n = nums.Length;
    int[] result = new int[n];
    Array.Fill(result, -1);
    
    var stack = new Stack<int>();  // Stores indices
    
    // Traverse twice to handle circular nature
    for (int i = 0; i < 2 * n; i++) {
        int num = nums[i % n];
        
        // Pop elements smaller than current
        while (stack.Count > 0 && nums[stack.Peek()] < num) {
            result[stack.Pop()] = num;
        }
        
        // Only push indices in first pass
        if (i < n) {
            stack.Push(i);
        }
    }
    
    return result;
}
```

**Complexity:**
- Time: O(n) - each element pushed/popped once
- Space: O(n) - stack

**Key Mechanics:**
1. **Traverse 2n iterations:** `for (int i = 0; i < 2 * n; i++)`
2. **Use modulo for access:** `nums[i % n]`
3. **Push only in first pass:** `if (i < n) stack.Push(i)`
4. **Standard monotonic stack logic:** Pop smaller, push current

---

## Edge Cases

1. **All decreasing:** `[3,2,1]` → `[-1,3,3]`
2. **All increasing:** `[1,2,3]` → `[2,3,-1]`
3. **All equal:** `[2,2,2]` → `[-1,-1,-1]`
4. **Single element:** `[1]` → `[-1]`
5. **Maximum at start:** `[5,4,3,2,1]` → `[-1,5,5,5,5]`

---

## Related Problems

1. **Next Greater Element I (LeetCode #496)** - Non-circular version
2. **Daily Temperatures (LeetCode #739)** - Next greater with distances
3. **Online Stock Span (LeetCode #901)** - Monotonic stack for spans

---

## Tags

`#monotonic-stack` `#circular-array` `#next-greater` `#modulo-arithmetic`
