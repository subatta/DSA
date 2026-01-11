# Pattern: Fast & Slow Pointers (Floyd's Cycle Detection)

## Pattern Description
**Fast & Slow Pointers** (also known as **Floyd's Cycle Detection** or **Tortoise and Hare**) is a technique where two pointers traverse a data structure at different speeds to detect cycles, find middle elements, or identify patterns.

### What is it?
- **Two pointers moving at different speeds:**
  - **Slow pointer:** Moves 1 step at a time
  - **Fast pointer:** Moves 2 steps at a time (or more)
- **Key insight:** If there's a cycle, fast pointer will eventually catch up to slow pointer
- **Space efficiency:** O(1) space - no need for visited set

### What abstract problem does it solve?
- Detect cycles in linked structures
- Find middle of linked list in one pass
- Detect repeating patterns in sequences
- Identify meeting points in circular structures
- Solve problems requiring O(1) space instead of O(n)

### Real-world / interview variants (simplest → harder):
1. [Find Middle of Linked List](variants/fast_slow_pointers/variant_3_find_middle.md) - LeetCode #876, Easy
2. [Linked List Cycle](variants/fast_slow_pointers/variant_1_linked_list_cycle.md) - LeetCode #141, Easy
3. [Linked List Cycle II](variants/fast_slow_pointers/variant_2_linked_list_cycle_ii.md) - LeetCode #142, Medium
4. [Palindrome Linked List](variants/fast_slow_pointers/variant_4_palindrome_linked_list.md) - LeetCode #234, Easy
5. [Happy Number](variants/fast_slow_pointers/variant_5_happy_number.md) - LeetCode #202, Easy
6. [Remove Nth Node From End](variants/fast_slow_pointers/variant_6_remove_nth_from_end.md) - LeetCode #19, Medium

---

## Canonical Skeleton (Cycle Detection)

```csharp
bool HasCycle(ListNode head)
{
    if (head == null || head.next == null)
        return false;
    
    ListNode slow = head;
    ListNode fast = head;
    
    while (fast != null && fast.next != null)
    {
        slow = slow.next;           // Move 1 step
        fast = fast.next.next;      // Move 2 steps
        
        if (slow == fast)
            return true;            // Cycle detected
    }
    
    return false;                   // No cycle
}
```

## Pattern Variants (6 Total)

### 🟢 Easy (4 variants)
**Build cycle detection and two-pointer intuition**

1. **[Find Middle of Linked List](variants/fast_slow_pointers/variant_3_find_middle.md)** - LeetCode #876
   - **Concept:** Fast pointer at 2x speed reaches end when slow is at middle
   - **Complexity:** O(n) time, O(1) space
   - **Why Easy:** Single-pass, no cycle handling, direct application

2. **[Linked List Cycle](variants/fast_slow_pointers/variant_1_linked_list_cycle.md)** - LeetCode #141 ⭐
   - **Concept:** If cycle exists, fast catches slow (Floyd's algorithm)
   - **Complexity:** O(n) time, O(1) space
   - **Why Easy:** Core pattern application, boolean result

3. **[Palindrome Linked List](variants/fast_slow_pointers/variant_4_palindrome_linked_list.md)** - LeetCode #234
   - **Concept:** Find middle, reverse second half, compare
   - **Complexity:** O(n) time, O(1) space
   - **Why Easy:** Combines middle-finding with standard list reversal

4. **[Happy Number](variants/fast_slow_pointers/variant_5_happy_number.md)** - LeetCode #202
   - **Concept:** Cycle detection in number transformation sequence
   - **Complexity:** O(log n) time, O(1) space
   - **Why Easy:** Pattern recognition - sequence = implicit linked list

### 🟡 Medium (2 variants)
**Master cycle location and gap-based techniques**

5. **[Linked List Cycle II](variants/fast_slow_pointers/variant_2_linked_list_cycle_ii.md)** - LeetCode #142
   - **Concept:** Two-phase: detect cycle, then find entry point
   - **Complexity:** O(n) time, O(1) space
   - **Why Medium:** Requires mathematical insight about meeting point distances

6. **[Remove Nth Node From End](variants/fast_slow_pointers/variant_6_remove_nth_from_end.md)** - LeetCode #19
   - **Concept:** Gap-based pointers (n+1 apart) for one-pass removal
   - **Complexity:** O(n) time, O(1) space
   - **Why Medium:** Requires dummy node, careful pointer manipulation

### Practice Progression
1. Start with **Find Middle** (#3) - simplest application
2. Master **Linked List Cycle** (#1) - core cycle detection
3. Apply to sequences with **Happy Number** (#5)
4. Learn multi-step with **Palindrome Linked List** (#4)
5. Advance to **Cycle II** (#2) - mathematical reasoning
6. Complete with **Remove Nth** (#6) - gap technique variation