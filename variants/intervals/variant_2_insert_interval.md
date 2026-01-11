# Variant #2: Insert Interval

**LeetCode #57 - Medium**

## Problem Statement
Given a set of non-overlapping intervals sorted by their start time and a new interval, insert the new interval and merge if necessary.

**Example:** `intervals = [[1,3],[6,9]]`, `newInterval = [2,5]` → `[[1,5],[6,9]]`

## 4-Step Analysis Framework

### Step 1: State Space Derivation

### **Problem Visualization:**
```
Input: intervals = [[1,3], [6,9]], newInterval = [2,5]

Timeline:
  1---3       6---9    (existing, non-overlapping)
    2-----5            (new interval)

Result after merge:
  1-------5   6---9

Three cases for each existing interval:
1. Before new interval: [1,2] vs [5,6] → no overlap, keep as-is
2. Overlaps new interval: [3,6] vs [5,8] → merge
3. After new interval: [10,12] vs [5,8] → no overlap, keep as-is
```

### **Core Question:**
How to insert and merge in one pass without re-sorting?

### **Deriving from First Principles:**

**Observation 1:** Input already sorted
- Don't need to sort again
- Can process in one linear pass

**Observation 2:** Three phases
```
Phase 1: Add intervals before newInterval
  While interval.end < newInterval.start

Phase 2: Merge overlapping intervals
  While interval overlaps with newInterval
  Expand newInterval: [min(starts), max(ends)]

Phase 3: Add intervals after
  Add remaining intervals
```

### **State Space Structure:**

**Type:** Insert and merge in sorted array  
**Cardinality:** Process each of n intervals once  
**Result:** At most n+1 intervals (if no merges)

---

### Step 4: Optimal Solution

```csharp
int[][] Insert(int[][] intervals, int[] newInterval)
{
    var result = new List<int[]>();
    int i = 0;
    int n = intervals.Length;
    
    // Phase 1: Add all intervals before newInterval
    while (i < n && intervals[i][1] < newInterval[0])
    {
        result.Add(intervals[i]);
        i++;
    }
    
    // Phase 2: Merge overlapping intervals
    while (i < n && intervals[i][0] <= newInterval[1])
    {
        newInterval[0] = Math.Min(newInterval[0], intervals[i][0]);
        newInterval[1] = Math.Max(newInterval[1], intervals[i][1]);
        i++;
    }
    result.Add(newInterval);
    
    // Phase 3: Add remaining intervals
    while (i < n)
    {
        result.Add(intervals[i]);
        i++;
    }
    
    return result.ToArray();
}
```

**Complexity:** O(n) time, O(n) space

---

## Tags
`#intervals` `#merge` `#sorted-array` `#medium` `#three-phase`
