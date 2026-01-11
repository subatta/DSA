# Largest Rectangle in Histogram

**Difficulty:** Hard  
**LeetCode Problem:** #84  
**Tags:** `Monotonic Stack`, `Array`, `Stack`, `Histogram`, `Rectangle Area`

---

## Problem Statement

Given an array of integers `heights` representing the histogram's bar height where the width of each bar is 1, return the area of the largest rectangle in the histogram.

**Constraints:**
- `1 <= heights.length <= 10^5`
- `0 <= heights[i] <= 10^4`

**Example 1:**
```
Input: heights = [2,1,5,6,2,3]
Output: 10
Explanation: The largest rectangle is formed with heights 5 and 6, width 2, area = 5 * 2 = 10
```

**Example 2:**
```
Input: heights = [2,4]
Output: 4
```

---

## Step 1: State Space

### Problem Visualization

For `heights = [2,1,5,6,2,3]`:

```
Height
  6 |           █
  5 |       █   █
  4 |       █   █
  3 |       █   █       █
  2 |   █   █   █   █   █
  1 |   █ █ █   █   █   █
  0 +--+--+--+--+--+--+--+
      0  1  2  3  4  5
```

Possible rectangles:
```
Rect 1: height=1, spans [0..5], width=6, area=6
Rect 2: height=2, spans [0,2..5], can't include index 1
        Best: [2..5], width=4, area=8
Rect 3: height=3, spans [2,5], width=2, area=6
Rect 4: height=5, spans [2,3], width=2, area=10  ← Maximum!
Rect 5: height=6, only [3], width=1, area=6
```

### Core Question

**For each bar as a potential rectangle height, what is the maximum width it can extend to?**

A bar at position `i` with height `h` can extend:
- **Left** until we hit a shorter bar
- **Right** until we hit a shorter bar

The key insight: We need to find **left boundary** (first bar shorter than h on left) and **right boundary** (first bar shorter than h on right).

### Deriving from First Principles

**Observation 1: Rectangle constraint**
For a rectangle of height `h`, all bars in its range must be ≥ `h`.

**Observation 2: Each bar as height**
For bar `i` with height `h[i]`, the maximum rectangle using this height extends from:
- Left boundary: last index `L` where `h[L] < h[i]` (exclusive)
- Right boundary: first index `R` where `h[R] < h[i]` (exclusive)
- Width = `R - L - 1`
- Area = `h[i] × (R - L - 1)`

**Observation 3: Boundaries are "next smaller" elements**
- Left boundary = position of previous smaller element
- Right boundary = position of next smaller element
- This is exactly what monotonic stack solves!

**Observation 4: Monotonic increasing stack**
We maintain a stack in **increasing order of heights**:
- When we see a taller bar, push it (can extend further)
- When we see a shorter bar, it becomes the **right boundary** for all taller bars in stack
- Pop taller bars and compute their areas

**Observation 5: Computing area on pop**
When popping bar at index `i` with height `h[i]`:
- Right boundary = current index (the bar that caused the pop)
- Left boundary = index of bar below it in stack (or -1 if stack empty)
- Width = `right - left - 1`
- Area = `h[i] × width`

**Formula:**
```
For bar at index i:
  leftBoundary = index of previous smaller element (or -1)
  rightBoundary = index of next smaller element (or n)
  width = rightBoundary - leftBoundary - 1
  area = heights[i] × width
  maxArea = max of all areas
```

### State Space Structure

**Type:** Implicit state space of all possible rectangles.

**Structure:** For each position i, we track:
- Stack of indices in increasing height order
- Current maximum area found

**Cardinality:** O(n) bars → O(n²) possible rectangles (each bar can extend to various widths).

**Key Property:** Using monotonic increasing stack, we process each bar exactly once, computing its maximum rectangle in O(1) per operation.

### Example Computation

Input: `heights = [2,1,5,6,2,3]`

