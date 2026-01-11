# Variant: Container With Most Water

**Pattern:** Two Pointers  
**Difficulty:** Medium  
**LeetCode:** #11

---

## Problem Statement

You are given an integer array `height` of length `n`. There are `n` vertical lines drawn such that the two endpoints of the `i-th` line are `(i, 0)` and `(i, height[i])`.

Find two lines that together with the x-axis form a container, such that the container contains the most water.

Return the **maximum amount of water** a container can store.

### Input/Output:
- Input: `height = [1,8,6,2,5,4,8,3,7]`
- Output: `49`
- Explanation: Max area between indices 1 and 8: `min(8,7) × (8-1) = 7 × 7 = 49`

---

## **Step 1: Derive Full State Space from Problem**

### **Problem Visualization:**
```
Input: height = [1,8,6,2,5,4,8,3,7]

Vertical lines form containers:

8 | |
7 | |                   |
6 | | |                 |
5 | | |   |             |
4 | | |   | |           |
3 | | |   | | |     |   |
2 | | | | | | |   | | |
1 | | | | | | | | | | |
  0 1 2 3 4 5 6 7 8

Best container: lines at index 1 and 8
Area = min(height[1], height[8]) × (8-1)
     = min(8, 7) × 7
     = 7 × 7 = 49

  ┌─────────────────────────────┐
8 | |~~~~~~~~~~~~~~~~~~~~~~~|
7 | |~~~~~~~~~~~~~~~~~~~~~~~|
6 | |~~~~~~~~~~~~~~~~~~~~~~~|
5 | |~~~~~~~~~~~~~~~~~~~~~~~|
4 | |~~~~~~~~~~~~~~~~~~~~~~~|
3 | |~~~~~~~~~~~~~~~~~~~~~~~|
2 | |~~~~~~~~~~~~~~~~~~~~~~~|
  └─────────────────────────────┘
    1                       8
    Width = 7, Height = 7 (limited by shorter line)
```

### **Core Question:**
Which pair of lines creates maximum container area?

### **Deriving from First Principles:**

**Observation 1:** Area formula
- Container is bounded by two vertical lines at positions i and j
- Water fills to height of **shorter line** (water overflows taller line)
- Area = min(height[i], height[j]) × |j - i|

**Observation 2:** Two factors determine area
- **Width:** Distance between lines (j - i)
- **Height:** Minimum of two line heights
- These factors often trade off: wider containers may have shorter height

**Observation 3:** Must check all pairs?
- Brute force: Try all n(n-1)/2 pairs
- Can we prune? If we move pointer with taller line inward, we:
  - Decrease width (always bad)
  - Cannot increase height (still limited by shorter line)
  - So moving taller line pointer is always worse!

### **Problem Analysis:**
- Choose two vertical lines (at different positions)
- Area = `min(height[i], height[j]) × (j - i)`
- Find maximum area among all pairs

### **State Space Definition:**

**Reference:** See [All Pairs State Space](../../state_spaces/all_pairs.md) for detailed mathematical derivation

- **Type**: All pairs of two **different** lines
- **Structure**: Pairs (i, j) where `0 ≤ i < j < n`
- **Cardinality**: n(n-1)/2 pairs = **O(n²)**
- **Generation**: Nested loops - outer for left line, inner for right line

### **Full State Space Enumeration:**
For `height = [1,8,6,2,5,4,8,3,7]` (n=9), there are 9×8/2 = **36 pairs**:

```
All possible containers (i, j):
(0,1), (0,2), (0,3), ..., (0,8)  ← 8 pairs with left=0
(1,2), (1,3), (1,4), ..., (1,8)  ← 7 pairs with left=1
(2,3), (2,4), (2,5), ..., (2,8)  ← 6 pairs with left=2
...
(7,8)                            ← 1 pair with left=7

Total: 8+7+6+5+4+3+2+1 = 36 pairs
```

