# Pattern: Heap / Priority Queue

## Pattern Description
**The Heap / Priority Queue pattern** is used when a problem requires **repeated access to the smallest or largest element** while elements are dynamically added or removed.

**What is it?**
- A heap is a **partially ordered binary tree** (commonly implemented as an array) that allows:
  - Fast access to min or max element (O(1))
  - Logarithmic insertion and removal (O(log n))
  - Efficient maintenance of top-K elements

**What abstract problem does it solve?**
- Keep track of top-K elements dynamically
- Continuously extract best/worst candidate
- Efficiently manage a dynamic ordered set
- Stream processing with ordering constraints

**Real-world / interview variants (simplest → harder):**
1. **Merge K Sorted Lists** - Multi-way merge with heap
2. **Find Median from Data Stream** - Two-heap technique (min-heap + max-heap)
3. **Task Scheduler** - Greedy scheduling with heap and cooldown
4. **Reorganize String** - Greedy character selection with heap
5. **Sliding Window Median** - Two heaps with lazy deletion
6. **Meeting Rooms II** - Track concurrent meetings with heap
7. **Process Tasks Using Servers** - Dynamic server allocation
8. **Smallest Range Covering K Lists** - Multi-list min-max tracking

**Note:** For Top K Elements problems (K Largest, Kth Largest, Top K Frequent, K Closest, etc.), see the dedicated **[Top K Elements Pattern](top_k_elements_pattern.md)** documentation.

---

## Heap Types

### Min Heap
- Root = smallest element
- Used for:
  - K largest
  - Merge sorted streams
  - Scheduling problems

### Max Heap
- Root = largest element
- Used for:
  - K smallest
  - Greedy selections

---

## Canonical Heap Skeleton

### Min Heap (C#)

```csharp
PriorityQueue<int, int> pq = new();

pq.Enqueue(value, value);

int smallest = pq.Dequeue();
```

### Max Heap (C#)

```csharp
PriorityQueue<int, int> pq = new();

pq.Enqueue(value, -value);

int largest = pq.Dequeue();
```

---

# Variant 1: Find K Largest Elements

## Input / Output
- Input: `[3,2,1,5,6,4]`, `k = 2`
- Output: `[5,6]`

## Full State Space
Sort all elements → O(n log n)

## Expected / Pruned State Space
Maintain only K largest elements in a min heap.

## Brute Force Approach

```csharp
Array.Sort(nums);
return nums[^k..];
```

## Optimized Heap Approach

```csharp
PriorityQueue<int, int> pq = new();

foreach (var n in nums)
{
    pq.Enqueue(n, n);
    if (pq.Count > k)
        pq.Dequeue();
}
```

## Variable Trace

| Step | Heap Contents |
|----|---------------|
| 3 | [3] |
| 2 | [2,3] |
| 1 | [2,3] |
| 5 | [3,5] |
| 6 | [5,6] |

## Big-O
- Time: O(n log k)
- Space: O(k)


<details>
<summary><b>Variant #2: Kth Largest Element in a Stream</b></summary>

## Variant #2: Kth Largest Element in a Stream

### Input/Output:
- Input: Stream of integers, `k = 3`
- Operations: `add(4)`, `add(5)`, `add(8)`, `add(2)`
- Output: `[-∞, -∞, 4, 4]` (first two return -∞ or null, then 4, 4)

### Full State Space:
After each add, sort all seen elements and return kth largest.
```
add(4): [4] → kth=null (only 1 element)
add(5): [4,5] → kth=null (only 2 elements)
add(8): [4,5,8] → sorted [4,5,8] → 3rd largest = 4
add(2): [2,4,5,8] → sorted [2,4,5,8] → 3rd largest = 4
Total: O(n log n) per add operation
```

### Expected/Pruned State Space:
Maintain min heap of size k containing k largest elements.
```
Heap root = kth largest element
Only need to track k elements, not all n
```

### State Space Leading to Output:
Min heap of size k where root is always the kth largest.

### Brute Force Canonical Skeleton:
```csharp
class KthLargestBruteForce
{
    private List<int> nums = new();
    private int k;
    
    public KthLargestBruteForce(int k, int[] initialNums)
    {
        this.k = k;
        foreach (int num in initialNums)
        {
            nums.Add(num);
        }
    }
    
    public int Add(int val)
    {
        nums.Add(val);
        
        if (nums.Count < k)
        {
            return int.MinValue; // Or return -1/null
        }
        
        // Sort and return kth largest
        nums.Sort();
        return nums[nums.Count - k];
    }
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `k = 3`, operations: `add(4)`, `add(5)`, `add(8)`, `add(2)`:

| operation | nums before | nums after | sorted | kth largest (3rd) | result |
|-----------|-------------|------------|--------|-------------------|--------|
| init | [] | [] | [] | - | - |
| add(4) | [] | [4] | [4] | none (size<3) | -∞ |
| add(5) | [4] | [4,5] | [4,5] | none (size<3) | -∞ |
| add(8) | [4,5] | [4,5,8] | [4,5,8] | nums[0]=4 | 4 |
| add(2) | [4,5,8] | [4,5,8,2] | [2,4,5,8] | nums[1]=4 | 4 |

### Optimized Solution from Canonical Skeleton:
```csharp
class KthLargest
{
    private PriorityQueue<int, int> pq = new();
    private int k;
    
