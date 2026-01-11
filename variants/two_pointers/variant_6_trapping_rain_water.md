# Variant: Trapping Rain Water

**Pattern:** Two Pointers  
**Difficulty:** Hard  
**LeetCode:** #42

---

## Problem Statement

Given `n` non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it can trap after raining.

### Input/Output:
- Input: `height = [0,1,0,2,1,0,1,3,2,1,2,1]`
- Output: `6`

---

## **Step 1: State Space Derivation**

### **Problem Visualization:**
```
Input: height = [0,1,0,2,1,0,1,3,2,1,2,1]

Elevation Map (# = bar):

Height 3:                      #
Height 2:          #           #  #     #
Height 1:    #     #  #     #  #  #  #  #  #
Index:    0  1  2  3  4  5  6  7  8  9 10 11

Water fills the valleys (~ = water):

Height 3:                      #
Height 2:          #  ~  ~  ~  #  #  ~  #
Height 1:    #  ~  #  #  ~  #  #  #  #  #  #
Index:    0  1  2  3  4  5  6  7  8  9 10 11

Detailed view:
Position: 0  1  2  3  4  5  6  7  8  9 10 11
Height:   0  1  0  2  1  0  1  3  2  1  2  1
Water:    0  0  1  0  1  2  1  0  0  1  0  0
          ─  ─  ^  ─  ^  ^  ^  ─  ─  ^  ─  ─
                 └──────┴──┴──┘        └─────> Water trapped here
Total water = 1 + 1 + 2 + 1 + 1 = 6 units
```

### **Core Question:**
For any position `i`, how much water can be held above it?

### **Deriving from First Principles:**

**Observation 1:** Water needs barriers on both sides
- Without a left barrier, water flows left and escapes
- Without a right barrier, water flows right and escapes
- Position 0 and position n-1 cannot trap water (no barrier on one side)

**Observation 2:** Water level is determined by the shorter barrier
- Imagine position i with height = 1
- Left side has max height = 3, right side has max height = 2
- Water fills up to height 2 (limited by shorter barrier)
- Water "overflows" over the shorter barrier if we try to fill higher

**Observation 3:** Water amount = (water level) - (bar height)
- If bar height = 1 and water level = 2, then 1 unit of water sits on top
- If bar height ≥ water level, no water can be trapped (0 units)

### **Formula Derivation:**
For position `i`:
- `leftMax[i]` = maximum height in range `[0...i]`
- `rightMax[i]` = maximum height in range `[i...n-1]`
- Water level at position `i` = `min(leftMax[i], rightMax[i])` (limited by shorter barrier)
- Water trapped at position `i` = `max(0, waterLevel - height[i])`
- Simplified: `water[i] = min(leftMax[i], rightMax[i]) - height[i]` (formula handles non-negative automatically)

### **State Space Structure:**
**Type:** For each position, compute left maximum and right maximum  
**Structure:** Arrays `leftMax[n]` and `rightMax[n]` where each entry requires scanning a range  
**Cardinality:** n positions × O(n) scan per position = **O(n²) time** for brute force

**Example Computation:**
For `height = [0,1,0,2,1,0,1,3,2,1,2,1]`:

```
Position i:   0  1  2  3  4  5  6  7  8  9 10 11
height[i]:    0  1  0  2  1  0  1  3  2  1  2  1
leftMax[i]:   0  1  1  2  2  2  2  3  3  3  3  3
rightMax[i]:  3  3  3  3  3  3  3  3  2  2  2  1
min(L,R):     0  1  1  2  2  2  2  3  2  2  2  1
water[i]:     0  0  1  0  1  2  1  0  0  1  0  0
                    ↑     ↑  ↑  ↑        ↑
Total water = 1 + 1 + 2 + 1 + 1 = 6 units
```

### **Generation Pattern (Brute Force):**
```csharp
void ComputeWaterTrapped(int[] height)
{
    for (int i = 0; i < height.Length; i++)
    {
        // Scan left to find leftMax[i] - O(n)
        int leftMax = 0;
        for (int j = 0; j <= i; j++)
            leftMax = Math.Max(leftMax, height[j]);
        
        // Scan right to find rightMax[i] - O(n)
        int rightMax = 0;
        for (int j = i; j < height.Length; j++)
            rightMax = Math.Max(rightMax, height[j]);
        
        // Compute water at position i
        water[i] = Math.Min(leftMax, rightMax) - height[i];
    }
}
// Total: n positions × O(n) per position = O(n²)
```

---

## **Step 2: Brute Force**

```csharp
int TrapBruteForce(int[] height) 
{
    int water = 0;
    
    for (int i = 0; i < height.Length; i++)
    {
        int leftMax = 0, rightMax = 0;
        
        // Find max height on left
        for (int j = 0; j <= i; j++)
            leftMax = Math.Max(leftMax, height[j]);
        
        // Find max height on right
        for (int j = i; j < height.Length; j++)
            rightMax = Math.Max(rightMax, height[j]);
        
        water += Math.Min(leftMax, rightMax) - height[i];
    }
    
    return water;
}
```

**Time**: O(n²)

---

## **Step 3: Pruning**

**Key Insight:** Use two pointers to track maxLeft and maxRight dynamically:
- Start from both ends
- Move pointer with smaller max (that determines water level)
- Update maxLeft/maxRight as we go

---

## **Step 4: Optimal Solution**

```csharp
int Trap(int[] height) 
{
    int left = 0, right = height.Length - 1;
    int leftMax = 0, rightMax = 0;
    int water = 0;
    
    while (left < right)
    {
        if (height[left] < height[right])
        {
            if (height[left] >= leftMax)
                leftMax = height[left];
            else
                water += leftMax - height[left];
            left++;
        }
        else
        {
            if (height[right] >= rightMax)
                rightMax = height[right];
            else
                water += rightMax - height[right];
            right--;
        }
    }
    
    return water;
}
```

**Big-O**: O(n²) → **O(n)** ✅  
**Space**: O(1)

### **Key Takeaway:**
**Two pointers with invariant tracking**: Move pointer with smaller max height, because water level at that position is determined by the smaller of the two maxes.

---

## **Tags:**
`two-pointers` `array` `hard` `greedy` `optimization`
