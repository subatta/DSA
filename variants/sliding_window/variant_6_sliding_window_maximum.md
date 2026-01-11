# Variant: Sliding Window Maximum

**Pattern:** Sliding Window + Monotonic Deque (HYBRID)  
**Difficulty:** Hard  
**LeetCode:** #239

---

## Problem Statement

Given an array `nums` and an integer `k`, return an array of the **maximum** value in each sliding window of size `k`.

### Input/Output:
- Input: `nums = [1,3,-1,-3,5,3,6,7]`, `k = 3`
- Output: `[3,3,5,5,6,7]`
- Explanation:
  ```
  Window [1,3,-1] → max = 3
  Window [3,-1,-3] → max = 3
  Window [-1,-3,5] → max = 5
  Window [-3,5,3] → max = 5
  Window [5,3,6] → max = 6
  Window [3,6,7] → max = 7
  ```

---

## **Step 1: Derive Full State Space from Problem**

### **Problem Analysis:**
- Fixed window size `k` slides through array
- For each position, need the **maximum** element in that window
- Window moves left-to-right, one element at a time

### **State Space Definition:**
- **Type**: All contiguous subarrays of **fixed size k**
- **Structure**: Linear sequences of exactly k elements
- **Cardinality**: **n - k + 1** windows = **O(n)**
- **Generation**: Single loop with fixed-size window

### **Full State Space Enumeration:**
For `nums = [1,3,-1,-3,5,3,6,7]` (n=8) and `k=3`, there are 8-3+1 = **6 windows**:

```
Window 0: [1, 3, -1]    indices [0,1,2]
Window 1: [3, -1, -3]   indices [1,2,3]
Window 2: [-1, -3, 5]   indices [2,3,4]
Window 3: [-3, 5, 3]    indices [3,4,5]
Window 4: [5, 3, 6]     indices [4,5,6]
Window 5: [3, 6, 7]     indices [5,6,7]
Total: 6 windows
```

**With maximum values:**
```
[1,3,-1] → max = 3
[3,-1,-3] → max = 3
[-1,-3,5] → max = 5
[-3,5,3] → max = 5
[5,3,6] → max = 6
[3,6,7] → max = 7
```

---

## **Step 2: Brute Force Solution with Value Tracing**

### **Brute Force Approach:**
1. Iterate through all starting positions (0 to n-k)
2. For each window, scan k elements to find maximum
3. Store maximum in result array

### **Brute Force Code:**
```csharp
int[] MaxSlidingWindowBruteForce(int[] nums, int k) 
{
    int n = nums.Length;
    int[] result = new int[n - k + 1];
    
    for (int i = 0; i <= n - k; i++)
    {
        int max = int.MinValue;
        
        // Find max in window [i, i+k-1]
        for (int j = i; j < i + k; j++)
        {
            max = Math.Max(max, nums[j]);
        }
        
        result[i] = max;
    }
    
    return result;
}
```

### **Brute Force Value Trace (nums = [1,3,-1,-3,5,3,6,7], k = 3):**

| Window | Start | End | Elements      | Max Search            | Max | result    |
|--------|-------|-----|---------------|-----------------------|-----|-----------|
| 0      | 0     | 2   | [1,3,-1]      | 1 → 3 → 3            | 3   | [3]       |
| 1      | 1     | 3   | [3,-1,-3]     | 3 → 3 → 3            | 3   | [3,3]     |
| 2      | 2     | 4   | [-1,-3,5]     | -1 → -1 → 5          | 5   | [3,3,5]   |
| 3      | 3     | 5   | [-3,5,3]      | -3 → 5 → 5           | 5   | [3,3,5,5] |
| 4      | 4     | 6   | [5,3,6]       | 5 → 5 → 6            | 6   | [3,3,5,5,6] |
| 5      | 5     | 7   | [3,6,7]       | 3 → 6 → 7            | 7   | [3,3,5,5,6,7] |

**Result: [3,3,5,5,6,7]**

### **Complexity:**
- **Time**: O(n × k) - for each of n-k+1 windows, scan k elements
- **Space**: O(1) excluding output array

---

## **Step 3: Can We Prune the Brute Force?**

### **Key Observations:**

