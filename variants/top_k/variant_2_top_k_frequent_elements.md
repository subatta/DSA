# Top K Frequent Elements

**Difficulty:** Medium  
**LeetCode Problem:** #347  
**Tags:** `Heap`, `Priority Queue`, `Hash Map`, `Frequency`, `Top K`

---

## Problem Statement

Given an integer array `nums` and an integer `k`, return the `k` most frequent elements. You may return the answer in any order.

**Constraints:**
- `1 <= nums.length <= 10^5`
- `-10^4 <= nums[i] <= 10^4`
- `k` is in the range `[1, number of unique elements in the array]`
- It is guaranteed that the answer is unique

**Example 1:**
```
Input: nums = [1,1,1,2,2,3], k = 2
Output: [1,2]
Explanation: 1 appears 3 times, 2 appears 2 times, 3 appears 1 time
```

**Example 2:**
```
Input: nums = [1], k = 1
Output: [1]
```

---

## Step 1: State Space

### Problem Visualization

For `nums = [1,1,1,2,2,3]`, `k = 2`:

```
Frequency count:
Element: 1  2  3
Freq:    3  2  1

Sorted by frequency (descending):
1: ███ (freq=3)
2: ██  (freq=2)
3: █   (freq=1)

Top 2 frequent: [1, 2]
```

Min-heap approach (heap of size k):
```
Process (1, freq=3): heap = [(1,3)], size=1
Process (2, freq=2): heap = [(2,2), (1,3)], size=2
Process (3, freq=1): heap = [(3,1), (2,2), (1,3)], size=3
  Size > k, remove min freq (3,1)
  heap = [(2,2), (1,3)]

Result: [2, 1] (or [1, 2], order doesn't matter)
```

### Core Question

**How can we identify the k elements that appear most frequently in the array?**

We need to:
1. Count frequency of each element
2. Find the k elements with highest frequencies

### Deriving from First Principles

**Observation 1: Need frequency count**
First step is to count occurrences of each element.
Use hash map: `element → frequency`

**Observation 2: Find top k by frequency**
After counting, we have pairs `(element, frequency)`.
Need to select k pairs with highest frequencies.

**Observation 3: Min-heap of size k**
Similar to k-th largest element:
- Min-heap ordered by frequency (not element value)
- Heap size = k
- Heap contains k most frequent elements
- Heap top has smallest frequency among these k (k-th highest frequency)

**Observation 4: Why min-heap?**
When processing element with frequency f:
- If heap size < k: Add to heap
- If heap size = k and f > min frequency in heap: Replace min with current
- This maintains k highest frequencies

