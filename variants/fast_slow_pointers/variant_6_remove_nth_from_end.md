# Variant #6: Remove Nth Node From End

**LeetCode #19 - Medium**

## Problem Statement
Given the head of a linked list, remove the nth node from the end of the list and return its head.

**Example:** `1→2→3→4→5`, n=2 → `1→2→3→5` (remove 4)

**Follow-up:** Can you do this in one pass?

## 4-Step Analysis Framework

### Step 1: State Space Derivation

### **Problem Visualization:**
```
Input: 1 → 2 → 3 → 4 → 5, n=2

Positions from end:
1 → 2 → 3 → 4 → 5
5th 4th 3rd 2nd 1st  (from end)

Remove 2nd from end (node 4):
Result: 1 → 2 → 3 → 5

Two-pass approach:
  Pass 1: Count length = 5
  Pass 2: Remove node at position (5-2) = 3 from start

One-pass with two pointers:
  
  Initial (both at dummy):
  dummy → 1 → 2 → 3 → 4 → 5 → null
  ↑
  fast, slow
  
  Move fast ahead by n=2 steps:
  dummy → 1 → 2 → 3 → 4 → 5 → null
  ↑            ↑
  slow        fast
  
  Now move both until fast reaches end:
  dummy → 1 → 2 → 3 → 4 → 5 → null
                 ↑            ↑
               slow        fast
  
  slow.next is the node to remove!
  slow.next = slow.next.next
```

### **Core Question:**
How to find nth node from end in one pass?

### **Deriving from First Principles:**

**Observation 1:** Relationship between positions
- nth from end = (length - n) from start
- Example: n=2, length=5 → 3rd from start
- But we don't know length without counting!

**Observation 2:** Two-pointer gap
- Use two pointers with fixed gap of n nodes
- When front pointer reaches end, back pointer is at (n-1)th from end
- The node to remove is back.next (nth from end)

**Observation 3:** Implementation steps
1. Move fast pointer n steps ahead
2. Move both pointers until fast reaches end
3. Gap between them stays n
4. When fast at end, slow is at (n-1)th from end
5. Remove slow.next

**Observation 4:** Edge case - removing head
- What if n = length? (remove first node)
- Solution: Use dummy node before head
- Dummy helps handle edge case uniformly

**Observation 5:** Why this works (mathematical proof)
```
Let L = list length, n = position from end

After initial gap creation:
  fast at position: n
  slow at position: 0
  gap = n

Move both until fast reaches end:
  fast at position: L
  slow moved: L - n steps
  slow at position: L - n

Position from end = L - (L - n) = n
So slow.next is the nth node from end!
```

### **State Space Structure:**

**Type:** Find and remove node at position n from end  
**Structure:** Linear linked list with removal operation  
**Cardinality:** n possible positions from end (1st from end to nth from end)

**State Space:**
```
List: 1→2→3→4→5, n=2
Position from end: 5th, 4th, 3rd, 2nd←(target), 1st
Need to remove: node with value 4 (2nd from end)
```

---

### Step 2: Brute Force with Value Tracing

**Approach:** Two passes - count length, then remove

```csharp
public ListNode RemoveNthFromEnd_BruteForce(ListNode head, int n)
{
    // Pass 1: Count length
    int length = 0;
    ListNode current = head;
    while (current != null)
    {
        length++;
        current = current.next;
    }
    
    // Pass 2: Remove node at position (length - n)
    // Use dummy node to handle edge case of removing head
    ListNode dummy = new ListNode(0);
    dummy.next = head;
    current = dummy;
    
    int stepsToTarget = length - n;
    for (int i = 0; i < stepsToTarget; i++)
    {
        current = current.next;
    }
    
    // Remove the node
    current.next = current.next.next;
    
    return dummy.next;
}
```

**Value Trace for:** `1→2→3→4→5`, n=2

| Pass | Step | current | Action | Result |
|------|------|---------|--------|--------|
| 1 | 0-4 | traverse | Count | length=5 |
| 2 | 0 | dummy | stepsToTarget=5-2=3 | - |
| 2 | 1-3 | 1→2→3 | Move to node 3 | current=3 |
| 2 | Remove | 3 | 3.next=3.next.next | 3→5 (skip 4) ✅ |

**Complexity:**
- **Time:** O(n) - Two passes
- **Space:** O(1)

---

### Step 3: Pruning Analysis - Can We Do Better?

**Key Observations:**