1. **Redundant Work**: When window slides, we rescan k-1 elements
   ```
   Window 0: [1, 3, -1] → scan all 3 ✅
   Window 1: [3, -1, -3] → rescan 3 and -1 (already saw them!)
   ```

2. **Previous Max Invalid**: When max element exits window, must find new max
   ```
   Window [3,-1,-3] max=3
   Slide → [−1,−3,5], element 3 is gone!
   Must rescan to find max=5
   ```

3. **Dominated Elements**: Smaller elements before larger ones can never be max
   ```
   Window [..., 1, 3, ...]
   Element 1 can NEVER be max while 3 is in window
   → Can discard 1 immediately
   ```

4. **Ordering Insight**: Need to track **potential maximums** in order
   ```
   If we see [7, 5, 3], when 7 leaves:
     Next max is 5
   When 5 leaves:
     Next max is 3
   Need decreasing sequence!
   ```

### **Can We Do Better?**
**YES!** Use **Monotonic Decreasing Deque** to track potential maximums

---

## **Step 4: Optimal Solution - Sliding Window + Monotonic Deque**

### **Key Insight: Monotonic Decreasing Deque**

Maintain a **deque** storing indices where:
1. Values are in **decreasing order** (front = largest)
2. Only indices within current window
3. Front element is always the current maximum