    public KthLargest(int k, int[] nums)
    {
        this.k = k;
        
        foreach (int num in nums)
        {
            pq.Enqueue(num, num);
            if (pq.Count > k)
            {
                pq.Dequeue();
            }
        }
    }
    
    public int Add(int val)
    {
        pq.Enqueue(val, val);
        
        if (pq.Count > k)
        {
            pq.Dequeue();
        }
        
        return pq.Count < k ? int.MinValue : pq.Peek();
    }
}
```

### Explanation of Pruning:
- **Maintain exactly k elements:** Always remove smallest when size > k
- **Kth largest = heap root:** Min heap property ensures root is kth largest
- **Key insight:** Don't need elements smaller than kth largest
- **Why it works:** If we have k largest elements, the smallest of them is kth largest overall

### Optimized Solution Code Walkthrough / Variable Trace:
For `k = 3`, operations: `add(4)`, `add(5)`, `add(8)`, `add(2)`:

| operation | heap before | enqueue | dequeue | heap after | pq.Peek() | result |
|-----------|-------------|---------|---------|------------|-----------|--------|
| init | [] | - | - | [] | - | - |
| add(4) | [] | 4 | none | [4] | 4 | -∞ (size<3) |
| add(5) | [4] | 5 | none | [4,5] | 4 | -∞ (size<3) |
| add(8) | [4,5] | 8 | none | [4,5,8] | 4 | 4 |
| add(2) | [4,5,8] | 2 | 2 | [4,5,8] | 4 | 4 |

### Big-O Analysis:
- **Brute Force:** O(n log n) per add → sorting each time
- **Optimized:** O(log k) per add → heap insert/delete
- **Space Complexity:** 
  - Brute Force: O(n) → store all elements
  - Optimized: O(k) → heap of size k
- **Initialization:** O(n log k) for processing n initial elements

</details>


<details>
<summary><b>Variant #3: Merge K Sorted Lists</b></summary>

## Variant #3: Merge K Sorted Lists

### Input/Output:
- Input: `lists = [[1,4,5],[1,3,4],[2,6]]`
- Output: `[1,1,2,3,4,4,5,6]`

### Full State Space:
Flatten all lists and sort.
```
Flatten: [1,4,5,1,3,4,2,6]
Sort: [1,1,2,3,4,4,5,6]
Time: O(n log n) where n = total elements
```

### Expected/Pruned State Space:
Only compare k current heads at any time.
```
At each step, pick minimum among k list heads
Advance that list's pointer
Total comparisons: n, each in O(log k)
```

### State Space Leading to Output:
Min heap tracks current smallest element from each list.

### Brute Force Canonical Skeleton:
```csharp
List<int> MergeKListsBruteForce(List<List<int>> lists)
{
    List<int> result = new();
    
    // Flatten all lists
    foreach (var list in lists)
    {
        foreach (int val in list)
        {
            result.Add(val);
        }
    }
    
    // Sort
    result.Sort();
    
    return result;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `lists = [[1,4,5],[1,3,4],[2,6]]`:

| Step | Action | result |
|------|--------|--------|
| 1 | Flatten list 0 | [1,4,5] |
| 2 | Flatten list 1 | [1,4,5,1,3,4] |
| 3 | Flatten list 2 | [1,4,5,1,3,4,2,6] |
| 4 | Sort | [1,1,2,3,4,4,5,6] |

### Optimized Solution from Canonical Skeleton:
```csharp
List<int> MergeKLists(List<List<int>> lists)
{
    List<int> result = new();
    
    // Min heap: (value, listIndex, elementIndex)
    PriorityQueue<(int val, int listIdx, int elemIdx), int> pq = new();
    
    // Initialize heap with first element from each list
    for (int i = 0; i < lists.Count; i++)
    {
        if (lists[i].Count > 0)
        {
            pq.Enqueue((lists[i][0], i, 0), lists[i][0]);
        }
    }
    
    while (pq.Count > 0)
    {
        var (val, listIdx, elemIdx) = pq.Dequeue();
        result.Add(val);
        
        // Add next element from same list
        if (elemIdx + 1 < lists[listIdx].Count)
        {
            int nextVal = lists[listIdx][elemIdx + 1];
            pq.Enqueue((nextVal, listIdx, elemIdx + 1), nextVal);
        }
    }
    
    return result;
}
```

### Explanation of Pruning:
- **Key insight:** Only need to compare k elements at a time (one from each list)
- **Min heap:** Efficiently finds minimum among k candidates in O(log k)
- **Invariant:** Heap contains at most k elements (one per list)
- **Why it works:** Since lists are sorted, we only need the current head of each list
- **Space savings:** O(k) heap vs O(n) for flattening

### Optimized Solution Code Walkthrough / Variable Trace:
For `lists = [[1,4,5],[1,3,4],[2,6]]`:

| step | heap | dequeue | result | add to heap | heap after |
|------|------|---------|--------|-------------|------------|
| init | [] | - | [] | (1,0,0),(1,1,0),(2,2,0) | [(1,0,0),(1,1,0),(2,2,0)] |
| 1 | [(1,0,0),(1,1,0),(2,2,0)] | (1,0,0) | [1] | (4,0,1) | [(1,1,0),(2,2,0),(4,0,1)] |
| 2 | [(1,1,0),(2,2,0),(4,0,1)] | (1,1,0) | [1,1] | (3,1,1) | [(2,2,0),(3,1,1),(4,0,1)] |
| 3 | [(2,2,0),(3,1,1),(4,0,1)] | (2,2,0) | [1,1,2] | (6,2,1) | [(3,1,1),(4,0,1),(6,2,1)] |
| 4 | [(3,1,1),(4,0,1),(6,2,1)] | (3,1,1) | [1,1,2,3] | (4,1,2) | [(4,0,1),(4,1,2),(6,2,1)] |
| 5 | [(4,0,1),(4,1,2),(6,2,1)] | (4,0,1) | [1,1,2,3,4] | (5,0,2) | [(4,1,2),(5,0,2),(6,2,1)] |
| 6 | [(4,1,2),(5,0,2),(6,2,1)] | (4,1,2) | [1,1,2,3,4,4] | none (list 1 done) | [(5,0,2),(6,2,1)] |
| 7 | [(5,0,2),(6,2,1)] | (5,0,2) | [1,1,2,3,4,4,5] | none (list 0 done) | [(6,2,1)] |
| 8 | [(6,2,1)] | (6,2,1) | [1,1,2,3,4,4,5,6] | none (list 2 done) | [] |

### Big-O Analysis:
- **Brute Force:** O(n log n) → sorting n total elements
- **Optimized:** O(n log k) → n elements, each operation O(log k)
- **Space Complexity:**
  - Brute Force: O(n) → store all elements before sorting
  - Optimized: O(k) → heap size
- **When k << n:** Heap approach is significantly better

</details>


<details>
<summary><b>Variant #4: Top K Frequent Elements</b></summary>

## Variant #4: Top K Frequent Elements

### Input/Output:
- Input: `nums = [1,1,1,2,2,3]`, `k = 2`
- Output: `[1,2]` (1 appears 3 times, 2 appears 2 times)

### Full State Space:
Count all frequencies, sort by frequency, return top k.
```
Frequency map: {1:3, 2:2, 3:1}
Sort by frequency: [(1,3), (2,2), (3,1)]
Take top k=2: [1, 2]
Time: O(n log n) for sorting
```

### Expected/Pruned State Space:
Use min heap of size k, ordered by frequency.
```
Heap maintains k most frequent elements
Heap size ≤ k at all times
Root = least frequent among top k
```

### State Space Leading to Output:
Min heap of (element, frequency) pairs, size k.

### Brute Force Canonical Skeleton:
```csharp
int[] TopKFrequentBruteForce(int[] nums, int k)
{
    // Count frequencies
    Dictionary<int, int> freqMap = new();
    foreach (int num in nums)
    {
        freqMap[num] = freqMap.GetValueOrDefault(num, 0) + 1;
    }
    
    // Convert to list and sort by frequency
    List<(int num, int freq)> pairs = new();
    foreach (var kvp in freqMap)
    {
        pairs.Add((kvp.Key, kvp.Value));
    }
    
    pairs.Sort((a, b) => b.freq.CompareTo(a.freq)); // Descending
    
    // Take top k
    int[] result = new int[k];
    for (int i = 0; i < k; i++)
    {
        result[i] = pairs[i].num;
    }
    
    return result;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `nums = [1,1,1,2,2,3]`, `k = 2`:

| Step | Action | freqMap | pairs | sorted pairs | result |
|------|--------|---------|-------|--------------|--------|
| 1 | Count frequencies | {1:3, 2:2, 3:1} | - | - | [] |
| 2 | Convert to pairs | - | [(1,3),(2,2),(3,1)] | - | [] |
| 3 | Sort by freq desc | - | - | [(1,3),(2,2),(3,1)] | [] |
| 4 | Take top k=2 | - | - | - | [1,2] |

### Optimized Solution from Canonical Skeleton:
```csharp
int[] TopKFrequent(int[] nums, int k)
{
    // Count frequencies
    Dictionary<int, int> freqMap = new();
    foreach (int num in nums)
    {
        freqMap[num] = freqMap.GetValueOrDefault(num, 0) + 1;
    }
    
    // Min heap: ordered by frequency
    PriorityQueue<int, int> pq = new();
    
    foreach (var kvp in freqMap)
    {
        pq.Enqueue(kvp.Key, kvp.Value); // element, frequency
        
        // Maintain heap size = k
        if (pq.Count > k)
        {
            pq.Dequeue(); // Remove least frequent
        }
    }
    
    // Extract all k elements
    int[] result = new int[k];
    for (int i = 0; i < k; i++)
    {
        result[i] = pq.Dequeue();
    }
    
    return result;
}
```

### Explanation of Pruning:
- **Two-phase approach:** Count frequencies (O(n)), then heap selection (O(m log k) where m = unique elements)
- **Min heap by frequency:** Keep k most frequent, remove least frequent when size > k
- **Key insight:** Only need to track k most frequent, not sort all elements
- **Why it works:** Least frequent among top k is at root
- **Space savings:** O(k) heap vs O(m) for sorting all unique elements

### Optimized Solution Code Walkthrough / Variable Trace:
For `nums = [1,1,1,2,2,3]`, `k = 2`:

**Phase 1: Count frequencies**

| num | freqMap |
|-----|----------|
| 1 | {1:1} |
| 1 | {1:2} |
| 1 | {1:3} |
| 2 | {1:3, 2:1} |
| 2 | {1:3, 2:2} |
| 3 | {1:3, 2:2, 3:1} |

**Phase 2: Build heap**

| element | freq | heap before | action | heap after | explanation |
|---------|------|-------------|--------|------------|-------------|
| 1 | 3 | [] | enqueue (1,3) | [(1,3)] | size=1 |
| 2 | 2 | [(1,3)] | enqueue (2,2) | [(2,2),(1,3)] | size=2 |
| 3 | 1 | [(2,2),(1,3)] | enqueue (3,1), dequeue | [(2,2),(1,3)] | size=3→2, remove min freq(3,1) |

**Final result:** [2,1] or [1,2] (order may vary)

### Big-O Analysis:
- **Brute Force:** O(n + m log m) → counting + sorting m unique elements
- **Optimized:** O(n + m log k) → counting + heap operations for m elements
- **Space Complexity:**
  - Both: O(m) for frequency map
  - Heap: Additional O(k) for priority queue
- **When k << m:** Heap approach is better

</details>


<details>
<summary><b>Variant #5: Find Median from Data Stream</b></summary>

## Variant #5: Find Median from Data Stream

### Input/Output:
- Input: Stream of integers
- Operations: `addNum(1)`, `findMedian()` → 1.0, `addNum(2)`, `findMedian()` → 1.5, `addNum(3)`, `findMedian()` → 2.0
- Output: Median after each query

### Full State Space:
After each add, sort all elements and find median.
```
add(1): [1] → median = 1
add(2): [1,2] → median = (1+2)/2 = 1.5
add(3): [1,2,3] → median = 2
Time per query: O(n log n) for sorting
```

### Expected/Pruned State Space:
Use two heaps to maintain balanced halves.
```
Max heap: lower half (largest at top)
Min heap: upper half (smallest at top)
Median = balance point between heaps
```

### State Space Leading to Output:
Two heaps divide elements into lower and upper halves.

### Brute Force Canonical Skeleton:
```csharp
class MedianFinderBruteForce
{
    private List<int> nums = new();
    
    public void AddNum(int num)
    {
        nums.Add(num);
    }
    
    public double FindMedian()
    {
        nums.Sort();
        int n = nums.Count;
        
        if (n % 2 == 0)
        {
            return (nums[n / 2 - 1] + nums[n / 2]) / 2.0;
        }
        else
        {
            return nums[n / 2];
        }
    }
}
```

### Brute Force Code Walkthrough / Variable Trace:
Operations: `add(1)`, `median()`, `add(2)`, `median()`, `add(3)`, `median()`:

| operation | nums | sorted | median calculation | result |
|-----------|------|--------|-------------------|---------|
| add(1) | [1] | - | - | - |
| median() | [1] | [1] | nums[0] = 1 | 1.0 |
| add(2) | [1,2] | - | - | - |
| median() | [1,2] | [1,2] | (nums[0]+nums[1])/2 = 1.5 | 1.5 |
| add(3) | [1,2,3] | - | - | - |
| median() | [1,2,3] | [1,2,3] | nums[1] = 2 | 2.0 |

### Optimized Solution from Canonical Skeleton:
```csharp
class MedianFinder
{
    // Max heap for lower half (negate values for max behavior)
    private PriorityQueue<int, int> maxHeap = new();
    
    // Min heap for upper half
    private PriorityQueue<int, int> minHeap = new();
    
    public void AddNum(int num)
    {
        // Add to max heap (lower half)
        maxHeap.Enqueue(num, -num);
        
        // Balance: move largest from lower to upper
        minHeap.Enqueue(maxHeap.Peek(), maxHeap.Peek());
        maxHeap.Dequeue();
        
        // Ensure maxHeap has equal or one more element
        if (maxHeap.Count < minHeap.Count)
        {
            maxHeap.Enqueue(minHeap.Peek(), -minHeap.Peek());
            minHeap.Dequeue();
        }
    }
    
    public double FindMedian()
    {
        if (maxHeap.Count > minHeap.Count)
        {
            return maxHeap.Peek();
        }
        else
        {
            return (maxHeap.Peek() + minHeap.Peek()) / 2.0;
        }
    }
}
```

### Explanation of Pruning:
- **Two-heap technique:** 
  - Max heap stores lower half (largest of lower at top)
  - Min heap stores upper half (smallest of upper at top)
- **Invariant:** `maxHeap.Count >= minHeap.Count` and difference ≤ 1
- **Median access:**
  - If sizes equal: average of two tops
  - If maxHeap larger: its top is median
- **Key insight:** Don't need full sort, just maintain balance point
- **Why it works:** Heaps maintain partial order sufficient for median

### Optimized Solution Code Walkthrough / Variable Trace:
Operations: `add(1)`, `median()`, `add(2)`, `median()`, `add(3)`, `median()`:

| operation | maxHeap before | minHeap before | action | maxHeap after | minHeap after | median |
|-----------|----------------|----------------|--------|---------------|---------------|--------|
| add(1) | [] | [] | enq max(1), move to min, move back | [1] | [] | - |
| median() | [1] | [] | - | [1] | [] | 1.0 |
| add(2) | [1] | [] | enq max(2), move 2 to min | [1] | [2] | - |
| median() | [1] | [2] | - | [1] | [2] | (1+2)/2=1.5 |
| add(3) | [1] | [2] | enq max(3), move 3 to min, move 2 back | [1,2] | [3] | - |
| median() | [1,2] | [3] | - | [1,2] | [3] | 2.0 |

**Detailed trace for add(3):**
1. maxHeap.Enqueue(3, -3) → maxHeap = [1,3]
2. Move max from maxHeap to minHeap: minHeap.Enqueue(3, 3), maxHeap.Dequeue() → maxHeap=[1], minHeap=[2,3]
3. Balance: maxHeap.Count(1) < minHeap.Count(2), move from min to max → maxHeap=[1,2], minHeap=[3]

### Big-O Analysis:
- **Brute Force:** O(n log n) per findMedian() → sorting
- **Optimized:** 
  - AddNum: O(log n) → heap insert/delete
  - FindMedian: O(1) → peek at heap tops
- **Space Complexity:** O(n) → both approaches store all elements
- **Trade-off:** More complex implementation for much faster queries

</details>


<details>
<summary><b>Variant #6: Task Scheduler</b></summary>

## Variant #6: Task Scheduler

### Input/Output:
- Input: `tasks = ['A','A','A','B','B','B']`, `n = 2` (cooldown period)
- Output: `8` (minimum intervals needed)
- Explanation: `A -> B -> idle -> A -> B -> idle -> A -> B`

### Full State Space:
Try all permutations respecting cooldown constraints.
```
Generate all valid schedules
For each, count intervals
Return minimum
Time: Exponential
```

### Expected/Pruned State Space:
Greedy approach: always schedule most frequent available task.
```
Max heap: task frequencies
Queue: tasks in cooldown
At each interval, schedule highest frequency task
```

### State Space Leading to Output:
Heap tracks available tasks, queue tracks cooling tasks.

### Brute Force Canonical Skeleton:
```csharp
int TaskSchedulerBruteForce(char[] tasks, int n)
{
    // Count frequencies
    Dictionary<char, int> freq = new();
    foreach (char task in tasks)
    {
        freq[task] = freq.GetValueOrDefault(task, 0) + 1;
    }
    
    // Calculate using formula (for most frequent task)
    int maxFreq = freq.Values.Max();
    int maxCount = freq.Values.Count(f => f == maxFreq);
    
    // Intervals = (maxFreq - 1) * (n + 1) + maxCount
    int intervals = (maxFreq - 1) * (n + 1) + maxCount;
    
    // At minimum, need tasks.Length intervals
    return Math.Max(intervals, tasks.Length);
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `tasks = ['A','A','A','B','B','B']`, `n = 2`:

| Step | Action | freq | maxFreq | maxCount | calculation | result |
|------|--------|------|---------|----------|-------------|---------|
| 1 | Count | {A:3, B:3} | 3 | 2 | (3-1)×(2+1)+2 = 8 | 8 |

**Explanation:** 
- maxFreq = 3 (both A and B appear 3 times)
- Need (3-1)=2 "chunks" with cooldown
- Each chunk size = n+1 = 3
- Last chunk only needs maxCount=2 tasks
- Total: 2×3 + 2 = 8

### Optimized Solution from Canonical Skeleton:
```csharp
int TaskScheduler(char[] tasks, int n)
{
    // Count frequencies
    Dictionary<char, int> freq = new();
    foreach (char task in tasks)
    {
        freq[task] = freq.GetValueOrDefault(task, 0) + 1;
    }
    
    // Max heap by frequency (negate for max behavior)
    PriorityQueue<int, int> maxHeap = new();
    foreach (int count in freq.Values)
    {
        maxHeap.Enqueue(count, -count);
    }
    
    // Queue for cooldown: (frequency, availableTime)
    Queue<(int freq, int time)> cooldown = new();
    
    int time = 0;
    
    while (maxHeap.Count > 0 || cooldown.Count > 0)
    {
        time++;
        
        // Check if any task finished cooling
        if (cooldown.Count > 0 && cooldown.Peek().time == time)
        {
            var (freq, _) = cooldown.Dequeue();
            maxHeap.Enqueue(freq, -freq);
        }
        
        // Schedule most frequent available task
        if (maxHeap.Count > 0)
        {
            int taskFreq = maxHeap.Dequeue();
            taskFreq--;
            
            // Put back in cooldown if not done
            if (taskFreq > 0)
            {
                cooldown.Enqueue((taskFreq, time + n + 1));
            }
        }
        // Else: idle interval
    }
    
    return time;
}
```

### Explanation of Pruning:
- **Greedy strategy:** Always pick most frequent task available
- **Max heap:** Efficiently finds highest frequency in O(log k)
- **Cooldown queue:** Tracks when tasks become available again
- **Key insight:** Most frequent task first minimizes idle time
- **Why it works:** Greedy choice doesn't block better future choices

### Optimized Solution Code Walkthrough / Variable Trace:
For `tasks = ['A','A','A','B','B','B']`, `n = 2`:

| time | maxHeap | cooldown | action | result |
|------|---------|----------|--------|--------|
| 0 | [3,3] | [] | - | - |
| 1 | [3] | [(2,4)] | schedule A | A |
| 2 | [] | [(2,4),(2,5)] | schedule B | A,B |
| 3 | [] | [(2,4),(2,5)] | idle | A,B,idle |
| 4 | [2] | [(2,5)] | schedule A | A,B,idle,A |
| 5 | [] | [(1,8),(2,5)] | schedule B | A,B,idle,A,B |
| 6 | [] | [(1,8),(1,9)] | idle | A,B,idle,A,B,idle |
| 7 | [] | [(1,8),(1,9)] | idle (wait) | ... |
| 8 | [1] | [(1,9)] | schedule A | ...A |
| 9 | [] | [] | schedule B | ...A,B |

**Result:** 8 intervals (matches formula approach)

### Big-O Analysis:
- **Formula Approach:** O(n) → count frequencies
- **Simulation Approach:** O(n log k) → n tasks, heap operations O(log k)
- **Space Complexity:** O(k) → k unique tasks in heap/queue
- **Formula is simpler but simulation is more intuitive**

</details>


<details>
<summary><b>Variant #7: Reorganize String</b></summary>

## Variant #7: Reorganize String

### Input/Output:
- Input: `s = "aab"`
- Output: `"aba"` (or `"baa"`, no two adjacent characters are the same)

### Full State Space:
Generate all permutations, check for valid arrangement.
```
Permutations of "aab": aab, aba, baa
Valid (no adjacent same): aba, baa
Time: O(n!) → exponential
```

### Expected/Pruned State Space:
Greedy: always pick top two most frequent characters alternately.
```
Max heap by character frequency
Pick highest, then second highest, repeat
Avoid adjacent duplicates
```

### State Space Leading to Output:
Max heap ensures we use most frequent characters first.

### Brute Force Canonical Skeleton:
```csharp
string ReorganizeStringBruteForce(string s)
{
    // Count frequencies
    Dictionary<char, int> freq = new();
    foreach (char c in s)
    {
        freq[c] = freq.GetValueOrDefault(c, 0) + 1;
    }
    
    // Check if possible
    int maxFreq = freq.Values.Max();
    if (maxFreq > (s.Length + 1) / 2)
    {
        return ""; // Impossible
    }
    
    // Try backtracking
    char[] result = new char[s.Length];
    
    bool Backtrack(int idx, char prev)
    {
        if (idx == s.Length) return true;
        
        foreach (var kvp in freq.Where(x => x.Value > 0 && x.Key != prev))
        {
            result[idx] = kvp.Key;
            freq[kvp.Key]--;
            
            if (Backtrack(idx + 1, kvp.Key))
                return true;
            
            freq[kvp.Key]++;
        }
        
        return false;
    }
    
    if (Backtrack(0, '\0'))
        return new string(result);
    
    return "";
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `s = "aab"`:

| idx | prev | try | freq after | result | backtrack |
|-----|------|-----|------------|--------|------------|
| 0 | \0 | 'a' | {a:1,b:1} | [a] | continue |
| 1 | 'a' | 'b' | {a:1,b:0} | [a,b] | continue |
| 2 | 'b' | 'a' | {a:0,b:0} | [a,b,a] | success! |

**Result:** "aba"

### Optimized Solution from Canonical Skeleton:
```csharp
string ReorganizeString(string s)
{
    // Count frequencies
    Dictionary<char, int> freq = new();
    foreach (char c in s)
    {
        freq[c] = freq.GetValueOrDefault(c, 0) + 1;
    }
    
    // Check if possible
    int maxFreq = freq.Values.Max();
    if (maxFreq > (s.Length + 1) / 2)
    {
        return ""; // Impossible
    }
    
    // Max heap by frequency
    PriorityQueue<char, int> maxHeap = new();
    foreach (var kvp in freq)
    {
        maxHeap.Enqueue(kvp.Key, -kvp.Value);
    }
    
    StringBuilder result = new();
    char prev = '\0';
    int prevFreq = 0;
    
    while (maxHeap.Count > 0)
    {
        // Pick most frequent
        char current = maxHeap.Dequeue();
        int currentFreq = freq[current];
        
        result.Append(current);
        freq[current]--;
        
        // Put previous back if still has count
        if (prevFreq > 0)
        {
            maxHeap.Enqueue(prev, -prevFreq);
        }
        
        prev = current;
        prevFreq = freq[current];
    }
    
    return result.ToString();
}
```

### Explanation of Pruning:
- **Greedy strategy:** Use most frequent character first (prevents getting stuck)
- **Alternating pattern:** Hold previous character, pick next most frequent
- **Impossibility check:** If maxFreq > ⌈n/2⌉, no valid arrangement exists
- **Key insight:** By using most frequent first, we maximize chances of success
- **Why it works:** Greedy prevents accumulation of same character

### Optimized Solution Code Walkthrough / Variable Trace:
For `s = "aab"`:

| step | maxHeap | dequeue | result | prev | prevFreq | put back |
|------|---------|---------|--------|------|----------|----------|
| init | [a:2,b:1] | - | "" | \0 | 0 | - |
| 1 | [b:1] | a | "a" | a | 1 | none |
| 2 | [a:1] | b | "ab" | b | 0 | a:1 |
| 3 | [] | a | "aba" | a | 0 | none |

**Result:** "aba"

### Big-O Analysis:
- **Brute Force:** O(n!) → try all permutations
- **Optimized:** O(n log k) → n characters, heap operations O(log k)
- **Space Complexity:** O(k) → k unique characters in heap
- **k ≤ 26 for lowercase letters, effectively O(n)**

</details>


<details>
<summary><b>Variant #8: Sliding Window Median</b></summary>

## Variant #8: Sliding Window Median

### Input/Output:
- Input: `nums = [1,3,-1,-3,5,3,6,7]`, `k = 3`
- Output: `[1,-1,-1,3,5,6]` (median of each window)

### Full State Space:
For each window, extract elements, sort, find median.
```
Window [1,3,-1] → sorted [-1,1,3] → median = 1
Window [3,-1,-3] → sorted [-3,-1,3] → median = -1
...
Time per window: O(k log k)
Total: O(n × k log k)
```

### Expected/Pruned State Space:
Two heaps with lazy deletion using hash map.
```
Max heap: lower half
Min heap: upper half
Hash map: track removed elements
Balance heaps while accounting for "lazy" deletions
```

### State Space Leading to Output:
Two heaps maintain median, hash map tracks deletions.

### Brute Force Canonical Skeleton:
```csharp
double[] MedianSlidingWindowBruteForce(int[] nums, int k)
{
    double[] result = new double[nums.Length - k + 1];
    
    for (int i = 0; i <= nums.Length - k; i++)
    {
        // Extract window
        int[] window = new int[k];
        for (int j = 0; j < k; j++)
        {
            window[j] = nums[i + j];
        }
        
        // Sort
        Array.Sort(window);
        
        // Find median
        if (k % 2 == 0)
        {
            result[i] = (window[k / 2 - 1] + window[k / 2]) / 2.0;
        }
        else
        {
            result[i] = window[k / 2];
        }
    }
    
    return result;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `nums = [1,3,-1,-3,5,3,6,7]`, `k = 3`:

| i | window | sorted | median | result |
|---|--------|--------|--------|--------|
| 0 | [1,3,-1] | [-1,1,3] | 1 | [1] |
| 1 | [3,-1,-3] | [-3,-1,3] | -1 | [1,-1] |
| 2 | [-1,-3,5] | [-3,-1,5] | -1 | [1,-1,-1] |
| 3 | [-3,5,3] | [-3,3,5] | 3 | [1,-1,-1,3] |
| 4 | [5,3,6] | [3,5,6] | 5 | [1,-1,-1,3,5] |
| 5 | [3,6,7] | [3,6,7] | 6 | [1,-1,-1,3,5,6] |

### Optimized Solution from Canonical Skeleton:
```csharp
double[] MedianSlidingWindow(int[] nums, int k)
{
    double[] result = new double[nums.Length - k + 1];
    
    // Two heaps
    PriorityQueue<int, int> maxHeap = new(); // lower half
    PriorityQueue<int, int> minHeap = new(); // upper half
    Dictionary<int, int> toRemove = new(); // lazy deletion map
    
    // Helper: balance heaps
    void Balance()
    {
        // Remove invalid tops
        while (maxHeap.Count > 0 && toRemove.ContainsKey(maxHeap.Peek()) && toRemove[maxHeap.Peek()] > 0)
        {
            toRemove[maxHeap.Peek()]--;
            maxHeap.Dequeue();
        }
        while (minHeap.Count > 0 && toRemove.ContainsKey(minHeap.Peek()) && toRemove[minHeap.Peek()] > 0)
        {
            toRemove[minHeap.Peek()]--;
            minHeap.Dequeue();
        }
        
        // Balance sizes
        int validMaxSize = maxHeap.Count - toRemove.Values.Where(v => v > 0).Sum();
        int validMinSize = minHeap.Count - toRemove.Values.Where(v => v > 0).Sum();
        
        if (validMaxSize > validMinSize + 1)
        {
            minHeap.Enqueue(maxHeap.Peek(), maxHeap.Peek());
            maxHeap.Dequeue();
        }
        else if (validMinSize > validMaxSize)
        {
            maxHeap.Enqueue(minHeap.Peek(), -minHeap.Peek());
            minHeap.Dequeue();
        }
    }
    
    // Initialize first window
    for (int i = 0; i < k; i++)
    {
        maxHeap.Enqueue(nums[i], -nums[i]);
    }
    
    // Move half to minHeap
    for (int i = 0; i < k / 2; i++)
    {
        minHeap.Enqueue(maxHeap.Peek(), maxHeap.Peek());
        maxHeap.Dequeue();
    }
    
    // Process windows
    for (int i = k; i < nums.Length; i++)
    {
        // Get median
        Balance();
        result[i - k] = k % 2 == 1 ? maxHeap.Peek() : (maxHeap.Peek() + minHeap.Peek()) / 2.0;
        
        // Remove outgoing element (lazy)
        int outgoing = nums[i - k];
        toRemove[outgoing] = toRemove.GetValueOrDefault(outgoing, 0) + 1;
        
        // Add incoming element
        int incoming = nums[i];
        if (maxHeap.Count > 0 && incoming <= maxHeap.Peek())
        {
            maxHeap.Enqueue(incoming, -incoming);
        }
        else
        {
            minHeap.Enqueue(incoming, incoming);
        }
    }
    
    // Last window
    Balance();
    result[nums.Length - k] = k % 2 == 1 ? maxHeap.Peek() : (maxHeap.Peek() + minHeap.Peek()) / 2.0;
    
    return result;
}
```

### Explanation of Pruning:
- **Two-heap technique:** Same as Find Median from Stream
- **Lazy deletion:** Mark elements for removal instead of searching heap
- **Balance operation:** Clean invalid tops and rebalance
- **Key insight:** Don't need to maintain sorted order for all elements
- **Why it works:** Heaps + lazy deletion simulate balanced BST operations

### Optimized Solution Code Walkthrough / Variable Trace:
For `nums = [1,3,-1,-3,5,3,6,7]`, `k = 3` (simplified trace):

| window | maxHeap (approx) | minHeap (approx) | median | action |
|--------|------------------|------------------|--------|--------|
| [1,3,-1] | [1,-1] | [3] | 1 | initial |
| [3,-1,-3] | [-1,-3] | [3] | -1 | remove 1, add -3 |
| [-1,-3,5] | [-3,-1] | [5] | -1 | remove 3, add 5 |
| [-3,5,3] | [-3,3] | [5] | 3 | remove -1, add 3 |
| [5,3,6] | [3,5] | [6] | 5 | remove -3, add 6 |
| [3,6,7] | [3,6] | [7] | 6 | remove 5, add 7 |

### Big-O Analysis:
- **Brute Force:** O(n × k log k) → n windows, each sorted in O(k log k)
- **Optimized:** O(n log k) → n operations, each heap operation O(log k)
- **Space Complexity:** O(k) → heaps + deletion map
- **Significant improvement when k is large**

</details>

---

# Key Takeaways

## Pattern Recognition
- **Heap/Priority Queue** = efficient way to **repeatedly access min/max** elements
- Typical signals: "k largest", "k smallest", "median", "top k frequent", "next best"
- Problems involve **dynamic ordering** where full sort is overkill

## Heap Types
- **Min Heap:** Root = smallest (use for k largest elements)
- **Max Heap:** Root = largest (use for k smallest elements, negate priorities in C#)
- **Two Heaps:** Maintain balance point (median problems)

## Core Template
```csharp
// Min heap
PriorityQueue<T, int> pq = new();
pq.Enqueue(item, priority);
T min = pq.Dequeue();

// Max heap (negate priorities)
pq.Enqueue(item, -priority);
T max = pq.Dequeue();
```

## Common Patterns
1. **Top-K Selection:** Min heap of size k for k largest (or max heap for k smallest)
2. **K-Way Merge:** Min heap tracks current heads of k sorted sequences
3. **Frequency-Based:** Count frequencies, use heap to select top k
4. **Median Maintenance:** Two heaps (max for lower, min for upper)
5. **Greedy Scheduling:** Max heap for highest priority task
6. **Lazy Deletion:** Hash map tracks removed elements, clean during balance

## Why O(n log k) instead of O(n log n)?
- **Full sort:** O(n log n) for n elements
- **Heap with size k:** O(n log k) → n operations, each O(log k)
- **When k << n:** Massive improvement (e.g., k=10, n=1M: log 10 vs log 1M)

## Key Insights
- **Partial ordering sufficient:** Don't need full sort, just min/max access
- **Space-time tradeoff:** O(k) space for O(n log k) time
- **Heap vs. Sort:** Use heap when k < n and repeated access needed
- **Two-heap technique:** Maintains balance point without full sort

---

# Mental Checklist for Heap / Priority Queue

- Do I repeatedly need min or max?
- Is partial ordering sufficient?
- Is K much smaller than N?
- Do elements arrive dynamically?
- Do I need to maintain top-K elements?
- Is there a "best next choice" decision?

If yes → Heap / Priority Queue applies.
