# Merge K Sorted Lists

**Difficulty:** Hard  
**LeetCode Problem:** #23  
**Tags:** `Heap`, `Linked List`, `Divide and Conquer`, `Merge`

---

## Problem Statement

You are given an array of `k` linked-lists `lists`, each linked-list is sorted in ascending order.

Merge all the linked-lists into one sorted linked-list and return it.

**Constraints:**
- `k == lists.length`
- `0 <= k <= 10^4`
- `0 <= lists[i].length <= 500`
- `-10^4 <= lists[i][j] <= 10^4`
- `lists[i]` is sorted in ascending order.
- The sum of `lists[i].length` will not exceed `10^4`.

**Example 1:**
```
Input: lists = [[1,4,5],[1,3,4],[2,6]]
Output: [1,1,2,3,4,4,5,6]
Explanation: The linked-lists are:
[
  1->4->5,
  1->3->4,
  2->6
]
merging them into one sorted list:
1->1->2->3->4->4->5->6
```

**Example 2:**
```
Input: lists = []
Output: []
```

**Example 3:**
```
Input: lists = [[]]
Output: []
```

---

## Step 1: State Space

### Problem Visualization

Lists: `[[1,4,5], [1,3,4], [2,6]]`

```
List 0: 1 -> 4 -> 5
List 1: 1 -> 3 -> 4
List 2: 2 -> 6

Merge process (using min-heap):

Initial heap (heads of lists):
  Heap: [(1, list0), (1, list1), (2, list2)]
  
Step 1: Pop min (1, list0)
  Result: 1 ->
  Advance list0: 4 -> 5
  Heap: [(1, list1), (2, list2), (4, list0)]

Step 2: Pop min (1, list1)
  Result: 1 -> 1 ->
  Advance list1: 3 -> 4
  Heap: [(2, list2), (3, list1), (4, list0)]

Step 3: Pop min (2, list2)
  Result: 1 -> 1 -> 2 ->
  Advance list2: 6
  Heap: [(3, list1), (4, list0), (6, list2)]

Step 4: Pop min (3, list1)
  Result: 1 -> 1 -> 2 -> 3 ->
  Advance list1: 4
  Heap: [(4, list0), (4, list1), (6, list2)]

Step 5: Pop min (4, list0)
  Result: 1 -> 1 -> 2 -> 3 -> 4 ->
  Advance list0: 5
  Heap: [(4, list1), (5, list0), (6, list2)]

Step 6: Pop min (4, list1)
  Result: 1 -> 1 -> 2 -> 3 -> 4 -> 4 ->
  list1 exhausted
  Heap: [(5, list0), (6, list2)]

Step 7: Pop min (5, list0)
  Result: 1 -> 1 -> 2 -> 3 -> 4 -> 4 -> 5 ->
  list0 exhausted
  Heap: [(6, list2)]

Step 8: Pop min (6, list2)
  Result: 1 -> 1 -> 2 -> 3 -> 4 -> 4 -> 5 -> 6
  list2 exhausted
  Heap: []

Final: 1 -> 1 -> 2 -> 3 -> 4 -> 4 -> 5 -> 6
```

### Core Question

**How can we efficiently merge k sorted lists into one sorted list?**

At each step, we need the minimum among k candidates (current head of each list).

### Deriving from First Principles

**Observation 1: Merge two sorted lists**
```
Merging 2 lists: O(n₁ + n₂)
Use two pointers, pick smaller at each step
```

**Observation 2: Extend to k lists - naive**
```
At each step, scan all k list heads for minimum: O(k)
Total: O(n * k) where n = total nodes
Too slow when k is large
```

**Observation 3: Min-heap optimization**
```
Maintain min-heap of current heads (size ≤ k)
- Extract min: O(log k)
- Add next node: O(log k)
- Total operations: n
- Total time: O(n log k)

Much better when k is large!
```

