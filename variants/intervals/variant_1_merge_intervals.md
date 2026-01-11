# Variant #1: Merge Intervals

**LeetCode #56 - Medium**

## Problem Statement
Given an array of `intervals` where `intervals[i] = [start_i, end_i]`, merge all overlapping intervals and return an array of the non-overlapping intervals that cover all intervals in the input.

**Example:** `intervals = [[1,3],[2,6],[8,10],[15,18]]` → `[[1,6],[8,10],[15,18]]`

## 4-Step Analysis Framework

### Step 1: State Space Derivation

### **Problem Visualization:**
```
Input: intervals = [[1,3], [2,6], [8,10], [15,18]]

Visual timeline:
   1---3
     2-----6
              8--10
                      15---18

Overlaps:
  [1,3] and [2,6] overlap → merge to [1,6]
  [8,10] standalone
  [15,18] standalone

Result: [[1,6], [8,10], [15,18]]

Key insight: After sorting by start time:
  Two intervals [a,b] and [c,d] overlap if: b >= c
  (first interval ends at or after second starts)

Sorted: [1,3], [2,6], [8,10], [15,18]

Merge process:
  Start with [1,3]
  Next [2,6]: 3 >= 2 → overlap! Merge to [1, max(3,6)] = [1,6]
  Next [8,10]: 6 < 8 → no overlap, output [1,6], start new [8,10]
  Next [15,18]: 10 < 15 → no overlap, output [8,10], start new [15,18]
  End: output [15,18]

Result: [[1,6], [8,10], [15,18]]
```

### **Core Question:**
How to identify and merge overlapping intervals?

### **Deriving from First Principles:**

**Observation 1:** When do intervals overlap?
```
Intervals [a, b] and [c, d] overlap if:
  - a starts before c ends: a <= d
  - c starts before a ends: c <= b
  
Simplified (assuming a <= c after sorting):
  - They overlap if: b >= c
  
Visual:
  [a-----b]
      [c-----d]  ← b >= c, overlap
      
  [a--b]
         [c---d] ← b < c, no overlap
```

**Observation 2:** Sorting simplifies comparison
- Unsorted: Need to compare all pairs O(n²)
- Sorted by start: Only check consecutive intervals O(n)

**Observation 3:** Merging logic
```
When intervals [a,b] and [c,d] overlap (b >= c):
  Merged interval: [a, max(b,d)]
  
Why max(b,d)?
  [1,5] + [3,4] → [1,5]  (5 > 4)
  [1,4] + [3,6] → [1,6]  (6 > 4)
```

**Observation 4:** Algorithm flow
1. Sort intervals by start time: O(n log n)
2. Iterate through sorted intervals
3. If current overlaps with previous: merge
4. Otherwise: output previous, start new interval

### **State Space Structure:**

**Type:** Merge overlapping intervals  
**Structure:** After sorting, linear scan merges consecutive overlaps  
**Cardinality:** n intervals → at most n merged intervals  
**Key operation:** Check if current.start <= previous.end

**Example Computation:**
For `intervals = [[1,3],[2,6],[8,10],[15,18]]`:

```
After sorting: [[1,3],[2,6],[8,10],[15,18]]

Start with [1,3] as current

Process [2,6]:
  2 <= 3? Yes → overlap
  Merge: [1, max(3,6)] = [1,6]
  Current = [1,6]

Process [8,10]:
  8 <= 6? No → no overlap
  Output [1,6], start new
  Current = [8,10]

Process [15,18]:
  15 <= 10? No → no overlap
  Output [8,10], start new
  Current = [15,18]

End: Output [15,18]

Result: [[1,6], [8,10], [15,18]]
```

---

### Step 2: Brute Force

**Approach:** Compare all pairs, repeatedly merge

```csharp
int[][] Merge_BruteForce(int[][] intervals)
{
    bool merged;
    var result = new List<int[]>(intervals);
    
    do
    {
        merged = false;
        for (int i = 0; i < result.Count; i++)
        {
            for (int j = i + 1; j < result.Count; j++)
            {
                if (Overlaps(result[i], result[j]))
                {
                    result[i] = MergeTwo(result[i], result[j]);
                    result.RemoveAt(j);
                    merged = true;
                    break;
                }
            }
            if (merged) break;
        }
    } while (merged);
    
    return result.ToArray();
}

bool Overlaps(int[] a, int[] b)
{
    return Math.Max(a[0], b[0]) <= Math.Min(a[1], b[1]);
}
```

**Complexity:** O(n³) worst case

---

### Step 3: Pruning Analysis

**Key Insight:** Sort first, then single linear scan

---

### Step 4: Optimal Solution

```csharp
int[][] Merge(int[][] intervals)
{
    if (intervals.Length == 0) return intervals;
    
    // Sort by start time
    Array.Sort(intervals, (a, b) => a[0].CompareTo(b[0]));
    
    var merged = new List<int[]>();
    int[] current = intervals[0];
    
    for (int i = 1; i < intervals.Length; i++)
    {
        if (intervals[i][0] <= current[1])
        {
            // Overlap: merge
            current[1] = Math.Max(current[1], intervals[i][1]);
        }
        else
        {
            // No overlap: output current, start new
            merged.Add(current);
            current = intervals[i];
        }
    }
    
    merged.Add(current);  // Don't forget last interval
    
    return merged.ToArray();
}
```

**Complexity:** O(n log n) time (sorting), O(n) space

---

## Edge Cases

1. **No overlaps:** `[[1,2],[3,4]]` → `[[1,2],[3,4]]`
2. **All merge:** `[[1,4],[2,5],[3,6]]` → `[[1,6]]`
3. **Touching:** `[[1,2],[2,3]]` → `[[1,3]]` (inclusive)
4. **Single interval:** `[[1,5]]` → `[[1,5]]`
5. **Contained:** `[[1,10],[2,3]]` → `[[1,10]]`

---

## Tags
`#intervals` `#sorting` `#merge` `#medium` `#greedy`