```
Process bar by bar:

i=0, h=2:
  Stack: []
  Push 0
  Stack: [0]
  
i=1, h=1:
  Stack top: 0 (h=2) > 1, pop and compute
    Pop i=0, h=2
    right=1, left=-1 (stack empty)
    width = 1 - (-1) - 1 = 1
    area = 2 × 1 = 2
  Push 1
  Stack: [1]
  
i=2, h=5:
  Stack top: 1 (h=1) < 5, push
  Stack: [1, 2]
  
i=3, h=6:
  Stack top: 2 (h=5) < 6, push
  Stack: [1, 2, 3]
  
i=4, h=2:
  Stack top: 3 (h=6) > 2, pop and compute
    Pop i=3, h=6
    right=4, left=2
    width = 4 - 2 - 1 = 1
    area = 6 × 1 = 6
  Stack top: 2 (h=5) > 2, pop and compute
    Pop i=2, h=5
    right=4, left=1
    width = 4 - 1 - 1 = 2
    area = 5 × 2 = 10  ← Maximum!
  Stack top: 1 (h=1) < 2, push
  Stack: [1, 4]
  
i=5, h=3:
  Stack top: 4 (h=2) < 3, push
  Stack: [1, 4, 5]
  
After loop, process remaining stack:
  Pop i=5, h=3
    right=6, left=4
    width = 6 - 4 - 1 = 1
    area = 3 × 1 = 3
  Pop i=4, h=2
    right=6, left=1
    width = 6 - 1 - 1 = 4
    area = 2 × 4 = 8
  Pop i=1, h=1
    right=6, left=-1
    width = 6 - (-1) - 1 = 6
    area = 1 × 6 = 6

Maximum area found: 10
```

### Generation Pattern

**Brute Force (all rectangles):**
```csharp
public int LargestRectangleArea(int[] heights) {
    int n = heights.Length;
    int maxArea = 0;
    
    // Try each position as left boundary
    for (int left = 0; left < n; left++) {
        int minHeight = heights[left];
        
        // Try each position as right boundary
        for (int right = left; right < n; right++) {
            minHeight = Math.Min(minHeight, heights[right]);
            int width = right - left + 1;
            int area = minHeight * width;
            maxArea = Math.Max(maxArea, area);
        }
    }
    
    return maxArea;
}
// Time: O(n²), Space: O(1)
```

**Optimal (monotonic stack):**
```csharp
public int LargestRectangleArea(int[] heights) {
    int n = heights.Length;
    var stack = new Stack<int>();
    int maxArea = 0;
    
    for (int i = 0; i < n; i++) {
        // Pop all bars taller than current
        while (stack.Count > 0 && heights[stack.Peek()] > heights[i]) {
            int height = heights[stack.Pop()];
            int width = stack.Count == 0 ? i : i - stack.Peek() - 1;
            maxArea = Math.Max(maxArea, height * width);
        }
        stack.Push(i);
    }
    
    // Process remaining bars
    while (stack.Count > 0) {
        int height = heights[stack.Pop()];
        int width = stack.Count == 0 ? n : n - stack.Peek() - 1;
        maxArea = Math.Max(maxArea, height * width);
    }
    
    return maxArea;
}
// Time: O(n), Space: O(n)
```

---

## Step 2: Brute Force

```csharp
public int LargestRectangleArea(int[] heights) {
    int n = heights.Length;
    int maxArea = 0;
    
    // For each bar, find its left and right boundaries
    for (int i = 0; i < n; i++) {
        int height = heights[i];
        
        // Find left boundary (first smaller on left)
        int left = i;
        while (left > 0 && heights[left - 1] >= height) {
            left--;
        }
        
        // Find right boundary (first smaller on right)
        int right = i;
        while (right < n - 1 && heights[right + 1] >= height) {
            right++;
        }
        
        // Compute area with current bar as height
        int width = right - left + 1;
        int area = height * width;
        maxArea = Math.Max(maxArea, area);
    }
    
    return maxArea;
}
```

**Complexity:**
- Time: O(n²) - for each bar, scan left and right
- Space: O(1)

**Why this works:**
For each bar, we find the maximum width rectangle that can use this bar's height. The width extends until we hit shorter bars on both sides.

**Why it's slow:**
We repeatedly scan left and right for each bar, doing redundant work.

---

