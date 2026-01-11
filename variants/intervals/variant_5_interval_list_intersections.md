# Interval List Intersections

**Difficulty:** Medium  
**LeetCode Problem:** #986  
**Tags:** `Intervals`, `Two Pointers`, `Merge`

---

## Problem Statement

You are given two lists of closed intervals, `firstList` and `secondList`, where `firstList[i] = [starti, endi]` and `secondList[j] = [startj, endj]`. Each list of intervals is pairwise disjoint and in sorted order.

Return the intersection of these two interval lists.

A closed interval `[a, b]` (with `a <= b`) denotes the set of real numbers `x` with `a <= x <= b`.

The intersection of two closed intervals is a set of real numbers that are either empty or represented as a closed interval. For example, the intersection of `[1, 3]` and `[2, 4]` is `[2, 3]`.

**Constraints:**
- `0 <= firstList.length, secondList.length <= 1000`
- `firstList.length + secondList.length >= 1`
- `0 <= starti < endi <= 10^9`
- `0 <= startj < endj <= 10^9`
- Each list is sorted and pairwise disjoint

**Example 1:**
```
Input: 
firstList = [[0,2],[5,10],[13,23],[24,25]]
secondList = [[1,5],[8,12],[15,24],[25,26]]
Output: [[1,2],[5,5],[8,10],[15,23],[24,24],[25,25]]
```

**Example 2:**
```
Input: firstList = [[1,3],[5,9]], secondList = []
Output: []
```

---

## Step 1: State Space

### Problem Visualization

For `firstList = [[0,2],[5,10]]`, `secondList = [[1,5],[8,12]]`:

```
Timeline:
       0  1  2  3  4  5  6  7  8  9 10 11 12
First: [─────]        [─────────────]
Second:   [──────────]      [─────────────]

Intersections:
         [──]           [──]  [─────]
       [1,2]          [5,5] [8,10]
```

Detailed intersection calculation:
```
[0,2] ∩ [1,5]:
  start = max(0,1) = 1
  end = min(2,5) = 2
  → [1,2] ✓

[0,2] ∩ [8,12]:
  start = max(0,8) = 8
  end = min(2,12) = 2
  8 > 2 → No intersection

[5,10] ∩ [1,5]:
  start = max(5,1) = 5
  end = min(10,5) = 5
  → [5,5] ✓

[5,10] ∩ [8,12]:
  start = max(5,8) = 8
  end = min(10,12) = 10
  → [8,10] ✓
```

### Core Question

**How do we find all intervals that are the intersection of intervals from two sorted lists?**

We need to check each pair of intervals for overlap and compute their intersection if they overlap.

### Deriving from First Principles

**Observation 1: Intersection formula**
Two intervals `[a,b]` and `[c,d]` intersect if and only if:
- `max(a,c) <= min(b,d)`

The intersection is:
- `[max(a,c), min(b,d)]`

**Observation 2: Both lists are sorted**
Since both lists are sorted and disjoint within themselves:
- We can use two pointers
- No need to backtrack
- Process intervals linearly

**Observation 3: When to advance pointers**
After checking intersection of intervals at positions i and j:
- If `firstList[i]` ends before `secondList[j]`, advance i
- If `secondList[j]` ends before `firstList[i]`, advance j
- If they end at same time, advance both

**Observation 4: Why advance based on end time?**
The interval that ends first cannot intersect with any future intervals from the other list (since other list is sorted and disjoint).

**Observation 5: Two-pointer algorithm**
```
i = 0, j = 0
while i < n and j < m:
  Compute intersection of firstList[i] and secondList[j]
  If intersection exists, add to result
  
  If firstList[i] ends first:
    i++
  Else:
    j++
```

**Formula:**
```
Intersection of [a,b] and [c,d]:
  start = max(a, c)
  end = min(b, d)
  
  if start <= end:
    intersection = [start, end]
  else:
    no intersection
```

### State Space Structure

**Type:** Two-pointer merge of sorted interval lists.

**Structure:**
- Two pointers (i, j) scanning both lists
- Compute intersection at each position
- Advance pointer of interval that ends first

**Cardinality:**
- O(n + m) comparisons
- O(k) intersections (k <= min(n,m))

**Key Property:** Sorted + disjoint within each list allows single-pass two-pointer approach.

### Example Computation

Input: `firstList = [[0,2],[5,10]]`, `secondList = [[1,5],[8,12]]`

