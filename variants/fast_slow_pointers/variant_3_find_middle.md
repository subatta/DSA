# Variant #3: Find Middle of Linked List

**LeetCode #876 - Easy**

## Problem Statement
Given the head of a singly linked list, return the middle node. If there are two middle nodes, return the second middle node.

Example: `1→2→3→4→5` returns node 3, `1→2→3→4→5→6` returns node 4

## 4-Step Analysis Framework

### Step 1: State Space Derivation

### **Problem Visualization:**
```
Case 1: Odd length (n=5)
1 → 2 → 3 → 4 → 5
        ↑
      middle
Return node 3 (index 2)

Case 2: Even length (n=6)
1 → 2 → 3 → 4 → 5 → 6
            ↑
      second middle
Return node 4 (index 3)

Fast & Slow pointer approach:

Initial: slow=1, fast=1

Step 1: slow moves 1, fast moves 2
  1 → 2 → 3 → 4 → 5
      ↑       ↑
     slow     fast

Step 2: slow moves 1, fast moves 2
  1 → 2 → 3 → 4 → 5
          ↑         ↑
          slow      fast (null)
          
When fast reaches end, slow is at middle!
```

### **Core Question:**
How to find middle without counting nodes twice?

### **Deriving from First Principles:**

**Observation 1:** Two-pass approach
- Pass 1: Count total nodes (n)
- Pass 2: Traverse to position n/2
- Works but requires two full traversals

**Observation 2:** Fast & Slow pointer insight
- slow pointer: moves 1 node per step
- fast pointer: moves 2 nodes per step
- fast moves at **2× speed** of slow
- When fast reaches end, slow has traveled half the distance!

**Observation 3:** Mathematical proof
- Let total distance = n nodes
- Time when fast reaches end: t steps
- fast traveled: 2t nodes = n ⇒ t = n/2
- slow traveled: t nodes = n/2
- slow is at middle!

**Observation 4:** Odd vs even length
- **Odd (n=5):** fast lands on last node (index 4)
  - slow at index 2 (exact middle)
- **Even (n=6):** fast goes beyond (null)
  - slow at index 3 (second middle)
- Algorithm naturally handles both cases!

### **State Space Structure:**

**Type:** Find middle position in linked list  
**Structure:** Linear sequence of n nodes  
**Cardinality:** n nodes, middle is at position ⌈n/2⌉

**State Space:**
```
n=5: [1,2,3,4,5] → middle at index 2 (node 3)
n=6: [1,2,3,4,5,6] → middle at index 3 (node 4)
```

---

### Step 2: Brute Force with Value Tracing

**Approach:** Count nodes, then traverse to position n/2

```csharp
public ListNode FindMiddle_BruteForce(ListNode head)
{
    // Pass 1: Count nodes
    int count = 0;
    ListNode current = head;
    while (current != null)
    {
        count++;
        current = current.next;
    }
    
    // Pass 2: Go to middle
    int middle = count / 2;
    current = head;
    for (int i = 0; i < middle; i++)
    {
        current = current.next;
    }
    
    return current;
}
```

**Value Trace for:** `1→2→3→4→5`

| Pass | Step | current | count/position | Action |
|------|------|---------|---------------|--------|
| 1 | 0-4 | 1→2→3→4→5 | count=5 | Count all |
| 2 | 0 | 1 | pos=0, target=2 | Start |
| 2 | 1 | 2 | pos=1, target=2 | Advance |
| 2 | 2 | 3 | pos=2, target=2 | **Return 3** ✅ |

**Complexity:**
- **Time:** O(n) - Two passes (count + traverse)
- **Space:** O(1)

---

### Step 3: Pruning Analysis - Can We Do Better?

**Key Observations:**

1. **Two passes needed:** Count first, then traverse
2. **Can we find middle in one pass?** Yes, with two pointers!
3. **Speed relationship:** If one pointer moves 2x speed, when fast reaches end, slow is at middle

**Mathematical insight:**
```
When fast travels n steps (to end):
Slow travels n/2 steps (to middle)

Fast speed = 2 × Slow speed
→ Fast reaches end when Slow reaches middle!
```

**Decision:** ✅ Use fast & slow pointers to find middle in single pass!

---

### Step 4: Optimal Solution with Skeleton Transformation

```csharp
public ListNode FindMiddle_Optimal(ListNode head)
{
    ListNode slow = head;
    ListNode fast = head;
    
    // Fast moves 2x, slow moves 1x
    while (fast != null && fast.next != null)
    {
        slow = slow.next;
        fast = fast.next.next;
    }
    
    // When fast reaches end, slow is at middle
    return slow;
}
```

**Skeleton Transformation:**
```
BRUTE FORCE:
├── Pass 1: count = CountNodes()
├── Pass 2: traverse to count/2
└── return node

         ↓

OPTIMAL:
├── slow = head, fast = head
├── while (fast != null && fast.next != null):
│   ├── slow = slow.next (1 step)
│   └── fast = fast.next.next (2 steps)
└── return slow

KEY CHANGES:
- Two passes → Single pass
- Separate count → Implicit via speed difference
```

**Value Trace for:** `1→2→3→4→5`

| Iter | slow | fast | Notes |
|------|------|------|-------|
| 0 | 1 | 1 | Initial |
| 1 | 2 | 3 | fast.next.next exists |
| 2 | 3 | 5 | fast.next.next exists |
| 3 | - | - | fast.next==null, exit |
| Return | **3** ✅ | - | Middle node |

**Value Trace for:** `1→2→3→4→5→6`

| Iter | slow | fast | Notes |
|------|------|------|-------|
| 0 | 1 | 1 | Initial |
| 1 | 2 | 3 | Continue |
| 2 | 3 | 5 | Continue |
| 3 | 4 | null | fast==null, exit |
| Return | **4** ✅ | - | Second middle |

---

## Big-O Analysis

**Brute Force:**
- **Time:** O(n) - Two passes: O(n) + O(n/2) = O(n)
- **Space:** O(1)

**Optimal:**
- **Time:** O(n) - Single pass: O(n/2) when fast reaches end
- **Space:** O(1)

**Key Advantage:** Single pass instead of two passes (2x faster constant factor)

---

## Pattern Recognition

**When to use:**
- ✅ Need middle of linked list
- ✅ Single pass preferred
- ✅ Cannot modify list
- ✅ Fast/slow speed difference finds midpoint

---

## Edge Cases

1. **Single node:** Returns head
2. **Two nodes:** Returns second node (per problem definition)
3. **Odd length:** Returns exact middle
4. **Even length:** Returns second of two middles

---

## Related Problems

- **Palindrome Linked List** (LeetCode #234) - Uses middle finding
- **Reorder List** (LeetCode #143) - Split at middle
- **Delete Middle Node** (LeetCode #2095) - Find then delete
