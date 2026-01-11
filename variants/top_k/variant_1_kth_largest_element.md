# Kth Largest Element in an Array

**Difficulty:** Medium  
**LeetCode Problem:** #215  
**Tags:** `Heap`, `Priority Queue`, `Quickselect`, `Top K`

---

## Problem Statement

Given an integer array `nums` and an integer `k`, return the `k-th` largest element in the array.

Note that it is the `k-th` largest element in the sorted order, not the `k-th` distinct element.

Can you solve it without sorting?

**Constraints:**
- `1 <= k <= nums.length <= 10^5`
- `-10^4 <= nums[i] <= 10^4`

**Example 1:**
```
Input: nums = [3,2,1,5,6,4], k = 2
Output: 5
Explanation: Sorted: [6,5,4,3,2,1], 2nd largest is 5
```

**Example 2:**
```
Input: nums = [3,2,3,1,2,4,5,5,6], k = 4
Output: 4
Explanation: Sorted: [6,5,5,4,3,3,2,2,1], 4th largest is 4
```

---

## Step 1: State Space

### Problem Visualization

For `nums = [3,2,1,5,6,4]`, `k = 2`:

```
Original: [3, 2, 1, 5, 6, 4]
Sorted:   [6, 5, 4, 3, 2, 1]
              ↑
           2nd largest

Min-heap approach (maintain k largest):
Start: []
Add 3: [3]
Add 2: [2, 3]
Add 1: [1, 2, 3] (size > k, remove min)
       [2, 3]
Add 5: [2, 3, 5] (size > k, remove min)
       [3, 5]
Add 6: [3, 5, 6] (size > k, remove min)
       [5, 6]
Add 4: [4, 5, 6] (size > k, remove min)
       [5, 6]

Heap top (minimum of k largest): 5
```

Heap structure (min-heap of size k=2):
```
     5  ← top (kth largest)
    /
   6
```

### Core Question

**How can we efficiently find the k-th largest element without fully sorting the array?**

We need to track the top k largest elements. A min-heap of size k keeps the k largest elements seen so far, with the smallest of these k elements at the top (which is exactly the k-th largest overall).

### Deriving from First Principles

**Observation 1: K-th largest definition**
If we sort in descending order, the k-th largest is at index k-1.
But sorting is O(n log n)—can we do better?

**Observation 2: Only need top k elements**
We don't need to sort all n elements, just identify the top k.
Maintain a collection of k largest elements seen so far.

**Observation 3: Min-heap tracks k largest**
Use a min-heap of size k:
- Heap contains k largest elements
- Heap top is the smallest of these k elements (k-th largest overall)
- When heap size exceeds k, remove the minimum

**Observation 4: Why min-heap, not max-heap?**
- Min-heap of size k: Top is k-th largest (smallest of the k largest)
- Max-heap would give 1st largest, not k-th
- We want to efficiently remove the smallest of the k largest

**Observation 5: Algorithm flow**
```
For each element:
  Add to heap
  If heap size > k:
    Remove minimum (pop from min-heap)
    
After processing all elements:
  Heap top = k-th largest
```

**Formula:**
```
minHeap = new MinHeap()
for each num in nums:
  minHeap.add(num)
  if minHeap.size() > k:
    minHeap.poll()  // Remove minimum
return minHeap.peek()  // K-th largest
```

### State Space Structure

**Type:** Maintaining top k elements using min-heap.

**Structure:**
- Min-heap of size k
- Heap property: Parent ≤ children
- Top element: K-th largest (smallest in heap)

**Cardinality:**
- Process n elements
- Each operation: O(log k)
- Total: O(n log k)

**Key Property:** Min-heap of size k naturally maintains the boundary between top k and remaining elements.

### Example Computation

Input: `nums = [3,2,1,5,6,4]`, `k = 2`

```
Heap = [] (min-heap, max size = 2)

Process 3:
  Add 3: Heap = [3]
  Size = 1 ≤ 2, don't remove
  
Process 2:
  Add 2: Heap = [2, 3]
  Size = 2 ≤ 2, don't remove

Process 1:
  Add 1: Heap = [1, 2, 3]
  Size = 3 > 2, remove min (1)
  Heap = [2, 3]

Process 5:
  Add 5: Heap = [2, 3, 5]
  Size = 3 > 2, remove min (2)
  Heap = [3, 5]

Process 6:
  Add 6: Heap = [3, 5, 6]
  Size = 3 > 2, remove min (3)
  Heap = [5, 6]

Process 4:
  Add 4: Heap = [4, 5, 6]
  Size = 3 > 2, remove min (4)
  Heap = [5, 6]

Result: heap.peek() = 5
```

### Generation Pattern

**Brute force (sort):**
```csharp
public int FindKthLargest(int[] nums, int k) {
    Array.Sort(nums);
    return nums[nums.Length - k];
}
// Time: O(n log n), Space: O(1) or O(n) depending on sort
```

