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

## Variant Deep Dives

Each variant demonstrates the Fast & Slow Pointers pattern with a 4-step analysis framework:

### Cycle Detection Variants
- [Variant #1: Linked List Cycle](variants/fast_slow_pointers/variant_1_linked_list_cycle.md) - Detect if cycle exists (Floyd's basic algorithm)
- [Variant #2: Linked List Cycle II](variants/fast_slow_pointers/variant_2_linked_list_cycle_ii.md) - Find where cycle begins (two-phase approach)
- [Variant #5: Happy Number](variants/fast_slow_pointers/variant_5_happy_number.md) - Cycle detection in number sequences

### List Navigation Variants
- [Variant #3: Find Middle of Linked List](variants/fast_slow_pointers/variant_3_find_middle.md) - One-pass middle finding
- [Variant #6: Remove Nth Node From End](variants/fast_slow_pointers/variant_6_remove_nth_from_end.md) - Gap-based two pointers

### Combined Technique Variants
- [Variant #4: Palindrome Linked List](variants/fast_slow_pointers/variant_4_palindrome_linked_list.md) - Combines middle-finding + reversal + comparison

---

**Note:** The embedded variant content has been moved to individual files for better organization. Each file contains:
1. State Space Derivation (cardinality, structure, generation)
2. Brute Force with Value Tracing
3. Pruning Analysis (can we do better?)
4. Optimal Solution with Skeleton Transformation
