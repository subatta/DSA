# Pattern: Top K Elements

## Pattern Description
**Top K Elements** is a pattern for efficiently finding the K largest, K smallest, or Kth element in a dataset using heaps (priority queues). It's one of the most common patterns in coding interviews.

### What is it?
- **Finding K elements** among N elements without fully sorting
- **Using heaps** to maintain K elements efficiently
- **Two approaches:**
  - **Min-heap of size K** for K largest (remove smallest when heap > K)
  - **Max-heap of size K** for K smallest (remove largest when heap > K)
  
### What abstract problem does it solve?
- Find K largest/smallest elements
- Find Kth largest/smallest element
- Maintain running K elements in stream
- Frequency-based K elements
- K closest elements to target

### Real-world / interview variants (simplest → harder):
1. **Kth Largest Element** - Find Kth largest in array
2. **K Largest Elements** - Return K largest elements
3. **Kth Largest in Stream** - Maintain Kth largest as elements arrive
4. **Top K Frequent Elements** - K most frequent items
5. **K Closest Points to Origin** - K nearest points
6. **K Closest Numbers in Sorted Array** - K closest to target
7. **Kth Smallest in Sorted Matrix** - 2D matrix navigation
8. **Find K Pairs with Smallest Sums** - Two array merging

---

## Canonical Skeleton (K Largest)

```csharp
int FindKthLargest(int[] nums, int k)
{
    // Min-heap of size K
    // Root is smallest of K largest → Kth largest
    var minHeap = new PriorityQueue<int, int>();
    
    foreach (int num in nums)
    {
        minHeap.Enqueue(num, num);
        
        // Keep only K largest
        if (minHeap.Count > k)
            minHeap.Dequeue();      // Remove smallest
    }
    
    return minHeap.Peek();          // Kth largest
}
```

---

<details>
<summary><b>Variant #1: Kth Largest Element in Array</b></summary>

## Variant #1: Kth Largest Element in Array

### Input/Output:
- **Input:** `nums = [3,2,1,5,6,4]`, `k = 2`
- **Output:** `5` (2nd largest element)
- **Input:** `nums = [3,2,3,1,2,4,5,5,6]`, `k = 4`
- **Output:** `4` (4th largest element)

### Full State Space:
Sort entire array, return element at position `n-k`:
```
[3,2,1,5,6,4] → sort → [1,2,3,4,5,6]
For k=2, return nums[6-2] = nums[4] = 5
Time: O(n log n)
Space: O(1) or O(n) depending on sort
```

```csharp
int KthLargestBruteForceSorting(int[] nums, int k)
{
    Array.Sort(nums);
    return nums[nums.Length - k];
}
```

### Expected / Pruned State Space:
Use min-heap of size K to track K largest:
```
Process [3,2,1,5,6,4], k=2:

Step 1: Add 3 → heap: [3]
Step 2: Add 2 → heap: [2,3]
Step 3: Add 1 → heap: [2,3], remove 1 (heap > k)
Step 4: Add 5 → heap: [3,5], remove 2
Step 5: Add 6 → heap: [5,6], remove 3
Step 6: Add 4 → heap: [5,6], don't add (4 < 5)

Final heap: [5,6] → root (min) = 5 = Kth largest
```

