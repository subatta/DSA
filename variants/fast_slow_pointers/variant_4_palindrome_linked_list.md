# Variant #4: Palindrome Linked List

**LeetCode #234 - Easy**

## Problem Statement
Given the head of a singly linked list, return `true` if it is a palindrome, `false` otherwise.

**Follow-up:** Can you do it in O(n) time and O(1) space?

## 4-Step Analysis Framework

### Step 1: State Space Derivation

### **Problem Visualization:**
```
Palindrome example: 1 → 2 → 3 → 2 → 1

Strategy: Compare first half with reversed second half

Step 1: Find middle
  1 → 2 → 3 → 2 → 1
          ↑
        middle

Step 2: Reverse second half
  First:  1 → 2 → 3
  Second: 1 ← 2 (reversed from 2→1)
  
Step 3: Compare
  1 → 2 → 3      1 ← 2
  ↑              ↑
  p1             p2
  
  Compare: 1=1 ✓, 2=2 ✓ → Palindrome!

Non-palindrome: 1 → 2 → 3 → 4
  First:  1 → 2
  Second: 4 ← 3
  Compare: 1≠4 ✗ → Not palindrome
```

### **Core Question:**
How to check palindrome property with O(1) space?

### **Deriving from First Principles:**

**Observation 1:** Array approach (baseline)
- Copy all values to array: O(n) space
- Use two pointers from both ends: O(n) time
- Simple but violates O(1) space constraint

**Observation 2:** Palindrome property
- A palindrome reads same forward and backward
- First half mirrors second half
- Only need to compare: list[0...n/2-1] with list[n/2...n-1] reversed

**Observation 3:** In-place reversal
- Cannot reverse entire list (would lose original order)
- But can reverse **second half** only!
- Steps:
  1. Find middle using fast & slow pointers
  2. Reverse second half in-place
  3. Compare first half with reversed second half
  4. (Optional) Restore list by reversing second half again

**Observation 4:** Why this achieves O(1) space
- Fast & slow pointers: O(1)
- Reversal: O(1) extra space (just pointers, modify in-place)
- Comparison: O(1)
- Only a constant number of pointers used!

**Observation 5:** Odd vs even length
- **Odd:** Middle element ignored (always matches itself)
  - [1,2,3,2,1]: compare [1,2] with [2,1]
- **Even:** All elements compared
  - [1,2,2,1]: compare [1,2] with [2,1]

### **State Space Structure:**

**Type:** Verify if list reads same forward and backward  
**Structure:** Compare first half with reversed second half  
**Cardinality:** n/2 comparisons needed

**State Space:**
```
Palindrome: 1→2→3→2→1
  Forward: [1,2,3,2,1]
  Backward: [1,2,3,2,1] ✅ Match

Not palindrome: 1→2→3→4→5
  Forward: [1,2,3,4,5]
  Backward: [5,4,3,2,1] ❌ Different
```

---

### Step 2: Brute Force with Value Tracing

**Approach:** Copy to array, use two pointers to compare

```csharp
public bool IsPalindrome_BruteForce(ListNode head)
{
    // Copy to array
    var values = new List<int>();
    ListNode current = head;
    while (current != null)
    {
        values.Add(current.val);
        current = current.next;
    }
    
    // Two pointers comparison
    int left = 0, right = values.Count - 1;
    while (left < right)
    {
        if (values[left] != values[right])
            return false;
        left++;
        right--;
    }
    
    return true;
}
```

**Value Trace for:** `1→2→3→2→1`

| Step | Operation | values | left | right | Match? |
|------|-----------|--------|------|-------|--------|
| Copy | Traverse | [1,2,3,2,1] | - | - | - |
| 1 | Compare | [1,2,3,2,1] | 0 | 4 | 1==1 ✅ |
| 2 | Compare | [1,2,3,2,1] | 1 | 3 | 2==2 ✅ |
| 3 | left>=right | - | 2 | 2 | **Return true** ✅ |

**Complexity:**
- **Time:** O(n) - Copy + compare
- **Space:** O(n) - Store all values

---

### Step 3: Pruning Analysis - Can We Do Better?

**Key Observations:**

