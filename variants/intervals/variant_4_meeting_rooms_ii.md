# Meeting Rooms II

**Difficulty:** Medium  
**LeetCode Problem:** #253  
**Tags:** `Intervals`, `Heap`, `Sweep Line`, `Sorting`

---

## Problem Statement

Given an array of meeting time intervals `intervals` where `intervals[i] = [starti, endi]`, return the minimum number of conference rooms required.

**Constraints:**
- `1 <= intervals.length <= 10^4`
- `0 <= starti < endi <= 10^6`

**Example 1:**
```
Input: intervals = [[0,30],[5,10],[15,20]]
Output: 2
Explanation: 
- Meeting 1: [0,30]
- Meeting 2: [5,10] (overlaps with 1, need 2nd room)
- Meeting 3: [15,20] (overlaps with 1, can use 2nd room)
Max concurrent: 2
```

**Example 2:**
```
Input: intervals = [[7,10],[2,4]]
Output: 1
Explanation: No overlap, can use same room
```

---

## Step 1: State Space

### Problem Visualization

For `intervals = [[0,30],[5,10],[15,20]]`:

```
Timeline:
Time:  0    5    10   15   20   30
Room1: [────────────────────────]  [0,30]
Room2:      [────]                 [5,10]
Room2:           [───────]         [15,20] (reuse room2)

Concurrent meetings over time:
Time:  0    5    10   15   20   30
Count: 1    2    1    2    1    0
           max=2
```

Sweep line visualization:
```
Events (sorted by time):
  0: +1 (start [0,30])
  5: +1 (start [5,10])
 10: -1 (end [5,10])
 15: +1 (start [15,20])
 20: -1 (end [15,20])
 30: -1 (end [0,30])

Running count:
  0: 1
  5: 2 ← maximum concurrent
 10: 1
 15: 2
 20: 1
 30: 0
```

### Core Question

**What is the maximum number of meetings happening concurrently at any point in time?**

This is the minimum number of rooms needed to accommodate all meetings.

### Deriving from First Principles

**Observation 1: Rooms needed = Max concurrent meetings**
At any time t, if k meetings are ongoing, we need k rooms.
The answer is the maximum k across all time points.

**Observation 2: Events approach (sweep line)**
Each interval creates two events:
- Start event: +1 meeting (need a room)
- End event: -1 meeting (free a room)

Process events chronologically, track running count.

**Observation 3: Event ordering matters**
If meeting ends at time t and another starts at time t:
- End should process before start (room becomes free, then reused)
- This requires careful sorting

**Observation 4: Min-heap approach**
Alternative approach:
- Sort meetings by start time
- Use min-heap to track end times of ongoing meetings
- When starting new meeting, remove all meetings that have ended
- Heap size = current concurrent meetings
- Max heap size = answer

**Observation 5: Two separate arrays**
Another approach:
- Create sorted array of start times
- Create sorted array of end times
- Use two pointers to count concurrent meetings

**Formula (Sweep Line):**
```
events = []
for each interval [s, e]:
  events.add((s, +1))  // Start
  events.add((e, -1))  // End

Sort events by time (end before start if tie)

maxRooms = 0, currentRooms = 0
for each event:
  currentRooms += event.delta
  maxRooms = max(maxRooms, currentRooms)
  
return maxRooms
```

### State Space Structure

**Type:** Counting maximum concurrent intervals using sweep line or heap.

**Structure:**
- Approach 1 (Sweep): Events sorted by time
- Approach 2 (Heap): Min-heap of end times
- Approach 3 (Two arrays): Separate start/end arrays

**Cardinality:**
- Sweep line: O(n log n) for sorting, O(n) for sweep
- Min-heap: O(n log n) for sorting and heap operations
- Two arrays: O(n log n) for sorting, O(n) for scan

**Key Property:** Maximum concurrent meetings occurs at some meeting's start time.

### Example Computation

Input: `intervals = [[0,30],[5,10],[15,20]]`

**Approach 1: Sweep Line**
```
Events:
  (0, +1), (30, -1), (5, +1), (10, -1), (15, +1), (20, -1)

Sort:
  (0, +1), (5, +1), (10, -1), (15, +1), (20, -1), (30, -1)

Sweep:
  Time 0: +1 → current=1, max=1
  Time 5: +1 → current=2, max=2 ← Answer
  Time 10: -1 → current=1, max=2
  Time 15: +1 → current=2, max=2
  Time 20: -1 → current=1, max=2
  Time 30: -1 → current=0, max=2

Result: 2
```

**Approach 2: Min-Heap**
```
Sort by start: [[0,30],[5,10],[15,20]]
Heap = [] (min-heap of end times)

Meeting [0,30]:
  Heap empty, add end time 30
  Heap = [30], size=1, max=1

Meeting [5,10]:
  Check heap: min=30 > 5 (not ended yet)
  Add end time 10
  Heap = [10, 30], size=2, max=2 ← Answer

Meeting [15,20]:
  Check heap: min=10 ≤ 15 (ended, remove)
  Heap = [30]
  Add end time 20
  Heap = [20, 30], size=2, max=2

Result: 2
```