### Brute Force Canonical Skeleton:
```csharp
public int FindKthLargestBruteForce(int[] nums, int k)
{
    // Full sort
    Array.Sort(nums);
    
    // Return Kth from end
    return nums[nums.Length - k];
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `nums = [3,2,1,5,6,4]`, `k = 2`:

| Step | Action | Array State | Result |
|------|--------|-------------|--------|
| 0 | Initial | [3,2,1,5,6,4] | - |
| 1 | Sort | [1,2,3,4,5,6] | - |
| 2 | Access [n-k] | Index 6-2=4 | Return nums[4]=5 |

**Time:** O(n log n) for sorting  
**Space:** O(1) or O(n) depending on sort algorithm

### Optimized Solution (Min-Heap):
```csharp
public int FindKthLargest(int[] nums, int k)
{
    // Min-heap maintains K largest elements
    // Root = smallest of K largest = Kth largest
    var minHeap = new PriorityQueue<int, int>();
    
    foreach (int num in nums)
    {
        minHeap.Enqueue(num, num);
        
        // Keep heap size = K
        if (minHeap.Count > k)
        {
            minHeap.Dequeue();      // Remove smallest
        }
    }
    
    // Root is Kth largest
    return minHeap.Peek();
}
```

### Explanation of Pruning:
- **Key insight:** Don't need full sort, just K largest
- **Min-heap of size K:**
  - Maintains K largest seen so far
  - Root (minimum) is smallest of K largest
  - When element > root: Remove root, add element
  - When element ≤ root: Skip (not in K largest)
  
- **Why min-heap not max-heap?**
  - Max-heap would give largest, not Kth largest
  - Min-heap's root is boundary between K largest and rest
  
- **Time:** O(n log k) vs O(n log n)
- **Space:** O(k) vs O(1)

### Optimized Solution Code Walkthrough / Variable Trace:
For `nums = [3,2,1,5,6,4]`, `k = 2`:

| Iteration | num | Heap Before | Action | Heap After | Count | Result |
|-----------|-----|-------------|--------|------------|-------|--------|
| 0 | - | [] | - | [] | 0 | - |
| 1 | 3 | [] | Add 3 | [3] | 1 | Continue |
| 2 | 2 | [3] | Add 2 | [2,3] | 2 | Continue |
| 3 | 1 | [2,3] | Add 1 → [1,2,3], remove 1 | [2,3] | 2 | Continue |
| 4 | 5 | [2,3] | Add 5 → [2,3,5], remove 2 | [3,5] | 2 | Continue |
| 5 | 6 | [3,5] | Add 6 → [3,5,6], remove 3 | [5,6] | 2 | Continue |
| 6 | 4 | [5,6] | Add 4 → [4,5,6], remove 4 | [5,6] | 2 | Continue |
| End | - | [5,6] | Peek | root=5 | - | Return 5 |

**Heap visualization at end:**
```
   5 (root = Kth largest)
  /
 6
```

### Big-O Analysis:
- **Brute Force (Sorting):**
  - Time: O(n log n) → full sort
  - Space: O(1) or O(n) → in-place or copy
  
- **Optimized (Min-Heap):**
  - Time: O(n log k) → n insertions, each O(log k)
  - Space: O(k) → heap stores K elements
  
- **Better when:** k ≪ n (k much smaller than n)

</details>

---

<details>
<summary><b>Variant #2: K Largest Elements (Return All)</b></summary>

## Variant #2: K Largest Elements

### Input/Output:
- **Input:** `nums = [3,2,1,5,6,4]`, `k = 2`
- **Output:** `[5,6]` or `[6,5]` (order doesn't matter)
- **Input:** `nums = [3,2,3,1,2,4,5,5,6]`, `k = 4`
- **Output:** `[4,5,5,6]` (can include duplicates)

### Full State Space:
Sort and take last K elements:
```csharp
int[] KLargestBruteForce(int[] nums, int k)
{
    Array.Sort(nums);
    
    int[] result = new int[k];
    for (int i = 0; i < k; i++)
    {
        result[i] = nums[nums.Length - k + i];
    }
    
    return result;
}
```

### Optimized Solution:
```csharp
public int[] FindKLargest(int[] nums, int k)
{
    var minHeap = new PriorityQueue<int, int>();
    
    foreach (int num in nums)
    {
        minHeap.Enqueue(num, num);
        
        if (minHeap.Count > k)
            minHeap.Dequeue();
    }
    
    // Extract all K elements from heap
    int[] result = new int[k];
    for (int i = 0; i < k; i++)
    {
        result[i] = minHeap.Dequeue();
    }
    
    return result;
}
```

### Explanation of Pruning:
- Same min-heap approach
- Extract all K elements instead of just peeking
- Result may not be sorted (heap order ≠ sorted order)

### Big-O Analysis:
- **Time:** O(n log k) + O(k log k) = O(n log k)
- **Space:** O(k)

</details>

---

<details>
<summary><b>Variant #3: Kth Largest in Stream</b></summary>

## Variant #3: Kth Largest Element in Stream

### Input/Output:
```csharp
KthLargest kthLargest = new KthLargest(3, [4,5,8,2]);
kthLargest.Add(3);  // returns 4
kthLargest.Add(5);  // returns 5
kthLargest.Add(10); // returns 5
kthLargest.Add(9);  // returns 8
kthLargest.Add(4);  // returns 8
```

### Full State Space:
Store all elements, sort on each query:
```csharp
class KthLargestBruteForce
{
    private List<int> nums;
    private int k;
    