**Observation 4: Heap structure**
```
Heap stores: (node_value, node_reference)
Always contains current head of each non-empty list
Pop min gives globally smallest among all lists
```

**Observation 5: Alternative - Divide and Conquer**
```
Merge lists pairwise:
Round 1: k lists → k/2 merged lists
Round 2: k/2 lists → k/4 merged lists
...
Log k rounds, each processes all n nodes
Time: O(n log k)
```

**Formula (Min-Heap):**
```
heap = min-heap
for each list in lists:
  if list not empty:
    heap.push((list.val, list))

result = dummy_head
tail = dummy_head

while heap not empty:
  (val, node) = heap.pop()
  tail.next = node
  tail = tail.next
  
  if node.next:
    heap.push((node.next.val, node.next))

return dummy_head.next
```

### State Space Structure

**Type:** Multi-way merge using min-heap.

**Structure:**
- Min-heap of size ≤ k
- Each element: (value, node)
- Result: linked list built incrementally

**Cardinality:**
- n = total nodes across all lists
- k = number of lists
- Heap operations: n times
- Each operation: O(log k)
- Total: O(n log k)
- Space: O(k) for heap

**Key Property:** Heap maintains smallest unprocessed node among all lists.

### Example Computation

Lists: `[[1,4,5], [1,3,4], [2,6]]`

```
Initialize heap with heads:
  heap = [(1, node₀), (1, node₁), (2, node₂)]
  result = []

Iteration 1:
  Pop (1, node₀) from heap
  result = [1]
  node₀.next = 4, push (4, node₀')
  heap = [(1, node₁), (2, node₂), (4, node₀')]

Iteration 2:
  Pop (1, node₁) from heap
  result = [1, 1]
  node₁.next = 3, push (3, node₁')
  heap = [(2, node₂), (3, node₁'), (4, node₀')]

Iteration 3:
  Pop (2, node₂) from heap
  result = [1, 1, 2]
  node₂.next = 6, push (6, node₂')
  heap = [(3, node₁'), (4, node₀'), (6, node₂')]

Continue until heap empty...
Final result = [1, 1, 2, 3, 4, 4, 5, 6]
```

### Generation Pattern

**Brute force:**
```csharp
// Collect all nodes in array: O(n)
// Sort array: O(n log n)
// Rebuild linked list: O(n)
// Total: O(n log n)
```

---

## Step 2: Brute Force

```csharp
public ListNode MergeKLists(ListNode[] lists) {
    var allNodes = new List<int>();
    
    // Collect all values
    foreach (var list in lists) {
        var curr = list;
        while (curr != null) {
            allNodes.Add(curr.val);
            curr = curr.next;
        }
    }
    
    // Sort all values
    allNodes.Sort();
    
    // Build result list
    var dummy = new ListNode(0);
    var tail = dummy;
    foreach (int val in allNodes) {
        tail.next = new ListNode(val);
        tail = tail.next;
    }
    
    return dummy.next;
}
```

**Complexity:**
- Time: O(n log n) - sorting dominates
- Space: O(n) - store all nodes

**Problem:** Doesn't leverage that lists are already sorted!

---

## Step 3: Optimization Ideas

### Key Insights
1. **Lists already sorted:** Should leverage this
2. **Need minimum among k candidates:** Min-heap perfect for this
3. **K is small relative to n:** O(n log k) better than O(n log n)
4. **Alternative:** Divide and conquer merging

---

## Step 4: Optimal Solution (Min-Heap)

```csharp
public class Solution {
    public ListNode MergeKLists(ListNode[] lists) {
        if (lists == null || lists.Length == 0) {
            return null;
        }
        
        // Min-heap: priority queue ordered by node value
        var heap = new PriorityQueue<ListNode, int>();
        
        // Initialize heap with head of each list
        foreach (var list in lists) {
            if (list != null) {
                heap.Enqueue(list, list.val);
            }
        }
        
        // Build result list
        var dummy = new ListNode(0);
        var tail = dummy;
        
        while (heap.Count > 0) {
            // Get node with minimum value
            var node = heap.Dequeue();
            
            // Add to result
            tail.next = node;
            tail = tail.next;
            
            // Add next node from same list to heap
            if (node.next != null) {
                heap.Enqueue(node.next, node.next.val);
            }
        }
        
        return dummy.next;
    }
}
```

