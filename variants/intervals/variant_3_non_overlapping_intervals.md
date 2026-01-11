# Non-overlapping Intervals

**Difficulty:** Medium  
**LeetCode Problem:** #435  
**Tags:** `Intervals`, `Greedy`, `Sorting`, `Activity Selection`

---

## Problem Statement

Given an array of intervals `intervals` where `intervals[i] = [starti, endi]`, return the minimum number of intervals you need to remove to make the rest of the intervals non-overlapping.

**Constraints:**
- `1 <= intervals.length <= 10^5`
- `intervals[i].length == 2`
- `-5 * 10^4 <= starti < endi <= 5 * 10^4`

**Example 1:**
```
Input: intervals = [[1,2],[2,3],[3,4],[1,3]]
Output: 1
Explanation: Remove [1,3], rest are non-overlapping
```

**Example 2:**
```
Input: intervals = [[1,2],[1,2],[1,2]]
Output: 2
Explanation: Keep one interval, remove 2
```

**Example 3:**
```
Input: intervals = [[1,2],[2,3]]
Output: 0
Explanation: No overlap, remove 0
```

---

## Step 1: State Space

### Problem Visualization

For `intervals = [[1,2],[2,3],[3,4],[1,3]]`:

```
Timeline:
  1     2     3     4
  [─────)           
        [─────)     
              [─────)
  [───────────)     

Overlapping pairs:
[1,2] and [1,3]: overlap
[1,3] and [2,3]: overlap  
[1,3] and [3,4]: no overlap (touching at 3)

Remove [1,3] → all non-overlapping:
  [─────)           
        [─────)     
              [─────)
```

Greedy choice visualization:
```
Sort by end time:
[1,2], [2,3], [1,3], [3,4]

Process:
Keep [1,2]   (first interval)
Keep [2,3]   (no overlap with [1,2])
Skip [1,3]   (overlaps with [2,3]) ← Remove
Keep [3,4]   (no overlap with [2,3])

Removed: 1
```

### Core Question

**What is the minimum number of intervals to remove so the remaining intervals don't overlap?**

Equivalently: **What is the maximum number of non-overlapping intervals we can keep?**

If max non-overlapping = k, then removals = n - k.

### Deriving from First Principles

**Observation 1: Equivalent problem**
Minimizing removals = Maximizing intervals kept.
This is the classic "Activity Selection" problem.

**Observation 2: Overlap definition**
Two intervals [a,b) and [c,d) overlap if:
- a < d AND c < b (intervals intersect)
- Note: [1,2] and [2,3] don't overlap (touching is OK)

**Observation 3: Greedy strategy - sort by end time**
To fit maximum intervals, we should:
1. Sort intervals by end time
2. Greedily select intervals that end earliest
3. This leaves maximum room for future intervals

**Observation 4: Why earliest end time?**
If we select interval that ends later, it occupies more "timeline space" and may conflict with more future intervals. Choosing earliest end time is locally optimal and globally optimal.

**Observation 5: Algorithm**
```
Sort by end time
Keep first interval (ends earliest)
For each subsequent interval:
  If start >= last kept interval's end:
    Keep it (non-overlapping)
  Else:
    Skip it (overlapping, remove)
```

**Formula:**
```
Sort intervals by end time
kept = 1  (first interval)
lastEnd = intervals[0].end

for i = 1 to n-1:
  if intervals[i].start >= lastEnd:
    kept++
    lastEnd = intervals[i].end

removals = n - kept
```

### State Space Structure

**Type:** Greedy activity selection on sorted intervals.

**Structure:**
- Sorted array by end time
- Track last kept interval's end
- Count non-overlapping intervals

**Cardinality:**
- O(n log n) for sorting
- O(n) for greedy selection
- Total: O(n log n)

**Key Property:** Greedy choice of earliest end time is optimal (Activity Selection theorem).

### Example Computation

Input: `intervals = [[1,2],[2,3],[3,4],[1,3]]`

```
Step 1: Sort by end time
  [[1,2], [2,3], [1,3], [3,4]]

Step 2: Greedy selection
  kept = 0, lastEnd = -∞

  Interval [1,2]:
    1 >= -∞ → Keep it
    kept = 1, lastEnd = 2

  Interval [2,3]:
    2 >= 2 → Keep it (touching is OK)
    kept = 2, lastEnd = 3

  Interval [1,3]:
    1 < 3 → Skip (overlaps with [2,3])
    kept = 2

  Interval [3,4]:
    3 >= 3 → Keep it
    kept = 3, lastEnd = 4

Step 3: Calculate removals
  removals = 4 - 3 = 1
```

