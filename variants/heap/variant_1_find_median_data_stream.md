# Find Median from Data Stream

**Difficulty:** Hard  
**LeetCode Problem:** #295  
**Tags:** `Heap`, `Design`, `Two Heaps`, `Data Stream`

---

## Problem Statement

The **median** is the middle value in an ordered integer list. If the size of the list is even, there is no middle value, and the median is the mean of the two middle values.

Implement the MedianFinder class:
- `MedianFinder()` initializes the object.
- `void addNum(int num)` adds the integer `num` from the data stream.
- `double findMedian()` returns the median of all elements so far.

**Constraints:**
- `-10^5 <= num <= 10^5`
- There will be at least one element before calling `findMedian`.
- At most `5 * 10^4` calls will be made to `addNum` and `findMedian`.

**Example:**
```
Input:
["MedianFinder", "addNum", "addNum", "findMedian", "addNum", "findMedian"]
[[], [1], [2], [], [3], []]

Output:
[null, null, null, 1.5, null, 2.0]

Explanation:
MedianFinder medianFinder = new MedianFinder();
medianFinder.addNum(1);    // arr = [1]
medianFinder.addNum(2);    // arr = [1, 2]
medianFinder.findMedian(); // return 1.5 (average of 1 and 2)
medianFinder.addNum(3);    // arr = [1, 2, 3]
medianFinder.findMedian(); // return 2.0
```

---

## Step 1: State Space

### Problem Visualization

Stream: `[1, 2, 3]`

```
After addNum(1):
  Numbers: [1]
  Median: 1

After addNum(2):
  Numbers: [1, 2]
  Median: (1 + 2) / 2 = 1.5

After addNum(3):
  Numbers: [1, 2, 3]
  Median: 2
```

**Two Heaps Approach:**

```
Maintain two heaps:
- maxHeap (left half):  stores smaller half, max at top
- minHeap (right half): stores larger half, min at top

Invariants:
1. maxHeap.size() == minHeap.size() OR maxHeap.size() == minHeap.size() + 1
2. maxHeap.top() <= minHeap.top() (all left <= all right)

Median:
- If sizes equal: (maxHeap.top() + minHeap.top()) / 2
- If maxHeap larger: maxHeap.top()

Example: [1, 2, 3]

Add 1:
  maxHeap: [1]        minHeap: []
  Median: 1

Add 2:
  Compare 2 to maxHeap.top (1): 2 > 1, goes to minHeap
  maxHeap: [1]        minHeap: [2]
  Median: (1 + 2) / 2 = 1.5

Add 3:
  Compare 3 to maxHeap.top (1): 3 > 1, goes to minHeap
  minHeap: [2, 3]
  Rebalance: move minHeap.top (2) to maxHeap
  maxHeap: [1, 2]     minHeap: [3]
  Median: maxHeap.top = 2
```

### Core Question

**How can we efficiently maintain the median as numbers arrive in a stream?**

The median requires access to middle element(s). Two heaps partition the data around the median.

### Deriving from First Principles

**Observation 1: Median position**
```
For n numbers sorted: [a₀, a₁, ..., aₙ₋₁]
If n is odd:  median = a[n/2]
If n is even: median = (a[n/2-1] + a[n/2]) / 2

Need efficient access to middle element(s).
```

**Observation 2: Partition approach**
Divide numbers into two halves:
- Left half: smaller numbers
- Right half: larger numbers

If we keep halves balanced, median is at boundary.

**Observation 3: Heap properties**
- Max-heap for left: O(1) access to largest of smaller half
- Min-heap for right: O(1) access to smallest of larger half
- These are the middle elements!

**Observation 4: Size invariant**
```
Keep heaps balanced:
|maxHeap.size - minHeap.size| <= 1

If even total: both same size, median = average of tops
If odd total: maxHeap has 1 extra, median = maxHeap.top
```