**Complexity:**
- Time: O(n log k)
  - n total nodes
  - Each node: O(log k) heap operation
  - k = number of lists
- Space: O(k) - heap size

---

## Alternative: Divide and Conquer

Merge lists pairwise in rounds:

```csharp
public ListNode MergeKLists(ListNode[] lists) {
    if (lists == null || lists.Length == 0) {
        return null;
    }
    
    return MergeHelper(lists, 0, lists.Length - 1);
}

private ListNode MergeHelper(ListNode[] lists, int left, int right) {
    if (left == right) {
        return lists[left];
    }
    
    if (left > right) {
        return null;
    }
    
    int mid = left + (right - left) / 2;
    ListNode leftList = MergeHelper(lists, left, mid);
    ListNode rightList = MergeHelper(lists, mid + 1, right);
    
    return MergeTwoLists(leftList, rightList);
}

private ListNode MergeTwoLists(ListNode l1, ListNode l2) {
    var dummy = new ListNode(0);
    var tail = dummy;
    
    while (l1 != null && l2 != null) {
        if (l1.val <= l2.val) {
            tail.next = l1;
            l1 = l1.next;
        } else {
            tail.next = l2;
            l2 = l2.next;
        }
        tail = tail.next;
    }
    
    tail.next = l1 ?? l2;
    return dummy.next;
}
```

**Complexity:**
- Time: O(n log k)
  - Log k levels of merging
  - Each level processes all n nodes
- Space: O(log k) - recursion stack

**Comparison:**
- **Min-Heap:** Simpler, good when k is large
- **Divide & Conquer:** No heap needed, good cache locality

---

## Edge Cases

1. **Empty input:** `lists = []` → `null`
2. **All empty lists:** `lists = [[], [], []]` → `null`
3. **Single list:** `lists = [[1,2,3]]` → `[1,2,3]`
4. **Two lists:** Same as merge two sorted lists
5. **Lists of different lengths:** Handle naturally

---

## Visualization

```
Heap approach with 3 lists:

Initial:
  L0: 1 -> 4 -> 5
  L1: 1 -> 3 -> 4
  L2: 2 -> 6
  
  Heap: [1₀, 1₁, 2₂]
  Result: []

Step 1: Extract 1₀
  Heap: [1₁, 2₂, 4₀]
  Result: 1 ->

Step 2: Extract 1₁
  Heap: [2₂, 3₁, 4₀]
  Result: 1 -> 1 ->

Step 3: Extract 2₂
  Heap: [3₁, 4₀, 6₂]
  Result: 1 -> 1 -> 2 ->

Continue until heap empty...
```

---

## Related Problems

1. **Merge Two Sorted Lists (LeetCode #21)** - Base case
2. **Merge Sorted Array (LeetCode #88)** - Array variant
3. **Find K Pairs with Smallest Sums (LeetCode #373)** - Similar heap pattern
4. **Smallest Range Covering K Lists (LeetCode #632)** - Multi-list processing

---

## Pattern Recognition

**Problem Asks For:**
- Merge multiple sorted sequences
- Maintain global sorted order
- Efficient merging

**This Suggests:**
- Need minimum among k candidates
- Min-heap for O(log k) access
- Or divide and conquer
- → **Min-Heap Multi-Way Merge**

**Key Indicators:**
- "Merge k sorted..."
- Multiple sorted inputs
- Need global ordering

---

## Tags

`#heap` `#min-heap` `#linked-list` `#merge` `#divide-and-conquer` `#multi-way-merge`
