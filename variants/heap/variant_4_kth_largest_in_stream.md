# Kth Largest Element in a Stream

**Difficulty:** Easy  
**LeetCode Problem:** #703  
**Tags:** `Heap`, `Design`, `Data Stream`, `Min-Heap`

---

## Problem Statement

Design a class to find the `k`th largest element in a stream. Note that it is the `k`th largest element in the sorted order, not the `k`th distinct element.

Implement `KthLargest` class:
- `KthLargest(int k, int[] nums)` Initializes the object with the integer `k` and the stream of integers `nums`.
- `int add(int val)` Appends the integer `val` to the stream and returns the element representing the `k`th largest element in the stream.

**Constraints:**
- `1 <= k <= 10^4`
- `0 <= nums.length <= 10^4`
- `-10^4 <= nums[i] <= 10^4`
- `-10^4 <= val <= 10^4`
- At most `10^4` calls will be made to `add`.
- It is guaranteed that there will be at least `k` elements in the array when you search for the `k`th element.

**Example:**
```
Input:
["KthLargest", "add", "add", "add", "add", "add"]
[[3, [4, 5, 8, 2]], [3], [5], [10], [9], [4]]

Output:
[null, 4, 5, 5, 8, 8]

Explanation:
KthLargest kthLargest = new KthLargest(3, [4, 5, 8, 2]);
kthLargest.add(3);   // return 4 (stream: [2,3,4,5,8], 3rd largest is 4)
kthLargest.add(5);   // return 5 (stream: [2,3,4,5,5,8], 3rd largest is 5)
kthLargest.add(10);  // return 5 (stream: [2,3,4,5,5,8,10], 3rd largest is 5)
kthLargest.add(9);   // return 8 (stream: [2,3,4,5,5,8,9,10], 3rd largest is 8)
kthLargest.add(4);   // return 8 (stream: [2,3,4,4,5,5,8,9,10], 3rd largest is 8)
```

---

## Step 1: State Space

### Problem Visualization

k = 3, initial stream: [4, 5, 8, 2]

```
Find 3rd largest (k=3):

Sorted stream: [2, 4, 5, 8]
                     ^
                3rd largest = 5

Add 3:
Sorted: [2, 3, 4, 5, 8]
               ^
          3rd largest = 4

Add 5:
Sorted: [2, 3, 4, 5, 5, 8]
                  ^
             3rd largest = 5

Add 10:
Sorted: [2, 3, 4, 5, 5, 8, 10]
                  ^
             3rd largest = 5

Add 9:
Sorted: [2, 3, 4, 5, 5, 8, 9, 10]
                        ^
                   3rd largest = 8
```

**Min-Heap of Size k:**

```
Maintain min-heap with k largest elements
Heap top = kth largest

Initial [4,5,8,2], k=3:
  Sort: [2,4,5,8]
  Keep top 3: [5,8,4] (as min-heap: [4,5,8])
  Heap top = 4? No, need to build correctly
  
  Actually: Keep largest k elements
  [4,5,8] with 4 at top (min of these k)
  No wait, we have 4 elements initially
  
Let me trace carefully:

Initialize with [4,5,8,2], k=3:
  Add all to min-heap of size k:
  Add 4: heap = [4]
  Add 5: heap = [4,5]
  Add 8: heap = [4,5,8]
  Add 2: heap size = 3, 2 < 4, don't add
  
  Wait, we want k LARGEST, so:
  Add 4: heap = [4]
  Add 5: heap = [4,5]
  Add 8: heap = [4,5,8]
  Add 2: 2 < 4 (heap top), skip
  
  Final heap: [4,5,8] (min-heap structure)
  Top = 4, but 3rd largest of [2,4,5,8] is 5!
  
Error in my thinking. Let me reconsider:

k=3 means we want 3rd largest.
Stream: [4,5,8,2]
Sorted: [2,4,5,8]
3rd largest = 5 (from the end: 8,5,4)

If we keep min-heap of size 3 with largest 3 elements:
  Heap: [5,8,?] structure would be min-heap
  Actually heap of [5,6,8] would have 5 at top
  
Correct approach:
  Heap maintains k largest elements
  Min-heap, so smallest of k largest is at top
  That's the kth largest!
  
Initialize [4,5,8,2], k=3:
  Process all elements:
  After 4: heap=[4], size=1
  After 5: heap=[4,5], size=2
  After 8: heap=[4,5,8], size=3
  After 2: 2 < 4, size=3, keep heap=[4,5,8]
  
  Wait, heap structure is min-heap, so:
  heap=[4,5,8] in array but conceptually:
        4
       / \
      5   8
  Top = 4
  
  But 3rd largest of [2,4,5,8] is 5, not 4!
  
I'm confusing myself. Let me look at correct example:

Actually, from problem: initially return null
Then first add(3) returns 4

After constructor with [4,5,8,2]:
  sorted: [2,4,5,8]
  3rd largest = 5
  But problem doesn't return from constructor
  
After add(3):
  sorted: [2,3,4,5,8]
  3rd largest = 4 ✓
  
So after constructor, we should have heap ready
Then add(3) should give us 4

Min-heap of size k=3 containing largest 3:
After [4,5,8,2]:
  sorted: [2,4,5,8]
  largest 3: [8,5,4]
  min-heap of these: 4 at top, heap=[4,5,8]
  
After add(3):
  sorted: [2,3,4,5,8]
  largest 3: [8,5,4]
  min-heap: 4 at top
  return 4 ✓

That matches! My confusion was about heap structure vs which elements.
```

