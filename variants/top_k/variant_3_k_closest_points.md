# K Closest Points to Origin

**Difficulty:** Medium  
**LeetCode Problem:** #973  
**Tags:** `Heap`, `Priority Queue`, `Top K`, `Distance`, `Geometry`

---

## Problem Statement

Given an array of `points` where `points[i] = [xi, yi]` represents a point on the X-Y plane and an integer `k`, return the `k` closest points to the origin `(0, 0)`.

The distance between two points on the X-Y plane is the Euclidean distance (i.e., `√(x² + y²)`).

You may return the answer in any order. The answer is guaranteed to be unique (except for the order that it is in).

**Constraints:**
- `1 <= k <= points.length <= 10^4`
- `-10^4 <= xi, yi <= 10^4`

**Example 1:**
```
Input: points = [[1,3],[-2,2]], k = 1
Output: [[-2,2]]
Explanation:
Distance (-2,2) to origin: √(4+4) = √8 ≈ 2.83
Distance (1,3) to origin: √(1+9) = √10 ≈ 3.16
Closest point: [-2,2]
```

**Example 2:**
```
Input: points = [[3,3],[5,-1],[-2,4]], k = 2
Output: [[3,3],[-2,4]]
Explanation: (Order doesn't matter, [[-2,4],[3,3]] is also accepted)
```

---

## Step 1: State Space

### Problem Visualization

For `points = [[1,3],[-2,2],[3,3]]`, `k = 2`:

```
Y-axis
  4 |     
  3 |   ●(1,3)       ●(3,3)
  2 | ●(-2,2)
  1 |       
  0 +─────────────── X-axis
 -2  0   1   2   3

Distances to origin (0,0):
  (1,3):   √(1² + 3²) = √10 ≈ 3.16
  (-2,2):  √(4 + 4) = √8 ≈ 2.83     ← closest
  (3,3):   √(9 + 9) = √18 ≈ 4.24

K=2 closest: [(-2,2), (1,3)]
```

Distance formula:
```
d = √(x² + y²)

Since we're only comparing distances, we can use:
d² = x² + y² (avoids expensive sqrt operation)
```

### Core Question

**Which k points have the smallest Euclidean distance to the origin?**

This is a "top k smallest" problem—we need the k points with minimum distance values.

### Deriving from First Principles

**Observation 1: Distance metric**
Distance to origin: `d = √(x² + y²)`
For comparison purposes, can use squared distance: `d² = x² + y²`
(Avoids expensive square root, preserves ordering)

**Observation 2: Top k smallest distances**
We need k points with smallest distances.
This is the opposite of "k largest"—use max-heap instead of min-heap.

**Observation 3: Max-heap of size k**
For k smallest elements:
- Use max-heap of size k (largest distance at top)
- When heap size > k, remove maximum (farthest point)
- After processing, heap contains k closest points

**Observation 4: Why max-heap for k smallest?**
- Min-heap keeps smallest at top → can't efficiently remove non-closest
- Max-heap keeps largest at top → remove farthest when needed
- Rule: Max-heap for k smallest, Min-heap for k largest

**Observation 5: Store point with distance**
Heap should order by distance but return points.
Store pairs: `(point, distance²)` ordered by distance.

**Formula:**
```
maxHeap = new MaxHeap(by distance)
for each point in points:
  dist² = point.x² + point.y²
  maxHeap.add((point, dist²))
  if maxHeap.size() > k:
    maxHeap.poll()  // Remove farthest point
    
result = all points in maxHeap
```

### State Space Structure

**Type:** Top k smallest by distance using max-heap.

**Structure:**
- Max-heap of size k ordered by distance
- Each element: (point, distance²)
- Top of heap: Farthest of the k closest points

**Cardinality:**
- Process n points
- Each operation: O(log k)
- Total: O(n log k)

**Key Property:** Max-heap of size k maintains k smallest by removing largest when exceeding k.

### Example Computation

Input: `points = [[1,3],[-2,2],[3,3]]`, `k = 2`

```
MaxHeap = [] (ordered by distance, max at top)

Process [1,3]:
  dist² = 1 + 9 = 10
  Add: heap = [([1,3], 10)]
  Size = 1 ≤ 2

Process [-2,2]:
  dist² = 4 + 4 = 8
  Add: heap = [([1,3], 10), ([-2,2], 8)]
  Size = 2 ≤ 2

Process [3,3]:
  dist² = 9 + 9 = 18
  Add: heap = [([3,3], 18), ([1,3], 10), ([-2,2], 8)]
  Size = 3 > 2
  Remove max: ([3,3], 18)
  heap = [([1,3], 10), ([-2,2], 8)]

Result: [[1,3], [-2,2]] (order may vary)
```

