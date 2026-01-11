# Variant #2: Daily Temperatures

**LeetCode #739 - Medium**

## Problem Statement
Given an array of integers `temperatures` representing daily temperatures, return an array `answer` such that `answer[i]` is the number of days you have to wait after the `i-th` day to get a warmer temperature. If there is no future day for which this is possible, keep `answer[i] == 0` instead.

**Example:** `temperatures = [73,74,75,71,69,72,76,73]` → `[1,1,4,2,1,1,0,0]`

## 4-Step Analysis Framework

### Step 1: State Space Derivation

### **Problem Visualization:**
```
Input: temperatures = [73, 74, 75, 71, 69, 72, 76, 73]

For each day, find next warmer day:

Day 0 (73°): Next warmer is day 1 (74°) → wait 1 day
Day 1 (74°): Next warmer is day 2 (75°) → wait 1 day
Day 2 (75°): Next warmer is day 6 (76°) → wait 4 days
Day 3 (71°): Next warmer is day 5 (72°) → wait 2 days
Day 4 (69°): Next warmer is day 5 (72°) → wait 1 day
Day 5 (72°): Next warmer is day 6 (76°) → wait 1 day
Day 6 (76°): No warmer day → wait 0 days
Day 7 (73°): No warmer day → wait 0 days

Result: [1, 1, 4, 2, 1, 1, 0, 0]

Monotonic decreasing stack (store indices):
  Stack: indices of days waiting for warmer temp
  
  Day 0: stack=[0] (day 0 waiting)
  Day 1: 74>73, day 0 found! answer[0]=1-0=1, stack=[1]
  Day 2: 75>74, day 1 found! answer[1]=2-1=1, stack=[2]
  Day 3: 71<75, push, stack=[2,3]
  Day 4: 69<71, push, stack=[2,3,4]
  Day 5: 72>69, day 4 found! answer[4]=5-4=1
         72>71, day 3 found! answer[3]=5-3=2
         72<75, stop, stack=[2,5]
  Day 6: 76>72, day 5 found! answer[5]=6-5=1
         76>75, day 2 found! answer[2]=6-2=4
         stack=[6]
  Day 7: 73<76, push, stack=[6,7]
  
  Days 6,7 remain → answer[6]=0, answer[7]=0
```

### **Core Question:**
How to find next warmer temperature for each day efficiently?

### **Deriving from First Principles:**

**Observation 1:** This is "Next Greater Element" with distances
- Find next element > current
- Return distance (index difference), not value

**Observation 2:** Monotonic decreasing stack
- Store indices (need to calculate distances)
- When temp[i] > temp[stack.top]: found warmer day
  - Distance = i - stack.pop()
- Stack maintains indices of decreasing temperatures

**Observation 3:** Why indices, not values?
- Need to compute distance: current_index - previous_index
- Values alone don't tell us distances

### **State Space Structure:**

**Type:** For each day, find next warmer day  
**Structure:** Array of distances to next warmer temperature  
**Cardinality:** n days, each needs one answer  
**Stack space:** At most n elements (worst case: all decreasing)

---

### Step 2: Brute Force

**Approach:** For each day, scan forward to find next warmer

```csharp
int[] DailyTemperatures_BruteForce(int[] temperatures)
{
    int n = temperatures.Length;
    int[] answer = new int[n];
    
    for (int i = 0; i < n; i++)  // O(n)
    {
        for (int j = i + 1; j < n; j++)  // O(n)
        {
            if (temperatures[j] > temperatures[i])
            {
                answer[i] = j - i;
                break;
            }
        }
        // If not found, answer[i] remains 0
    }
    
    return answer;
}
```

**Complexity:** O(n²) time, O(1) space

---

### Step 3: Pruning Analysis

**Key Insight:** Monotonic stack avoids rescanning

---

### Step 4: Optimal Solution

```csharp
int[] DailyTemperatures(int[] temperatures)
{
    int n = temperatures.Length;
    int[] answer = new int[n];
    var stack = new Stack<int>();  // Store indices
    
    for (int i = 0; i < n; i++)
    {
        // Pop indices with cooler temperatures
        while (stack.Count > 0 && temperatures[i] > temperatures[stack.Peek()])
        {
            int prevIndex = stack.Pop();
            answer[prevIndex] = i - prevIndex;  // Distance
        }
        
        stack.Push(i);  // Current day waiting
    }
    
    // Remaining in stack: no warmer day (answer=0 by default)
    
    return answer;
}
```

**Complexity:** O(n) time, O(n) space

---

## Tags
`#monotonic-stack` `#stack` `#array` `#next-greater` `#medium` `#distances`