### Core Question

**How can we efficiently maintain the kth largest element as new elements arrive?**

Keep a min-heap of size k containing the k largest elements. The top is the kth largest.

### Deriving from First Principles

**Observation 1: Kth largest = smallest of top k**
```
If we have the k largest elements, the kth largest is the smallest among them.
Example: k=3, elements=[1,2,3,4,5,6,7]
  Top 3: [7,6,5]
  3rd largest = 5 = min(7,6,5)
```

**Observation 2: Min-heap for k largest**
```
Maintain min-heap of size k
Contains k largest elements seen so far
Top of heap = smallest of k largest = kth largest
```

**Observation 3: Adding new element**
```
If heap.size < k:
  Add element directly
  
If val > heap.top():
  Remove heap.top() (it's no longer in top k)
  Add val
  
If val <= heap.top():
  Ignore (not in top k)
  
Return heap.top() (kth largest)
```

**Observation 4: Complexity**
```
Constructor: Build heap with k elements: O(k log k) or O(n log k)
Add: Compare + potential heap operation: O(log k)
Space: O(k) for heap
```

**Observation 5: Why min-heap, not max-heap?**
```
Min-heap of k largest: Top is kth largest ✓
Max-heap of k smallest: Top is kth smallest ✗

We need quick access to smallest of largest k elements.
Min-heap provides this at top in O(1).
```

**Formula:**
```
Constructor(k, nums):
  heap = min-heap
  for num in nums:
    heap.push(num)
    if heap.size > k:
      heap.pop()

add(val):
  heap.push(val)
  if heap.size > k:
    heap.pop()
  return heap.top()
```

### State Space Structure

**Type:** Min-heap maintaining k largest elements.

**Structure:**
- Min-heap of size ≤ k
- Contains k largest elements from stream
- Top = kth largest (smallest of top k)

**Cardinality:**
- Constructor: O(n log k) where n = initial array size
- Add: O(log k) per operation
- Space: O(k)

**Key Property:** Min-heap top gives kth largest in O(1) access.

### Example Computation

k = 3, nums = [4, 5, 8, 2]

```
Constructor:
  Add 4: heap = [4], size = 1
  Add 5: heap = [4,5], size = 2 (min-heap: 4 at root)
  Add 8: heap = [4,5,8], size = 3
  Add 2: heap = [4,5,8], size = 3
    2 < 4 (top), don't add (or add then pop top)
  
  Heap after constructor: [4,5,8]
  Top = 4... but we don't return yet

add(3):
  Add 3: heap = [3,4,5,8], size = 4
  Size > k, pop top: heap = [4,5,8], size = 3
  Return top = 4 ✓

add(5):
  Add 5: heap = [4,5,5,8], size = 4
  Pop top: heap = [5,5,8], size = 3
  Return top = 5 ✓

add(10):
  Add 10: heap = [5,5,8,10], size = 4
  Pop top: heap = [5,8,10], size = 3
  Return top = 5 ✓

add(9):
  Add 9: heap = [5,8,9,10], size = 4
  Pop top: heap = [8,9,10], size = 3
  Return top = 8 ✓

add(4):
  Add 4: heap = [4,8,9,10], size = 4
  Pop top: heap = [8,9,10], size = 3
  Return top = 8 ✓
```

### Generation Pattern

**Brute force:**
```csharp
// Maintain sorted list of all elements
// Add: insert in sorted position: O(n)
// Kth largest: access index [n-k]: O(1)
```

---

## Step 2: Brute Force

