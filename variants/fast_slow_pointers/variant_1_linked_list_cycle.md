# Variant #1: Linked List Cycle (Detect)

**LeetCode #141 - Easy**

## Problem Statement
Given `head`, the head of a linked list, determine if the linked list has a cycle in it.

A cycle exists if there is some node in the list that can be reached again by continuously following the `next` pointer.

## 4-Step Analysis Framework

### Step 1: State Space Derivation

### **Problem Visualization:**
```
Case 1: No Cycle
1 → 2 → 3 → 4 → null
Traversal ends at null

Case 2: Cycle Exists
1 → 2 → 3 → 4 → 5
         ↑           ↓
         └─────────┘
Traversal path: 1 → 2 → 3 → 4 → 5 → 3 → 4 → 5 → 3 ... (infinite)

With two pointers moving at different speeds:

Step 0:  slow=1, fast=1
Step 1:  slow=2, fast=3  (fast moves 2, slow moves 1)
Step 2:  slow=3, fast=5
Step 3:  slow=4, fast=4  ← They meet! Cycle detected
```

### **Core Question:**
How to detect if a linked list has a cycle without using O(n) extra space?

### **Deriving from First Principles:**

**Observation 1:** Naive approach with hash set
- Track all visited nodes in a HashSet
- If we revisit a node → cycle detected
- If we reach null → no cycle
- Space: O(n) for the hash set

**Observation 2:** Can we detect cycle with O(1) space?
- Need different approach - cannot store all visited nodes
- Key insight: If there's a cycle, any traversal will loop forever
- Use **two pointers** moving at different speeds

**Observation 3:** Floyd's Cycle Detection (Tortoise & Hare)
- **slow pointer:** moves 1 step at a time
- **fast pointer:** moves 2 steps at a time
- If no cycle: fast reaches null first
- If cycle exists: fast will eventually "lap" slow and meet it

**Why they must meet in a cycle:**
```
Once both pointers enter the cycle:
- Gap between them decreases by 1 each step
- Fast gains 1 position on slow every iteration
- Eventually gap becomes 0 → they meet

Math proof:
Let cycle length = C
When slow enters cycle, fast is k positions ahead (0 ≤ k < C)
After t steps: 
  slow position: t mod C
  fast position: (k + 2t) mod C
They meet when: t ≡ k + 2t (mod C)
              ⇒ t ≡ -k (mod C)
              ⇒ t ≡ C - k (mod C)
So they meet after at most C steps inside the cycle.
```

### **State Space Structure:**

**Type:** Cycle detection in linked list  
**Structure:** Path through linked list nodes until null or cycle detected  
**Cardinality:** Up to n nodes to visit, potentially infinite if cycle exists

**State Space Example:**
For a linked list with cycle: `1 → 2 → 3 → 4 → 2 (cycle back)`

```
Nodes to visit: [1, 2, 3, 4, 2, 3, 4, 2, 3, 4, ...]
- Without cycle detection: Infinite loop
- With cycle detection: Visit each node at most twice
```

**Generation Pattern:**
```csharp
void ExploreAllPaths(ListNode head)
{
    ListNode current = head;
    var visited = new HashSet<ListNode>();
    
    // Traverse until end or revisit
    while (current != null)
    {
        if (visited.Contains(current))
        {
            // Cycle detected!
            return true;
        }
        visited.Add(current);
        current = current.next;
    }
    
    return false; // No cycle
}
```

---

### Step 2: Brute Force with Value Tracing

**Approach:** Track all visited nodes in a HashSet. If we encounter a node we've seen before, there's a cycle.

```csharp
public bool HasCycle_BruteForce(ListNode head)
{
    var visited = new HashSet<ListNode>();
    ListNode current = head;
    
    while (current != null)
    {
        // If already visited, cycle exists
        if (visited.Contains(current))
            return true;
        
        visited.Add(current);
        current = current.next;
    }
    
    return false; // Reached end, no cycle
}
```

**Value Trace for list:** `1 → 2 → 3 → 4 → 2` (cycle)

| Step | current | visited | Action | Result |
|------|---------|---------|--------|--------|
| 0 | 1 | {} | Add 1 | {} → {1} |
| 1 | 2 | {1} | Add 2 | {1} → {1,2} |
| 2 | 3 | {1,2} | Add 3 | {1,2} → {1,2,3} |
| 3 | 4 | {1,2,3} | Add 4 | {1,2,3} → {1,2,3,4} |
| 4 | 2 | {1,2,3,4} | **Found in set!** | **Return true** |

**Value Trace for list without cycle:** `1 → 2 → 3 → null`

| Step | current | visited | Action | Result |
|------|---------|---------|--------|--------|
| 0 | 1 | {} | Add 1 | {} → {1} |
| 1 | 2 | {1} | Add 2 | {1} → {1,2} |
| 2 | 3 | {1,2} | Add 3 | {1,2} → {1,2,3} |
| 3 | null | {1,2,3} | **Exit loop** | **Return false** |

