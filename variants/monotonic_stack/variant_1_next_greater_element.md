# Variant #1: Next Greater Element I

**LeetCode #496 - Easy**

## Problem Statement
Given two integer arrays `nums1` and `nums2` where `nums1` is a subset of `nums2`, find the **next greater element** for each element of `nums1` in `nums2`.

The next greater element of `nums1[i]` in `nums2` is the first greater element to its right in `nums2`. If it doesn't exist, return -1 for this number.

**Example:** `nums1 = [4,1,2]`, `nums2 = [1,3,4,2]` → `[-1,3,-1]`

## 4-Step Analysis Framework

### Step 1: State Space Derivation

### **Problem Visualization:**
```
Input: nums1 = [4,1,2], nums2 = [1,3,4,2]

For each element in nums1, find next greater in nums2:

nums2:  1   3   4   2
        ↑   
Find for 1: look right [3,4,2], first >1 is 3 ✓

nums2:  1   3   4   2
            ↑   
Find for 3: look right [4,2], first >3 is 4 ✓

nums2:  1   3   4   2
                ↑   
Find for 4: look right [2], first >4 is none → -1

nums2:  1   3   4   2
                    ↑
Find for 2: look right [], none → -1

Answer mapping:
  4 → -1
  1 → 3
  2 → -1
Result: [-1, 3, -1]

Brute force: For each in nums1, find in nums2, scan right O(n×m)

Monotonic stack insight:
  Build next greater mapping for ALL nums2 elements: O(n)
  
  Stack maintains decreasing sequence:
  
  Process nums2 left to right:
  [1]: stack=[1]
  [3]: 3>1, so 1's next greater is 3, stack=[3]
  [4]: 4>3, so 3's next greater is 4, stack=[4]
  [2]: 2<4, push, stack=[4,2]
  
  Result map: {1:3, 3:4, 4:-1, 2:-1}
```

### **Core Question:**
How to find next greater element efficiently for multiple queries?

### **Deriving from First Principles:**

**Observation 1:** Naive approach per element
- For element x at position i:
  - Scan right: positions [i+1, n-1]
  - Find first element > x
- Time: O(n) per element
- For m elements in nums1: O(m × n)

**Observation 2:** Precompute for all elements
- Instead of querying individually, build mapping for entire nums2
- Store: {element → next greater element}
- Query becomes O(1) lookup
- But how to build this mapping efficiently?

**Observation 3:** Monotonic stack pattern
```
Key insight: Elements waiting for next greater form DECREASING sequence!

Example: nums2 = [5, 3, 7, ...]
  When we reach 7:
    - 7 is next greater for both 5 and 3
    - But we process them in reverse order: 3 first, then 5
    - Stack helps maintain this order: [5, 3] (top to bottom)
    - Pop 3: 3's next is 7
    - Pop 5: 5's next is 7
```

**Observation 4:** Stack maintains candidates
- **Stack property:** Elements in DECREASING order (top to bottom)
- When we see element x:
  - While stack.top < x: pop and record (x is their next greater)
  - Push x onto stack (it may find next greater later)
