# Find K Pairs with Smallest Sums

**Difficulty:** Medium  
**LeetCode Problem:** #373  
**Tags:** `Heap`, `Priority Queue`, `Top K`, `Two Arrays`

---

## Problem Statement

You are given two integer arrays `nums1` and `nums2` sorted in non-decreasing order and an integer `k`.

Define a pair `(u, v)` which consists of one element from the first array and one element from the second array.

Return the `k` pairs `(u1, v1), (u2, v2), ..., (uk, vk)` with the smallest sums.

**Constraints:**
- `1 <= nums1.length, nums2.length <= 10^5`
- `-10^9 <= nums1[i], nums2[i] <= 10^9`
- `nums1` and `nums2` are sorted in non-decreasing order
- `1 <= k <= 10^4`

**Example 1:**
```
Input: nums1 = [1,7,11], nums2 = [2,4,6], k = 3
Output: [[1,2],[1,4],[1,6]]
Explanation: First 3 pairs with smallest sums:
[1,2]: sum=3
[1,4]: sum=5
[1,6]: sum=7
```

**Example 2:**
```
Input: nums1 = [1,1,2], nums2 = [1,2,3], k = 2
Output: [[1,1],[1,1]]
Explanation: All possible pairs:
[1,1]:1+1=2, [1,1]:1+1=2, [1,2]:1+2=3, [1,3]:1+3=4,
[1,1]:1+1=2, [1,2]:1+2=3, [1,3]:1+3=4,
[2,1]:2+1=3, [2,2]:2+2=4, [2,3]:2+3=5
```

---

## Step 1: State Space

### Problem Visualization

For `nums1 = [1,7,11]`, `nums2 = [2,4,6]`, `k = 3`:

```
Matrix of sums (nums1[i] + nums2[j]):

         nums2:  2   4   6
nums1: 1        3   5   7
       7        9  11  13
      11       13  15  17

Pairs sorted by sum:
(1,2): 3  ← smallest
(1,4): 5
(1,6): 7
(7,2): 9
(7,4): 11
...

K=3: [(1,2), (1,4), (1,6)]
```

Key observation:
```
For sorted arrays, smallest pairs form a "frontier":
Start with (nums1[0], nums2[0])
Next candidates: (nums1[0], nums2[1]) and (nums1[1], nums2[0])

Like merging k sorted lists, but 2D.
```

### Core Question

**How do we efficiently find the k pairs with smallest sums without generating all n×m pairs?**

Brute force would generate all pairs (O(n×m)), sort them (O(n×m log(n×m))), and take k. Can we do better using the sorted property?

### Deriving from First Principles

**Observation 1: Total pairs**
With arrays of size n and m, there are n×m possible pairs.
If k is small relative to n×m, generating all pairs is wasteful.

**Observation 2: Smallest pair**
The smallest pair is always `(nums1[0], nums2[0])` (both arrays sorted).

**Observation 3: Next smallest candidates**
After extracting `(nums1[i], nums2[j])`, the next candidates are:
- `(nums1[i+1], nums2[j])` - move down in nums1
- `(nums1[i], nums2[j+1])` - move right in nums2

This forms a search frontier, similar to Dijkstra's algorithm.

**Observation 4: Min-heap for frontier**
Use min-heap to track candidates ordered by sum:
- Start with `(nums1[0], nums2[0])`
- Extract minimum, add to result
- Add next candidates to heap (if not seen)
- Repeat k times

**Observation 5: Avoid duplicates**
Track visited pairs to avoid adding same pair multiple times.
Use set of (i, j) indices.

**Formula:**
```
minHeap = new MinHeap(by sum)
visited = new Set()

Add (0, 0) to heap and visited

for k iterations:
  (i, j, sum) = heap.pop()
  result.add([nums1[i], nums2[j]])
  
  if (i+1, j) not visited:
    Add (i+1, j) to heap and visited
  if (i, j+1) not visited:
    Add (i, j+1) to heap and visited

return result
```

### State Space Structure

**Type:** Min-heap based exploration of sorted pair space.

**Structure:**
- Min-heap of tuples: (sum, i, j)
- Visited set: (i, j) pairs already explored
- Result list: k pairs with smallest sums

**Cardinality:**
- K iterations
- Each iteration: O(log heap_size)
- Heap size ≤ min(k, n×m)
- Total: O(k log k)

**Key Property:** Sorted arrays ensure we can explore pairs in increasing sum order using a frontier approach.

### Example Computation

Input: `nums1 = [1,7,11]`, `nums2 = [2,4,6]`, `k = 3`