**Observation 5: Insertion strategy**
```
To add number x:
1. Decide which heap (compare to maxHeap.top)
2. Add to appropriate heap
3. Rebalance if size difference > 1

Rebalance: move top of larger heap to smaller heap
```

**Formula:**
```
addNum(x):
  if maxHeap is empty OR x <= maxHeap.top():
    maxHeap.push(x)
  else:
    minHeap.push(x)
  
  // Rebalance
  if maxHeap.size() > minHeap.size() + 1:
    minHeap.push(maxHeap.pop())
  elif minHeap.size() > maxHeap.size():
    maxHeap.push(minHeap.pop())

findMedian():
  if maxHeap.size() == minHeap.size():
    return (maxHeap.top() + minHeap.top()) / 2.0
  else:
    return maxHeap.top()
```

### State Space Structure

**Type:** Two heaps partitioning data stream.

**Structure:**
- Max-heap (left partition): stores ≤ median
- Min-heap (right partition): stores ≥ median
- Maintain balance: |size difference| ≤ 1

**Cardinality:**
- addNum: O(log n) - heap insertion + potential rebalance
- findMedian: O(1) - peek at heap tops
- Space: O(n) - store all numbers

**Key Property:** Heaps maintain sorted order at boundary (median position).

### Example Computation

Input: `addNum(5), addNum(15), addNum(10), findMedian(), addNum(20), findMedian()`

```
Initial:
  maxHeap: []    minHeap: []

addNum(5):
  maxHeap empty, add to maxHeap
  maxHeap: [5]   minHeap: []
  Sizes: 1, 0 (difference = 1, OK)

addNum(15):
  15 > 5 (maxHeap.top), add to minHeap
  maxHeap: [5]   minHeap: [15]
  Sizes: 1, 1 (balanced)

addNum(10):
  10 > 5, add to minHeap
  maxHeap: [5]   minHeap: [10, 15]
  Sizes: 1, 2 (difference = 1, need rebalance)
  Move minHeap.top (10) to maxHeap
  maxHeap: [5, 10]   minHeap: [15]
  After rebalance: [10] on left, [15] on right
  
findMedian():
  Sizes: 2, 1 (maxHeap larger)
  Return maxHeap.top() = 10

addNum(20):
  20 > 10, add to minHeap
  maxHeap: [5, 10]   minHeap: [15, 20]
  Sizes: 2, 2 (balanced)

findMedian():
  Sizes equal
  Return (10 + 15) / 2 = 12.5
```

### Generation Pattern

**Brute force:**
```csharp
// Store all numbers in sorted list
// Insert: O(n) to maintain sorted order
// Find median: O(1) access middle
```

---

## Step 2: Brute Force

```csharp
// Maintain sorted list
private List<int> nums = new List<int>();

public void AddNum(int num) {
    // Binary search for insertion position: O(log n)
    int pos = nums.BinarySearch(num);
    if (pos < 0) pos = ~pos;
    nums.Insert(pos, num);  // O(n) to shift elements
}

public double FindMedian() {
    int n = nums.Count;
    if (n % 2 == 1) {
        return nums[n / 2];
    } else {
        return (nums[n / 2 - 1] + nums[n / 2]) / 2.0;
    }
}
```

**Complexity:**
- addNum: O(n) - insertion requires shifting
- findMedian: O(1)
- Space: O(n)

**Problem:** Insertion is expensive for large streams.

---

## Step 3: Optimization Ideas

### Key Insights
1. **Two heaps partition:** Split around median
2. **Max-heap for left:** Quick access to largest of smaller half
3. **Min-heap for right:** Quick access to smallest of larger half
4. **Balance heaps:** Keep sizes within 1
5. **O(log n) insertion:** Both heaps support fast insert

---

## Step 4: Optimal Solution

