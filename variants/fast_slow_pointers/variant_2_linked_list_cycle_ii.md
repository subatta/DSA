# Variant #2: Linked List Cycle II (Find Start)

**LeetCode #142 - Medium**

## Problem Statement
Given the head of a linked list, return the node where the cycle begins. If there is no cycle, return `null`.

**Follow-up:** Can you solve it using O(1) space?

## 4-Step Analysis Framework

### Step 1: State Space Derivation

### **Problem Visualization:**
```
Linked list with cycle:

1 → 2 → 3 → 4 → 5 → 6
         ↑           ↓
         └─────────┘

Terminology:
- Head: node 1
- Cycle entry: node 3 ← THIS is what we need to find
- k = 2 (distance from head to cycle entry)
- C = 4 (cycle length: nodes 3, 4, 5, 6)

Floyd's algorithm stages:

Stage 1: Detect cycle (fast & slow meet)
Initial:     slow=1, fast=1
Step 1:      slow=2, fast=3
Step 2:      slow=3, fast=5
Step 3:      slow=4, fast=3 (fast wrapped around)
Step 4:      slow=5, fast=5 ← MEET at node 5

Stage 2: Find entry point
Reset one pointer to head: ptr1=1, ptr2=5
Move both 1 step at a time:
Step 1:      ptr1=2, ptr2=6
Step 2:      ptr1=3, ptr2=3 ← MEET at entry point!
```

### **Core Question:**
How to find the exact node where the cycle begins?

### **Deriving from First Principles:**

**Observation 1:** HashSet approach (baseline)
- Traverse list, store each node in a set
- First revisited node = cycle entry
- Space: O(n)

**Observation 2:** Can we use O(1) space?
- From Variant #1: We know how to detect cycle
- But meeting point is NOT necessarily the cycle entry
- Need mathematical relationship

**Observation 3:** Mathematical derivation

Let:
- k = distance from head to cycle entry
- C = cycle length
- m = distance from cycle entry to meeting point

When slow and fast meet:
- slow traveled: k + m (entered cycle, went m steps inside)
- fast traveled: k + m + nC (same path + n full loops)
- fast = 2 × slow: k + m + nC = 2(k + m)

Solving:
  k + m + nC = 2k + 2m
  nC = k + m
  k = nC - m

Key insight: Distance from head to entry = (n loops - distance to meeting point)

This means:
- If we start one pointer at head
- And another at meeting point
- Moving both 1 step at a time
- They will meet at the cycle entry!

**Why this works:**
```
Pointer from head travels k steps to reach entry
Pointer from meeting point travels:
  - (C - m) to complete current loop and return to entry
  - Then (n-1)C more loops
  - Total: nC - m = k
  
Both travel k steps → meet at entry!
```

### **State Space Structure:**

**Type:** Find cycle entry point in linked list  
**Structure:** Path from head to cycle start, then cycle itself  
**Cardinality:** n nodes total, cycle of length C starting at position k

**State Space Example:**
For list: `1 → 2 → 3 → 4 → 5 → 3` (cycle starts at node 3)

```
Linear part: 1 → 2
Cycle part: 3 → 4 → 5 → (back to 3)

k = 2 (distance from head to cycle start)
C = 3 (cycle length)
n = 5 (total unique nodes)
```

**All positions to consider:**
```
Start at head, need to find: Which node is the cycle entry?
Candidates: Any node in the list could be the entry point
```

---

### Step 2: Brute Force with Value Tracing

**Approach 1: HashSet (track first revisited node)**

```csharp
public ListNode DetectCycle_BruteForce(ListNode head)
{
    var visited = new HashSet<ListNode>();
    ListNode current = head;
    
    while (current != null)
    {
        // First node we revisit = cycle start
        if (visited.Contains(current))
            return current;
        
        visited.Add(current);
        current = current.next;
    }
    
    return null; // No cycle
}
```

**Value Trace for:** `1 → 2 → 3 → 4 → 5 → 3` (cycle at 3)

| Step | current | visited | Action | Result |
|------|---------|---------|--------|--------|
| 0 | 1 | {} | Add 1 | {1} |
| 1 | 2 | {1} | Add 2 | {1,2} |
| 2 | 3 | {1,2} | Add 3 | {1,2,3} |
| 3 | 4 | {1,2,3} | Add 4 | {1,2,3,4} |
| 4 | 5 | {1,2,3,4} | Add 5 | {1,2,3,4,5} |
| 5 | 3 | {1,2,3,4,5} | **Found!** | **Return node 3** ✅ |

**Complexity:**
- **Time:** O(n)
- **Space:** O(n) - Store all nodes

---

### Step 3: Pruning Analysis - Can We Do Better?

**Key Observations:**

1. **We know from Variant #1:** Fast & Slow pointers can detect cycle in O(1) space
2. **Challenge:** After detecting cycle, how to find the START without extra space?
3. **Mathematical insight needed:** Relationship between meeting point and cycle start

**Mathematical Derivation:**

Let:
- `k` = distance from head to cycle start
- `C` = cycle length
- Meeting point = where fast and slow first meet inside cycle

**When slow enters cycle:**
- Slow has traveled: k steps
- Fast has traveled: 2k steps
- Fast is (2k - k) = k steps ahead inside cycle
- Fast position in cycle: k mod C

**When they meet:**
- Let slow travel additional `m` steps in cycle
- Fast travels additional `2m` steps in cycle
- They meet when: (k + m) ≡ (k + 2m) mod C
- This means: m ≡ 0 mod C (fast has lapped slow exactly once)