**Sample calculations:**
```
(0,1): min(1,8) × (1-0) = 1 × 1 = 1
(1,8): min(8,7) × (8-1) = 7 × 7 = 49 ✅
(0,8): min(1,7) × (8-0) = 1 × 8 = 8
```

---

## **Step 2: Brute Force Solution with Value Tracing**

### **Brute Force Approach:**
1. Try all pairs of lines
2. For each pair, calculate area = min(height[i], height[j]) × width
3. Track maximum area

### **Brute Force Code:**
```csharp
int MaxAreaBruteForce(int[] height) 
{
    int maxArea = 0;
    
    // Try all pairs
    for (int i = 0; i < height.Length; i++)
    {
        for (int j = i + 1; j < height.Length; j++)
        {
            int h = Math.Min(height[i], height[j]);
            int w = j - i;
            int area = h * w;
            
            maxArea = Math.Max(maxArea, area);
        }
    }
    
    return maxArea;
}
```

### **Brute Force Value Trace (height = [1,8,6,2,5,4,8,3,7], showing key pairs):**

| i | j | height[i] | height[j] | min height | width | area | maxArea |
|---|---|-----------|-----------|------------|-------|------|----------|
| 0 | 1 | 1         | 8         | 1          | 1     | 1    | 1        |
| 0 | 2 | 1         | 6         | 1          | 2     | 2    | 2        |
| 0 | 8 | 1         | 7         | 1          | 8     | 8    | 8        |
| 1 | 2 | 8         | 6         | 6          | 1     | 6    | 8        |
| 1 | 6 | 8         | 8         | 8          | 5     | 40   | 40       |
| 1 | 8 | 8         | 7         | 7          | 7     | 49   | **49**   |
| 2 | 8 | 6         | 7         | 6          | 6     | 36   | 49       |
| ... | ... | ...     | ...       | ...        | ...   | ...  | ...      |

**Result: 49**

### **Complexity:**
- **Time**: O(n²) - check all pairs
- **Space**: O(1)

---

## **Step 3: Can We Prune the Brute Force?**

### **Key Observations:**

1. **Area Formula**: `area = min(h[i], h[j]) × (j - i)`
   - Height is limited by **shorter** line
   - Width is distance between lines

2. **Width Decreases**: When moving pointers inward, width always decreases
   ```
   Start: width = n-1 (maximum)
   Each inward move: width decreases by 1
   ```

3. **Height Trade-off**: To compensate for lost width, need taller height
   ```
   If move shorter line → might get taller line → area could increase
   If move taller line → keep shorter bottleneck → area MUST decrease
   ```

4. **Elimination Logic**: When we have heights h1 < h2:
   ```
   Current: h1 ---- width ---- h2  (area = h1 × width)
   
   Move h2 inward: h1 -- new_width -- h2'  
     → Still limited by h1, but smaller width
     → DEFINITELY worse (h1 × smaller_width < h1 × width)
   
   Move h1 inward: h1' -- new_width -- h2
     → Maybe h1' > h1, could compensate for lost width
     → MIGHT be better
   ```

### **Can We Do Better?**
**YES!** Start with widest container, move shorter line inward (only way to improve)

---

## **Step 4: Optimal Solution - Two Pointers**

### **Key Insight:**
Start with **maximum width** (left=0, right=n-1), then:
- Always move the **shorter** line inward
- Why? Because moving the taller line can only decrease area (width decreases, height still limited by shorter line)
- Moving shorter line gives chance to find taller line that compensates for lost width

### **Why This Works:**
```
height = [1, 8, 6, 2, 5, 4, 8, 3, 7]
         ↑                       ↑
         left=0                 right=8
         h=1                    h=7

Area = min(1,7) × 8 = 1 × 8 = 8

Move left (shorter) because:
- Moving right: keeps h=1, width=7 → area = 1×7 = 7 (worse)
- Moving left: might get h>1, width=7 → could be better
```