1. **Two passes needed:** Count, then traverse to (length - n)
2. **One-pass challenge:** How to know "length - n" position without counting first?
3. **Gap strategy:** Use two pointers with n-node gap between them
4. **When fast reaches end, slow is at (length - n)!**

**Mathematical insight:**
```
fast pointer at position: length
slow pointer at position: length - gap

If gap = n:
slow position = length - n ✅ (target position)
```

**Visual:**
```
Gap of n=2:
fast → → → → → null
       ↓ (2 steps behind)
       slow

When fast reaches end, slow is n steps from end!
```

**Decision:** ✅ Use two pointers with n-node gap for one-pass solution!

---

### Step 4: Optimal Solution with Skeleton Transformation

```csharp
public ListNode RemoveNthFromEnd_Optimal(ListNode head, int n)
{
    ListNode dummy = new ListNode(0);
    dummy.next = head;
    
    ListNode fast = dummy;
    ListNode slow = dummy;
    
    // Move fast pointer n+1 steps ahead
    for (int i = 0; i <= n; i++)
    {
        fast = fast.next;
    }
    
    // Move both until fast reaches end
    while (fast != null)
    {
        slow = slow.next;
        fast = fast.next;
    }
    
    // Remove the node
    slow.next = slow.next.next;
    
    return dummy.next;
}
```

**Skeleton Transformation:**
```
BRUTE FORCE:
├── Pass 1: count length
├── Pass 2: traverse to (length - n)
└── remove node

         ↓

OPTIMAL (Two Pointers with Gap):
├── dummy node (handle edge cases)
├── fast = slow = dummy
├── Move fast n+1 steps ahead
├── Move both together until fast reaches end
├── slow is now at node BEFORE target
└── remove slow.next

KEY CHANGES:
- Two passes → One pass
- Explicit length → Implicit via pointer gap
- Count then traverse → Gap maintenance
```

**Value Trace for:** `1→2→3→4→5`, n=2

**Phase 1: Create n+1 gap (n=2, so 3 steps)**

| Step | fast | slow | Gap |
|------|------|------|-----|
| 0 | dummy | dummy | 0 |
| 1 | 1 | dummy | 1 |
| 2 | 2 | dummy | 2 |
| 3 | 3 | dummy | 3 |

**Phase 2: Move together until fast reaches end**

| Step | fast | slow | Action |
|------|------|------|--------|
| 1 | 4 | 1 | Move both |
| 2 | 5 | 2 | Move both |
| 3 | null | 3 | fast==null, stop |

**Phase 3: Remove**
```
slow = 3
slow.next = 4 (target to remove)
slow.next = slow.next.next = 5
Result: 1→2→3→5 ✅
```

---

## Big-O Analysis

**Brute Force:**
- **Time:** O(n) - Two passes: count + traverse
- **Space:** O(1)

**Optimal:**
- **Time:** O(n) - Single pass with gap maintenance
- **Space:** O(1)

**Key Advantage:** Two passes → One pass (better constant factor)

---

## Pattern Recognition

**When to use:**
- ✅ Position from end in linked list
- ✅ One-pass constraint
- ✅ Gap-based two-pointer technique
- ✅ Cannot traverse backward

**Why gap technique works:**
- Maintains constant distance between pointers
- When leading pointer reaches end, trailing pointer is at target
- No need to know list length upfront

---

## Edge Cases & Dummy Node Usage

**Why dummy node?**
- Handles case where head is removed (n = length)
- Simplifies code (no special case for head removal)
- slow.next always valid for removal

**Edge cases:**
1. **Remove head:** n = length, dummy.next updated
2. **Single node:** Remove only node, return null
3. **Two nodes, remove first:** Works via dummy
4. **Two nodes, remove last:** Works via gap

---

## Visualizing the Gap

```
Example: 1→2→3→4→5, n=2

Step 1: Create gap of 3 (n+1)
dummy→1→2→3→4→5→null
↑           ↑
slow        fast

Step 2: Move together
dummy→1→2→3→4→5→null
      ↑        ↑
      slow   fast

Step 3: Move together (fast reaches null)
dummy→1→2→3→4→5→null
          ↑       ↑
          slow  fast(null)

Step 4: Remove slow.next (node 4)
dummy→1→2→3→5→null
```

---

## Related Problems

- **Find Middle** (LeetCode #876) - Similar two-pointer technique
- **Linked List Cycle** (LeetCode #141) - Different speed relationship
- **Reorder List** (LeetCode #143) - Combines multiple techniques