```
Heap = [], Visited = {}, Result = []

Initialize:
  Add (1+2=3, i=0, j=0) to heap
  Heap = [(3, 0, 0)]
  Visited = {(0,0)}

Iteration 1:
  Pop (3, 0, 0): nums1[0]=1, nums2[0]=2
  Result = [[1,2]]
  Add neighbors:
    (0+1, 0): (1,0) not visited, add (7+2=9, 1, 0)
    (0, 0+1): (0,1) not visited, add (1+4=5, 0, 1)
  Heap = [(5, 0, 1), (9, 1, 0)]
  Visited = {(0,0), (1,0), (0,1)}

Iteration 2:
  Pop (5, 0, 1): nums1[0]=1, nums2[1]=4
  Result = [[1,2], [1,4]]
  Add neighbors:
    (1, 1): not visited, add (7+4=11, 1, 1)
    (0, 2): not visited, add (1+6=7, 0, 2)
  Heap = [(7, 0, 2), (9, 1, 0), (11, 1, 1)]
  Visited = {(0,0), (1,0), (0,1), (1,1), (0,2)}

Iteration 3:
  Pop (7, 0, 2): nums1[0]=1, nums2[2]=6
  Result = [[1,2], [1,4], [1,6]]
  k=3 reached, stop

Return [[1,2], [1,4], [1,6]]
```

### Generation Pattern

**Brute force:**
```csharp
public IList<IList<int>> KSmallestPairs(int[] nums1, int[] nums2, int k) {
    var allPairs = new List<(int sum, int u, int v)>();
    
    foreach (int u in nums1) {
        foreach (int v in nums2) {
            allPairs.Add((u + v, u, v));
        }
    }
    
    allPairs.Sort((a, b) => a.sum.CompareTo(b.sum));
    
    var result = new List<IList<int>>();
    for (int i = 0; i < Math.Min(k, allPairs.Count); i++) {
        result.Add(new List<int> { allPairs[i].u, allPairs[i].v });
    }
    
    return result;
}
// Time: O(n*m log(n*m)), Space: O(n*m)
```

---

## Step 2: Brute Force

Generate all n×m pairs, sort by sum, return first k: O(n×m log(n×m)).

---

## Step 3: Optimization Ideas

### Key Insights
1. **Sorted property:** Smallest pairs form exploration frontier
2. **Min-heap exploration:** Similar to Dijkstra's or merge k sorted
3. **Only generate k pairs:** Don't need all n×m pairs
4. **Avoid duplicates:** Track visited indices

---

## Step 4: Optimal Solution

```csharp
public IList<IList<int>> KSmallestPairs(int[] nums1, int[] nums2, int k) {
    var result = new List<IList<int>>();
    if (nums1.Length == 0 || nums2.Length == 0 || k == 0) {
        return result;
    }
    
    // Min-heap: (sum, i, j)
    var minHeap = new PriorityQueue<(int sum, int i, int j), int>();
    var visited = new HashSet<(int, int)>();
    
    // Start with smallest pair
    minHeap.Enqueue((nums1[0] + nums2[0], 0, 0), nums1[0] + nums2[0]);
    visited.Add((0, 0));
    
    while (result.Count < k && minHeap.Count > 0) {
        var (sum, i, j) = minHeap.Dequeue();
        result.Add(new List<int> { nums1[i], nums2[j] });
        
        // Add next candidates
        if (i + 1 < nums1.Length && !visited.Contains((i + 1, j))) {
            int newSum = nums1[i + 1] + nums2[j];
            minHeap.Enqueue((newSum, i + 1, j), newSum);
            visited.Add((i + 1, j));
        }
        
        if (j + 1 < nums2.Length && !visited.Contains((i, j + 1))) {
            int newSum = nums1[i] + nums2[j + 1];
            minHeap.Enqueue((newSum, i, j + 1), newSum);
            visited.Add((i, j + 1));
        }
    }
    
    return result;
}
```

**Complexity:**
- Time: O(k log k)
  - K iterations
  - Each heap operation: O(log heap_size) ≤ O(log k)
- Space: O(k) - heap + visited set

**Optimization (avoid duplicate candidates):**
Only add (i+1, j) when j=0 to avoid duplicates:
```csharp
// Simplified: only expand rightward, initialize first column
minHeap.Enqueue((nums1[0] + nums2[0], 0, 0), nums1[0] + nums2[0]);

while (result.Count < k && minHeap.Count > 0) {
    var (sum, i, j) = minHeap.Dequeue();
    result.Add(new List<int> { nums1[i], nums2[j] });
    
    if (j == 0 && i + 1 < nums1.Length) {
        minHeap.Enqueue((nums1[i+1] + nums2[0], i+1, 0), 
                       nums1[i+1] + nums2[0]);
    }
    if (j + 1 < nums2.Length) {
        minHeap.Enqueue((nums1[i] + nums2[j+1], i, j+1), 
                       nums1[i] + nums2[j+1]);
    }
}
```

---

## Edge Cases

1. **k larger than pairs:** `k > n*m` → Return all pairs
2. **Single element arrays:** `[1], [2], k=1` → `[[1,2]]`
3. **k = 1:** Return `[[nums1[0], nums2[0]]]`
4. **Duplicate values:** Handle correctly in sorted order

---

## Related Problems

1. **Merge K Sorted Lists (LeetCode #23)** - Similar heap approach
2. **Kth Smallest Element in Sorted Matrix (LeetCode #378)** - 2D sorted space
3. **Find K Closest Elements (LeetCode #658)** - Top k by distance

---

## Tags

`#heap` `#priority-queue` `#top-k` `#two-arrays` `#sorted` `#frontier-search`