```
i=0, j=0:
  first[0]=[0,2], second[0]=[1,5]
  start = max(0,1) = 1
  end = min(2,5) = 2
  1 <= 2 → Intersection [1,2] ✓
  
  first[0] ends at 2, second[0] ends at 5
  2 < 5 → Advance i
  
i=1, j=0:
  first[1]=[5,10], second[0]=[1,5]
  start = max(5,1) = 5
  end = min(10,5) = 5
  5 <= 5 → Intersection [5,5] ✓
  
  first[1] ends at 10, second[0] ends at 5
  10 > 5 → Advance j

i=1, j=1:
  first[1]=[5,10], second[1]=[8,12]
  start = max(5,8) = 8
  end = min(10,12) = 10
  8 <= 10 → Intersection [8,10] ✓
  
  first[1] ends at 10, second[1] ends at 12
  10 < 12 → Advance i

i=2 (out of bounds) → Stop

Result: [[1,2], [5,5], [8,10]]
```

### Generation Pattern

**Brute force:**
```csharp
public int[][] IntervalIntersection(int[][] firstList, int[][] secondList) {
    var result = new List<int[]>();
    
    foreach (var first in firstList) {
        foreach (var second in secondList) {
            int start = Math.Max(first[0], second[0]);
            int end = Math.Min(first[1], second[1]);
            if (start <= end) {
                result.Add(new int[] { start, end });
            }
        }
    }
    
    return result.ToArray();
}
// Time: O(n × m), Space: O(k)
```

---

## Step 2: Brute Force

Check all n×m pairs of intervals: O(n×m).

---

## Step 3: Optimization Ideas

### Key Insights
1. **Both lists sorted:** Use two pointers
2. **Advance based on end time:** Interval ending first won't intersect with future intervals
3. **One pass:** O(n+m) linear scan

---

## Step 4: Optimal Solution

```csharp
public int[][] IntervalIntersection(int[][] firstList, int[][] secondList) {
    var result = new List<int[]>();
    int i = 0, j = 0;
    
    while (i < firstList.Length && j < secondList.Length) {
        // Compute intersection
        int start = Math.Max(firstList[i][0], secondList[j][0]);
        int end = Math.Min(firstList[i][1], secondList[j][1]);
        
        // If valid intersection, add to result
        if (start <= end) {
            result.Add(new int[] { start, end });
        }
        
        // Advance pointer of interval that ends first
        if (firstList[i][1] < secondList[j][1]) {
            i++;
        } else {
            j++;
        }
    }
    
    return result.ToArray();
}
```

**Complexity:**
- Time: O(n + m) - single pass through both lists
- Space: O(1) excluding output (O(k) for output)

**Key Mechanics:**
1. **Two pointers:** Scan both lists simultaneously
2. **Intersection formula:** `[max(starts), min(ends)]`
3. **Valid check:** `start <= end`
4. **Advance strategy:** Pointer of interval with smaller end time

---

## Edge Cases

1. **Empty list:** `firstList=[[1,2]], secondList=[]` → `[]`
2. **No intersections:** `[[1,2]], [[3,4]]` → `[]`
3. **Complete overlap:** `[[1,5]], [[1,5]]` → `[[1,5]]`
4. **Point intersection:** `[[1,2]], [[2,3]]` → `[[2,2]]`
5. **One contains other:** `[[1,10]], [[2,3]]` → `[[2,3]]`

---

## Intersection Conditions

**Two intervals `[a,b]` and `[c,d]` intersect when:**
```
Condition: max(a,c) <= min(b,d)

Equivalently: a <= d AND c <= b
```

**Non-overlapping cases:**
```
[a,b] comes before [c,d]: b < c
[c,d] comes before [a,b]: d < a
```

---

## Related Problems

1. **Merge Intervals (LeetCode #56)** - Combine overlapping intervals
2. **Insert Interval (LeetCode #57)** - Insert into sorted list
3. **Employee Free Time (LeetCode #759)** - Find gaps between intervals
4. **Range Module (LeetCode #715)** - Interval add/query

---

## Pattern Recognition

**Problem Asks For:**
- Intersection of interval lists
- Common time periods
- Overlapping ranges

**This Suggests:**
- Two pointers on sorted lists
- Intersection formula: max/min
- Advance based on end time
- → **Intervals + Two Pointers pattern**

**Key Indicators:**
- Two sorted interval lists
- Find common/overlapping parts
- Linear merge operation

---

## Tags

`#intervals` `#two-pointers` `#intersection` `#merge` `#sorted-lists`