## Step 3: Optimization Ideas

### Key Insights
1. **Next smaller element pattern:** We need previous/next smaller for each bar
2. **Monotonic stack computes this in O(n):** Process once instead of n times
3. **Stack stores indices:** To compute widths
4. **Increasing order:** Bars in stack can form rectangles extending right

### Why Monotonic Stack Works
- **Invariant:** Stack contains indices in increasing height order
- **When we see shorter bar:** It's the right boundary for all taller bars in stack
- **Pop and compute:** For each popped bar, compute its maximum rectangle
- **Left boundary:** The bar below it in stack (or start of array)

### Optimization Strategy
Instead of finding boundaries for each bar independently (O(n) per bar), use monotonic stack to find all boundaries in a single O(n) pass.

---

## Step 4: Optimal Solution

```csharp
public int LargestRectangleArea(int[] heights) {
    int n = heights.Length;
    var stack = new Stack<int>();  // Stores indices
    int maxArea = 0;
    
    for (int i = 0; i < n; i++) {
        // Current bar is shorter than stack top
        // It becomes right boundary for bars in stack
        while (stack.Count > 0 && heights[stack.Peek()] > heights[i]) {
            int heightIndex = stack.Pop();
            int height = heights[heightIndex];
            
            // Width calculation:
            // right boundary = i (current position)
            // left boundary = stack.Peek() (or -1 if stack empty)
            int width = stack.Count == 0 ? i : i - stack.Peek() - 1;
            
            int area = height * width;
            maxArea = Math.Max(maxArea, area);
        }
        
        stack.Push(i);
    }
    
    // Process remaining bars (they extend to end)
    while (stack.Count > 0) {
        int heightIndex = stack.Pop();
        int height = heights[heightIndex];
        int width = stack.Count == 0 ? n : n - stack.Peek() - 1;
        int area = height * width;
        maxArea = Math.Max(maxArea, area);
    }
    
    return maxArea;
}
```

**Complexity:**
- Time: O(n) - each bar pushed and popped once
- Space: O(n) - stack

**Why it's faster:**
Each bar is processed exactly once. When popped, we compute its maximum rectangle using the stack to find boundaries in O(1).

**Key Mechanics:**
1. **Push indices when increasing:** Bar can extend right
2. **Pop when decreasing:** Found right boundary, compute area
3. **Width from indices:** `current - stack.peek() - 1`
4. **After loop:** Process bars that extend to end

---

## Edge Cases

1. **All increasing heights:** `[1,2,3,4,5]`
   - Bars stay in stack until end
   - Process all in final phase
   - Answer: 9 (height 3, width 3)

2. **All decreasing heights:** `[5,4,3,2,1]`
   - Each bar pops previous immediately
   - Each bar forms rectangle of width 1
   - Answer: 5

3. **Single bar:** `[5]`
   - Area = 5

4. **All equal heights:** `[3,3,3,3]`
   - All bars stay in stack
   - Final bar extends full width
   - Area = 3 × 4 = 12

5. **Zero height:** `[2,0,2]`
   - Zero bar forces pop of all previous
   - Splits histogram into segments

---

## Related Problems

1. **Maximal Rectangle (LeetCode #85)** - Apply this to each row of matrix
2. **Trapping Rain Water (LeetCode #42)** - Alternative monotonic stack approach
3. **Next Greater Element** - Same monotonic stack pattern
4. **Remove K Digits (LeetCode #402)** - Monotonic increasing stack

---

## Pattern Recognition

**Problem Asks For:**
- Maximum area in histogram
- Rectangle optimization
- Bar height constraints

**This Suggests:**
- Each bar defines potential rectangle height
- Need to find width (left/right boundaries)
- Boundaries = next smaller elements
- → **Monotonic Stack pattern**

**Red Flags for Monotonic Stack:**
- "Next greater/smaller element"
- "Maximum rectangle/area"
- "For each element, find boundaries"
- Need to process elements with dependency on neighbors

---

## Tags

`#monotonic-stack` `#histogram` `#rectangle` `#stack` `#boundaries` `#next-smaller` `#area-optimization` `#hard`
