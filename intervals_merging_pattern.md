# Pattern: Intervals – Merging

## Pattern Description
The Intervals Merging pattern deals with problems where input consists of **ranges (intervals)** and the goal is to **merge, insert, remove, or analyze overlaps**.

### What is it?
- A pattern for handling overlapping or adjacent intervals by **sorting and sweeping** through them
- Maintains a current active interval while processing remaining intervals
- Core operations: merge, detect overlap, find gaps, count conflicts

### What abstract problem does it solve?
- Collapsing overlapping ranges into merged intervals
- Detecting conflicts or overlaps between ranges
- Scheduling and resource allocation problems
- Finding coverage and gaps in ranges
- Counting maximum concurrent intervals

### Real-world / interview variants (simplest → harder)
1. Merge Intervals (collapse overlapping ranges)
2. Insert Interval (merge into sorted list)
3. Meeting Rooms I (detect any overlap)
4. Meeting Rooms II (count maximum concurrent)
5. Non-overlapping Intervals (minimum removal)
6. Interval Intersection (find common ranges)
7. Minimum Arrows to Burst Balloons (greedy scheduling)
8. Employee Free Time (find gaps across schedules)

---

## Canonical Interval Sweep Skeleton

```csharp
intervals.Sort((a, b) => a.start.CompareTo(b.start));

var result = new List<Interval>();
var current = intervals[0];

foreach (var next in intervals)
{
    if (next.start <= current.end)
        current.end = Math.Max(current.end, next.end);
    else
    {
        result.Add(current);
        current = next;
    }
}
result.Add(current);
```

---

<details>
<summary><b>Variant #1: Merge Intervals</b></summary>

## Variant #1: Merge Intervals

### Input/Output:
- Input: `intervals = [[1,3],[2,6],[8,10],[15,18]]`
- Output: `[[1,6],[8,10],[15,18]]`

### Full State Space:
All possible pairs of intervals that might overlap.
```
For n=4 intervals: n*(n-1)/2 = 6 pairs to check
[1,3] vs [2,6], [1,3] vs [8,10], [1,3] vs [15,18]
[2,6] vs [8,10], [2,6] vs [15,18]
[8,10] vs [15,18]
```
```csharp
void GenerateAllOverlapChecks(int[][] intervals)
{
    for (int i = 0; i < intervals.Length; i++)
    {
        for (int j = i + 1; j < intervals.Length; j++)
        {
            bool overlaps = intervals[i][1] >= intervals[j][0] && 
                           intervals[j][1] >= intervals[i][0];
            Console.WriteLine($"[{intervals[i][0]},{intervals[i][1]}] vs [{intervals[j][0]},{intervals[j][1]}]: {overlaps}");
        }
    }
}
```

### Expected/Pruned State Space:
Only **adjacent intervals after sorting** need to be checked for overlap.
```
After sorting by start time: [[1,3],[2,6],[8,10],[15,18]]
Only check n-1 adjacent pairs instead of n*(n-1)/2
```

### State Space Leading to Output:
Collapse all overlapping intervals into merged ranges through a single sweep.