### **Canonical Skeleton Applied:**
```csharp
int TwoPointersTemplate(int[] nums) {
    int left = 0, right = nums.Length - 1;
    int result = 0;
    
    while (left < right) {
        // Process current pair
        result = UpdateResult(result, nums[left], nums[right]);
        
        if (nums[left] < nums[right]) {
            left++;
        }
        else {
            right--;
        }
    }
    
    return result;
}
```

### **Optimized Solution:**
```csharp
int MaxArea(int[] height) 
{
    int left = 0, right = height.Length - 1;
    int maxArea = 0;
    
    while (left < right) 
    {
        // Calculate current area
        int h = Math.Min(height[left], height[right]);
        int w = right - left;
        int area = h * w;
        
        maxArea = Math.Max(maxArea, area);
        
        // Move shorter line inward
        if (height[left] < height[right]) {
            left++;
        }
        else {
            right--;
        }
    }
    
    return maxArea;
}
```

### **Changes from Skeleton:**
1. **Opposite ends**: Start left=0, right=n-1 (maximum width)
2. **UpdateResult**: Calculate area and track maximum
3. **Move logic**: Move whichever pointer points to shorter line
4. **Termination**: When pointers meet, no more containers possible

### **Optimized Value Trace (height = [1,8,6,2,5,4,8,3,7]):**

| Step | left | right | h[left] | h[right] | min | width | area | maxArea | Move |
|------|------|-------|---------|----------|-----|-------|------|---------|------|
| 1    | 0    | 8     | 1       | 7        | 1   | 8     | 8    | 8       | left++ (1<7) |
| 2    | 1    | 8     | 8       | 7        | 7   | 7     | 49   | **49**  | right-- (8>7) |
| 3    | 1    | 7     | 8       | 3        | 3   | 6     | 18   | 49      | right-- (8>3) |
| 4    | 1    | 6     | 8       | 8        | 8   | 5     | 40   | 49      | left++ (8=8) |
| 5    | 2    | 6     | 6       | 8        | 6   | 4     | 24   | 49      | left++ (6<8) |
| 6    | 3    | 6     | 2       | 8        | 2   | 3     | 6    | 49      | left++ (2<8) |
| 7    | 4    | 6     | 5       | 8        | 5   | 2     | 10   | 49      | left++ (5<8) |
| 8    | 5    | 6     | 4       | 8        | 4   | 1     | 4    | 49      | left++ (4<8) |

**Result: 49** (found at step 2: indices 1 and 8)

### **State Space Comparison:**

| Approach | Pairs Checked | Pattern | Total Comparisons |
|----------|---------------|---------|-------------------|
| Brute Force | All 36 pairs | Enumerate all | O(n²) = 36 |
| Two Pointers | 8 pairs | Start wide, move inward | O(n) = 8 |

**Why We Don't Miss Optimal:**
Suppose optimal is between indices i and j (i < j):
- We'll eventually have left=i or right=j
- When we reach this configuration, we calculate its area
- We won't skip it because:
  - To skip, we'd need to move both pointers past i and j
  - But we only move the shorter line
  - So we'll check (i, something ≥ j) or (something ≤ i, j)
  - The optimal configuration will be evaluated

### **Big-O Analysis:**
- **Time**: O(n) - each pointer moves at most n times
- **Space**: O(1)
- **Optimization**: O(n²) → **O(n)** ✅

### **Key Takeaway:**
**Greedy Two Pointers with invariant**: Start with maximum width, then greedily move the pointer that could potentially improve the solution (shorter line). This works because moving the taller line is ALWAYS worse (proven by width decrease + same height bottleneck).

---

## **Related Problems:**
- Trapping Rain Water - Similar but more complex (calculate total trapped water)
- Largest Rectangle in Histogram - Use monotonic stack instead
- Maximum Width Ramp - Different greedy strategy

## **Tags:**
`two-pointers` `array` `greedy` `O(n²)-state-space` `medium` `optimization`