### Generation Pattern

**Brute force:**
```csharp
// For each pair of intervals, check if they overlap
// Build overlap graph, find maximum clique
// Time: O(n²) or worse
```

---

## Step 2: Brute Force

Check all pairs for overlaps, build conflict graph, find maximum concurrent: O(n²).

---

## Step 3: Optimization Ideas

### Key Insights
1. **Sweep line:** Process events chronologically
2. **Min-heap:** Track end times of active meetings
3. **Two pointers:** Separate sorted start/end arrays

### Comparison
- Sweep line: Most intuitive, handles ties carefully
- Min-heap: Natural representation of "rooms in use"
- Two pointers: Memory efficient, elegant

---

## Step 4: Optimal Solution

### Approach 1: Min-Heap (Most Common)

```csharp
public int MinMeetingRooms(int[][] intervals) {
    if (intervals.Length == 0) return 0;
    
    // Sort by start time
    Array.Sort(intervals, (a, b) => a[0].CompareTo(b[0]));
    
    // Min-heap of end times
    var endTimes = new PriorityQueue<int, int>();
    endTimes.Enqueue(intervals[0][1], intervals[0][1]);
    
    int maxRooms = 1;
    
    for (int i = 1; i < intervals.Length; i++) {
        // Remove all meetings that have ended
        while (endTimes.Count > 0 && endTimes.Peek() <= intervals[i][0]) {
            endTimes.Dequeue();
        }
        
        // Add current meeting's end time
        endTimes.Enqueue(intervals[i][1], intervals[i][1]);
        
        // Track maximum concurrent meetings
        maxRooms = Math.Max(maxRooms, endTimes.Count);
    }
    
    return maxRooms;
}
```

**Complexity:**
- Time: O(n log n) - sorting + heap operations
- Space: O(n) - heap

### Approach 2: Sweep Line

```csharp
public int MinMeetingRooms(int[][] intervals) {
    var events = new List<(int time, int delta)>();
    
    foreach (var interval in intervals) {
        events.Add((interval[0], 1));   // Start
        events.Add((interval[1], -1));  // End
    }
    
    // Sort by time; if tie, end before start
    events.Sort((a, b) => {
        if (a.time != b.time) return a.time.CompareTo(b.time);
        return a.delta.CompareTo(b.delta);  // -1 before +1
    });
    
    int maxRooms = 0;
    int currentRooms = 0;
    
    foreach (var evt in events) {
        currentRooms += evt.delta;
        maxRooms = Math.Max(maxRooms, currentRooms);
    }
    
    return maxRooms;
}
```

**Complexity:**
- Time: O(n log n)
- Space: O(n) - events list

### Approach 3: Two Arrays

```csharp
public int MinMeetingRooms(int[][] intervals) {
    int n = intervals.Length;
    int[] starts = new int[n];
    int[] ends = new int[n];
    
    for (int i = 0; i < n; i++) {
        starts[i] = intervals[i][0];
        ends[i] = intervals[i][1];
    }
    
    Array.Sort(starts);
    Array.Sort(ends);
    
    int rooms = 0;
    int maxRooms = 0;
    int endPtr = 0;
    
    for (int i = 0; i < n; i++) {
        // Check if any meeting has ended
        while (endPtr < n && ends[endPtr] <= starts[i]) {
            rooms--;
            endPtr++;
        }
        
        // Start new meeting
        rooms++;
        maxRooms = Math.Max(maxRooms, rooms);
    }
    
    return maxRooms;
}
```

**Complexity:**
- Time: O(n log n)
- Space: O(n)

---

## Edge Cases

1. **No overlaps:** `[[1,2],[3,4],[5,6]]` → `1`
2. **All overlap:** `[[0,10],[1,2],[3,4]]` → `3`
3. **Single meeting:** `[[1,5]]` → `1`
4. **Back-to-back meetings:** `[[1,2],[2,3]]` → `1` (room freed at 2)
5. **Nested intervals:** `[[1,10],[2,3],[4,5]]` → `2`

---

## Related Problems

1. **Meeting Rooms (LeetCode #252)** - Check if person can attend all
2. **Non-overlapping Intervals (LeetCode #435)** - Remove to make non-overlapping
3. **Merge Intervals (LeetCode #56)** - Combine overlapping
4. **Car Pooling (LeetCode #1094)** - Similar concurrent count

---

## Pattern Recognition

**Problem Asks For:**
- Maximum concurrent intervals
- Minimum resources needed
- Count of overlapping at peak

**This Suggests:**
- Sweep line algorithm
- Min-heap of end times
- Sort and scan
- → **Intervals + Counting pattern**

**Key Indicators:**
- "Minimum rooms/resources"
- "Maximum concurrent"
- Overlapping intervals

---

## Tags

`#intervals` `#heap` `#sweep-line` `#concurrent` `#meeting-rooms` `#scheduling` `#two-pointers`
