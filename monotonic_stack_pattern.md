# Pattern Title: Monotonic Stack

## Pattern Description:
- What is it?  
  A stack that maintains elements in **monotonically increasing or decreasing order**. When a new element violates the monotonic property, we pop elements until the property is restored, processing those popped elements before pushing the new one.

- What abstract problem does it solve?  
  Converts O(n²) nested loop comparisons for finding next/previous greater/smaller elements into O(n) single pass by discarding irrelevant candidates early.

- Real-world problem variants (simplest first):  
  1. [Next Greater Element I](variants/monotonic_stack/variant_1_next_greater_element.md)
  2. [Daily Temperatures](variants/monotonic_stack/variant_2_daily_temperatures.md)
  3. [Largest Rectangle in Histogram](variants/monotonic_stack/variant_3_largest_rectangle_histogram.md)
  4. [Next Greater Element II](variants/monotonic_stack/variant_4_next_greater_element_ii.md)
  5. [Remove K Digits](variants/monotonic_stack/variant_5_remove_k_digits.md)

## Canonical Code Skeleton:

```csharp
int[] MonotonicStackTemplate(int[] nums) {
    int n = nums.Length;
    int[] result = new int[n];
    Stack<int> stack = new(); // stores indices
    
    for (int i = 0; i < n; i++) {
        // Pop elements that violate monotonic property
        while (stack.Count > 0 && nums[i] > nums[stack.Peek()]) {
            int idx = stack.Pop();
            result[idx] = nums[i]; // Process popped element
        }
        stack.Push(i);
    }
    
    // Handle remaining elements (no next greater)
    while (stack.Count > 0) {
        result[stack.Pop()] = -1;
    }
    
    return result;
}
```

## Pattern Variants (5 Total)

### 🟡 Medium (5 variants)
**Master monotonic stack mechanics and applications**

1. **[Next Greater Element I](variants/monotonic_stack/variant_1_next_greater_element.md)** - LeetCode #496
   - **Concept:** Basic monotonic decreasing stack, pop when current > top
   - **Complexity:** O(n) time, O(n) space
   - **Why Medium:** Foundation pattern, requires understanding of stack maintenance

2. **[Daily Temperatures](variants/monotonic_stack/variant_2_daily_temperatures.md)** - LeetCode #739 ⭐
   - **Concept:** Find distance to next warmer day (next greater element variant)
   - **Complexity:** O(n) time, O(n) space
   - **Why Medium:** Common interview problem, distance calculation instead of value

3. **[Next Greater Element II](variants/monotonic_stack/variant_4_next_greater_element_ii.md)** - LeetCode #503
   - **Concept:** Circular array - traverse twice to handle wraparound
   - **Complexity:** O(n) time, O(n) space
   - **Why Medium:** Circular array handling, modulo arithmetic

4. **[Remove K Digits](variants/monotonic_stack/variant_5_remove_k_digits.md)** - LeetCode #402
   - **Concept:** Monotonic increasing stack to build smallest number
   - **Complexity:** O(n) time, O(n) space
   - **Why Medium:** Edge cases (leading zeros, k >= n), string manipulation

### 🔴 Hard (1 variant)
**Master complex boundary tracking**

5. **[Largest Rectangle in Histogram](variants/monotonic_stack/variant_3_largest_rectangle_histogram.md)** - LeetCode #84 ⭐⭐
   - **Concept:** Find left/right boundaries for each bar using monotonic increasing stack
   - **Complexity:** O(n) time, O(n) space
   - **Why Hard:** Two boundaries, area calculation, sentinel values, non-obvious optimization