```csharp
public class KthLargest {
    private List<int> nums;
    private int k;
    
    public KthLargest(int k, int[] nums) {
        this.k = k;
        this.nums = new List<int>(nums);
        this.nums.Sort();
    }
    
    public int Add(int val) {
        // Binary search for insertion position
        int pos = nums.BinarySearch(val);
        if (pos < 0) pos = ~pos;
        nums.Insert(pos, val);  // O(n) to shift elements
        
        // Return kth largest (n-k index from start)
        return nums[nums.Count - k];
    }
}
```

**Complexity:**
- Constructor: O(n log n)
- Add: O(n) - insertion with shift
- Space: O(n) - store all elements

**Problem:** Add is O(n), too slow for many operations.

---

## Step 3: Optimization Ideas

### Key Insights
1. **Only need k elements:** Don't store all
2. **Min-heap of size k:** Contains k largest
3. **Heap top = kth largest:** Direct access
4. **Add is O(log k):** Much better than O(n)

---

## Step 4: Optimal Solution

```csharp
public class KthLargest {
    private PriorityQueue<int, int> minHeap;
    private int k;
    
    public KthLargest(int k, int[] nums) {
        this.k = k;
        this.minHeap = new PriorityQueue<int, int>();
        
        // Build heap with k largest elements
        foreach (int num in nums) {
            minHeap.Enqueue(num, num);
            if (minHeap.Count > k) {
                minHeap.Dequeue();
            }
        }
    }
    
    public int Add(int val) {
        // Add new value
        minHeap.Enqueue(val, val);
        
        // Maintain heap size k
        if (minHeap.Count > k) {
            minHeap.Dequeue();
        }
        
        // Top of min-heap is kth largest
        return minHeap.Peek();
    }
}
```

**Complexity:**
- Constructor: O(n log k) where n = initial array length
- Add: O(log k) - heap insert + potential removal
- Space: O(k) - heap stores only k elements

**Optimization in Constructor:**
Can build heap more efficiently:
```csharp
public KthLargest(int k, int[] nums) {
    this.k = k;
    this.minHeap = new PriorityQueue<int, int>();
    
    // Add first k elements
    int i = 0;
    while (i < nums.Length && i < k) {
        minHeap.Enqueue(nums[i], nums[i]);
        i++;
    }
    
    // For remaining, only add if larger than top
    while (i < nums.Length) {
        if (nums[i] > minHeap.Peek()) {
            minHeap.Dequeue();
            minHeap.Enqueue(nums[i], nums[i]);
        }
        i++;
    }
}
```

---

## Edge Cases

1. **k = 1:** Maintain max element (single element heap)
2. **Initial array empty:** `nums = []`, heap empty initially
3. **All elements same:** Kth largest = that value
4. **Negative numbers:** Works correctly
5. **k = array length:** Min element is kth largest

---

## Comparison with Top K Problems

**Kth Largest Element in Array (LeetCode #215):**
- One-time query on static array
- Options: quickselect O(n), heap O(n log k)

**Kth Largest in Stream (This problem):**
- Continuous queries on growing stream
- Must use data structure for efficiency
- Min-heap optimal: O(log k) per add

---

## Visualization

```
k = 3, stream: [4,5,8,2] then add [3,5,10]

After initialization:
  All elements: [2,4,5,8]
  Top 3 largest: [8,5,4]
  Min-heap: [4,5,8]
          4
         / \
        5   8
  Top = 4

After add(3):
  All: [2,3,4,5,8]
  Top 3: [8,5,4]
  Heap: [4,5,8]
  Return: 4

After add(5):
  All: [2,3,4,5,5,8]
  Top 3: [8,5,5]
  Heap: [5,5,8]
          5
         / \
        5   8
  Return: 5

After add(10):
  All: [2,3,4,5,5,8,10]
  Top 3: [10,8,5]
  Heap: [5,8,10]
          5
         / \
        8  10
  Return: 5
```

---

## Related Problems

1. **Kth Largest Element in Array (LeetCode #215)** - Static version
2. **Find Median from Data Stream (LeetCode #295)** - Two heaps
3. **Top K Frequent Elements (LeetCode #347)** - Frequency + heap
4. **Sliding Window Median (LeetCode #480)** - Moving window

---

## Pattern Recognition

**Problem Asks For:**
- Maintain kth largest dynamically
- Data stream with additions
- Efficient queries

**This Suggests:**
- Data structure for online queries
- Heap for top-k elements
- Min-heap for kth largest
- → **Min-Heap of Size K pattern**

**Key Indicators:**
- "In a stream" or "online"
- "Kth largest/smallest"
- Multiple add/query operations

---

## Tags

`#heap` `#min-heap` `#data-stream` `#design` `#top-k` `#kth-largest`