```csharp
public class MedianFinder {
    // Max-heap for smaller half (negate values for max behavior)
    private PriorityQueue<int, int> maxHeap;
    // Min-heap for larger half
    private PriorityQueue<int, int> minHeap;
    
    public MedianFinder() {
        // Max-heap: use negative priorities
        maxHeap = new PriorityQueue<int, int>(Comparer<int>.Create((a, b) => b.CompareTo(a)));
        // Min-heap: default behavior
        minHeap = new PriorityQueue<int, int>();
    }
    
    public void AddNum(int num) {
        // Add to appropriate heap
        if (maxHeap.Count == 0 || num <= maxHeap.Peek()) {
            maxHeap.Enqueue(num, num);
        } else {
            minHeap.Enqueue(num, num);
        }
        
        // Rebalance heaps
        // Maintain: maxHeap.size == minHeap.size OR maxHeap.size == minHeap.size + 1
        if (maxHeap.Count > minHeap.Count + 1) {
            int val = maxHeap.Dequeue();
            minHeap.Enqueue(val, val);
        } else if (minHeap.Count > maxHeap.Count) {
            int val = minHeap.Dequeue();
            maxHeap.Enqueue(val, val);
        }
    }
    
    public double FindMedian() {
        if (maxHeap.Count == minHeap.Count) {
            return (maxHeap.Peek() + minHeap.Peek()) / 2.0;
        } else {
            return maxHeap.Peek();
        }
    }
}
```

**Complexity:**
- addNum: O(log n) - heap insertion and potential rebalance
- findMedian: O(1) - peek at tops
- Space: O(n) - store all n numbers

**Why Two Heaps?**
- Max-heap top = largest of left half
- Min-heap top = smallest of right half
- Together they give us the middle element(s)
- Both operations are O(log n)

---

## Alternative: Multiset/TreeMap

In languages with balanced BST:

```csharp
// Using SortedSet (but harder to access middle)
// Or custom BST with rank queries
// Time: O(log n) for insert, O(log n) for median
```

C# doesn't have multiset, but could use `SortedDictionary<int, int>` with counts.

---

## Edge Cases

1. **Single element:** `[5]` → median = 5
2. **Two elements:** `[5, 10]` → median = 7.5
3. **All same:** `[5, 5, 5]` → median = 5
4. **Negative numbers:** `[-5, -3, -1]` → median = -3
5. **Large stream:** Performance with 50,000 elements

---

## Visualization

```
Stream: [5, 15, 10, 20, 3]

After [5]:
  maxHeap: [5]
  minHeap: []
  Median: 5

After [5, 15]:
  maxHeap: [5]        (larger of smaller half)
  minHeap: [15]       (smaller of larger half)
  Median: (5+15)/2 = 10

After [5, 15, 10]:
  maxHeap: [5, 10]    top=10
  minHeap: [15]       top=15
  Median: 10

After [5, 15, 10, 20]:
  maxHeap: [5, 10]    top=10
  minHeap: [15, 20]   top=15
  Median: (10+15)/2 = 12.5

After [5, 15, 10, 20, 3]:
  maxHeap: [3, 5, 10]  top=10
  minHeap: [15, 20]    top=15
  Median: 10
```

---

## Related Problems

1. **Sliding Window Median (LeetCode #480)** - Two heaps with removal
2. **IPO (LeetCode #502)** - Two heaps for project selection
3. **Find K Pairs with Smallest Sums (LeetCode #373)** - Heap frontier

---

## Pattern Recognition

**Problem Asks For:**
- Maintain median dynamically
- Efficient insertion from stream
- Efficient median retrieval

**This Suggests:**
- Need middle element(s) access
- Heap for O(log n) insertion
- Two heaps to partition data
- → **Two Heaps pattern**

**Key Indicators:**
- "Data stream" or "online algorithm"
- "Median" or "middle element"
- Multiple queries after insertions

---

## Tags

`#heap` `#two-heaps` `#data-stream` `#design` `#median` `#priority-queue`