    public KthLargestBruteForce(int k, int[] initialNums)
    {
        this.k = k;
        this.nums = new List<int>(initialNums);
    }
    
    public int Add(int val)
    {
        nums.Add(val);
        nums.Sort();
        return nums[nums.Count - k];
    }
}
```

**Problem:** Sorting on every add is O(n log n)

### Optimized Solution (Min-Heap):
```csharp
public class KthLargest
{
    private PriorityQueue<int, int> minHeap;
    private int k;
    
    public KthLargest(int k, int[] nums)
    {
        this.k = k;
        this.minHeap = new PriorityQueue<int, int>();
        
        // Build initial heap
        foreach (int num in nums)
        {
            Add(num);
        }
    }
    
    public int Add(int val)
    {
        minHeap.Enqueue(val, val);
        
        // Maintain size K
        if (minHeap.Count > k)
        {
            minHeap.Dequeue();
        }
        
        // Root is Kth largest
        return minHeap.Peek();
    }
}
```

### Explanation of Pruning:
- **Streaming data:** Elements arrive one at a time
- **Min-heap maintains K largest:** Always ready to answer
- **O(log k) per add** vs O(n log n) with sorting
- **Persistent state:** Heap survives across calls

### Optimized Solution Code Walkthrough / Variable Trace:
Initialize with `k=3`, `nums=[4,5,8,2]`:

| Operation | Val | Heap Before | Action | Heap After | Return |
|-----------|-----|-------------|--------|------------|--------|
| Init | 4 | [] | Add 4 | [4] | - |
| Init | 5 | [4] | Add 5 | [4,5] | - |
| Init | 8 | [4,5] | Add 8 | [4,5,8] | - |
| Init | 2 | [4,5,8] | Add 2, remove 2 | [4,5,8] | - |
| Add | 3 | [4,5,8] | Add 3, remove 3 | [4,5,8] | 4 |
| Add | 5 | [4,5,8] | Add 5, remove 4 | [5,5,8] | 5 |
| Add | 10 | [5,5,8] | Add 10, remove 5 | [5,8,10] | 5 |
| Add | 9 | [5,8,10] | Add 9, remove 5 | [8,9,10] | 8 |
| Add | 4 | [8,9,10] | Add 4, remove 4 | [8,9,10] | 8 |

### Big-O Analysis:
- **Brute Force:**
  - Time per add: O(n log n) → sort every time
  - Space: O(n) → store all elements
  
- **Optimized:**
  - Time per add: O(log k) → heap operation
  - Space: O(k) → only K elements

</details>

---

<details>
<summary><b>Variant #4: Top K Frequent Elements</b></summary>

## Variant #4: Top K Frequent Elements

### Input/Output:
- **Input:** `nums = [1,1,1,2,2,3]`, `k = 2`
- **Output:** `[1,2]` (1 appears 3 times, 2 appears 2 times)
- **Input:** `nums = [1]`, `k = 1`
- **Output:** `[1]`

### Full State Space:
1. Count frequencies → O(n)
2. Sort by frequency → O(n log n)
3. Take top K → O(k)

```csharp
int[] TopKFrequentBruteForce(int[] nums, int k)
{
    // Step 1: Count frequencies
    var freq = new Dictionary<int, int>();
    foreach (int num in nums)
    {
        freq[num] = freq.GetValueOrDefault(num, 0) + 1;
    }
    
    // Step 2: Sort by frequency (descending)
    var sorted = freq.OrderByDescending(x => x.Value).ToList();
    
    // Step 3: Take top K
    int[] result = new int[k];
    for (int i = 0; i < k; i++)
    {
        result[i] = sorted[i].Key;
    }
    
    return result;
}
```

### Expected / Pruned State Space:
Use min-heap of size K based on frequency:
```
For [1,1,1,2,2,3], k=2:

Frequencies: {1:3, 2:2, 3:1}

Process frequencies:
- Add (1, freq=3) → heap: [(1,3)]
- Add (2, freq=2) → heap: [(2,2), (1,3)]
- Add (3, freq=1) → heap: [(2,2), (1,3), (3,1)], remove (3,1)

Final: [(2,2), (1,3)] → return [2,1] or [1,2]
```

### Optimized Solution (Min-Heap by Frequency):
```csharp
public int[] TopKFrequent(int[] nums, int k)
{
    // Step 1: Count frequencies
    var freq = new Dictionary<int, int>();
    foreach (int num in nums)
    {
        freq[num] = freq.GetValueOrDefault(num, 0) + 1;
    }
    
    // Step 2: Min-heap of size K (by frequency)
    var minHeap = new PriorityQueue<int, int>();
    
    foreach (var kvp in freq)
    {
        minHeap.Enqueue(kvp.Key, kvp.Value);      // Priority = frequency
        
        if (minHeap.Count > k)
        {
            minHeap.Dequeue();                     // Remove least frequent
        }
    }
    
    // Step 3: Extract K elements
    int[] result = new int[k];
    for (int i = 0; i < k; i++)
    {
        result[i] = minHeap.Dequeue();
    }
    
    return result;
}
```

### Explanation of Pruning:
- **Two-step process:**
  1. Count frequencies: O(n)
  2. Heap operations on M unique elements: O(M log k)
  
- **Min-heap by frequency:**
  - Element with lowest frequency at root
  - Maintain K most frequent
  - Remove least frequent when heap > K
  
- **Time:** O(n + M log k) where M = unique elements
  - Better than O(n log n) when k is small

### Optimized Solution Code Walkthrough / Variable Trace:
For `nums = [1,1,1,2,2,3]`, `k = 2`:

**Phase 1: Count Frequencies**
| num | freq map | Result |
|-----|----------|--------|
| 1 | {1:1} | - |
| 1 | {1:2} | - |
| 1 | {1:3} | - |
| 2 | {1:3, 2:1} | - |
| 2 | {1:3, 2:2} | - |
| 3 | {1:3, 2:2, 3:1} | - |

**Phase 2: Min-Heap (by frequency)**
| Element | Frequency | Heap Before | Action | Heap After (num:freq) |
|---------|-----------|-------------|--------|----------------------|
| 1 | 3 | [] | Add (1,3) | [(1,3)] |
| 2 | 2 | [(1,3)] | Add (2,2) | [(2,2), (1,3)] |
| 3 | 1 | [(2,2), (1,3)] | Add (3,1), remove (3,1) | [(2,2), (1,3)] |

**Phase 3: Extract**
- Dequeue: 2
- Dequeue: 1
- Result: [2,1]

### Big-O Analysis:
- **Brute Force:**
  - Time: O(n + n log n) = O(n log n)
  - Space: O(n)
  
- **Optimized:**
  - Time: O(n + M log k) where M ≤ n
  - Space: O(n + k)
  - Better when k ≪ M

</details>

---

<details>
<summary><b>Variant #5: K Closest Points to Origin</b></summary>

## Variant #5: K Closest Points to Origin

### Input/Output:
- **Input:** `points = [[1,3], [-2,2]]`, `k = 1`
- **Output:** `[[-2,2]]` (distance = √8 vs √10)
- **Input:** `points = [[3,3], [5,-1], [-2,4]]`, `k = 2`
- **Output:** `[[3,3], [-2,4]]`

### Full State Space:
Calculate all distances, sort, take K closest:
```csharp
int[][] KClosestBruteForce(int[][] points, int k)
{
    Array.Sort(points, (a, b) =>
    {
        int distA = a[0] * a[0] + a[1] * a[1];
        int distB = b[0] * b[0] + b[1] * b[1];
        return distA.CompareTo(distB);
    });
    
    return points.Take(k).ToArray();
}
```

### Optimized Solution (Max-Heap):
```csharp
public int[][] KClosest(int[][] points, int k)
{
    // Max-heap by distance (for K smallest distances)
    var maxHeap = new PriorityQueue<int[], int>(
        Comparer<int>.Create((a, b) => b.CompareTo(a))  // Max-heap
    );
    
    foreach (var point in points)
    {
        int dist = point[0] * point[0] + point[1] * point[1];
        maxHeap.Enqueue(point, dist);
        
        // Keep K closest (smallest distances)
        if (maxHeap.Count > k)
        {
            maxHeap.Dequeue();      // Remove farthest
        }
    }
    
    // Extract all K points
    int[][] result = new int[k][];
    for (int i = 0; i < k; i++)
    {
        result[i] = maxHeap.Dequeue();
    }
    
    return result;
}
```

### Explanation of Pruning:
- **K smallest distances:** Use **max-heap**
  - Root = largest of K smallest = boundary
  - Remove root when larger distance arrives
  
- **Distance calculation:** Don't need √ (x²+y² is enough)
- **Time:** O(n log k) vs O(n log n)

### Big-O Analysis:
- **Brute Force:** O(n log n) time, O(1) space
- **Optimized:** O(n log k) time, O(k) space

</details>

---

<details>
<summary><b>Variant #6: K Closest Numbers in Sorted Array</b></summary>

## Variant #6: K Closest Numbers to Target in Sorted Array

### Input/Output:
- **Input:** `arr = [1,2,3,4,5]`, `k = 4`, `target = 3`
- **Output:** `[1,2,3,4]` or `[2,3,4,5]` (both 4 elements equally close)
- **Input:** `arr = [1,2,3,4,5]`, `k = 4`, `target = -1`
- **Output:** `[1,2,3,4]`

### Full State Space:
Calculate all distances to target, sort, take K:
```csharp
List<int> KClosestBruteForce(int[] arr, int k, int target)
{
    var withDist = arr.Select(x => new { Num = x, Dist = Math.Abs(x - target) })
                      .OrderBy(x => x.Dist)
                      .ThenBy(x => x.Num)
                      .Take(k)
                      .Select(x => x.Num)
                      .ToList();
    
    return withDist;
}
```

### Optimized Solution (Binary Search + Two Pointers):
```csharp
public List<int> FindClosestElements(int[] arr, int k, int target)
{
    // Binary search to find closest starting point
    int left = 0;
    int right = arr.Length - k;
    
    // Find best window of size k
    while (left < right)
    {
        int mid = left + (right - left) / 2;
        
        // Compare distances of window boundaries
        if (target - arr[mid] > arr[mid + k] - target)
        {
            left = mid + 1;
        }
        else
        {
            right = mid;
        }
    }
    
    // Extract k elements starting at left
    List<int> result = new List<int>();
    for (int i = left; i < left + k; i++)
    {
        result.Add(arr[i]);
    }
    
    return result;
}
```

### Explanation of Pruning:
- **Sorted array property:** K closest form contiguous subarray
- **Binary search:** Find best starting position in O(log(n-k))
- **No heap needed:** Just sliding window logic
- **Time:** O(log(n-k) + k) vs O(n log n)

### Big-O Analysis:
- **Brute Force:** O(n log n) time
- **Optimized:** O(log(n-k) + k) time, O(1) space

</details>

---

<details>
<summary><b>Variant #7: Kth Smallest in Sorted Matrix</b></summary>

## Variant #7: Kth Smallest Element in Sorted Matrix

### Input/Output:
- **Input:** 
  ```
  matrix = [[1,  5,  9],
            [10, 11, 13],
            [12, 13, 15]]
  k = 8
  ```
- **Output:** `13` (8th smallest: 1,5,9,10,11,12,13,13)

### Full State Space:
Flatten matrix, sort, return Kth:
```csharp
int KthSmallestBruteForce(int[][] matrix, int k)
{
    var allElements = new List<int>();
    
    foreach (var row in matrix)
    {
        allElements.AddRange(row);
    }
    
    allElements.Sort();
    return allElements[k - 1];
}
```

**Time:** O(n² log n²) where n×n is matrix size

### Optimized Solution (Min-Heap):
```csharp
public int KthSmallest(int[][] matrix, int k)
{
    int n = matrix.Length;
    
    // Min-heap: (value, row, col)
    var minHeap = new PriorityQueue<(int val, int row, int col), int>();
    
    // Add first element of each row
    for (int r = 0; r < Math.Min(n, k); r++)
    {
        minHeap.Enqueue((matrix[r][0], r, 0), matrix[r][0]);
    }
    
    // Extract K-1 times
    int result = 0;
    for (int i = 0; i < k; i++)
    {
        var (val, row, col) = minHeap.Dequeue();
        result = val;
        
        // Add next element in same row
        if (col + 1 < n)
        {
            minHeap.Enqueue(
                (matrix[row][col + 1], row, col + 1),
                matrix[row][col + 1]
            );
        }
    }
    
    return result;
}
```

### Explanation of Pruning:
- **Row-wise sorted:** Each row is sorted
- **Start with first column:** All smallest candidates
- **Extract min, add next in row:** Maintain sorted order
- **K operations:** Only process K elements, not all n²
- **Time:** O(k log n) vs O(n² log n²)

### Big-O Analysis:
- **Brute Force:** O(n² log n²) time, O(n²) space
- **Optimized:** O(k log n) time, O(n) space
- **Much better when:** k ≪ n²

</details>

---

<details>
<summary><b>Variant #8: Find K Pairs with Smallest Sums</b></summary>

## Variant #8: Find K Pairs with Smallest Sums

### Input/Output:
- **Input:** `nums1 = [1,7,11]`, `nums2 = [2,4,6]`, `k = 3`
- **Output:** `[[1,2], [1,4], [1,6]]`
- **Explanation:** First 3 pairs with smallest sums:
  ```
  [1,2] → sum = 3
  [1,4] → sum = 5
  [1,6] → sum = 7
  ```

### Full State Space:
Generate all pairs, sort by sum, take K:
```csharp
List<int[]> KSmallestPairsBruteForce(int[] nums1, int[] nums2, int k)
{
    var allPairs = new List<(int sum, int[] pair)>();
    
    foreach (int n1 in nums1)
    {
        foreach (int n2 in nums2)
        {
            allPairs.Add((n1 + n2, new int[] { n1, n2 }));
        }
    }
    
    return allPairs.OrderBy(x => x.sum)
                   .Take(k)
                   .Select(x => x.pair)
                   .ToList();
}
```

**Time:** O(m×n log(m×n)) where m,n are array sizes

### Optimized Solution (Min-Heap):
```csharp
public List<int[]> KSmallestPairs(int[] nums1, int[] nums2, int k)
{
    var result = new List<int[]>();
    
    if (nums1.Length == 0 || nums2.Length == 0 || k == 0)
        return result;
    
    // Min-heap: (sum, index1, index2)
    var minHeap = new PriorityQueue<(int i1, int i2), int>();
    
    // Start with pairs (0, j) for all j
    for (int j = 0; j < Math.Min(nums2.Length, k); j++)
    {
        minHeap.Enqueue((0, j), nums1[0] + nums2[j]);
    }
    
    // Extract K pairs
    while (k > 0 && minHeap.Count > 0)
    {
        var (i1, i2) = minHeap.Dequeue();
        result.Add(new int[] { nums1[i1], nums2[i2] });
        
        // Add next pair with same i2, next i1
        if (i1 + 1 < nums1.Length)
        {
            minHeap.Enqueue(
                (i1 + 1, i2),
                nums1[i1 + 1] + nums2[i2]
            );
        }
        
        k--;
    }
    
    return result;
}
```

### Explanation of Pruning:
- **Both arrays sorted:** Smallest sums start with first elements
- **Strategy:**
  1. Start with (0,0), (0,1), ..., (0,k-1)
  2. Extract minimum
  3. Add next candidate in same column
  
- **Avoid duplicates:** Only expand in one direction (rows)
- **Time:** O(k log k) vs O(m×n log(m×n))

### Optimized Solution Code Walkthrough / Variable Trace:
For `nums1 = [1,7,11]`, `nums2 = [2,4,6]`, `k = 3`:

**Initialize heap with first row:**
| Pair | Sum | Heap |
|------|-----|------|
| (0,0) | 1+2=3 | [(0,0):3] |
| (0,1) | 1+4=5 | [(0,0):3, (0,1):5] |
| (0,2) | 1+6=7 | [(0,0):3, (0,1):5, (0,2):7] |

**Extract K=3 times:**
| Iter | Dequeue | Result | Add to Heap | Heap After |
|------|---------|--------|-------------|------------|
| 1 | (0,0):3 | [1,2] | (1,0):9 | [(0,1):5, (0,2):7, (1,0):9] |
| 2 | (0,1):5 | [1,4] | (1,1):11 | [(0,2):7, (1,0):9, (1,1):11] |
| 3 | (0,2):7 | [1,6] | (1,2):13 | [(1,0):9, (1,1):11, (1,2):13] |

**Final result:** `[[1,2], [1,4], [1,6]]`

### Big-O Analysis:
- **Brute Force:** O(m×n log(m×n)) time, O(m×n) space
- **Optimized:** O(k log k) time, O(k) space
- **Huge improvement when:** k ≪ m×n

</details>

---

# Key Takeaways

## Core Principle: Heap Size = K

The Top K pattern maintains a heap of exactly K elements:

| Goal | Heap Type | Why? | Root Represents |
|------|-----------|------|-----------------|
| **K largest** | Min-heap | Remove smallest when > K | Kth largest (smallest of K largest) |
| **K smallest** | Max-heap | Remove largest when > K | Kth smallest (largest of K smallest) |
| **K by frequency** | Min-heap by freq | Remove least frequent | Kth most frequent |
| **K closest** | Max-heap by distance | Remove farthest | Kth closest (farthest of K closest) |

**Key insight:** Heap root is the "boundary" element of the K elements.

---

## Pattern Recognition Guide

### When to Use Top K Elements:

✅ **Strong Signals:**
- "K largest", "K smallest", "Kth largest", "top K"
- "K most/least frequent"
- "K closest" (to target, origin, etc.)
- Don't need full sort, just K elements
- Streaming data with K constraint

✅ **Problem Characteristics:**
- Need top/bottom K out of N elements (K < N)
- Don't care about full ordering
- Can trade space (O(k)) for time
- Online algorithm (streaming)

❌ **Don't Use When:**
- Need all elements sorted
- K = N (just sort)
- Need median (use two heaps pattern)
- K is proportion (percentiles may need different approach)

---

## Common Variations

### 1. **Basic Kth Element** (Return one value)
```csharp
int FindKthLargest(int[] nums, int k)
{
    var minHeap = new PriorityQueue<int, int>();
    
    foreach (int num in nums)
    {
        minHeap.Enqueue(num, num);
        if (minHeap.Count > k)
            minHeap.Dequeue();
    }
    
    return minHeap.Peek();      // Kth largest
}
```

### 2. **K Elements** (Return array/list)
```csharp
int[] FindKLargest(int[] nums, int k)
{
    var minHeap = new PriorityQueue<int, int>();
    
    foreach (int num in nums)
    {
        minHeap.Enqueue(num, num);
        if (minHeap.Count > k)
            minHeap.Dequeue();
    }
    
    // Extract all K
    int[] result = new int[k];
    for (int i = 0; i < k; i++)
        result[i] = minHeap.Dequeue();
    
    return result;
}
```

### 3. **Streaming** (Class with Add method)
```csharp
class KthLargest
{
    private PriorityQueue<int, int> minHeap;
    private int k;
    