**Complexity:**
- **Time:** O(n) - Visit each node once
- **Space:** O(n) - Store all visited nodes

---

### Step 3: Pruning Analysis - Can We Do Better?

**Key Observations:**

1. **Space bottleneck:** HashSet stores every visited node (O(n) space)
2. **Do we need to remember ALL nodes?** No! We only need to detect if paths overlap
3. **Cycle property:** If there's a cycle, any two pointers entering it will eventually meet
4. **Different speeds:** Fast pointer (2x) will "lap" slow pointer (1x) inside cycle

**Mathematical Insight - Why Fast & Slow Work:**

If there's a cycle of length C:
- Slow pointer moves 1 step per iteration
- Fast pointer moves 2 steps per iteration
- **Relative speed:** Fast gains 1 step on slow per iteration
- Once both are in cycle, fast will catch slow in at most C iterations

**Proof sketch:**
```
Distance gap = k (some initial gap when both enter cycle)
Each iteration: gap decreases by 1 (fast gains 1 step)
After k iterations: gap = 0 → they meet!
```

**Decision:** ✅ We can eliminate O(n) space by using two pointers at different speeds!

---

### Step 4: Optimal Solution with Skeleton Transformation

**Algorithm: Floyd's Cycle Detection (Tortoise and Hare)**

```csharp
public bool HasCycle_Optimal(ListNode head)
{
    // Edge case: empty or single node
    if (head == null || head.next == null)
        return false;
    
    // Initialize two pointers
    ListNode slow = head;
    ListNode fast = head;
    
    // Move pointers at different speeds
    while (fast != null && fast.next != null)
    {
        slow = slow.next;        // Move 1 step
        fast = fast.next.next;   // Move 2 steps
        
        // If they meet, cycle exists
        if (slow == fast)
            return true;
    }
    
    // Fast reached end, no cycle
    return false;
}
```

**Skeleton Transformation:**

```
BRUTE FORCE SKELETON:
├── visited = HashSet<ListNode>()
├── while (current != null):
│   ├── if current in visited: return true
│   └── visited.add(current)
└── return false

                    ↓ TRANSFORM ↓

OPTIMAL SKELETON:
├── slow = head, fast = head
├── while (fast != null && fast.next != null):
│   ├── slow = slow.next (1 step)
│   ├── fast = fast.next.next (2 steps)
│   └── if slow == fast: return true
└── return false

KEY CHANGES:
- Remove HashSet → Two pointers
- Single traversal → Two speeds (1x, 2x)
- O(n) space → O(1) space
```

**Value Trace for list with cycle:** `1 → 2 → 3 → 4 → 2`

| Iter | slow | fast | slow.next | fast.next.next | Meet? |
|------|------|------|-----------|----------------|-------|
| 0 | 1 | 1 | - | - | No (initial) |
| 1 | 2 | 3 | 2 | 3 | No |
| 2 | 3 | 2 | 3 | 2 | No |
| 3 | 4 | 4 | 4 | 4 | **Yes!** ✅ |

**Value Trace for list without cycle:** `1 → 2 → 3 → null`

| Iter | slow | fast | slow.next | fast.next.next | Meet? |
|------|------|------|-----------|----------------|-------|
| 0 | 1 | 1 | - | - | No (initial) |
| 1 | 2 | 3 | 2 | 3 | No |
| 2 | 3 | null | 3 | - | fast==null, exit |

---

## Big-O Analysis

**Brute Force (HashSet):**
- **Time:** O(n) - Visit each node once
- **Space:** O(n) - Store all visited nodes

**Optimal (Fast & Slow):**
- **Time:** O(n) - In worst case, fast traverses list twice before meeting slow
- **Space:** O(1) - Only two pointers

**Key Advantage:** Same time complexity but **O(n) → O(1) space reduction!**

---

## Pattern Recognition

**When to use Fast & Slow Pointers:**
- ✅ Cycle detection in linked list
- ✅ Need O(1) space constraint
- ✅ Can't modify data structure (no visited flags)
- ✅ Input is singly linked list (can't traverse backward)

**Why it works:**
- Different speeds guarantee meeting inside cycle
- Fast reaches end quickly if no cycle
- No extra space needed

---

## Edge Cases

1. **Empty list:** `head == null` → No cycle
2. **Single node:** `1 → null` → No cycle
3. **Two nodes with cycle:** `1 ⇄ 2` → Has cycle (fast.next would be null check needed)
4. **Self-loop:** `1 → 1` → Has cycle (detected immediately)
5. **Large cycle:** Works regardless of cycle size

---

## Related Problems

- **Linked List Cycle II** (LeetCode #142) - Find where cycle starts
- **Happy Number** (LeetCode #202) - Cycle detection in number sequence
- **Find Duplicate Number** (LeetCode #287) - Array as implicit linked list