### Brute Force Canonical Skeleton:
```csharp
int[][] MergeIntervalsBruteForce(int[][] intervals)
{
    var merged = new List<int[]>();
    bool[] used = new bool[intervals.Length];

    for (int i = 0; i < intervals.Length; i++)
    {
        if (used[i]) continue;
        
        int[] current = new int[] { intervals[i][0], intervals[i][1] };
        
        // Keep merging with all overlapping intervals
        bool changed = true;
        while (changed)
        {
            changed = false;
            for (int j = 0; j < intervals.Length; j++)
            {
                if (used[j]) continue;
                
                // Check overlap
                if (intervals[j][0] <= current[1] && intervals[j][1] >= current[0])
                {
                    current[0] = Math.Min(current[0], intervals[j][0]);
                    current[1] = Math.Max(current[1], intervals[j][1]);
                    used[j] = true;
                    changed = true;
                }
            }
        }
        
        merged.Add(current);
    }
    
    return merged.ToArray();
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `intervals = [[1,3],[2,6],[8,10],[15,18]]`:

| i | current | j | intervals[j] | overlaps | action | used |
|---|---------|---|--------------|----------|--------|------|
| 0 | [1,3] | 0 | [1,3] | yes | merge → [1,3] | [T,F,F,F] |
| 0 | [1,3] | 1 | [2,6] | yes | merge → [1,6] | [T,T,F,F] |
| 0 | [1,6] | 2 | [8,10] | no | skip | [T,T,F,F] |
| 0 | [1,6] | 3 | [15,18] | no | skip | [T,T,F,F] |
| 0 | - | - | - | - | add [1,6] | [T,T,F,F] |
| 2 | [8,10] | 2 | [8,10] | yes | merge → [8,10] | [T,T,T,F] |
| 2 | [8,10] | 3 | [15,18] | no | skip | [T,T,T,F] |
| 2 | - | - | - | - | add [8,10] | [T,T,T,F] |
| 3 | [15,18] | 3 | [15,18] | yes | merge → [15,18] | [T,T,T,T] |
| 3 | - | - | - | - | add [15,18] | [T,T,T,T] |

### Optimized Solution from Canonical Skeleton:
```csharp
int[][] MergeIntervals(int[][] intervals)
{
    if (intervals.Length == 0) return new int[0][];
    
    // Sort by start time
    Array.Sort(intervals, (a, b) => a[0].CompareTo(b[0]));
    
    var merged = new List<int[]>();
    int[] current = intervals[0];
    
    for (int i = 1; i < intervals.Length; i++)
    {
        int[] next = intervals[i];
        
        if (next[0] <= current[1]) // Overlap
        {
            // Merge: extend current end
            current[1] = Math.Max(current[1], next[1]);
        }
        else // No overlap
        {
            merged.Add(current);
            current = next;
        }
    }
    
    merged.Add(current); // Add last interval
    return merged.ToArray();
}
```

### Explanation of Pruning:
- **Sort by start time:** Brings overlapping intervals adjacent to each other
- **Single sweep:** Only check adjacent intervals after sorting (n-1 comparisons instead of n²)
- **Overlap condition:** `next.start <= current.end` means intervals overlap or touch
- **Merge strategy:** Extend `current.end` to `max(current.end, next.end)`
- **No overlap:** Add current to result and move to next interval

### Optimized Solution Code Walkthrough / Variable Trace:
For `intervals = [[1,3],[2,6],[8,10],[15,18]]` (already sorted):

| i | current | next | next[0] <= current[1] | action | merged |
|---|---------|------|----------------------|--------|--------|
| 0 | [1,3] | - | - | initialize | [] |
| 1 | [1,3] | [2,6] | 2 <= 3, yes | extend → [1,6] | [] |
| 2 | [1,6] | [8,10] | 8 <= 6, no | add [1,6], current=[8,10] | [[1,6]] |
| 3 | [8,10] | [15,18] | 15 <= 10, no | add [8,10], current=[15,18] | [[1,6],[8,10]] |
| - | [15,18] | - | - | add last | [[1,6],[8,10],[15,18]] |

### Big-O Analysis:
- **Brute Force:** O(n³) → n intervals × n passes × n comparisons
- **Optimized:** O(n log n) → sorting dominates
- **Space Complexity:** O(n) → result array

</details>


<details>
<summary><b>Variant #2: Insert Interval</b></summary>

## Variant #2: Insert Interval

### Input/Output:
- Input: `intervals = [[1,3],[6,9]], newInterval = [2,5]`
- Output: `[[1,5],[6,9]]`

### Full State Space:
New interval could overlap with any subset of existing intervals.
```
For intervals = [[1,3],[6,9]], newInterval = [2,5]
Possible overlaps: [1,3] only, [6,9] only, both, or neither
```

### Expected/Pruned State Space:
Only check overlaps in a single pass through sorted intervals.
```
Three regions:
1. Before newInterval: intervals ending before new starts
2. Overlapping: intervals that overlap with new
3. After newInterval: intervals starting after new ends
```

### State Space Leading to Output:
Merge all overlapping intervals with newInterval in one sweep.

### Brute Force Canonical Skeleton:
```csharp
int[][] InsertIntervalBruteForce(int[][] intervals, int[] newInterval)
{
    // Add new interval to array
    var allIntervals = new List<int[]>(intervals);
    allIntervals.Add(newInterval);
    
    // Use merge intervals brute force
    return MergeIntervalsBruteForce(allIntervals.ToArray());
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `intervals = [[1,3],[6,9]], newInterval = [2,5]`:

| step | intervals | action |
|------|-----------|--------|
| 1 | [[1,3],[6,9],[2,5]] | add newInterval |
| 2 | [[1,3],[2,5],[6,9]] | sort by start |
| 3 | [[1,5],[6,9]] | merge overlapping |

### Optimized Solution from Canonical Skeleton:
```csharp
int[][] InsertInterval(int[][] intervals, int[] newInterval)
{
    var result = new List<int[]>();
    int i = 0;
    int n = intervals.Length;
    
    // Add all intervals ending before newInterval starts
    while (i < n && intervals[i][1] < newInterval[0])
    {
        result.Add(intervals[i]);
        i++;
    }
    
    // Merge all overlapping intervals with newInterval
    while (i < n && intervals[i][0] <= newInterval[1])
    {
        newInterval[0] = Math.Min(newInterval[0], intervals[i][0]);
        newInterval[1] = Math.Max(newInterval[1], intervals[i][1]);
        i++;
    }
    result.Add(newInterval);
    
    // Add all intervals starting after newInterval ends
    while (i < n)
    {
        result.Add(intervals[i]);
        i++;
    }
    
    return result.ToArray();
}
```

### Explanation of Pruning:
- **Three-phase approach:** before, overlapping, after
- **Phase 1:** Add intervals that end before newInterval starts (no overlap possible)
- **Phase 2:** Merge all intervals that overlap with newInterval
- **Phase 3:** Add remaining intervals that start after newInterval ends
- **Single pass:** O(n) since intervals are already sorted

### Optimized Solution Code Walkthrough / Variable Trace:
For `intervals = [[1,3],[6,9]], newInterval = [2,5]`:

| i | phase | intervals[i] | condition | newInterval | action | result |
|---|-------|--------------|-----------|-------------|--------|--------|
| 0 | 1 | [1,3] | 3 < 2, no | [2,5] | skip phase 1 | [] |
| 0 | 2 | [1,3] | 1 <= 5, yes | [2,5] | merge → [1,5] | [] |
| 1 | 2 | [6,9] | 6 <= 5, no | [1,5] | end phase 2, add [1,5] | [[1,5]] |
| 1 | 3 | [6,9] | - | - | add remaining | [[1,5],[6,9]] |

### Big-O Analysis:
- **Brute Force:** O(n log n) → sort and merge
- **Optimized:** O(n) → single pass (intervals already sorted)
- **Space Complexity:** O(n) → result array

</details>


<details>
<summary><b>Variant #3: Meeting Rooms I (Detect Any Overlap)</b></summary>

## Variant #3: Meeting Rooms I

### Input/Output:
- Input: `intervals = [[0,30],[5,10],[15,20]]`
- Output: `false` (meetings [0,30] and [5,10] overlap)

### Full State Space:
All pairs of intervals need to be checked for overlap.
```
For n=3 intervals: n*(n-1)/2 = 3 pairs
[0,30] vs [5,10], [0,30] vs [15,20], [5,10] vs [15,20]
```

### Expected/Pruned State Space:
After sorting, only check adjacent intervals.
```
Sort by start: [[0,30],[5,10],[15,20]]
Only check n-1 adjacent pairs
```

### State Space Leading to Output:
If any two adjacent intervals (after sorting) overlap, return false.

### Brute Force Canonical Skeleton:
```csharp
bool CanAttendMeetingsBruteForce(int[][] intervals)
{
    // Check all pairs
    for (int i = 0; i < intervals.Length; i++)
    {
        for (int j = i + 1; j < intervals.Length; j++)
        {
            // Check if intervals[i] and intervals[j] overlap
            if (intervals[i][0] < intervals[j][1] && intervals[j][0] < intervals[i][1])
                return false; // Overlap found
        }
    }
    return true; // No overlaps
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `intervals = [[0,30],[5,10],[15,20]]`:

| i | j | intervals[i] | intervals[j] | overlap check | result |
|---|---|--------------|--------------|---------------|--------|
| 0 | 1 | [0,30] | [5,10] | 0<10 && 5<30, yes | false |

### Optimized Solution from Canonical Skeleton:
```csharp
bool CanAttendMeetings(int[][] intervals)
{
    if (intervals.Length == 0) return true;
    
    // Sort by start time
    Array.Sort(intervals, (a, b) => a[0].CompareTo(b[0]));
    
    // Check adjacent intervals for overlap
    for (int i = 1; i < intervals.Length; i++)
    {
        if (intervals[i][0] < intervals[i - 1][1]) // Overlap
            return false;
    }
    
    return true;
}
```

### Explanation of Pruning:
- **Sort by start time:** Brings potentially overlapping intervals adjacent
- **Key insight:** If sorted by start, only need to check if `current.start < previous.end`
- **Early termination:** Return false as soon as overlap is found
- **Single pass:** O(n) after sorting instead of O(n²) all-pairs check

### Optimized Solution Code Walkthrough / Variable Trace:
For `intervals = [[0,30],[5,10],[15,20]]` → sorted: `[[0,30],[5,10],[15,20]]`:

| i | intervals[i-1] | intervals[i] | intervals[i][0] < intervals[i-1][1] | result |
|---|----------------|--------------|-------------------------------------|--------|
| 1 | [0,30] | [5,10] | 5 < 30, yes | false (return) |

### Big-O Analysis:
- **Brute Force:** O(n²) → check all pairs
- **Optimized:** O(n log n) → sorting dominates
- **Space Complexity:** O(1) → no extra space (excluding sort)

</details>

<details>
<summary><b>Variant #4: Meeting Rooms II (Maximum Concurrent)</b></summary>

## Variant #4: Meeting Rooms II

### Input/Output:
- Input: `intervals = [[0,30],[5,10],[15,20]]`
- Output: `2` (maximum 2 concurrent meetings)

### Full State Space:
All possible time points where meeting count changes.
```
Time points: 0(+1), 5(+1), 10(-1), 15(+1), 20(-1), 30(-1)
Concurrent count at each point: 0→1→2→1→2→1→0
Maximum: 2
```

### Expected/Pruned State Space:
Track only meeting start and end events, process chronologically.
```
Events: [0,start], [5,start], [10,end], [15,start], [20,end], [30,end]
```

### State Space Leading to Output:
Count concurrent meetings at each event, track maximum.

### Brute Force Canonical Skeleton:
```csharp
int MinMeetingRoomsBruteForce(int[][] intervals)
{
    int maxRooms = 0;
    
    // Check every minute
    int maxTime = intervals.Max(x => x[1]);
    for (int time = 0; time <= maxTime; time++)
    {
        int count = 0;
        foreach (var interval in intervals)
        {
            if (interval[0] <= time && time < interval[1])
                count++;
        }
        maxRooms = Math.Max(maxRooms, count);
    }
    
    return maxRooms;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `intervals = [[0,30],[5,10],[15,20]]`:

| time | [0,30] active | [5,10] active | [15,20] active | count | maxRooms |
|------|---------------|---------------|----------------|-------|----------|
| 0 | yes | no | no | 1 | 1 |
| 5 | yes | yes | no | 2 | 2 |
| 10 | yes | no | no | 1 | 2 |
| 15 | yes | no | yes | 2 | 2 |
| 20 | yes | no | no | 1 | 2 |

### Optimized Solution from Canonical Skeleton:
```csharp
int MinMeetingRooms(int[][] intervals)
{
    if (intervals.Length == 0) return 0;
    
    // Sort by start time
    Array.Sort(intervals, (a, b) => a[0].CompareTo(b[0]));
    
    // Min-heap to track end times of ongoing meetings
    var pq = new PriorityQueue<int, int>();
    pq.Enqueue(intervals[0][1], intervals[0][1]);
    
    for (int i = 1; i < intervals.Length; i++)
    {
        // If earliest ending meeting finished before current starts
        if (intervals[i][0] >= pq.Peek())
        {
            pq.Dequeue(); // Reuse room
        }
        
        // Add current meeting's end time
        pq.Enqueue(intervals[i][1], intervals[i][1]);
    }
    
    return pq.Count; // Number of rooms needed
}
```

### Explanation of Pruning:
- **Sort by start time:** Process meetings in chronological order
- **Min-heap of end times:** Track when each room becomes free
- **Key insight:** If earliest ending meeting finishes before current starts, reuse that room
- **Heap size:** Represents current number of concurrent meetings
- **Maximum heap size:** Minimum rooms needed

### Optimized Solution Code Walkthrough / Variable Trace:
For `intervals = [[0,30],[5,10],[15,20]]`:

| i | intervals[i] | pq.Peek() | intervals[i][0] >= pq.Peek() | action | pq (end times) | pq.Count |
|---|--------------|-----------|------------------------------|--------|----------------|----------|
| 0 | [0,30] | - | - | add 30 | [30] | 1 |
| 1 | [5,10] | 30 | 5 >= 30, no | add 10 | [10,30] | 2 |
| 2 | [15,20] | 10 | 15 >= 10, yes | dequeue 10, add 20 | [20,30] | 2 |

### Big-O Analysis:
- **Brute Force:** O(n × maxTime) → check every time point
- **Optimized:** O(n log n) → sorting + heap operations
- **Space Complexity:** O(n) → heap can hold all meetings

</details>

<details>
<summary><b>Variant #5: Non-overlapping Intervals (Minimum Removal)</b></summary>

## Variant #5: Non-overlapping Intervals

### Input/Output:
- Input: `intervals = [[1,2],[2,3],[3,4],[1,3]]`
- Output: `1` (remove `[1,3]`)

### Full State Space:
All possible subsets of intervals to remove.
```
For n=4 intervals: 2^4 = 16 possible removal combinations
Find minimum size subset whose removal eliminates all overlaps
```

### Expected/Pruned State Space:
Greedy approach: keep intervals with earliest end times.
```
Sort by end time, greedily keep non-overlapping intervals
Removal count = total - kept count
```

### State Space Leading to Output:
Maximize non-overlapping intervals kept = minimize removals.

### Brute Force Canonical Skeleton:
```csharp
int EraseOverlapIntervalsBruteForce(int[][] intervals)
{
    // Try all 2^n subsets, find smallest removal set
    int minRemovals = intervals.Length;
    
    for (int mask = 0; mask < (1 << intervals.Length); mask++)
    {
        var kept = new List<int[]>();
        for (int i = 0; i < intervals.Length; i++)
        {
            if ((mask & (1 << i)) == 0) // Not removed
                kept.Add(intervals[i]);
        }
        
        // Check if kept intervals are non-overlapping
        if (IsNonOverlapping(kept))
        {
            int removals = intervals.Length - kept.Count;
            minRemovals = Math.Min(minRemovals, removals);
        }
    }
    
    return minRemovals;
}

bool IsNonOverlapping(List<int[]> intervals)
{
    for (int i = 0; i < intervals.Count; i++)
        for (int j = i + 1; j < intervals.Count; j++)
            if (intervals[i][0] < intervals[j][1] && intervals[j][0] < intervals[i][1])
                return false;
    return true;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `intervals = [[1,2],[2,3],[3,4],[1,3]]`:

| mask | kept intervals | non-overlapping | removals |
|------|----------------|-----------------|----------|
| 0111 | [[1,2],[2,3],[3,4]] | yes | 1 |
| 1011 | [[1,2],[3,4],[1,3]] | no | - |
| ... | ... | ... | ... |

### Optimized Solution from Canonical Skeleton:
```csharp
int EraseOverlapIntervals(int[][] intervals)
{
    if (intervals.Length == 0) return 0;
    
    // Sort by end time (greedy: prefer intervals that end early)
    Array.Sort(intervals, (a, b) => a[1].CompareTo(b[1]));
    
    int kept = 1; // Keep first interval
    int currentEnd = intervals[0][1];
    
    for (int i = 1; i < intervals.Length; i++)
    {
        if (intervals[i][0] >= currentEnd) // No overlap
        {
            kept++;
            currentEnd = intervals[i][1];
        }
        // else: overlaps with previous, skip (remove) this one
    }
    
    return intervals.Length - kept; // Total - kept = removed
}
```

### Explanation of Pruning:
- **Greedy strategy:** Sort by end time, keep intervals ending earliest
- **Key insight:** If interval ends early, leaves more room for future intervals
- **Activity selection problem:** Classic greedy algorithm
- **Overlap check:** If `current.start >= previous.end`, no overlap
- **Count kept intervals:** Removals = total - kept

### Optimized Solution Code Walkthrough / Variable Trace:
For `intervals = [[1,2],[2,3],[3,4],[1,3]]` → sorted by end: `[[1,2],[2,3],[1,3],[3,4]]`:

| i | intervals[i] | currentEnd | intervals[i][0] >= currentEnd | action | kept |
|---|--------------|------------|-------------------------------|--------|------|
| 0 | [1,2] | 2 | - | initialize | 1 |
| 1 | [2,3] | 2 | 2 >= 2, yes | keep, currentEnd=3 | 2 |
| 2 | [1,3] | 3 | 1 >= 3, no | skip (remove) | 2 |
| 3 | [3,4] | 3 | 3 >= 3, yes | keep, currentEnd=4 | 3 |
| - | - | - | - | return 4-3=1 | - |

### Big-O Analysis:
- **Brute Force:** O(2^n × n²) → try all subsets, check each
- **Optimized:** O(n log n) → sorting dominates
- **Space Complexity:** O(1) → only variables

</details>


<details>
<summary><b>Variant #6: Interval Intersection</b></summary>

## Variant #6: Interval Intersection

### Input/Output:
- Input: `A = [[0,2],[5,10],[11,13]], B = [[1,5],[8,12]]`
- Output: `[[1,2],[5,5],[8,10],[11,12]]`

### Full State Space:
All pairs of intervals from A and B need to be checked.
```
For A with n intervals and B with m intervals: n × m pairs
For this example: 3 × 2 = 6 pairs to check
```

### Expected/Pruned State Space:
Two-pointer approach checks only relevant pairs.
```
Advance pointer with smaller end time
Only O(n + m) comparisons instead of O(n × m)
```

### State Space Leading to Output:
Find all overlapping regions between intervals from A and B.

### Brute Force Canonical Skeleton:
```csharp
int[][] IntervalIntersectionBruteForce(int[][] A, int[][] B)
{
    var result = new List<int[]>();
    
    // Check all pairs
    foreach (var a in A)
    {
        foreach (var b in B)
        {
            // Check if a and b overlap
            int start = Math.Max(a[0], b[0]);
            int end = Math.Min(a[1], b[1]);
            
            if (start <= end) // Valid intersection
                result.Add(new int[] { start, end });
        }
    }
    
    return result.ToArray();
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `A = [[0,2],[5,10],[11,13]], B = [[1,5],[8,12]]`:

| a | b | start=max | end=min | start<=end | intersection |
|---|---|-----------|---------|------------|-------------|
| [0,2] | [1,5] | max(0,1)=1 | min(2,5)=2 | yes | [1,2] |
| [0,2] | [8,12] | max(0,8)=8 | min(2,12)=2 | no | - |
| [5,10] | [1,5] | max(5,1)=5 | min(10,5)=5 | yes | [5,5] |
| [5,10] | [8,12] | max(5,8)=8 | min(10,12)=10 | yes | [8,10] |
| [11,13] | [1,5] | max(11,1)=11 | min(13,5)=5 | no | - |
| [11,13] | [8,12] | max(11,8)=11 | min(13,12)=12 | yes | [11,12] |

### Optimized Solution from Canonical Skeleton:
```csharp
int[][] IntervalIntersection(int[][] A, int[][] B)
{
    var result = new List<int[]>();
    int i = 0, j = 0;
    
    while (i < A.Length && j < B.Length)
    {
        // Check if A[i] and B[j] intersect
        int start = Math.Max(A[i][0], B[j][0]);
        int end = Math.Min(A[i][1], B[j][1]);
        
        if (start <= end)
            result.Add(new int[] { start, end });
        
        // Advance pointer with smaller end time
        if (A[i][1] < B[j][1])
            i++;
        else
            j++;
    }
    
    return result.ToArray();
}
```

### Explanation of Pruning:
- **Two-pointer technique:** Process both arrays simultaneously
- **Intersection formula:** `[max(start1, start2), min(end1, end2)]`
- **Valid intersection:** When `start <= end`
- **Key insight:** Advance pointer with smaller end time (that interval can't intersect with future intervals from other array)
- **Linear time:** O(n + m) instead of O(n × m)

### Optimized Solution Code Walkthrough / Variable Trace:
For `A = [[0,2],[5,10],[11,13]], B = [[1,5],[8,12]]`:

| i | j | A[i] | B[j] | start | end | valid | action | result |
|---|---|------|------|-------|-----|-------|--------|--------|
| 0 | 0 | [0,2] | [1,5] | 1 | 2 | yes | add, i++ (2<5) | [[1,2]] |
| 1 | 0 | [5,10] | [1,5] | 5 | 5 | yes | add, j++ (5<10) | [[1,2],[5,5]] |
| 1 | 1 | [5,10] | [8,12] | 8 | 10 | yes | add, i++ (10<12) | [[1,2],[5,5],[8,10]] |
| 2 | 1 | [11,13] | [8,12] | 11 | 12 | yes | add, j++ (12<13) | [[1,2],[5,5],[8,10],[11,12]] |
| 2 | 2 | - | - | - | - | - | done | - |

### Big-O Analysis:
- **Brute Force:** O(n × m) → check all pairs
- **Optimized:** O(n + m) → two-pointer sweep
- **Space Complexity:** O(k) → k intersections in result

</details>


<details>
<summary><b>Variant #7: Minimum Arrows to Burst Balloons</b></summary>

## Variant #7: Minimum Arrows to Burst Balloons

### Input/Output:
- Input: `points = [[10,16],[2,8],[1,6],[7,12]]`
- Output: `2` (shoot at x=6 and x=11)

### Full State Space:
All possible positions to shoot arrows.
```
Could shoot at any x-coordinate in range [1, 16]
Need to find minimum number of positions that hit all balloons
```

### Expected/Pruned State Space:
Greedy approach: shoot at end of earliest-ending balloon.
```
Sort by end position, shoot at each group's end
```

### State Space Leading to Output:
Group balloons that can be burst by single arrow, count groups.

### Brute Force Canonical Skeleton:
```csharp
int FindMinArrowShotsBruteForce(int[][] points)
{
    // Try all possible sets of arrow positions
    int minArrows = points.Length;
    
    // This is exponential - simplified for illustration
    // Would need to generate all valid shooting combinations
    return minArrows; // Placeholder
}
```

### Optimized Solution from Canonical Skeleton:
```csharp
int FindMinArrowShots(int[][] points)
{
    if (points.Length == 0) return 0;
    
    // Sort by end position
    Array.Sort(points, (a, b) => a[1].CompareTo(b[1]));
    
    int arrows = 1;
    int arrowPos = points[0][1]; // Shoot at end of first balloon
    
    for (int i = 1; i < points.Length; i++)
    {
        // If balloon starts after arrow position, need new arrow
        if (points[i][0] > arrowPos)
        {
            arrows++;
            arrowPos = points[i][1]; // Shoot at end of this balloon
        }
        // else: current arrow hits this balloon too
    }
    
    return arrows;
}
```

### Explanation of Pruning:
- **Greedy strategy:** Sort by end position, shoot at earliest end
- **Key insight:** Shooting at end of balloon maximizes balloons hit
- **Similar to interval scheduling:** Keep non-overlapping groups
- **Overlap condition:** Next balloon starts after current arrow position

### Optimized Solution Code Walkthrough / Variable Trace:
For `points = [[10,16],[2,8],[1,6],[7,12]]` → sorted by end: `[[1,6],[2,8],[7,12],[10,16]]`:

| i | points[i] | arrowPos | points[i][0] > arrowPos | action | arrows |
|---|-----------|----------|-------------------------|--------|--------|
| 0 | [1,6] | 6 | - | shoot at 6 | 1 |
| 1 | [2,8] | 6 | 2 > 6, no | same arrow | 1 |
| 2 | [7,12] | 6 | 7 > 6, yes | new arrow at 12 | 2 |
| 3 | [10,16] | 12 | 10 > 12, no | same arrow | 2 |

### Big-O Analysis:
- **Brute Force:** Exponential → try all combinations
- **Optimized:** O(n log n) → sorting dominates
- **Space Complexity:** O(1) → only variables

</details>

<details>
<summary><b>Variant #8: Employee Free Time</b></summary>

## Variant #8: Employee Free Time

### Input/Output:
- Input: `schedule = [[[1,3],[6,7]],[[2,4]],[[2,5],[9,12]]]`
- Output: `[[5,6],[7,9]]` (common free time)

### Full State Space:
All time points across all employees' schedules.
```
Flattened: [1,3],[6,7],[2,4],[2,5],[9,12]
Need to find gaps between merged intervals
```

### Expected/Pruned State Space:
Merge all intervals, find gaps between merged intervals.
```
Merged: [1,5],[6,7],[9,12]
Gaps: [5,6],[7,9]
```

### State Space Leading to Output:
Flatten all schedules, merge intervals, find gaps.

### Brute Force Canonical Skeleton:
```csharp
int[][] EmployeeFreeTi meBruteForce(int[][][] schedule)
{
    // Check every minute to see if all employees are free
    var result = new List<int[]>();
    
    int maxTime = schedule.SelectMany(s => s).Max(i => i[1]);
    
    for (int time = 0; time <= maxTime; time++)
    {
        bool allFree = true;
        foreach (var employee in schedule)
        {
            foreach (var interval in employee)
            {
                if (interval[0] <= time && time < interval[1])
                {
                    allFree = false;
                    break;
                }
            }
            if (!allFree) break;
        }
        // Group consecutive free times into intervals
    }
    
    return result.ToArray();
}
```

### Optimized Solution from Canonical Skeleton:
```csharp
int[][] EmployeeFreeTime(int[][][] schedule)
{
    // Flatten all intervals from all employees
    var allIntervals = new List<int[]>();
    foreach (var employee in schedule)
        foreach (var interval in employee)
            allIntervals.Add(interval);
    
    // Sort by start time
    allIntervals.Sort((a, b) => a[0].CompareTo(b[0]));
    
    // Merge overlapping intervals
    var merged = new List<int[]>();
    int[] current = allIntervals[0];
    
    for (int i = 1; i < allIntervals.Count; i++)
    {
        if (allIntervals[i][0] <= current[1])
            current[1] = Math.Max(current[1], allIntervals[i][1]);
        else
        {
            merged.Add(current);
            current = allIntervals[i];
        }
    }
    merged.Add(current);
    
    // Find gaps between merged intervals
    var freeTime = new List<int[]>();
    for (int i = 1; i < merged.Count; i++)
    {
        freeTime.Add(new int[] { merged[i - 1][1], merged[i][0] });
    }
    
    return freeTime.ToArray();
}
```

### Explanation of Pruning:
- **Flatten schedules:** Combine all employees' intervals into one list
- **Merge intervals:** Find when anyone is working
- **Find gaps:** Free time is between merged working intervals
- **Three-phase approach:** flatten → merge → find gaps

### Optimized Solution Code Walkthrough / Variable Trace:
For `schedule = [[[1,3],[6,7]],[[2,4]],[[2,5],[9,12]]]`:

| phase | data | result |
|-------|------|--------|
| 1. Flatten | [[1,3],[6,7],[2,4],[2,5],[9,12]] | - |
| 2. Sort | [[1,3],[2,4],[2,5],[6,7],[9,12]] | - |
| 3. Merge | [[1,5],[6,7],[9,12]] | - |
| 4. Gaps | [5,6] between [1,5] and [6,7] | [[5,6],[7,9]] |
| 4. Gaps | [7,9] between [6,7] and [9,12] | [[5,6],[7,9]] |

### Big-O Analysis:
- **Brute Force:** O(n × maxTime) → check every time point
- **Optimized:** O(n log n) → sorting dominates
- **Space Complexity:** O(n) → flattened intervals

</details>

---

## Key Takeaways

### Interval Pattern Essentials
Always check:
- **Are intervals given in sorted order?** (if not, sort first)
- **What defines overlap?** `a.start < b.end && b.start < a.end`
- **What am I looking for?** Merge, count, detect, or find gaps?

### Common Strategies
1. **Merge overlapping:** Sort by start, sweep with current interval
2. **Detect any overlap:** Sort by start, check adjacent pairs
3. **Count concurrent:** Use min-heap or event-based sweep
4. **Greedy scheduling:** Sort by end, keep earliest ending
5. **Two-pointer:** For two sorted lists, advance smaller end

### Overlap Conditions
```csharp
// Two intervals [a, b] and [c, d] overlap if:
a < d && c < b  // or: a <= d && c <= b (inclusive)

// For sorted intervals by start:
if (next.start <= current.end) // overlap
```

### Greedy Insight
- **Sort by end time:** Maximizes remaining space (activity selection)
- **Sort by start time:** Simplifies merging and overlap detection

### Problem Recognition
- **Keywords:** meetings, schedules, ranges, overlapping, conflicts
- **Input format:** Array of [start, end] pairs
- **Question asks:** merge, minimum rooms, can attend, conflicts

---

_End of Intervals Merging Pattern_