**Optimal (min-heap):**
```csharp
public int FindKthLargest(int[] nums, int k) {
    var minHeap = new PriorityQueue<int, int>();
    
    foreach (int num in nums) {
        minHeap.Enqueue(num, num);
        if (minHeap.Count > k) {
            minHeap.Dequeue();
        }
    }
    
    return minHeap.Peek();
}
// Time: O(n log k), Space: O(k)
```

---

## Step 2: Brute Force

```csharp
public int FindKthLargest(int[] nums, int k) {
    Array.Sort(nums);
    // After sorting, k-th largest is at index (n - k)
    return nums[nums.Length - k];
}
```

**Complexity:**
- Time: O(n log n) - sorting
- Space: O(1) or O(n) depending on sort implementation

---

## Step 3: Optimization Ideas

### Key Insights
1. **Don't need full sort:** Only need k-th largest
2. **Min-heap of size k:** Tracks k largest elements efficiently
3. **Better complexity:** O(n log k) vs O(n log n)

### Alternative Approaches
1. **Min-heap:** O(n log k), best for small k
2. **Max-heap:** Build max-heap, pop k times: O(n + k log n)
3. **Quickselect:** O(n) average, O(n²) worst case
4. **Counting sort:** O(n + range) if range is small

---

## Step 4: Optimal Solution

### Approach 1: Min-Heap (Best for small k)

```csharp
public int FindKthLargest(int[] nums, int k) {
    // Use PriorityQueue as min-heap (default in .NET)
    var minHeap = new PriorityQueue<int, int>();
    
    foreach (int num in nums) {
        minHeap.Enqueue(num, num);
        
        // Maintain heap size = k
        if (minHeap.Count > k) {
            minHeap.Dequeue();  // Remove smallest
        }
    }
    
    // Top of min-heap is k-th largest
    return minHeap.Peek();
}
```

**Complexity:**
- Time: O(n log k)
  - Process n elements
  - Each add/remove: O(log k)
- Space: O(k) - heap size

### Approach 2: Max-Heap

```csharp
public int FindKthLargest(int[] nums, int k) {
    // Create max-heap by negating values
    var maxHeap = new PriorityQueue<int, int>();
    
    foreach (int num in nums) {
        maxHeap.Enqueue(num, -num);  // Negate for max-heap
    }
    
    // Pop k-1 times
    for (int i = 0; i < k - 1; i++) {
        maxHeap.Dequeue();
    }
    
    return maxHeap.Peek();
}
```

**Complexity:**
- Time: O(n + k log n) - heapify + k pops
- Space: O(n)

### Approach 3: Quickselect (Advanced)

```csharp
public int FindKthLargest(int[] nums, int k) {
    // Convert to 0-indexed position in ascending order
    return Quickselect(nums, 0, nums.Length - 1, nums.Length - k);
}

private int Quickselect(int[] nums, int left, int right, int k) {
    if (left == right) return nums[left];
    
    int pivotIndex = Partition(nums, left, right);
    
    if (k == pivotIndex) {
        return nums[k];
    } else if (k < pivotIndex) {
        return Quickselect(nums, left, pivotIndex - 1, k);
    } else {
        return Quickselect(nums, pivotIndex + 1, right, k);
    }
}

private int Partition(int[] nums, int left, int right) {
    int pivot = nums[right];
    int i = left;
    
    for (int j = left; j < right; j++) {
        if (nums[j] <= pivot) {
            Swap(nums, i, j);
            i++;
        }
    }
    
    Swap(nums, i, right);
    return i;
}

private void Swap(int[] nums, int i, int j) {
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}
```

**Complexity:**
- Time: O(n) average, O(n²) worst case
- Space: O(1)

---

## Comparison of Approaches

| Approach | Time | Space | Best When |
|----------|------|-------|-----------|
| Min-heap | O(n log k) | O(k) | Small k |
| Max-heap | O(n + k log n) | O(n) | Large k (close to n) |
| Quickselect | O(n) avg | O(1) | General case, if randomized |
| Sort | O(n log n) | O(1)-O(n) | Simplicity, need sorted array |

---

## Edge Cases

1. **k = 1 (largest):** Return max element
2. **k = n (smallest):** Return min element
3. **Single element:** `[1], k=1` → `1`
4. **All equal:** `[5,5,5,5], k=2` → `5`
5. **Duplicates:** Handle correctly (not distinct elements)

---

## Related Problems

1. **Kth Smallest Element (LeetCode #378)** - Similar pattern
2. **Top K Frequent Elements (LeetCode #347)** - Heap with frequency
3. **Find Median from Data Stream (LeetCode #295)** - Two heaps
4. **K Closest Points to Origin (LeetCode #973)** - Min-heap by distance

---

## Pattern Recognition

**Problem Asks For:**
- K-th largest/smallest element
- Top K elements
- K most frequent

**This Suggests:**
- Heap of size k
- Min-heap for k largest (top is k-th largest)
- Max-heap for k smallest (top is k-th smallest)
- → **Top K Elements pattern**

**Key Indicators:**
- Need k-th element or top k elements
- Don't need full sorted order
- k << n (heap is efficient)

---

## Tags

`#heap` `#priority-queue` `#top-k` `#kth-largest` `#min-heap` `#quickselect`