### Generation Pattern

**Brute force (sort):**
```csharp
public int[][] KClosest(int[][] points, int k) {
    Array.Sort(points, (a, b) => {
        int dist1 = a[0] * a[0] + a[1] * a[1];
        int dist2 = b[0] * b[0] + b[1] * b[1];
        return dist1.CompareTo(dist2);
    });
    
    return points.Take(k).ToArray();
}
// Time: O(n log n), Space: O(1) or O(n)
```

---

## Step 2: Brute Force

Compute distances, sort by distance, take first k: O(n log n).

---

## Step 3: Optimization Ideas

### Key Insights
1. **Don't need full sort:** Only need k smallest
2. **Max-heap of size k:** More efficient than sorting
3. **Squared distance:** Avoids expensive sqrt
4. **Max-heap for k smallest:** Opposite of min-heap for k largest

---

## Step 4: Optimal Solution

### Approach 1: Max-Heap

```csharp
public int[][] KClosest(int[][] points, int k) {
    // Max-heap ordered by distance (negate for max behavior)
    var maxHeap = new PriorityQueue<int[], int>();
    
    foreach (var point in points) {
        int distSquared = point[0] * point[0] + point[1] * point[1];
        
        // Negate distance for max-heap behavior
        maxHeap.Enqueue(point, -distSquared);
        
        if (maxHeap.Count > k) {
            maxHeap.Dequeue();  // Remove farthest
        }
    }
    
    // Extract all points from heap
    var result = new int[k][];
    for (int i = 0; i < k; i++) {
        result[i] = maxHeap.Dequeue();
    }
    
    return result;
}
```

**Complexity:**
- Time: O(n log k)
  - Process n points
  - Each add/remove: O(log k)
- Space: O(k) - heap size

### Approach 2: Quickselect (Optimal Average Case)

```csharp
public int[][] KClosest(int[][] points, int k) {
    Quickselect(points, 0, points.Length - 1, k);
    
    // Return first k points (now partitioned)
    var result = new int[k][];
    Array.Copy(points, result, k);
    return result;
}

private void Quickselect(int[][] points, int left, int right, int k) {
    if (left >= right) return;
    
    int pivotIndex = Partition(points, left, right);
    
    if (pivotIndex == k) {
        return;  // Found k-th position
    } else if (pivotIndex < k) {
        Quickselect(points, pivotIndex + 1, right, k);
    } else {
        Quickselect(points, left, pivotIndex - 1, k);
    }
}

private int Partition(int[][] points, int left, int right) {
    int[] pivot = points[right];
    int pivotDist = Distance(pivot);
    int i = left;
    
    for (int j = left; j < right; j++) {
        if (Distance(points[j]) <= pivotDist) {
            Swap(points, i, j);
            i++;
        }
    }
    
    Swap(points, i, right);
    return i;
}

private int Distance(int[] point) {
    return point[0] * point[0] + point[1] * point[1];
}

private void Swap(int[][] points, int i, int j) {
    var temp = points[i];
    points[i] = points[j];
    points[j] = temp;
}
```

**Complexity:**
- Time: O(n) average, O(n²) worst
- Space: O(1)

---

## Comparison of Approaches

| Approach | Time | Space | Best When |
|----------|------|-------|-----------|
| Max-heap | O(n log k) | O(k) | Small k, stable performance |
| Quickselect | O(n) avg | O(1) | Large k, average case |
| Sort | O(n log n) | O(1)-O(n) | Simplicity, need sorted |

---

## Edge Cases

1. **k = 1:** Return closest single point
2. **k = n:** Return all points
3. **Points at origin:** Distance = 0
4. **Tie distances:** Any order acceptable
5. **Single point:** `[[1,1]], k=1` → `[[1,1]]`

---

## Related Problems

1. **Kth Largest Element (LeetCode #215)** - Basic top k pattern
2. **Top K Frequent Elements (LeetCode #347)** - Top k by frequency
3. **Find K Pairs with Smallest Sums (LeetCode #373)** - Top k pairs
4. **Kth Smallest Element in Sorted Matrix (LeetCode #378)** - Top k in matrix

---

## Pattern Recognition

**Problem Asks For:**
- K closest/farthest points
- K smallest/largest by some metric
- K best elements based on distance/score

**This Suggests:**
- Compute metric (distance, score)
- Use heap of size k
- Max-heap for k smallest, Min-heap for k largest
- → **Top K Elements pattern**

**Key Indicators:**
- "K closest", "K farthest"
- Distance or metric-based selection
- Don't need sorted order, just top k

---

## Tags

`#heap` `#priority-queue` `#top-k` `#distance` `#geometry` `#max-heap` `#quickselect`