**Key insight:**
After meeting, if slow continues from meeting point and new pointer starts from head:
- Both travel at same speed (1 step)
- They will meet at cycle start!

**Proof:**
```
Distance from head to cycle start: k
Distance from meeting point to cycle start: C - (meeting point position)
But meeting point position = k mod C

So both pointers travel same distance to reach cycle start!
```

**Decision:** ✅ Use Floyd's algorithm + second phase with two pointers from different starting points!

---

### Step 4: Optimal Solution with Skeleton Transformation

**Algorithm: Floyd's Cycle Detection + Finding Start**

```csharp
public ListNode DetectCycle_Optimal(ListNode head)
{
    // Phase 1: Detect if cycle exists using fast & slow
    ListNode slow = head;
    ListNode fast = head;
    
    while (fast != null && fast.next != null)
    {
        slow = slow.next;
        fast = fast.next.next;
        
        if (slow == fast)
        {
            // Cycle detected, proceed to Phase 2
            break;
        }
    }
    
    // No cycle found
    if (fast == null || fast.next == null)
        return null;
    
    // Phase 2: Find cycle start
    // Reset one pointer to head, keep other at meeting point
    slow = head;
    
    // Move both at same speed until they meet
    while (slow != fast)
    {
        slow = slow.next;
        fast = fast.next;
    }
    
    // They meet at cycle start!
    return slow;
}
```

**Skeleton Transformation:**

```
BRUTE FORCE SKELETON:
├── visited = HashSet<ListNode>()
├── while (current != null):
│   ├── if current in visited: return current
│   └── visited.add(current)
└── return null

                    ↓ TRANSFORM ↓

OPTIMAL SKELETON (Two Phases):
├── PHASE 1: Detect cycle
│   ├── slow = head, fast = head
│   ├── while (fast != null && fast.next != null):
│   │   ├── slow = slow.next
│   │   ├── fast = fast.next.next
│   │   └── if slow == fast: break
│   └── if no cycle: return null
│
├── PHASE 2: Find start
│   ├── slow = head (reset to head)
│   ├── fast = meeting_point (stays at meeting)
│   ├── while (slow != fast):
│   │   ├── slow = slow.next
│   │   └── fast = fast.next
│   └── return slow (cycle start)

KEY CHANGES:
- Remove HashSet → Two-phase pointer algorithm
- Single pass → Two phases (detect, then locate)
- O(n) space → O(1) space
```

**Value Trace for:** `1 → 2 → 3 → 4 → 5 → 3` (k=2, C=3)

**Phase 1: Detect Cycle**

| Iter | slow | fast | Meet? |
|------|------|------|-------|
| 0 | 1 | 1 | No |
| 1 | 2 | 3 | No |
| 2 | 3 | 5 | No |
| 3 | 4 | 4 | **Yes!** (meet at node 4) |

**Phase 2: Find Start**

| Iter | slow (from head) | fast (from meeting=4) | Meet? |
|------|------------------|----------------------|-------|
| 0 | 1 | 4 | No |
| 1 | 2 | 5 | No |
| 2 | 3 | 3 | **Yes!** ✅ (cycle start) |

**Why it works:**
- Distance from head to start = 2
- Distance from meeting (4) to start = 2 steps (4→5→3)
- Both travel 2 steps and meet at cycle start!

---

## Big-O Analysis

**Brute Force (HashSet):**
- **Time:** O(n) - Visit each node once
- **Space:** O(n) - Store all visited nodes

**Optimal (Floyd's Two-Phase):**
- **Time:** O(n) - Phase 1: O(n), Phase 2: O(k) where k ≤ n
- **Space:** O(1) - Only pointers

**Key Advantage:** Elegant O(1) space solution with same time complexity!

---

## Pattern Recognition

**When to use this variant:**
- ✅ Need to find cycle start position
- ✅ O(1) space constraint
- ✅ Cannot modify list structure
- ✅ Mathematical property: distance relationship

**Why two phases work:**
- Phase 1: Proves cycle exists (from Variant #1)
- Phase 2: Exploits mathematical relationship between k and meeting point
- Both pointers travel equal distance to reach start

---

## Mathematical Proof (Detailed)

Given:
- `k` = distance head → cycle start
- `C` = cycle length
- `m` = distance traveled inside cycle before meeting

**At meeting point:**
- Slow: traveled k + m
- Fast: traveled 2(k + m)
- Fast also at: k + m + nC (for some integer n)

Therefore: `2(k + m) = k + m + nC`
Simplify: `k + m = nC`
So: `k = nC - m`

**Distance from meeting to cycle start:** `C - m`

**After meeting:**
- Pointer from head travels: k
- Pointer from meeting travels: C - m
- Since k = nC - m, and we only care about position in cycle:
  - k mod C = (nC - m) mod C = -m mod C = C - m
- Both reach cycle start! ✅

---

## Edge Cases

1. **No cycle:** Return null
2. **Cycle at head:** k=0, returns head immediately
3. **Single node self-loop:** Returns that node
4. **Large cycle:** Works regardless of cycle size
5. **Cycle covers entire list:** k=0, C=n

---

## Related Problems

- **Linked List Cycle** (LeetCode #141) - Prerequisite: detect cycle
- **Find Duplicate Number** (LeetCode #287) - Array as implicit linked list with cycle
- **Happy Number** (LeetCode #202) - Cycle detection variant