1. **Space bottleneck:** Array stores all n values
2. **Palindrome property:** First half should equal reversed second half
3. **Linked list challenge:** Can only traverse forward, not backward
4. **Solution components:**
   - Find middle (Variant #3: Fast & Slow)
   - Reverse second half in-place
   - Compare first half with reversed second half

**Why this works:**
```
Original: 1→2→3→2→1
         ↓
Step 1: Find middle → 3
Step 2: Reverse second half → 1→2→3←2←1
Step 3: Compare: (1→2→3) with (1→2→3) ✅
```

**Decision:** ✅ Combine middle-finding + in-place reversal for O(1) space!

---

### Step 4: Optimal Solution with Skeleton Transformation

```csharp
public bool IsPalindrome_Optimal(ListNode head)
{
    if (head == null || head.next == null)
        return true;
    
    // Step 1: Find middle using fast & slow
    ListNode slow = head, fast = head;
    while (fast != null && fast.next != null)
    {
        slow = slow.next;
        fast = fast.next.next;
    }
    
    // Step 2: Reverse second half
    ListNode secondHalf = ReverseList(slow);
    ListNode firstHalf = head;
    
    // Step 3: Compare both halves
    bool result = true;
    
    while (secondHalf != null) // Second half is shorter or equal
    {
        if (firstHalf.val != secondHalf.val)
        {
            result = false;
            break;
        }
        firstHalf = firstHalf.next;
        secondHalf = secondHalf.next;
    }
    
    // Optional: Restore list (reverse second half back)
    // ReverseList(secondHalf);
    
    return result;
}

private ListNode ReverseList(ListNode head)
{
    ListNode prev = null, current = head;
    
    while (current != null)
    {
        ListNode next = current.next;
        current.next = prev;
        prev = current;
        current = next;
    }
    
    return prev;
}
```

**Skeleton Transformation:**
```
BRUTE FORCE:
├── Copy all values to array: O(n) space
├── Two pointers on array
└── Compare

         ↓

OPTIMAL (Three Phases):
├── Phase 1: Find middle (fast & slow)
├── Phase 2: Reverse second half in-place
├── Phase 3: Compare first & reversed second half
└── return result

KEY CHANGES:
- Array storage → In-place pointer manipulation
- Random access → Sequential comparison
- O(n) space → O(1) space
```

**Value Trace for:** `1→2→3→2→1`

**Phase 1: Find Middle**
| Iter | slow | fast | Action |
|------|------|------|--------|
| 0 | 1 | 1 | Initial |
| 1 | 2 | 3 | Continue |
| 2 | 3 | null | fast.next.next null, stop |

**Phase 2: Reverse from 3**
```
Before: 1→2→3→2→1
After:  1→2→3←2←1
        ↑     ↑
      first  second
```

**Phase 3: Compare**
| Step | p1 | p2 | p1.val | p2.val | Match? |
|------|----|----|--------|--------|--------|
| 1 | 1 | 1 | 1 | 1 | ✅ |
| 2 | 2 | 2 | 2 | 2 | ✅ |
| 3 | 3 | 3 | 3 | 3 | ✅ |
| Result | - | - | - | - | **true** ✅ |

---

## Big-O Analysis

**Brute Force:**
- **Time:** O(n) - Copy + compare
- **Space:** O(n) - Array storage

**Optimal:**
- **Time:** O(n) - Find middle O(n/2) + Reverse O(n/2) + Compare O(n/2) = O(n)
- **Space:** O(1) - Only pointers

**Key Advantage:** O(n) → O(1) space reduction while maintaining O(n) time!

---

## Pattern Recognition

**When to use:**
- ✅ Palindrome check on linked list
- ✅ O(1) space constraint
- ✅ Can modify list structure temporarily
- ✅ Combines: middle-finding + reversal + comparison

**Pattern combination:**
- Fast & Slow (find middle)
- In-place reversal
- Two-pointer comparison

---

## Edge Cases

1. **Empty list:** true (vacuously palindrome)
2. **Single node:** true
3. **Two nodes same:** 1→1 → true
4. **Two nodes different:** 1→2 → false
5. **Odd length:** Middle node handled correctly
6. **Even length:** Both halves equal size

---

## Trade-offs

**Pros:**
- O(1) space
- Elegant combination of techniques

**Cons:**
- Modifies list structure (can restore with second reversal)
- More complex than array approach
- Three distinct phases

---

## Related Problems

- **Valid Palindrome** (LeetCode #125) - String variant
- **Find Middle** (LeetCode #876) - Prerequisite technique
- **Reverse Linked List** (LeetCode #206) - Core operation used