### Generation Pattern

**Brute force (try all subsets):**
```csharp
// Try all 2^n subsets, check if non-overlapping
// Return max size subset that's non-overlapping
// Time: O(2^n × n)
```

---

## Step 2: Brute Force

Try all possible subsets of intervals, check each for non-overlapping property, return maximum size. Then removals = n - max size. Exponential time.

---

## Step 3: Optimization Ideas

### Key Insights
1. **Activity Selection problem:** Classic greedy algorithm
2. **Sort by end time:** Process intervals in order of completion
3. **Greedy choice is optimal:** Earliest end time leaves most room
4. **One pass after sorting:** O(n) to select non-overlapping

### Why Greedy Works
The Activity Selection problem has optimal substructure:
- If we choose interval with earliest end, remaining problem is independent
- Choosing later-ending interval never improves solution
- Greedy stays ahead of any other solution

---

## Step 4: Optimal Solution

```csharp
public int EraseOverlapIntervals(int[][] intervals) {
    if (intervals.Length <= 1) return 0;
    
    // Sort by end time
    Array.Sort(intervals, (a, b) => a[1].CompareTo(b[1]));
    
    int kept = 1;  // Keep first interval
    int lastEnd = intervals[0][1];
    
    for (int i = 1; i < intervals.Length; i++) {
        // If current interval starts at or after last kept interval ends
        if (intervals[i][0] >= lastEnd) {
            kept++;
            lastEnd = intervals[i][1];
        }
        // Else: overlaps, skip (implicitly remove)
    }
    
    return intervals.Length - kept;
}
```

**Complexity:**
- Time: O(n log n) - sorting dominates
- Space: O(1) or O(n) depending on sort

**Alternative (count removals directly):**
```csharp
public int EraseOverlapIntervals(int[][] intervals) {
    if (intervals.Length <= 1) return 0;
    
    Array.Sort(intervals, (a, b) => a[1].CompareTo(b[1]));
    
    int removals = 0;
    int lastEnd = intervals[0][1];
    
    for (int i = 1; i < intervals.Length; i++) {
        if (intervals[i][0] < lastEnd) {
            // Overlap detected
            removals++;
        } else {
            // No overlap, update lastEnd
            lastEnd = intervals[i][1];
        }
    }
    
    return removals;
}
```

---

## Edge Cases

1. **No overlaps:** `[[1,2],[2,3],[3,4]]` → `0`
2. **All overlap:** `[[1,5],[2,3],[3,4]]` → `2` (keep shortest)
3. **Single interval:** `[[1,2]]` → `0`
4. **Identical intervals:** `[[1,2],[1,2],[1,2]]` → `2`
5. **Nested intervals:** `[[1,10],[2,3],[4,5]]` → `1` (remove [1,10])

---

## Why Sort by End Time (Not Start Time)?

**Counterexample for sorting by start time:**
```
Intervals: [[1,10], [2,3], [4,5]]

Sort by start: [[1,10], [2,3], [4,5]]
Keep [1,10] → Must remove [2,3] and [4,5] → 2 removals

Sort by end: [[2,3], [4,5], [1,10]]
Keep [2,3] → Keep [4,5] → Remove [1,10] → 1 removal ✓
```

Sorting by end time ensures we make room for more intervals.

---

## Related Problems

1. **Meeting Rooms II (LeetCode #253)** - Count overlapping intervals
2. **Merge Intervals (LeetCode #56)** - Combine overlapping intervals
3. **Minimum Number of Arrows (LeetCode #452)** - Similar greedy
4. **Maximum Profit in Job Scheduling (LeetCode #1235)** - Weighted intervals

---

## Pattern Recognition

**Problem Asks For:**
- Minimum removals to make non-overlapping
- Maximum non-overlapping intervals
- Activity selection

**This Suggests:**
- Sort by end time
- Greedy selection (earliest end)
- Count kept or removed
- → **Intervals + Greedy pattern**

**Key Indicators:**
- "Non-overlapping" requirement
- "Minimum removal" or "maximum selection"
- Intervals on timeline

---

## Tags

`#intervals` `#greedy` `#activity-selection` `#sort-by-end` `#overlapping` `#scheduling`