**Observation 5: Extract result**
After processing all elements, heap contains k most frequent.
Extract all elements from heap (order doesn't matter).

**Formula:**
```
Step 1: Build frequency map
  freqMap = {}
  for each num in nums:
    freqMap[num]++

Step 2: Min-heap of size k
  minHeap = new MinHeap(by frequency)
  for each (element, freq) in freqMap:
    minHeap.add((element, freq))
    if minHeap.size() > k:
      minHeap.poll()

Step 3: Extract result
  result = []
  while minHeap not empty:
    result.add(minHeap.poll().element)
  return result
```

### State Space Structure

**Type:** Top k by frequency using hash map + min-heap.

**Structure:**
- Hash map: element → frequency
- Min-heap of pairs: (element, frequency), ordered by frequency
- Heap size: k

**Cardinality:**
- n elements to process
- u unique elements (u ≤ n)
- Frequency counting: O(n)
- Heap operations: O(u log k)
- Total: O(n + u log k)

**Key Property:** Min-heap maintains k highest frequencies by removing elements with lower frequencies.

### Example Computation

Input: `nums = [1,1,1,2,2,3]`, `k = 2`

```
Step 1: Count frequencies
  freqMap = {1: 3, 2: 2, 3: 1}

Step 2: Build min-heap of size k=2
  Heap = [] (min-heap by frequency)

  Process (1, freq=3):
    Add: heap = [(1,3)]
    Size = 1 ≤ 2

  Process (2, freq=2):
    Add: heap = [(2,2), (1,3)]
    Size = 2 ≤ 2

  Process (3, freq=1):
    Add: heap = [(3,1), (2,2), (1,3)]
    Size = 3 > 2
    Remove min: (3,1)
    heap = [(2,2), (1,3)]

Step 3: Extract elements
  Result = [2, 1] (or any order)
```

### Generation Pattern

**Brute force (sort):**
```csharp
public int[] TopKFrequent(int[] nums, int k) {
    var freqMap = new Dictionary<int, int>();
    foreach (int num in nums) {
        freqMap[num] = freqMap.GetValueOrDefault(num) + 1;
    }
    
    // Sort by frequency descending
    var sorted = freqMap.OrderByDescending(p => p.Value)
                        .Take(k)
                        .Select(p => p.Key)
                        .ToArray();
    return sorted;
}
// Time: O(n + u log u), Space: O(u)
```

---

## Step 2: Brute Force

Count frequencies, sort by frequency, take top k: O(n + u log u).

---

## Step 3: Optimization Ideas

### Key Insights
1. **Don't need full sort:** Only need top k
2. **Min-heap of size k:** More efficient than sorting
3. **Complexity improvement:** O(n + u log k) vs O(n + u log u)

### Alternative Approaches
1. **Min-heap:** O(n + u log k), best for small k
2. **Max-heap + k pops:** O(n + u + k log u)
3. **Bucket sort:** O(n) using frequency buckets (0 to n)

---

## Step 4: Optimal Solution

### Approach 1: Min-Heap

```csharp
public int[] TopKFrequent(int[] nums, int k) {
    // Step 1: Count frequencies
    var freqMap = new Dictionary<int, int>();
    foreach (int num in nums) {
        freqMap[num] = freqMap.GetValueOrDefault(num) + 1;
    }
    
    // Step 2: Min-heap of size k (ordered by frequency)
    var minHeap = new PriorityQueue<int, int>();  // (element, frequency)
    
    foreach (var pair in freqMap) {
        minHeap.Enqueue(pair.Key, pair.Value);
        
        if (minHeap.Count > k) {
            minHeap.Dequeue();  // Remove element with lowest frequency
        }
    }
    
    // Step 3: Extract result
    var result = new int[k];
    for (int i = 0; i < k; i++) {
        result[i] = minHeap.Dequeue();
    }
    
    return result;
}
```

**Complexity:**
- Time: O(n + u log k)
  - Frequency counting: O(n)
  - Heap operations: O(u log k)
- Space: O(u) - frequency map + heap

### Approach 2: Bucket Sort (Optimal)

```csharp
public int[] TopKFrequent(int[] nums, int k) {
    // Step 1: Count frequencies
    var freqMap = new Dictionary<int, int>();
    foreach (int num in nums) {
        freqMap[num] = freqMap.GetValueOrDefault(num) + 1;
    }
    
    // Step 2: Bucket sort by frequency
    // bucket[i] = list of elements with frequency i
    var buckets = new List<int>[nums.Length + 1];
    for (int i = 0; i <= nums.Length; i++) {
        buckets[i] = new List<int>();
    }
    
    foreach (var pair in freqMap) {
        buckets[pair.Value].Add(pair.Key);
    }
    
    // Step 3: Collect top k from highest frequency buckets
    var result = new List<int>();
    for (int freq = nums.Length; freq >= 0 && result.Count < k; freq--) {
        foreach (int num in buckets[freq]) {
            result.Add(num);
            if (result.Count == k) break;
        }
    }
    
    return result.ToArray();
}
```

**Complexity:**
- Time: O(n) - linear time!
  - Frequency counting: O(n)
  - Bucketing: O(u)
  - Collecting: O(k)
- Space: O(n) - buckets array

---

## Comparison of Approaches

| Approach | Time | Space | Best When |
|----------|------|-------|-----------|
| Min-heap | O(n + u log k) | O(u) | Small k, general case |
| Bucket sort | O(n) | O(n) | Need optimal time, k varies |
| Sort | O(n + u log u) | O(u) | Simplicity, small data |

---

## Edge Cases

1. **Single element:** `[1], k=1` → `[1]`
2. **All unique:** `[1,2,3,4,5], k=3` → Any 3 elements (all freq=1)
3. **All same:** `[1,1,1,1], k=1` → `[1]`
4. **k = unique count:** Return all unique elements

---

## Related Problems

1. **Kth Largest Element (LeetCode #215)** - Basic top k pattern
2. **Top K Frequent Words (LeetCode #692)** - With tiebreaker
3. **Sort Characters By Frequency (LeetCode #451)** - Frequency sorting
4. **K Closest Points to Origin (LeetCode #973)** - Top k by distance

---

## Pattern Recognition

**Problem Asks For:**
- Top k by some criteria (frequency, distance, score)
- K most/least frequent
- K best/worst elements

**This Suggests:**
- Count occurrences/compute metric
- Use min-heap of size k
- Order by the criteria (frequency, distance, etc.)
- → **Top K Elements pattern**

**Key Indicators:**
- "Top k", "k most", "k least"
- Need k elements based on some property
- Don't need full sorted order

---

## Tags

`#heap` `#priority-queue` `#top-k` `#frequency` `#hash-map` `#bucket-sort`