    public KthLargest(int k, int[] nums)
    {
        this.k = k;
        this.minHeap = new PriorityQueue<int, int>();
        foreach (int num in nums)
            Add(num);
    }
    
    public int Add(int val)
    {
        minHeap.Enqueue(val, val);
        if (minHeap.Count > k)
            minHeap.Dequeue();
        return minHeap.Peek();
    }
}
```

### 4. **Custom Comparison** (Frequency, distance, etc.)
```csharp
int[] TopKFrequent(int[] nums, int k)
{
    var freq = new Dictionary<int, int>();
    foreach (int num in nums)
        freq[num] = freq.GetValueOrDefault(num, 0) + 1;
    
    // Min-heap by frequency
    var minHeap = new PriorityQueue<int, int>();
    foreach (var kvp in freq)
    {
        minHeap.Enqueue(kvp.Key, kvp.Value);    // Priority = frequency
        if (minHeap.Count > k)
            minHeap.Dequeue();
    }
    
    return minHeap.UnorderedItems.Select(x => x.Element).ToArray();
}
```

---

## Comparison with Other Patterns

| Approach | Time | Space | When to Use |
|----------|------|-------|-------------|
| **Heap (Top K)** | O(n log k) | O(k) | K ≪ N, don't need full sort |
| **Full Sort** | O(n log n) | O(1) | Need all elements sorted, or K ≈ N |
| **QuickSelect** | O(n) avg | O(1) | Kth element only (not K elements), one-time query |
| **Counting Sort** | O(n+m) | O(m) | Limited range, integer elements |
| **Bucket Sort** | O(n+k) | O(n) | Uniform distribution |

**Rule of thumb:** When k ≪ n, heap is best. When k ≈ n, just sort.

---

## Problem-Solving Framework

```
1. Identify Top K pattern:
   ✓ Mentions "K largest/smallest/frequent/closest"?
   ✓ Don't need full ordering?
   ✓ K < N significantly?
   