**Why Decreasing?**
- When new element > back elements, those back elements can **never** be maximum
- Remove them immediately (they're "dominated")

### **Deque Operations:**
```
Add element 5:
  Deque: [7, 3, 2] → 5 > 2 → remove 2
         [7, 3]    → 5 > 3 → remove 3
         [7]       → 5 < 7 → keep 7, add 5 → [7, 5]

Add element 8:
  Deque: [7, 5] → 8 > 5 → remove 5
         [7]    → 8 > 7 → remove 7
         []     → add 8 → [8]  ← 8 dominates everything!
```

### **Canonical Skeleton Applied:**
```csharp
T[] SlidingWindowFixed<T>(T[] nums, int k, DataStructure auxStructure) {
    var result = new List<T>();
    
    for (int i = 0; i < nums.Length; i++) {
        // Remove elements outside window
        while (auxStructure.NotEmpty() && auxStructure.Front() < i - k + 1)
            auxStructure.RemoveFront();
        
        // Add current element (with auxiliary logic)
        AddToAuxStructure(auxStructure, nums[i], i);
        
        // Collect result once window is full
        if (i >= k - 1)
            result.Add(GetResultFromStructure(auxStructure));
    }
    
    return result.ToArray();
}
```

### **Optimized Solution:**
```csharp
int[] MaxSlidingWindow(int[] nums, int k) 
{
    var deque = new LinkedList<int>(); // Stores indices
    var result = new List<int>();
    
    for (int i = 0; i < nums.Length; i++)
    {
        // Remove indices outside current window [i-k+1, i]
        while (deque.Count > 0 && deque.First.Value < i - k + 1)
            deque.RemoveFirst();
        
        // Remove elements smaller than current (they can't be max)
        while (deque.Count > 0 && nums[deque.Last.Value] < nums[i])
            deque.RemoveLast();
        
        // Add current index
        deque.AddLast(i);
        
        // Once window is full, record max (front of deque)
        if (i >= k - 1)
            result.Add(nums[deque.First.Value]);
    }
    
    return result.ToArray();
}
```

### **Changes from Skeleton:**
1. **auxStructure** → `LinkedList<int>` (deque storing **indices**, not values)
2. **RemoveFront** → Remove indices outside window bounds
3. **AddToAuxStructure** → Maintain monotonic decreasing by removing dominated elements
4. **GetResultFromStructure** → `nums[deque.First.Value]` (value at front index)
5. **Window full check**: `i >= k - 1` before collecting results

### **Optimized Value Trace (nums = [1,3,-1,-3,5,3,6,7], k = 3):**

| i | nums[i] | Before Deque | Remove Dominated            | After Add | Window Valid? | Max (front) | result           |
|---|---------|--------------|-----------------------------|-----------| --------------|-------------|------------------|
| 0 | 1       | []           | -                           | [0]       | ❌ (size 1)   | -           | []               |
| 1 | 3       | [0]          | 3 > nums[0]=1 → remove 0   | [1]       | ❌ (size 2)   | -           | []               |
| 2 | -1      | [1]          | -1 < nums[1]=3 → keep      | [1,2]     | ✅ (size 3)   | nums[1]=3   | [3]              |
| 3 | -3      | [1,2]        | -3 < nums[2]=-1 → keep     | [1,2,3]   | ✅            | nums[1]=3   | [3,3]            |
| 4 | 5       | [1,2,3]      | 5 > all → remove 1,2,3     | [4]       | ✅            | nums[4]=5   | [3,3,5]          |
|   |         |              | Remove idx 1 (out of window)| [4]       |               |             |                  |
| 5 | 3       | [4]          | 3 < nums[4]=5 → keep       | [4,5]     | ✅            | nums[4]=5   | [3,3,5,5]        |
| 6 | 6       | [4,5]        | 6 > nums[5]=3 → remove 5   | [4,6]     | ✅            | nums[6]=6   | [3,3,5,5,6]      |
|   |         |              | 6 > nums[4]=5 → remove 4   | [6]       |               |             |                  |
| 7 | 7       | [6]          | 7 > nums[6]=6 → remove 6   | [7]       | ✅            | nums[7]=7   | [3,3,5,5,6,7]    |

**Detailed Step 4 (i=4, element=5):**
```
Current deque indices: [1,2,3] → values [3,-1,-3]
New element: nums[4] = 5

Removal phase (maintain decreasing):
  5 > nums[3]=-3? YES → remove 3 → deque=[1,2]
  5 > nums[2]=-1? YES → remove 2 → deque=[1]
  5 > nums[1]=3? YES → remove 1 → deque=[]

Window boundary check:
  Index 1 < 4-3+1=2? YES → would remove 1 (but already gone)

Add phase:
  deque=[4] → values [5]

Result: nums[deque.First.Value] = nums[4] = 5
```

**Result: [3,3,5,5,6,7]**

### **Deque State Visualization:**

```
i=0: deque=[0]           values=[1]
i=1: deque=[1]           values=[3]       ← 1 dominated by 3
i=2: deque=[1,2]         values=[3,-1]    ← -1 < 3, keep both
i=3: deque=[1,2,3]       values=[3,-1,-3] ← all decreasing
i=4: deque=[4]           values=[5]       ← 5 dominates everything
i=5: deque=[4,5]         values=[5,3]     ← 3 < 5, keep both
i=6: deque=[6]           values=[6]       ← 6 dominates 5,3
i=7: deque=[7]           values=[7]       ← 7 dominates 6
```

### **State Space Comparison:**

| Approach | Windows | Operations per Window | Total Operations |
|----------|---------|----------------------|------------------|
| Brute Force | 6 | O(k)=3 scans | O(n×k) ≈ 18 ops |
| Monotonic Deque | 6 | O(1) amortized | O(n) ≈ 8 ops |

**Why O(n) Total?**
- Each element enters deque once → n insertions
- Each element exits deque at most once → n deletions
- Total operations: 2n = O(n) ✅

### **Big-O Analysis:**
- **Time**: O(n) - each element enters/exits deque at most once
- **Space**: O(k) - deque holds at most k indices
- **Optimization**: O(n×k) → **O(n)** ✅

### **Key Takeaway:**
**Monotonic Deque** is a hybrid data structure for **range queries with ordering**:
- **Monotonic**: Maintains sorted order (increasing or decreasing)
- **Deque**: Allows efficient removal from both ends
- **Use case**: When you need min/max in sliding window and can discard dominated elements

This pattern appears in:
- Sliding window maximum/minimum
- Next greater/smaller element
- Stock span problem

---

## **Related Problems:**
- [Maximum Sum of Size K](variant_2_max_sum_size_k.md) - Fixed window, but sum (not max)
- Sliding Window Median - Use two heaps instead of deque
- Longest Continuous Subarray With Absolute Diff ≤ Limit - Two monotonic deques (min and max)

## **Tags:**
`sliding-window` `monotonic-deque` `hybrid` `array` `hard` `O(n)-state-space` `fixed-window` `range-query`