- **Why decreasing?** If a < b, and both waiting, when we find c > b:
  - c > b automatically means c > a
  - So b should be processed first (it's "closer" to blocking smaller elements)

**Observation 5:** Algorithm flow
```
1. Process nums2 left to right with monotonic stack
2. Build map: {element → next greater}
3. For elements still in stack: map to -1 (no next greater)
4. Answer nums1 queries using the map
```

### **State Space Structure:**

**Type:** For each element, find next greater element to the right  
**Structure:** Map from element value to next greater value  
**Cardinality:** At most n mappings for nums2 elements  
**Query complexity:** O(1) after O(n) preprocessing

**Example Computation:**
For `nums2 = [1, 3, 4, 2]`:

```
Stack simulation (store actual values):

Process 1:
  stack=[] empty
  push 1: stack=[1]

Process 3:
  stack=[1], top=1
  3 > 1: pop 1, map {1: 3}
  push 3: stack=[3]

Process 4:
  stack=[3], top=3
  4 > 3: pop 3, map {1:3, 3:4}
  push 4: stack=[4]

Process 2:
  stack=[4], top=4
  2 < 4: can't pop
  push 2: stack=[4, 2]

Remaining in stack: [4, 2] → no next greater
  map {1:3, 3:4, 4:-1, 2:-1}

Query nums1=[4,1,2]:
  4 → -1
  1 → 3
  2 → -1
  
Result: [-1, 3, -1]
```

### **Generation Pattern (Naive):**
```csharp
int[] NextGreaterElementNaive(int[] nums1, int[] nums2)
{
    int[] result = new int[nums1.Length];
    
    for (int i = 0; i < nums1.Length; i++)
    {
        int target = nums1[i];
        
        // Find target in nums2
        int pos = -1;
        for (int j = 0; j < nums2.Length; j++)
        {
            if (nums2[j] == target)
            {
                pos = j;
                break;
            }
        }
        
        // Find next greater after pos
        result[i] = -1;
        for (int j = pos + 1; j < nums2.Length; j++)
        {
            if (nums2[j] > target)
            {
                result[i] = nums2[j];
                break;
            }
        }
    }
    
    return result;
}
// Time: O(m × n), Space: O(1) extra
```

---

### Step 2: Brute Force with Value Tracing

**Approach:** For each element in nums1, find in nums2 and scan right

**Value Trace for:** `nums1 = [4,1,2]`, `nums2 = [1,3,4,2]`

**Finding next greater for 4:**
- Find 4 in nums2: index 2
- Scan right: [2]
- 2 < 4 ✗
- Result: -1

**Finding next greater for 1:**
- Find 1 in nums2: index 0
- Scan right: [3,4,2]
- 3 > 1 ✓
- Result: 3

**Finding next greater for 2:**
- Find 2 in nums2: index 3
- Scan right: []
- Result: -1

**Complexity:**
- Time: O(m × n) - for each of m elements, scan up to n positions
- Space: O(1) extra

---

### Step 3: Pruning Analysis - Can We Do Better?

**Key Insight:** Use monotonic decreasing stack to build next greater map

**Why stack works:**
```
Stack maintains elements in decreasing order.
When we see larger element, it "closes" all smaller waiting elements.

Example: [5, 3, 7]
  At 5: stack=[5]
  At 3: 3<5, stack=[5,3] (decreasing ✓)
  At 7: 7>3, pop 3 (3's next=7)
        7>5, pop 5 (5's next=7)
        stack=[7]
```

**Transformation:** O(m × n) → O(m + n)
- Build map for nums2: O(n) with stack
- Query nums1: O(m) lookups
- Total: O(m + n)

---

### Step 4: Optimal Solution

```csharp
// BEFORE: Naive O(m × n) approach
int[] NextGreaterElement_BruteForce(int[] nums1, int[] nums2)
{
    int[] result = new int[nums1.Length];
    
    for (int i = 0; i < nums1.Length; i++)  // O(m)
    {
        int target = nums1[i];
        int pos = Array.IndexOf(nums2, target);  // O(n)
        
        result[i] = -1;
        for (int j = pos + 1; j < nums2.Length; j++)  // O(n)
        {
            if (nums2[j] > target)
            {
                result[i] = nums2[j];
                break;
            }
        }
    }
    
    return result;
}

// AFTER: Monotonic stack O(m + n)
int[] NextGreaterElement(int[] nums1, int[] nums2)
{
    // Build next greater map for nums2
    var nextGreater = new Dictionary<int, int>();
    var stack = new Stack<int>();
    
    // Process nums2 left to right
    foreach (int num in nums2)
    {
        // Pop smaller elements (num is their next greater)
        while (stack.Count > 0 && stack.Peek() < num)
        {
            nextGreater[stack.Pop()] = num;
        }
        
        stack.Push(num);  // num waiting for its next greater
    }
    
    // Elements remaining in stack have no next greater
    while (stack.Count > 0)
    {
        nextGreater[stack.Pop()] = -1;
    }
    
    // Build result for nums1
    int[] result = new int[nums1.Length];
    for (int i = 0; i < nums1.Length; i++)
    {
        result[i] = nextGreater[nums1[i]];
    }
    
    return result;
}
```

**Complexity:**
- **Time:** O(m + n) - O(n) to build map, O(m) to query
- **Space:** O(n) for stack and map

**Execution Trace for nums2=[1,3,4,2]:**

```
stack=[], map={}

num=1:
  stack empty, push 1
  stack=[1]

num=3:
  3 > stack.peek(1): pop 1, map={1:3}
  push 3, stack=[3]

num=4:
  4 > stack.peek(3): pop 3, map={1:3, 3:4}
  push 4, stack=[4]

num=2:
  2 < stack.peek(4): don't pop
  push 2, stack=[4,2]

Cleanup remaining:
  pop 2: map={1:3, 3:4, 2:-1}
  pop 4: map={1:3, 3:4, 2:-1, 4:-1}

Query nums1=[4,1,2]:
  result = [-1, 3, -1]
```

---

## Edge Cases

1. **No next greater:** `[5,4,3,2,1]` → all -1
2. **All ascending:** `[1,2,3,4,5]` → each finds next
3. **Single element:** `[1]` → -1
4. **Duplicates:** Problem states all elements unique

---

## Related Problems

- **LeetCode #503:** Next Greater Element II (circular array)
- **LeetCode #556:** Next Greater Element III
- **LeetCode #739:** Daily Temperatures (similar pattern)

---

## Tags
`#monotonic-stack` `#stack` `#array` `#next-greater` `#easy` `#decreasing-stack`