2. Choose heap type:
   → K largest? Use min-heap
   → K smallest? Use max-heap
   → K by custom metric? Heap with custom comparator
   
3. Choose variant:
   → One-time query? Basic heap approach
   → Streaming? Class with persistent heap
   → Custom comparison? Map to metric first
   
4. Implement:
   → Maintain heap size = K
   → When heap.Count > k: Dequeue
   → Return Peek() for Kth, or extract all for K elements
   
5. Edge cases:
   → k = 0, k > n
   → Empty input
   → All elements equal
```

---

# Mental Checklist for Top K Elements

- [ ] **Problem asks for K largest/smallest?** → Top K pattern
- [ ] **K much smaller than N?** → Heap better than sort
- [ ] **Need Kth element only?** → Keep heap, peek at end
- [ ] **Need all K elements?** → Extract all from heap
- [ ] **Streaming data?** → Persistent heap in class
- [ ] **Custom comparison?** → Use PriorityQueue with custom priority
- [ ] **K largest?** → Use min-heap (counter-intuitive!)
- [ ] **K smallest?** → Use max-heap
- [ ] **Maintain heap size = K?** → Dequeue when Count > k
- [ ] **Extract elements?** → Dequeue k times for result

**If problem mentions "K largest/smallest/frequent/closest" → Top K Elements pattern applies.**