### Practice Progression
1. Start with **Next Greater Element I** (#1) - understand basic template
2. Practice **Daily Temperatures** (#2) - most common interview problem
3. Try **Next Greater Element II** (#3) - handle circular arrays
4. Challenge yourself with **Remove K Digits** (#4) - different application
5. Master **Largest Rectangle** (#5) - hardest optimization, requires deep insight

---

## Key Pattern Takeaways

### Core Concepts
- **Monotonic property**: Stack maintains increasing or decreasing order
- **Each element processed once**: Push once, pop at most once → O(n) amortized
- **Store indices, not values**: Enables distance/position calculations
- **Pop = Process**: When violating monotonic property, popped elements found their answer

### Monotonic Stack Types

| Type | Property | Use Case | Example |
|------|----------|----------|---------|
| **Decreasing** | `nums[i] > nums[stack.top]` triggers pop | Find next **greater** element | Daily Temperatures |
| **Increasing** | `nums[i] < nums[stack.top]` triggers pop | Find next **smaller** element | Largest Rectangle |

### When to Use This Pattern

✅ **Use when you see:**
- "Next greater/smaller element"
- "Distance to next element satisfying condition"
- "Range where element is minimum/maximum"
- "Span" or "width" calculations
- O(n²) brute force scanning left/right

❌ **Don't use when:**
- Need to track all elements (not just relevant candidates)
- Problem requires sorted order (use sorting instead)
- Need to access elements in middle of range

### Common Patterns

**1. Next Greater Element (Decreasing Stack)**
```csharp
Stack<int> stack = new();
for (int i = 0; i < n; i++) {
    while (stack.Count > 0 && nums[i] > nums[stack.Peek()]) {
        int idx = stack.Pop();
        result[idx] = nums[i]; // Found next greater
    }
    stack.Push(i);
}
```

**2. Next Smaller Element (Increasing Stack)**
```csharp
Stack<int> stack = new();
for (int i = 0; i < n; i++) {
    while (stack.Count > 0 && nums[i] < nums[stack.Peek()]) {
        int idx = stack.Pop();
        result[idx] = nums[i]; // Found next smaller
    }
    stack.Push(i);
}
```

**3. Distance Calculation**
```csharp
// Instead of storing value, store distance
result[idx] = i - idx; // Distance from idx to i
```

**4. Circular Array (2x Traversal)**
```csharp
for (int i = 0; i < 2 * n; i++) {
    int actualIdx = i % n;
    // Same monotonic stack logic
}
```

**5. Left and Right Boundaries**
```csharp
// First pass: find left boundary
for (int i = 0; i < n; i++) {
    while (stack.Count > 0 && nums[i] < nums[stack.Peek()]) {
        stack.Pop();
    }
    left[i] = stack.Count > 0 ? stack.Peek() : -1;
    stack.Push(i);
}

// Second pass: find right boundary (traverse right to left)
```

### Common Pitfalls

❌ **Storing values instead of indices** - Loses position information  
✅ Use indices to calculate distances or access original values

❌ **Wrong monotonic order** - Decreasing for next greater, increasing for next smaller  
✅ Think: "I pop when current is better than top"

❌ **Forgetting remaining stack elements** - After loop, stack has elements with no answer  
✅ Process remaining stack (usually set to -1 or default)

❌ **Not handling edge cases** - Empty arrays, all same values, circular arrays  
✅ Check constraints, handle n=1, test with monotonic input

---

## Summary of Key Dimensions

| Problem | Direction | Stack Order | Pop Condition | Result | Key Insight |
|---------|-----------|-------------|---------------|--------|-------------|
| Next Greater | Forward | Decreasing | current > top | value/distance | Basic template |
| Daily Temperatures | Forward | Decreasing | current > top | distance | Distance variant |
| Next Greater II | Forward (2×) | Decreasing | current > top | value | Circular array |
| Largest Rectangle | Forward | Increasing | current < top | area | Left/right boundaries |
| Remove K Digits | Forward | Increasing | current < top | smallest number | Lexicographical order |

---

**Note:** The embedded variant content has been moved to individual files for better organization. Each file contains:
1. State Space Derivation (cardinality, structure, generation)
2. Brute Force with Value Tracing
3. Pruning Analysis (can we do better?)
4. Optimal Solution with Skeleton Transformation
    Array.Fill(result, -1); // Default: no greater element
    
    Stack<int> stack = new(); // Monotonic decreasing (stores indices)
    
    for (int i = 0; i < n; i++)
    {
        // Pop all elements smaller than current
        while (stack.Count > 0 && nums[i] > nums[stack.Peek()])
        {
            int idx = stack.Pop();
            result[idx] = nums[i]; // Found next greater for idx
        }
        
        stack.Push(i); // Add current index
    }
    
    return result;
}
```

### Explanation of Pruning:
- **Monotonic decreasing stack:** Stack maintains indices in decreasing order of their values
- **Key insight:** If current element is greater than stack top, we found the answer for stack top
- **Why it works:** Elements in between are smaller, so they can't be the "next greater"
- **Each element:** Pushed once, popped at most once → O(n) total
- **Remaining in stack:** Have no next greater element (default -1)

### Optimized Solution Code Walkthrough / Variable Trace:
For `nums = [2,1,2,4,3]`:

| i | nums[i] | stack (before) | compare | action | stack (after) | result |
|---|---------|----------------|---------|--------|---------------|--------|
| 0 | 2 | [] | - | push 0 | [0] | [-1,-1,-1,-1,-1] |
| 1 | 1 | [0] | 1 > 2? no | push 1 | [0,1] | [-1,-1,-1,-1,-1] |
| 2 | 2 | [0,1] | 2 > 1? yes | pop 1, result[1]=2, push 2 | [0,2] | [-1,2,-1,-1,-1] |
| 3 | 4 | [0,2] | 4 > 2? yes | pop 2, result[2]=4 | [0] | [-1,2,4,-1,-1] |
| 3 | 4 | [0] | 4 > 2? yes | pop 0, result[0]=4 | [] | [4,2,4,-1,-1] |
| 3 | 4 | [] | - | push 3 | [3] | [4,2,4,-1,-1] |
| 4 | 3 | [3] | 3 > 4? no | push 4 | [3,4] | [4,2,4,-1,-1] |

### Big-O Analysis:
- **Brute Force:** O(n²) → nested loops for all pairs
- **Optimized:** O(n) → each element pushed/popped once
- **Space Complexity:** O(n) → stack in worst case

</details>


<details>
<summary><b>Variant #2: Daily Temperatures</b></summary>

## Variant #2: Daily Temperatures

### Input/Output:
- Input: `temperatures = [73,74,75,71,69,72,76,73]`
- Output: `[1,1,4,2,1,1,0,0]` (days until warmer temperature)

### Full State Space:
For each day, compare with all future days to find first warmer.
```
Day 0 (73°): check days 1,2,3,4,5,6,7
Day 1 (74°): check days 2,3,4,5,6,7
...
Total comparisons: 7+6+5+4+3+2+1 = 28 = O(n²)
```

### Expected/Pruned State Space:
Monotonic stack finds first warmer day in O(n).
```
Each day processed once
Result stores distance (i - idx) instead of value
```

### State Space Leading to Output:
Stack stores indices of days waiting for warmer temperature.

### Brute Force Canonical Skeleton:
```csharp
int[] DailyTemperaturesBruteForce(int[] temperatures)
{
    int n = temperatures.Length;
    int[] result = new int[n];
    
    for (int i = 0; i < n; i++)
    {
        result[i] = 0; // Default: no warmer day
        
        for (int j = i + 1; j < n; j++)
        {
            if (temperatures[j] > temperatures[i])
            {
                result[i] = j - i; // Distance to warmer day
                break;
            }
        }
    }
    
    return result;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `temperatures = [73,74,75,71,69,72,76,73]`:

| i | temp[i] | j | temp[j] | temp[j] > temp[i] | result[i] |
|---|---------|---|---------|-------------------|----------|
| 0 | 73 | 1 | 74 | yes | 1 |
| 1 | 74 | 2 | 75 | yes | 1 |
| 2 | 75 | 3 | 71 | no | 0 |
| 2 | 75 | 4 | 69 | no | 0 |
| 2 | 75 | 5 | 72 | no | 0 |
| 2 | 75 | 6 | 76 | yes | 4 |
| 3 | 71 | 4 | 69 | no | 0 |
| 3 | 71 | 5 | 72 | yes | 2 |

### Optimized Solution from Canonical Skeleton:
```csharp
int[] DailyTemperatures(int[] temperatures)
{
    int n = temperatures.Length;
    int[] result = new int[n]; // Default 0 (no warmer day)
    
    Stack<int> stack = new(); // Monotonic decreasing (stores indices)
    
    for (int i = 0; i < n; i++)
    {
        // Pop all days with cooler temperature
        while (stack.Count > 0 && temperatures[i] > temperatures[stack.Peek()])
        {
            int idx = stack.Pop();
            result[idx] = i - idx; // Days until warmer
        }
        
        stack.Push(i);
    }
    
    return result;
}
```

### Explanation of Pruning:
- **Same as Next Greater Element** but stores **distance** instead of value
- **Monotonic decreasing stack:** Maintains days in decreasing temperature order
- **Key difference:** `result[idx] = i - idx` instead of `result[idx] = temperatures[i]`
- **Remaining in stack:** Days with no warmer day ahead (default 0)

### Optimized Solution Code Walkthrough / Variable Trace:
For `temperatures = [73,74,75,71,69,72,76,73]`:

| i | temp[i] | stack | compare | action | result |
|---|---------|-------|---------|--------|--------|
| 0 | 73 | [] | - | push 0 | [0,0,0,0,0,0,0,0] |
| 1 | 74 | [0] | 74>73 | pop 0, result[0]=1-0=1 | [1,0,0,0,0,0,0,0] |
| 1 | 74 | [] | - | push 1 | [1,0,0,0,0,0,0,0] |
| 2 | 75 | [1] | 75>74 | pop 1, result[1]=2-1=1 | [1,1,0,0,0,0,0,0] |
| 2 | 75 | [] | - | push 2 | [1,1,0,0,0,0,0,0] |
| 3 | 71 | [2] | 71>75? no | push 3 | [1,1,0,0,0,0,0,0] |
| 4 | 69 | [2,3] | 69>71? no | push 4 | [1,1,0,0,0,0,0,0] |
| 5 | 72 | [2,3,4] | 72>69 | pop 4, result[4]=5-4=1 | [1,1,0,0,1,0,0,0] |
| 5 | 72 | [2,3] | 72>71 | pop 3, result[3]=5-3=2 | [1,1,0,2,1,0,0,0] |
| 5 | 72 | [2] | 72>75? no | push 5 | [1,1,0,2,1,0,0,0] |
| 6 | 76 | [2,5] | 76>72 | pop 5, result[5]=6-5=1 | [1,1,0,2,1,1,0,0] |
| 6 | 76 | [2] | 76>75 | pop 2, result[2]=6-2=4 | [1,1,4,2,1,1,0,0] |
| 6 | 76 | [] | - | push 6 | [1,1,4,2,1,1,0,0] |
| 7 | 73 | [6] | 73>76? no | push 7 | [1,1,4,2,1,1,0,0] |

### Big-O Analysis:
- **Brute Force:** O(n²) → nested loops
- **Optimized:** O(n) → each element pushed/popped once
- **Space Complexity:** O(n) → stack

</details>


<details>
<summary><b>Variant #3: Stock Span Problem</b></summary>

## Variant #3: Stock Span Problem

### Input/Output:
- Input: `prices = [100,80,60,70,60,75,85]`
- Output: `[1,1,1,2,1,4,6]` (consecutive days with price ≤ current)

### Full State Space:
For each day, count consecutive days backward with price ≤ current.
```
Day 0 (100): span=1 (only itself)
Day 1 (80): span=1 (100>80, stop)
Day 2 (60): span=1 (80>60, stop)
Day 3 (70): span=2 (60≤70, 80>70, stop)
Day 4 (60): span=1 (70>60, stop)
Day 5 (75): span=4 (60,70,60,60 all ≤75, 80>75, stop)
Day 6 (85): span=6 (75,60,70,60,80 all ≤85, 100>85, stop)
```

### Expected/Pruned State Space:
Monotonic stack maintains indices of larger prices.
```
Span = distance to previous larger price
Stack stores indices where prices are strictly greater
```

### State Space Leading to Output:
Stack stores potential "previous greater" candidates.

### Brute Force Canonical Skeleton:
```csharp
int[] StockSpanBruteForce(int[] prices)
{
    int n = prices.Length;
    int[] span = new int[n];
    
    for (int i = 0; i < n; i++)
    {
        span[i] = 1; // At least current day
        
        // Look backwards for consecutive days with price <= current
        for (int j = i - 1; j >= 0 && prices[j] <= prices[i]; j--)
        {
            span[i]++;
        }
    }
    
    return span;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `prices = [100,80,60,70,60,75,85]`:

| i | prices[i] | j | prices[j] | prices[j] <= prices[i] | span[i] |
|---|-----------|---|-----------|------------------------|--------|
| 0 | 100 | - | - | - | 1 |
| 1 | 80 | 0 | 100 | no | 1 |
| 2 | 60 | 1 | 80 | no | 1 |
| 3 | 70 | 2 | 60 | yes | 2 |
| 3 | 70 | 1 | 80 | no | 2 |
| 4 | 60 | 3 | 70 | no | 1 |
| 5 | 75 | 4 | 60 | yes | 2 |
| 5 | 75 | 3 | 70 | yes | 3 |
| 5 | 75 | 2 | 60 | yes | 4 |
| 5 | 75 | 1 | 80 | no | 4 |
| 6 | 85 | 5 | 75 | yes | 2 |
| 6 | 85 | 4 | 60 | yes | 3 |
| 6 | 85 | 3 | 70 | yes | 4 |
| 6 | 85 | 2 | 60 | yes | 5 |
| 6 | 85 | 1 | 80 | yes | 6 |
| 6 | 85 | 0 | 100 | no | 6 |

### Optimized Solution from Canonical Skeleton:
```csharp
int[] StockSpan(int[] prices)
{
    int n = prices.Length;
    int[] span = new int[n];
    
    Stack<int> stack = new(); // Monotonic decreasing (stores indices)
    
    for (int i = 0; i < n; i++)
    {
        // Pop all days with price <= current
        while (stack.Count > 0 && prices[stack.Peek()] <= prices[i])
        {
            stack.Pop();
        }
        
        // Span = distance to previous greater (or start if none)
        span[i] = stack.Count == 0 ? i + 1 : i - stack.Peek();
        
        stack.Push(i);
    }
    
    return span;
}
```

### Explanation of Pruning:
- **Monotonic decreasing stack:** Maintains prices in decreasing order
- **Key insight:** Pop all days with price ≤ current (they're covered by current span)
- **Span calculation:** If stack empty, span = i+1 (all previous days). Otherwise, span = i - stack.Peek()
- **Why it works:** Stack top is the nearest day with price > current

### Optimized Solution Code Walkthrough / Variable Trace:
For `prices = [100,80,60,70,60,75,85]`:

| i | prices[i] | stack | pop | stack.Peek() | span[i] calculation | span[i] |
|---|-----------|-------|-----|--------------|---------------------|--------|
| 0 | 100 | [] | - | - | 0+1 | 1 |
| 0 | 100 | [0] | - | - | - | 1 |
| 1 | 80 | [0] | no (100>80) | 0 | 1-0 | 1 |
| 1 | 80 | [0,1] | - | - | - | 1 |
| 2 | 60 | [0,1] | no (80>60) | 1 | 2-1 | 1 |
| 2 | 60 | [0,1,2] | - | - | - | 1 |
| 3 | 70 | [0,1,2] | yes (60≤70) | - | - | - |
| 3 | 70 | [0,1] | no (80>70) | 1 | 3-1 | 2 |
| 3 | 70 | [0,1,3] | - | - | - | 2 |
| 4 | 60 | [0,1,3] | no (70>60) | 3 | 4-3 | 1 |
| 4 | 60 | [0,1,3,4] | - | - | - | 1 |
| 5 | 75 | [0,1,3,4] | yes (60≤75) | - | - | - |
| 5 | 75 | [0,1,3] | yes (70≤75) | - | - | - |
| 5 | 75 | [0,1] | no (80>75) | 1 | 5-1 | 4 |
| 5 | 75 | [0,1,5] | - | - | - | 4 |
| 6 | 85 | [0,1,5] | yes (75≤85) | - | - | - |
| 6 | 85 | [0,1] | yes (80≤85) | - | - | - |
| 6 | 85 | [0] | no (100>85) | 0 | 6-0 | 6 |
| 6 | 85 | [0,6] | - | - | - | 6 |

### Big-O Analysis:
- **Brute Force:** O(n²) → nested loops backwards
- **Optimized:** O(n) → each element pushed/popped once
- **Space Complexity:** O(n) → stack

</details>


<details>
<summary><b>Variant #4: Largest Rectangle in Histogram</b></summary>

## Variant #4: Largest Rectangle in Histogram

### Input/Output:
- Input: `heights = [2,1,5,6,2,3]`
- Output: `10` (rectangle with height 5, width 2)

### Full State Space:
All possible rectangles formed by subarray ranges.
```
For each pair (i, j): find min height in [i, j], area = min_height × (j-i+1)
Total: n×(n+1)/2 subarrays = O(n²)
```
```csharp
void GenerateAllRectangles(int[] heights)
{
    for (int i = 0; i < heights.Length; i++)
    {
        for (int j = i; j < heights.Length; j++)
        {
            int minHeight = int.MaxValue;
            for (int k = i; k <= j; k++)
                minHeight = Math.Min(minHeight, heights[k]);
            int area = minHeight * (j - i + 1);
            Console.WriteLine($"[{i},{j}]: minHeight={minHeight}, width={j-i+1}, area={area}");
        }
    }
}
```

### Expected/Pruned State Space:
For each bar, find left and right boundaries where it's the minimum.
```
For each height h at index i:
  - Find left boundary: nearest bar to left with height < h
  - Find right boundary: nearest bar to right with height < h
  - Area = h × (right - left - 1)
Total: O(n) with monotonic stack
```

### State Space Leading to Output:
Each bar extends left/right until a shorter bar blocks it.

### Brute Force Canonical Skeleton:
```csharp
int LargestRectangleBruteForce(int[] heights)
{
    int maxArea = 0;
    int n = heights.Length;
    
    // Try all possible subarrays
    for (int i = 0; i < n; i++)
    {
        int minHeight = heights[i];
        
        for (int j = i; j < n; j++)
        {
            minHeight = Math.Min(minHeight, heights[j]);
            int width = j - i + 1;
            int area = minHeight * width;
            maxArea = Math.Max(maxArea, area);
        }
    }
    
    return maxArea;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `heights = [2,1,5,6,2,3]`:

| i | j | heights[i..j] | minHeight | width | area | maxArea |
|---|---|---------------|-----------|-------|------|--------|
| 0 | 0 | [2] | 2 | 1 | 2 | 2 |
| 0 | 1 | [2,1] | 1 | 2 | 2 | 2 |
| 0 | 2 | [2,1,5] | 1 | 3 | 3 | 3 |
| 0 | 3 | [2,1,5,6] | 1 | 4 | 4 | 4 |
| 0 | 4 | [2,1,5,6,2] | 1 | 5 | 5 | 5 |
| 0 | 5 | [2,1,5,6,2,3] | 1 | 6 | 6 | 6 |
| 1 | 1 | [1] | 1 | 1 | 1 | 6 |
| 1 | 2 | [1,5] | 1 | 2 | 2 | 6 |
| ... | ... | ... | ... | ... | ... | ... |
| 2 | 3 | [5,6] | 5 | 2 | 10 | 10 |
| ... | ... | ... | ... | ... | ... | 10 |

### Optimized Solution from Canonical Skeleton:
```csharp
int LargestRectangleArea(int[] heights)
{
    int maxArea = 0;
    Stack<int> stack = new(); // Monotonic increasing (stores indices)
    
    for (int i = 0; i <= heights.Length; i++)
    {
        // Use 0 as sentinel for end
        int currentHeight = i == heights.Length ? 0 : heights[i];
        
        // Pop all bars taller than current
        while (stack.Count > 0 && heights[stack.Peek()] > currentHeight)
        {
            int height = heights[stack.Pop()];
            
            // Width = distance from left boundary to current
            int width = stack.Count == 0 ? i : i - stack.Peek() - 1;
            
            maxArea = Math.Max(maxArea, height * width);
        }
        
        stack.Push(i);
    }
    
    return maxArea;
}
```

### Explanation of Pruning:
- **Monotonic increasing stack:** Maintains bars in increasing height order
- **Key insight:** When we encounter shorter bar, pop taller bars and calculate their max rectangles
- **Width calculation:** 
  - If stack empty after pop: width = i (bar extends to start)
  - Otherwise: width = i - stack.Peek() - 1 (between boundaries)
- **Sentinel:** Add 0 at end to flush remaining bars in stack
- **Why it works:** Each bar is limited by first shorter bar on left and right

### Optimized Solution Code Walkthrough / Variable Trace:
For `heights = [2,1,5,6,2,3]` (with sentinel 0 at end):

| i | currentHeight | stack | pop? | height | width calc | area | maxArea |
|---|---------------|-------|------|--------|------------|------|--------|
| 0 | 2 | [] | no | - | - | - | 0 |
| 0 | 2 | [0] | - | - | - | - | 0 |
| 1 | 1 | [0] | yes (2>1) | 2 | 1 (stack empty) | 2 | 2 |
| 1 | 1 | [] | no | - | - | - | 2 |
| 1 | 1 | [1] | - | - | - | - | 2 |
| 2 | 5 | [1] | no (1<5) | - | - | - | 2 |
| 2 | 5 | [1,2] | - | - | - | - | 2 |
| 3 | 6 | [1,2] | no (5<6) | - | - | - | 2 |
| 3 | 6 | [1,2,3] | - | - | - | - | 2 |
| 4 | 2 | [1,2,3] | yes (6>2) | 6 | 4-2-1=1 | 6 | 6 |
| 4 | 2 | [1,2] | yes (5>2) | 5 | 4-1-1=2 | 10 | 10 |
| 4 | 2 | [1] | no (1<2) | - | - | - | 10 |
| 4 | 2 | [1,4] | - | - | - | - | 10 |
| 5 | 3 | [1,4] | no (2<3) | - | - | - | 10 |
| 5 | 3 | [1,4,5] | - | - | - | - | 10 |
| 6 | 0 | [1,4,5] | yes (3>0) | 3 | 6-4-1=1 | 3 | 10 |
| 6 | 0 | [1,4] | yes (2>0) | 2 | 6-1-1=4 | 8 | 10 |
| 6 | 0 | [1] | yes (1>0) | 1 | 6 (empty) | 6 | 10 |

### Big-O Analysis:
- **Brute Force:** O(n²) → all subarrays with min finding
- **Optimized:** O(n) → each bar pushed/popped once
- **Space Complexity:** O(n) → stack

</details>


<details>
<summary><b>Variant #5: Trapping Rain Water</b></summary>

## Variant #5: Trapping Rain Water

### Input/Output:
- Input: `height = [0,1,0,2,1,0,1,3,2,1,2,1]`
- Output: `6` (units of water trapped)

### Full State Space:
Water trapped at each position = min(maxLeft, maxRight) - height[i].
```
For each position i:
  maxLeft = max height in [0..i-1]
  maxRight = max height in [i+1..n-1]
  water[i] = max(0, min(maxLeft, maxRight) - height[i])
Total: O(n²) or O(n) with preprocessing
```

### Expected/Pruned State Space:
Use monotonic stack to find boundaries or two-pointer technique.
```
Stack approach: Track descending bars, calculate water when ascending
Two-pointer: Track maxLeft and maxRight dynamically
```

### State Space Leading to Output:
Water fills gaps between taller bars.

### Brute Force Canonical Skeleton:
```csharp
int TrapRainWaterBruteForce(int[] height)
{
    int totalWater = 0;
    int n = height.Length;
    
    for (int i = 0; i < n; i++)
    {
        // Find max height to left
        int maxLeft = 0;
        for (int j = 0; j < i; j++)
        {
            maxLeft = Math.Max(maxLeft, height[j]);
        }
        
        // Find max height to right
        int maxRight = 0;
        for (int j = i + 1; j < n; j++)
        {
            maxRight = Math.Max(maxRight, height[j]);
        }
        
        // Water trapped at position i
        int waterLevel = Math.Min(maxLeft, maxRight);
        if (waterLevel > height[i])
        {
            totalWater += waterLevel - height[i];
        }
    }
    
    return totalWater;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `height = [0,1,0,2,1,0,1,3,2,1,2,1]`:

| i | height[i] | maxLeft | maxRight | waterLevel | water at i | totalWater |
|---|-----------|---------|----------|------------|------------|------------|
| 0 | 0 | 0 | 3 | 0 | 0 | 0 |
| 1 | 1 | 0 | 3 | 0 | 0 | 0 |
| 2 | 0 | 1 | 3 | 1 | 1 | 1 |
| 3 | 2 | 1 | 3 | 1 | 0 | 1 |
| 4 | 1 | 2 | 3 | 2 | 1 | 2 |
| 5 | 0 | 2 | 3 | 2 | 2 | 4 |
| 6 | 1 | 2 | 3 | 2 | 1 | 5 |
| 7 | 3 | 2 | 2 | 2 | 0 | 5 |
| 8 | 2 | 3 | 2 | 2 | 0 | 5 |
| 9 | 1 | 3 | 2 | 2 | 1 | 6 |
| 10 | 2 | 3 | 1 | 1 | 0 | 6 |
| 11 | 1 | 3 | 0 | 0 | 0 | 6 |

### Optimized Solution from Canonical Skeleton (Two-Pointer):
```csharp
int TrapRainWater(int[] height)
{
    int left = 0, right = height.Length - 1;
    int maxLeft = 0, maxRight = 0;
    int totalWater = 0;
    
    while (left < right)
    {
        if (height[left] < height[right])
        {
            // Process left side
            if (height[left] >= maxLeft)
            {
                maxLeft = height[left];
            }
            else
            {
                totalWater += maxLeft - height[left];
            }
            left++;
        }
        else
        {
            // Process right side
            if (height[right] >= maxRight)
            {
                maxRight = height[right];
            }
            else
            {
                totalWater += maxRight - height[right];
            }
            right--;
        }
    }
    
    return totalWater;
}
```

### Optimized Solution (Monotonic Stack Alternative):
```csharp
int TrapRainWaterStack(int[] height)
{
    int totalWater = 0;
    Stack<int> stack = new(); // Monotonic decreasing (stores indices)
    
    for (int i = 0; i < height.Length; i++)
    {
        // When we find a taller bar, calculate trapped water
        while (stack.Count > 0 && height[i] > height[stack.Peek()])
        {
            int bottom = stack.Pop();
            
            if (stack.Count == 0) break;
            
            int left = stack.Peek();
            int width = i - left - 1;
            int waterHeight = Math.Min(height[left], height[i]) - height[bottom];
            
            totalWater += width * waterHeight;
        }
        
        stack.Push(i);
    }
    
    return totalWater;
}
```

### Explanation of Pruning:
- **Two-Pointer Approach:** 
  - Move from both ends toward center
  - Process the side with smaller height (water limited by shorter wall)
  - Track maxLeft and maxRight dynamically
  - Water at position = max - height (if max > height)
  
- **Stack Approach:**
  - Monotonic decreasing stack tracks potential left boundaries
  - When taller bar appears, calculate water in the valley
  - Water forms rectangle: width × (min(left, right) - bottom)

### Optimized Solution Code Walkthrough / Variable Trace (Two-Pointer):
For `height = [0,1,0,2,1,0,1,3,2,1,2,1]`:

| left | right | h[left] | h[right] | maxLeft | maxRight | action | water | total |
|------|-------|---------|----------|---------|----------|--------|-------|-------|
| 0 | 11 | 0 | 1 | 0 | 0 | left: set maxLeft=0 | 0 | 0 |
| 1 | 11 | 1 | 1 | 0 | 0 | left: set maxLeft=1 | 0 | 0 |
| 2 | 11 | 0 | 1 | 1 | 0 | left: add 1-0 | 1 | 1 |
| 3 | 11 | 2 | 1 | 1 | 0 | right: set maxRight=1 | 0 | 1 |
| 3 | 10 | 2 | 2 | 1 | 1 | left: set maxLeft=2 | 0 | 1 |
| 4 | 10 | 1 | 2 | 2 | 1 | left: add 2-1 | 1 | 2 |
| 5 | 10 | 0 | 2 | 2 | 1 | left: add 2-0 | 2 | 4 |
| 6 | 10 | 1 | 2 | 2 | 1 | left: add 2-1 | 1 | 5 |
| 7 | 10 | 3 | 2 | 2 | 1 | right: set maxRight=2 | 0 | 5 |
| 7 | 9 | 3 | 1 | 2 | 2 | right: add 2-1 | 1 | 6 |
| 7 | 8 | 3 | 2 | 2 | 2 | right: set max=2 | 0 | 6 |
| 7 | 7 | done | - | - | - | - | - | 6 |

### Big-O Analysis:
- **Brute Force:** O(n²) → for each position, scan left and right
- **Optimized (Two-Pointer):** O(n) → single pass, O(1) space
- **Optimized (Stack):** O(n) time, O(n) space

</details>

<details>
<summary><b>Variant #6: Sum of Subarray Minimums</b></summary>

## Variant #6: Sum of Subarray Minimums

### Input/Output:
- Input: `arr = [3,1,2,4]`
- Output: `17` (sum of minimums across all subarrays)

### Full State Space:
All subarrays and their minimums.
```
[3] → min=3
[3,1] → min=1
[3,1,2] → min=1
[3,1,2,4] → min=1
[1] → min=1
[1,2] → min=1
[1,2,4] → min=1
[2] → min=2
[2,4] → min=2
[4] → min=4
Sum = 3+1+1+1+1+1+1+2+2+4 = 17
```

### Expected/Pruned State Space:
For each element, count how many subarrays it's the minimum of.
```
For arr[i]:
  - Find left boundary: nearest smaller element to left
  - Find right boundary: nearest smaller element to right
  - Count = (i - left) × (right - i)
  - Contribution = arr[i] × count
```

### State Space Leading to Output:
Each element contributes to all subarrays where it's the minimum.

### Brute Force Canonical Skeleton:
```csharp
int SumSubarrayMinsBruteForce(int[] arr)
{
    int sum = 0;
    int n = arr.Length;
    const int MOD = 1_000_000_007;
    
    // Generate all subarrays
    for (int i = 0; i < n; i++)
    {
        int min = arr[i];
        
        for (int j = i; j < n; j++)
        {
            min = Math.Min(min, arr[j]);
            sum = (sum + min) % MOD;
        }
    }
    
    return sum;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `arr = [3,1,2,4]`:

| i | j | subarray | min | sum |
|---|---|----------|-----|-----|
| 0 | 0 | [3] | 3 | 3 |
| 0 | 1 | [3,1] | 1 | 4 |
| 0 | 2 | [3,1,2] | 1 | 5 |
| 0 | 3 | [3,1,2,4] | 1 | 6 |
| 1 | 1 | [1] | 1 | 7 |
| 1 | 2 | [1,2] | 1 | 8 |
| 1 | 3 | [1,2,4] | 1 | 9 |
| 2 | 2 | [2] | 2 | 11 |
| 2 | 3 | [2,4] | 2 | 13 |
| 3 | 3 | [4] | 4 | 17 |

### Optimized Solution from Canonical Skeleton:
```csharp
int SumSubarrayMins(int[] arr)
{
    int n = arr.Length;
    const int MOD = 1_000_000_007;
    
    // Find previous less element for each index
    int[] prevLess = new int[n];
    Stack<int> stack = new();
    for (int i = 0; i < n; i++)
    {
        while (stack.Count > 0 && arr[stack.Peek()] >= arr[i])
        {
            stack.Pop();
        }
        prevLess[i] = stack.Count == 0 ? -1 : stack.Peek();
        stack.Push(i);
    }
    
    // Find next less element for each index
    int[] nextLess = new int[n];
    stack.Clear();
    for (int i = n - 1; i >= 0; i--)
    {
        while (stack.Count > 0 && arr[stack.Peek()] > arr[i])
        {
            stack.Pop();
        }
        nextLess[i] = stack.Count == 0 ? n : stack.Peek();
        stack.Push(i);
    }
    
    // Calculate contribution of each element
    long sum = 0;
    for (int i = 0; i < n; i++)
    {
        long left = i - prevLess[i];  // Distance to previous smaller
        long right = nextLess[i] - i;  // Distance to next smaller
        long contribution = (arr[i] * left % MOD * right % MOD) % MOD;
        sum = (sum + contribution) % MOD;
    }
    
    return (int)sum;
}
```

### Explanation of Pruning:
- **Key insight:** For each element arr[i], count subarrays where it's the minimum
- **Boundaries:** 
  - Previous less element (prevLess): how far left we can extend
  - Next less element (nextLess): how far right we can extend
- **Counting:** Number of subarrays = (i - prevLess[i]) × (nextLess[i] - i)
- **Contribution:** arr[i] × count
- **Note:** Use >= for prevLess and > for nextLess to avoid double-counting when duplicates exist

### Optimized Solution Code Walkthrough / Variable Trace:
For `arr = [3,1,2,4]`:

**Finding prevLess (previous smaller element):**

| i | arr[i] | stack | pop? | prevLess[i] | stack after |
|---|--------|-------|------|-------------|-------------|
| 0 | 3 | [] | no | -1 | [0] |
| 1 | 1 | [0] | yes (3>=1) | -1 | [1] |
| 2 | 2 | [1] | no (1<2) | 1 | [1,2] |
| 3 | 4 | [1,2] | no (2<4) | 2 | [1,2,3] |

**Finding nextLess (next smaller element):**

| i | arr[i] | stack | pop? | nextLess[i] | stack after |
|---|--------|-------|------|-------------|-------------|
| 3 | 4 | [] | no | 4 | [3] |
| 2 | 2 | [3] | yes (4>2) | 4 | [2] |
| 1 | 1 | [2] | yes (2>1) | 4 | [1] |
| 0 | 3 | [1] | no (1<3) | 1 | [1,0] |

**Calculating contributions:**

| i | arr[i] | prevLess[i] | nextLess[i] | left | right | count | contribution | sum |
|---|--------|-------------|-------------|------|-------|-------|--------------|-----|
| 0 | 3 | -1 | 1 | 1 | 1 | 1 | 3×1 = 3 | 3 |
| 1 | 1 | -1 | 4 | 2 | 3 | 6 | 1×6 = 6 | 9 |
| 2 | 2 | 1 | 4 | 1 | 2 | 2 | 2×2 = 4 | 13 |
| 3 | 4 | 2 | 4 | 1 | 1 | 1 | 4×1 = 4 | 17 |

### Big-O Analysis:
- **Brute Force:** O(n²) → all subarrays with min finding
- **Optimized:** O(n) → two monotonic stack passes + contribution calculation
- **Space Complexity:** O(n) → two arrays + stack

</details>


<details>
<summary><b>Variant #7: Remove K Digits</b></summary>

## Variant #7: Remove K Digits

### Input/Output:
- Input: `num = "1432219", k = 3`
- Output: `"1219"`

### Full State Space:
All ways to remove k digits from the string.
```
Total combinations: C(n, k) = C(7, 3) = 35
For each combination, convert to number and find minimum
Example combinations:
  Remove indices [0,1,2]: "2219"
  Remove indices [1,2,3]: "1219" ← smallest
  Remove indices [1,3,4]: "1229"
  ...
```

### Expected/Pruned State Space:
Greedy approach: remove digits that violate monotonic increasing order.
```
Build smallest number by maintaining monotonic increasing stack
Remove larger digits when smaller digit appears (if k > 0)
```

### State Space Leading to Output:
Stack maintains digits for smallest possible number.

### Brute Force Canonical Skeleton:
```csharp
string RemoveKDigitsBruteForce(string num, int k)
{
    int n = num.Length;
    
    if (k >= n) return "0";
    
    // Try all combinations of removing k digits
    List<string> results = new();
    
    void Backtrack(int idx, int removed, StringBuilder current)
    {
        if (removed == k)
        {
            string result = current.ToString();
            if (string.IsNullOrEmpty(result)) result = "0";
            else result = result.TrimStart('0');
            if (string.IsNullOrEmpty(result)) result = "0";
            results.Add(result);
            return;
        }
        
        if (idx == n) return;
        
        // Remove current digit
        Backtrack(idx + 1, removed + 1, current);
        
        // Keep current digit
        current.Append(num[idx]);
        Backtrack(idx + 1, removed, current);
        current.Length--;
    }
    
    Backtrack(0, 0, new StringBuilder());
    
    // Find minimum
    results.Sort((a, b) => {
        if (a.Length != b.Length) return a.Length.CompareTo(b.Length);
        return string.Compare(a, b);
    });
    
    return results[0];
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `num = "1432219", k = 3`:

Sample exploration (showing a few paths):

| idx | digit | action | removed | current | result |
|-----|-------|--------|---------|---------|--------|
| 0 | '1' | remove | 1 | "" | ... |
| 0 | '1' | keep | 0 | "1" | ... |
| 1 | '4' | remove | 1 | "1" | ... |
| 1 | '4' | keep | 0 | "14" | ... |
| ... | ... | ... | ... | ... | ... |

**Too many combinations to list. After exploring all C(7,3)=35 combinations:**
- Smallest: "1219" (removed indices 1,2,3 → '4','3','2')

### Optimized Solution from Canonical Skeleton:
```csharp
string RemoveKDigits(string num, int k)
{
    if (k >= num.Length) return "0";
    
    Stack<char> stack = new(); // Monotonic increasing
    
    for (int i = 0; i < num.Length; i++)
    {
        // Pop larger digits while we can still remove digits
        while (stack.Count > 0 && k > 0 && stack.Peek() > num[i])
        {
            stack.Pop();
            k--;
        }
        
        stack.Push(num[i]);
    }
    
    // Remove remaining k digits from end
    while (k > 0)
    {
        stack.Pop();
        k--;
    }
    
    // Build result (reverse stack)
    StringBuilder result = new();
    while (stack.Count > 0)
    {
        result.Insert(0, stack.Pop());
    }
    
    // Remove leading zeros
    while (result.Length > 1 && result[0] == '0')
    {
        result.Remove(0, 1);
    }
    
    return result.Length == 0 ? "0" : result.ToString();
}
```

### Explanation of Pruning:
- **Greedy strategy:** Remove digits that make number larger
- **Monotonic increasing stack:** Maintains smallest possible prefix
- **Key rule:** Pop stack when `stack.top > current AND k > 0`
- **Edge cases:**
  - If k digits remain after loop, remove from end (largest digits)
  - Remove leading zeros from result
  - Return "0" if result is empty

### Optimized Solution Code Walkthrough / Variable Trace:
For `num = "1432219", k = 3`:

| i | num[i] | stack | k | compare | action | k after | stack after |
|---|--------|-------|---|---------|--------|---------|-------------|
| 0 | '1' | [] | 3 | - | push | 3 | ['1'] |
| 1 | '4' | ['1'] | 3 | '1'<'4' | push | 3 | ['1','4'] |
| 2 | '3' | ['1','4'] | 3 | '4'>'3' | pop '4' | 2 | ['1'] |
| 2 | '3' | ['1'] | 2 | '1'<'3' | push | 2 | ['1','3'] |
| 3 | '2' | ['1','3'] | 2 | '3'>'2' | pop '3' | 1 | ['1'] |
| 3 | '2' | ['1'] | 1 | '1'<'2' | push | 1 | ['1','2'] |
| 4 | '2' | ['1','2'] | 1 | '2'='2' | push | 1 | ['1','2','2'] |
| 5 | '1' | ['1','2','2'] | 1 | '2'>'1' | pop '2' | 0 | ['1','2'] |
| 5 | '1' | ['1','2'] | 0 | k=0 | push | 0 | ['1','2','1'] |
| 6 | '9' | ['1','2','1'] | 0 | k=0 | push | 0 | ['1','2','1','9'] |

**Result:** "1219"

### Big-O Analysis:
- **Brute Force:** O(C(n,k) × n) → exponential
- **Optimized:** O(n) → single pass with stack
- **Space Complexity:** O(n) → stack

</details>


<details>
<summary><b>Variant #8: Next Greater Element in Circular Array</b></summary>

## Variant #8: Next Greater Element in Circular Array

### Input/Output:
- Input: `nums = [1,2,1]`
- Output: `[2,-1,2]` (circular: after last element, continue from first)

### Full State Space:
For each element, search circularly for next greater.
```
nums[0]=1: check [2,1,1,2] → next greater = 2
nums[1]=2: check [1,1,2] → no greater = -1
nums[2]=1: check [1,2] → next greater = 2 (wraps to start)
```

### Expected/Pruned State Space:
Process array twice with modulo indexing.
```
Simulate circular array by iterating 2n times: index = i % n
Use monotonic stack to find next greater
```

### State Space Leading to Output:
Stack tracks indices waiting for greater element, considering circular nature.

### Brute Force Canonical Skeleton:
```csharp
int[] NextGreaterElementsCircularBruteForce(int[] nums)
{
    int n = nums.Length;
    int[] result = new int[n];
    
    for (int i = 0; i < n; i++)
    {
        result[i] = -1;
        
        // Search circularly
        for (int j = 1; j < n; j++)
        {
            int idx = (i + j) % n;
            if (nums[idx] > nums[i])
            {
                result[i] = nums[idx];
                break;
            }
        }
    }
    
    return result;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `nums = [1,2,1]`:

| i | nums[i] | j | idx | nums[idx] | nums[idx] > nums[i] | result[i] |
|---|---------|---|-----|-----------|---------------------|----------|
| 0 | 1 | 1 | 1 | 2 | yes | 2 |
| 1 | 2 | 1 | 2 | 1 | no | -1 |
| 1 | 2 | 2 | 0 | 1 | no | -1 |
| 2 | 1 | 1 | 0 | 1 | no | -1 |
| 2 | 1 | 2 | 1 | 2 | yes | 2 |

### Optimized Solution from Canonical Skeleton:
```csharp
int[] NextGreaterElementsCircular(int[] nums)
{
    int n = nums.Length;
    int[] result = new int[n];
    Array.Fill(result, -1);
    
    Stack<int> stack = new(); // Monotonic decreasing (stores indices)
    
    // Process array twice for circular behavior
    for (int i = 0; i < 2 * n; i++)
    {
        int idx = i % n;
        
        // Pop all elements smaller than current
        while (stack.Count > 0 && nums[stack.Peek()] < nums[idx])
        {
            result[stack.Pop()] = nums[idx];
        }
        
        // Only push during first pass
        if (i < n)
        {
            stack.Push(idx);
        }
    }
    
    return result;
}
```

### Explanation of Pruning:
- **Circular simulation:** Process 2n elements using modulo (i % n)
- **Monotonic decreasing stack:** Same pattern as Next Greater Element
- **Key optimization:** Only push indices during first pass (i < n)
- **Second pass purpose:** Resolve remaining elements using circular wrap
- **Elements in stack after 2n:** No greater element exists (remain -1)

### Optimized Solution Code Walkthrough / Variable Trace:
For `nums = [1,2,1]`:

| i | idx | nums[idx] | stack | compare | action | result |
|---|-----|-----------|-------|---------|--------|--------|
| 0 | 0 | 1 | [] | - | push 0 | [-1,-1,-1] |
| 1 | 1 | 2 | [0] | 2>1 | pop 0, result[0]=2 | [2,-1,-1] |
| 1 | 1 | 2 | [] | - | push 1 | [2,-1,-1] |
| 2 | 2 | 1 | [1] | 1>2? no | push 2 | [2,-1,-1] |
| 3 | 0 | 1 | [1,2] | 1>1? no | no push (i>=n) | [2,-1,-1] |
| 4 | 1 | 2 | [1,2] | 2>1 | pop 2, result[2]=2 | [2,-1,2] |
| 4 | 1 | 2 | [1] | 2>2? no | no push (i>=n) | [2,-1,2] |
| 5 | 2 | 1 | [1] | 1>2? no | no push (i>=n) | [2,-1,2] |

### Big-O Analysis:
- **Brute Force:** O(n²) → for each element, search circularly
- **Optimized:** O(n) → process 2n elements, each pushed/popped once
- **Space Complexity:** O(n) → stack

</details>

---

# Key Takeaways

## Pattern Recognition
- **Monotonic Stack** = efficient way to find **previous/next greater/smaller** elements
- Typical signals: "next larger", "nearest smaller", "until a condition breaks"
- Problems involve **one-directional comparisons** where past elements can become irrelevant

## Stack Types
- **Monotonic Increasing:** Pop when current < top (find previous/next **greater**)
- **Monotonic Decreasing:** Pop when current > top (find previous/next **smaller**)

## Core Template
```csharp
Stack<int> stack = new(); // Usually stores indices
for (int i = 0; i < n; i++)
{
    while (stack.Count > 0 && condition)
    {
        // Process popped element
        int idx = stack.Pop();
        // Calculate result using idx and i
    }
    stack.Push(i);
}
```

## Common Patterns
1. **Next Greater/Smaller:** Store result when popping
2. **Span/Distance:** Calculate `i - stack.Peek()` for range
3. **Area/Rectangle:** Width between boundaries, height from stack
4. **Contribution Counting:** Use prev/next boundaries to count subarrays
5. **Circular Arrays:** Process 2n elements with modulo indexing
6. **Greedy Selection:** Pop to maintain optimal prefix (Remove K Digits)

## Why O(n)?
Each element is **pushed once** and **popped at most once** → O(2n) = O(n)
---

# Mental Checklist for Monotonic Stack

- Am I looking for next greater/smaller?
- Do comparisons move only in one direction?
- Can previous elements become irrelevant?
- Does each element get “resolved” once?

If yes → Monotonic Stack applies.
