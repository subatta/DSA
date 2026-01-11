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
1. **Middle of Linked List** - Find center element
2. **Linked List Cycle Detection** - Detect if cycle exists
3. **Cycle Start Detection** - Find where cycle begins
4. **Happy Number** - Detect cycle in number transformation
5. **Palindrome Linked List** - Check if list reads same forward/backward
6. **Linked List Cycle Length** - Find length of cycle
7. **Reorder List** - Rearrange list by alternating ends
8. **Intersection of Two Linked Lists** - Find merge point

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

---

<details>
<summary><b>Variant #1: Middle of Linked List</b></summary>

## Variant #1: Middle of Linked List

### Input/Output:
- **Input:** Linked list `1 -> 2 -> 3 -> 4 -> 5`
- **Output:** Node with value `3` (middle node)
- **Input:** Linked list `1 -> 2 -> 3 -> 4 -> 5 -> 6`
- **Output:** Node with value `4` (second middle node)

### Full State Space:
Count all nodes first, then traverse to middle:
```
Pass 1: Count nodes → n = 5
Pass 2: Traverse to index n/2 = 2
Two passes through list
```

```csharp
// Brute force: Two passes
ListNode FindMiddleTwoPass(ListNode head)
{
    int count = 0;
    ListNode current = head;
    
    // Pass 1: Count nodes
    while (current != null)
    {
        count++;
        current = current.next;
    }
    
    // Pass 2: Go to middle
    current = head;
    for (int i = 0; i < count / 2; i++)
    {
        current = current.next;
    }
    
    return current;
}
```

### Expected / Pruned State Space:
Fast pointer moves 2x speed - when fast reaches end, slow is at middle:
```
Step 0: slow=1, fast=1
Step 1: slow=2, fast=3
Step 2: slow=3, fast=5
Fast reached end → slow at middle
```

### Brute Force Canonical Skeleton:
```csharp
public ListNode MiddleNodeBruteForce(ListNode head)
{
    // Convert to array first
    List<ListNode> nodes = new List<ListNode>();
    ListNode current = head;
    
    while (current != null)
    {
        nodes.Add(current);
        current = current.next;
    }
    
    return nodes[nodes.Count / 2];
}
```

### Brute Force Code Walkthrough / Variable Trace:
For list `1 -> 2 -> 3 -> 4 -> 5`:

| Step | current | nodes | Action |
|------|---------|-------|--------|
| 0 | 1 | [1] | Add to list |
| 1 | 2 | [1,2] | Add to list |
| 2 | 3 | [1,2,3] | Add to list |
| 3 | 4 | [1,2,3,4] | Add to list |
| 4 | 5 | [1,2,3,4,5] | Add to list |
| 5 | null | [1,2,3,4,5] | Done, return nodes[5/2]=nodes[2]=3 |

### Optimized Solution (Fast & Slow Pointers):
```csharp
public ListNode MiddleNode(ListNode head)
{
    ListNode slow = head;
    ListNode fast = head;
    
    // When fast reaches end, slow is at middle
    while (fast != null && fast.next != null)
    {
        slow = slow.next;           // Move 1 step
        fast = fast.next.next;      // Move 2 steps
    }
    
    return slow;
}
```

### Explanation of Pruning:
- **Key insight:** Fast pointer moves 2x speed → covers distance in half the time
- **When fast reaches end:** Slow has covered half the distance
- **One pass:** No need to count first
- **O(1) space:** No array needed
- **Even vs odd length:**
  - Odd (5 nodes): Fast lands on last node, slow at exact middle
  - Even (6 nodes): Fast lands on null, slow at second middle

### Optimized Solution Code Walkthrough / Variable Trace:
For list `1 -> 2 -> 3 -> 4 -> 5`:

| Iteration | slow | fast | fast.next | Continue? | Result |
|-----------|------|------|-----------|-----------|--------|
| 0 | 1 | 1 | 2 | Yes | Move pointers |
| 1 | 2 | 3 | 4 | Yes | Move pointers |
| 2 | 3 | 5 | null | No | Return slow=3 |

For list `1 -> 2 -> 3 -> 4 -> 5 -> 6`:

| Iteration | slow | fast | fast.next | Continue? | Result |
|-----------|------|------|-----------|-----------|--------|
| 0 | 1 | 1 | 2 | Yes | Move pointers |
| 1 | 2 | 3 | 4 | Yes | Move pointers |
| 2 | 3 | 5 | 6 | Yes | Move pointers |
| 3 | 4 | null | - | No | Return slow=4 |

### Big-O Analysis:
- **Brute Force (Array):**
  - Time: O(n) → one pass to build array
  - Space: O(n) → store all nodes
  
- **Optimized (Fast & Slow):**
  - Time: O(n) → one pass through list
  - Space: O(1) → only two pointers
  
- **Advantage:** Same time but much better space

</details>

---

<details>
<summary><b>Variant #2: Linked List Cycle Detection</b></summary>

## Variant #2: Linked List Cycle Detection

### Input/Output:
- **Input:** Linked list with nodes `[3,2,0,-4]` where `-4` points back to `2`
- **Output:** `true` (cycle exists)
- **Input:** Linked list `1 -> 2 -> 3 -> null`
- **Output:** `false` (no cycle)

### Full State Space:
Visit every node and track all visited nodes:
```
Visit 3 → seen: {3}
Visit 2 → seen: {3,2}
Visit 0 → seen: {3,2,0}
Visit -4 → seen: {3,2,0,-4}
Visit 2 → already in seen! Cycle detected
```

```csharp
bool HasCycleWithSet(ListNode head)
{
    var visited = new HashSet<ListNode>();
    ListNode current = head;
    
    while (current != null)
    {
        if (visited.Contains(current))
            return true;
        
        visited.Add(current);
        current = current.next;
    }
    
    return false;
}
```

### Expected / Pruned State Space:
Fast pointer moves 2x speed - will meet slow pointer inside cycle:
```
Step 0: slow=3, fast=3
Step 1: slow=2, fast=0
Step 2: slow=0, fast=2
Step 3: slow=-4, fast=-4 → MEET! Cycle detected
```

### Brute Force Canonical Skeleton:
```csharp
public bool HasCycleBruteForce(ListNode head)
{
    var visited = new HashSet<ListNode>();
    ListNode current = head;
    
    while (current != null)
    {
        if (visited.Contains(current))
            return true;
        
        visited.Add(current);
        current = current.next;
    }
    
    return false;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For cyclic list `3 -> 2 -> 0 -> -4 -> (back to 2)`:

| Step | current | visited | Contains? | Result |
|------|---------|---------|-----------|--------|
| 0 | 3 | {} | No | Add 3 |
| 1 | 2 | {3} | No | Add 2 |
| 2 | 0 | {3,2} | No | Add 0 |
| 3 | -4 | {3,2,0} | No | Add -4 |
| 4 | 2 | {3,2,0,-4} | Yes! | Return true |

### Optimized Solution (Floyd's Cycle Detection):
```csharp
public bool HasCycle(ListNode head)
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
            return true;            // Pointers met → cycle!
    }
    
    return false;                   // Fast reached end → no cycle
}
```

### Explanation of Pruning:
- **Floyd's Cycle Detection Algorithm:**
  - If cycle exists, fast pointer eventually laps slow pointer
  - Think of running track: faster runner catches slower runner
  - Gap closes by 1 each iteration (fast gains 2, slow gains 1)
  
- **Why it works:**
  - If no cycle: Fast reaches null
  - If cycle: Fast enters cycle first, then slow enters
  - Once both in cycle: Distance between them decreases by 1 each step
  - Eventually: Distance becomes 0 → they meet
  
- **Space efficiency:** O(1) vs O(n) with hash set

### Optimized Solution Code Walkthrough / Variable Trace:
For cyclic list `3 -> 2 -> 0 -> -4 -> (back to 2)`:

| Iteration | slow | fast | slow==fast? | Action |
|-----------|------|------|-------------|--------|
| 0 | 3 | 3 | Yes (start) | Continue (initial state) |
| 1 | 2 | 0 | No | Continue |
| 2 | 0 | 2 | No | Continue |
| 3 | -4 | -4 | Yes | Return true |

For acyclic list `1 -> 2 -> 3 -> null`:

| Iteration | slow | fast | fast.next | Action |
|-----------|------|------|-----------|--------|
| 0 | 1 | 1 | 2 | Continue |
| 1 | 2 | 3 | null | Stop, return false |

### Big-O Analysis:
- **Brute Force (Hash Set):**
  - Time: O(n) → visit each node once
  - Space: O(n) → store all visited nodes
  
- **Optimized (Fast & Slow):**
  - Time: O(n) → fast pointer visits each node at most twice
  - Space: O(1) → only two pointers
  
- **Key difference:** Space complexity

</details>

---

<details>
<summary><b>Variant #3: Find Cycle Start (Entry Point)</b></summary>

## Variant #3: Find Cycle Start

### Input/Output:
- **Input:** Linked list `3 -> 2 -> 0 -> -4 -> (back to 2)`
- **Output:** Node with value `2` (where cycle begins)
- **Input:** List with no cycle
- **Output:** `null`

### Full State Space:
Track all visited nodes, return first revisited:
```csharp
ListNode FindCycleStartBruteForce(ListNode head)
{
    var visited = new HashSet<ListNode>();
    ListNode current = head;
    
    while (current != null)
    {
        if (visited.Contains(current))
            return current;         // First revisited = cycle start
        
        visited.Add(current);
        current = current.next;
    }
    
    return null;
}
```

### Expected / Pruned State Space:
**Phase 1:** Detect cycle (fast & slow meet)
**Phase 2:** Find entry point (mathematical property)

**Key insight:** 
```
Let:
- L = distance from head to cycle start
- C = cycle length
- k = distance from cycle start to meeting point

When they meet:
- Slow traveled: L + k
- Fast traveled: L + k + nC (n = number of complete cycles)
- Fast = 2 × Slow
- L + k + nC = 2(L + k)
- L = nC - k

This means: Distance from head to cycle start = 
            Distance from meeting point to cycle start
```

### Optimized Solution:
```csharp
public ListNode DetectCycle(ListNode head)
{
    if (head == null || head.next == null)
        return null;
    
    // Phase 1: Detect if cycle exists
    ListNode slow = head;
    ListNode fast = head;
    bool hasCycle = false;
    
    while (fast != null && fast.next != null)
    {
        slow = slow.next;
        fast = fast.next.next;
        
        if (slow == fast)
        {
            hasCycle = true;
            break;
        }
    }
    
    if (!hasCycle)
        return null;
    
    // Phase 2: Find cycle start
    // Move slow back to head, keep fast at meeting point
    slow = head;
    
    // Move both 1 step at a time until they meet
    while (slow != fast)
    {
        slow = slow.next;
        fast = fast.next;
    }
    
    return slow;                    // Both point to cycle start
}
```

### Explanation of Pruning:
- **Mathematical proof:**
  - After detecting cycle, move slow to head
  - Move both pointers 1 step at a time
  - They meet at cycle entry point
  
- **Why it works:**
  - Distance from head to entry = distance from meeting to entry
  - Both pointers travel same distance → meet at entry
  
- **Space:** O(1) instead of O(n)

### Optimized Solution Code Walkthrough / Variable Trace:
For list `3 -> 2 -> 0 -> -4 -> (back to 2)`:

**Phase 1 (Detect Cycle):**

| Iter | slow | fast | Meet? |
|------|------|------|-------|
| 0 | 3 | 3 | No |
| 1 | 2 | 0 | No |
| 2 | 0 | 2 | No |
| 3 | -4 | -4 | Yes! |

**Phase 2 (Find Start):**

| Iter | slow (from head) | fast (from meeting) | Meet? |
|------|------------------|---------------------|-------|
| 0 | 3 | -4 | No |
| 1 | 2 | 2 | Yes! Return 2 |

### Big-O Analysis:
- **Brute Force:** O(n) time, O(n) space
- **Optimized:** O(n) time, O(1) space
- **Two phases:** Both linear in list length

</details>

---

<details>
<summary><b>Variant #4: Happy Number</b></summary>

## Variant #4: Happy Number

### Input/Output:
- **Input:** n = 19
- **Output:** `true` (happy number)
- **Process:** 
  ```
  19 → 1² + 9² = 82
  82 → 8² + 2² = 68
  68 → 6² + 8² = 100
  100 → 1² + 0² + 0² = 1 ✓
  ```

- **Input:** n = 2
- **Output:** `false` (enters cycle, never reaches 1)

### Full State Space:
Track all seen numbers until repeat or reach 1:
```csharp
bool IsHappyBruteForce(int n)
{
    var seen = new HashSet<int>();
    
    while (n != 1 && !seen.Contains(n))
    {
        seen.Add(n);
        n = GetNext(n);
    }
    
    return n == 1;
}

int GetNext(int n)
{
    int sum = 0;
    while (n > 0)
    {
        int digit = n % 10;
        sum += digit * digit;
        n /= 10;
    }
    return sum;
}
```

### Expected / Pruned State Space:
Use fast & slow pointers to detect cycle:
```
Slow moves 1 transformation at a time
Fast moves 2 transformations at a time
If cycle exists (not happy): They meet
If reaches 1 (happy): One pointer hits 1
```

### Optimized Solution (Cycle Detection):
```csharp
public bool IsHappy(int n)
{
    int slow = n;
    int fast = n;
    
    do
    {
        slow = GetNext(slow);               // 1 step
        fast = GetNext(GetNext(fast));      // 2 steps
        
        if (fast == 1 || slow == 1)
            return true;
        
    } while (slow != fast);
    
    return false;                           // Cycle detected, not happy
}

private int GetNext(int n)
{
    int sum = 0;
    
    while (n > 0)
    {
        int digit = n % 10;
        sum += digit * digit;
        n /= 10;
    }
    
    return sum;
}
```

### Explanation of Pruning:
- **Same as cycle detection:** Number sequence either:
  1. Reaches 1 (happy number)
  2. Enters cycle (unhappy number)
  
- **Fast & slow pointers:**
  - If happy: One pointer reaches 1
  - If unhappy: Pointers meet in cycle
  
- **Space:** O(1) vs O(log n) hash set

### Optimized Solution Code Walkthrough / Variable Trace:
For n = 19 (happy):

| Iteration | slow | fast | Action |
|-----------|------|------|--------|
| 0 | 19 | 19 | Start |
| 1 | 82 | 68 | Continue |
| 2 | 68 | 1 | fast==1, return true |

For n = 2 (unhappy):

| Iteration | slow | fast | Action |
|-----------|------|------|--------|
| 0 | 2 | 2 | Start |
| 1 | 4 | 20 | Continue |
| 2 | 16 | 16 | Meet in cycle, return false |

### Big-O Analysis:
- **Time:** O(log n) → number of digits determines iterations
- **Space:** 
  - Brute Force: O(log n) for hash set
  - Optimized: O(1) with two pointers

</details>

---

<details>
<summary><b>Variant #5: Palindrome Linked List</b></summary>

## Variant #5: Palindrome Linked List

### Input/Output:
- **Input:** `1 -> 2 -> 2 -> 1`
- **Output:** `true` (palindrome)
- **Input:** `1 -> 2 -> 3`
- **Output:** `false` (not palindrome)

### Full State Space:
Convert to array, check if palindrome:
```csharp
bool IsPalindromeBruteForce(ListNode head)
{
    List<int> values = new List<int>();
    ListNode current = head;
    
    // Store all values
    while (current != null)
    {
        values.Add(current.val);
        current = current.next;
    }
    
    // Check palindrome
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

### Expected / Pruned State Space:
**Steps:**
1. Find middle (fast & slow pointers)
2. Reverse second half
3. Compare first half with reversed second half

### Optimized Solution:
```csharp
public bool IsPalindrome(ListNode head)
{
    if (head == null || head.next == null)
        return true;
    
    // Step 1: Find middle using fast & slow
    ListNode slow = head;
    ListNode fast = head;
    
    while (fast != null && fast.next != null)
    {
        slow = slow.next;
        fast = fast.next.next;
    }
    
    // Step 2: Reverse second half
    ListNode secondHalf = ReverseList(slow);
    
    // Step 3: Compare first and second half
    ListNode firstHalf = head;
    ListNode secondHalfCopy = secondHalf;       // Keep for restoration
    
    bool isPalin = true;
    while (secondHalf != null)
    {
        if (firstHalf.val != secondHalf.val)
        {
            isPalin = false;
            break;
        }
        firstHalf = firstHalf.next;
        secondHalf = secondHalf.next;
    }
    
    // Optional: Restore list (reverse second half back)
    ReverseList(secondHalfCopy);
    
    return isPalin;
}

private ListNode ReverseList(ListNode head)
{
    ListNode prev = null;
    ListNode current = head;
    
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

### Explanation of Pruning:
- **Three phases:**
  1. Fast & slow to find middle → O(n/2)
  2. Reverse second half → O(n/2)
  3. Compare two halves → O(n/2)
  
- **Total:** O(n) time, O(1) space
- **Brute force:** O(n) time, O(n) space

### Optimized Solution Code Walkthrough / Variable Trace:
For list `1 -> 2 -> 2 -> 1`:

**Phase 1 (Find Middle):**
- slow=1, fast=1
- slow=2, fast=2 (second 2)
- slow=2 (second), fast=null → slow at middle

**Phase 2 (Reverse Second Half):**
- Before: `2 -> 1`
- After: `1 -> 2` (reversed)

**Phase 3 (Compare):**
- Compare 1 with 1 ✓
- Compare 2 with 2 ✓
- Result: true

### Big-O Analysis:
- **Brute Force:** O(n) time, O(n) space
- **Optimized:** O(n) time, O(1) space
- **Trade-off:** Modifies list (but can restore)

</details>

---

<details>
<summary><b>Variant #6: Cycle Length</b></summary>

## Variant #6: Linked List Cycle Length

### Input/Output:
- **Input:** Linked list with cycle of length 4
- **Output:** `4`
- **Input:** Linked list with no cycle
- **Output:** `0`

### Full State Space:
Store all nodes, find first repeat, count cycle:
```csharp
int CycleLengthBruteForce(ListNode head)
{
    var positions = new Dictionary<ListNode, int>();
    ListNode current = head;
    int pos = 0;
    
    while (current != null)
    {
        if (positions.ContainsKey(current))
            return pos - positions[current];    // Cycle length
        
        positions[current] = pos;
        current = current.next;
        pos++;
    }
    
    return 0;       // No cycle
}
```

### Optimized Solution:
```csharp
public int CycleLength(ListNode head)
{
    if (head == null)
        return 0;
    
    ListNode slow = head;
    ListNode fast = head;
    
    // Phase 1: Detect cycle
    while (fast != null && fast.next != null)
    {
        slow = slow.next;
        fast = fast.next.next;
        
        if (slow == fast)
        {
            // Phase 2: Count cycle length
            int length = 0;
            do
            {
                slow = slow.next;
                length++;
            } while (slow != fast);
            
            return length;
        }
    }
    
    return 0;       // No cycle
}
```

### Explanation of Pruning:
- **After detecting cycle:**
  - Keep one pointer at meeting point
  - Move it around cycle counting steps
  - When returns to meeting point → that's cycle length
  
- **Space:** O(1) vs O(n)

### Big-O Analysis:
- **Time:** O(n) for detection + O(C) for counting = O(n)
- **Space:** O(1)

</details>

---

<details>
<summary><b>Variant #7: Reorder List</b></summary>

## Variant #7: Reorder List

### Input/Output:
- **Input:** `1 -> 2 -> 3 -> 4 -> 5`
- **Output:** `1 -> 5 -> 2 -> 4 -> 3`
- **Pattern:** L₀ → Lₙ → L₁ → Lₙ₋₁ → L₂ → Lₙ₋₂ → ...

### Full State Space:
Store in array, rebuild with alternating indices:
```csharp
void ReorderListBruteForce(ListNode head)
{
    var nodes = new List<ListNode>();
    ListNode current = head;
    
    while (current != null)
    {
        nodes.Add(current);
        current = current.next;
    }
    
    int left = 0, right = nodes.Count - 1;
    while (left < right)
    {
        nodes[left].next = nodes[right];
        left++;
        
        if (left == right) break;
        
        nodes[right].next = nodes[left];
        right--;
    }
    
    nodes[left].next = null;
}
```

### Optimized Solution:
```csharp
public void ReorderList(ListNode head)
{
    if (head == null || head.next == null)
        return;
    
    // Step 1: Find middle (fast & slow)
    ListNode slow = head;
    ListNode fast = head;
    
    while (fast != null && fast.next != null)
    {
        slow = slow.next;
        fast = fast.next.next;
    }
    
    // Step 2: Reverse second half
    ListNode secondHalf = ReverseList(slow.next);
    slow.next = null;       // Split list
    
    // Step 3: Merge two halves alternating
    ListNode first = head;
    ListNode second = secondHalf;
    
    while (second != null)
    {
        ListNode temp1 = first.next;
        ListNode temp2 = second.next;
        
        first.next = second;
        second.next = temp1;
        
        first = temp1;
        second = temp2;
    }
}

private ListNode ReverseList(ListNode head)
{
    ListNode prev = null;
    ListNode current = head;
    
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

### Explanation of Pruning:
- **Three steps:**
  1. Find middle with fast & slow
  2. Reverse second half
  3. Merge alternating
  
- **O(1) space** vs O(n) array

### Big-O Analysis:
- **Time:** O(n) - three O(n) passes
- **Space:** O(1) vs O(n)

</details>

---

<details>
<summary><b>Variant #8: Intersection of Two Linked Lists</b></summary>

## Variant #8: Intersection of Two Linked Lists

### Input/Output:
- **Input:** Two lists intersecting at node with value `8`
  ```
  List A: 4 -> 1 -> 8 -> 4 -> 5
  List B: 5 -> 6 -> 1 -> 8 -> 4 -> 5
  ```
- **Output:** Node with value `8` (intersection point)

### Full State Space:
Store all nodes of list A, find first from B that's in set:
```csharp
ListNode GetIntersectionBruteForce(ListNode headA, ListNode headB)
{
    var nodesA = new HashSet<ListNode>();
    
    ListNode current = headA;
    while (current != null)
    {
        nodesA.Add(current);
        current = current.next;
    }
    
    current = headB;
    while (current != null)
    {
        if (nodesA.Contains(current))
            return current;
        current = current.next;
    }
    
    return null;
}
```

### Optimized Solution (Timing Approach):
```csharp
public ListNode GetIntersectionNode(ListNode headA, ListNode headB)
{
    if (headA == null || headB == null)
        return null;
    
    ListNode pointerA = headA;
    ListNode pointerB = headB;
    
    // Key insight: Both pointers travel same total distance
    // Distance = lenA + lenB
    // They meet at intersection (or both reach null)
    while (pointerA != pointerB)
    {
        // When reaches end, switch to other list
        pointerA = (pointerA == null) ? headB : pointerA.next;
        pointerB = (pointerB == null) ? headA : pointerB.next;
    }
    
    return pointerA;        // Either intersection or null
}
```

### Explanation of Pruning:
- **Mathematical insight:**
  ```
  List A length: a + c (c = common part)
  List B length: b + c
  
  Pointer A travels: a + c + b
  Pointer B travels: b + c + a
  
  Both travel same distance → meet at intersection
  ```
  
- **Elegant solution:** No counting, no hash set
- **Space:** O(1)

### Optimized Solution Code Walkthrough / Variable Trace:
For intersecting lists at node 8:
```
A: 4 -> 1 -> 8 -> 4 -> 5
B: 5 -> 6 -> 1 -> 8 -> 4 -> 5
```

| Step | ptrA | ptrB | Action |
|------|------|------|--------|
| 0 | 4 | 5 | Different, continue |
| 1 | 1 | 6 | Different, continue |
| 2 | 8 | 1 | Different, continue |
| 3 | 4 | 8 | Different, continue |
| 4 | 5 | 4 | Different, continue |
| 5 | null→5 | 5 | Switch A to B, continue |
| 6 | 6 | null→4 | Switch B to A, continue |
| 7 | 1 | 1 | Different node, continue |
| 8 | 8 | 8 | Same node! Return 8 |

### Big-O Analysis:
- **Brute Force:** O(m+n) time, O(m) space
- **Optimized:** O(m+n) time, O(1) space

</details>

---

# Key Takeaways

## Core Principle: Speed Differential

The fast & slow pointer pattern exploits **relative speed** to solve problems efficiently:

| Speed Ratio | Use Case | Why It Works |
|-------------|----------|--------------|
| **2:1 (Fast:Slow)** | Middle of list, cycle detection | When fast reaches end, slow at middle; in cycle, fast laps slow |
| **1:1 (Both slow after meeting)** | Cycle start | Mathematical property: distance from head to start = distance from meeting to start |
| **Transform:Transform** | Happy number | Apply transformation at different rates to detect cycle |
| **List A → List B** | Intersection | Switching lists equalizes path length |

---

## Pattern Recognition Guide

### When to Use Fast & Slow Pointers:

✅ **Strong Signals:**
- "cycle" in linked list
- "middle" of linked list
- "palindrome" of linked list
- O(1) space constraint
- "happy number" or repeating sequence
- "intersection" or "merge point"

✅ **Problem Characteristics:**
- Sequential data structure (linked list, sequence)
- Need to detect repetition without extra space
- Finding middle without counting
- Timing/phase difference matters

❌ **Don't Use When:**
- Array (use regular two pointers)
- Need actual cycle elements (use hash set)
- Tree structure (use tree traversal)

---

## Common Variations

### 1. **Detection Only** (Cycle exists?)
```csharp
while (fast != null && fast.next != null)
{
    slow = slow.next;
    fast = fast.next.next;
    if (slow == fast) return true;
}
return false;
```

### 2. **Find Position** (Middle, intersection)
```csharp
while (fast != null && fast.next != null)
{
    slow = slow.next;
    fast = fast.next.next;
}
return slow;        // Middle or meeting point
```

### 3. **Two-Phase** (Detect + Find start)
```csharp
// Phase 1: Detect
while (fast != null && fast.next != null) { ... }

// Phase 2: Find start
slow = head;
while (slow != fast) { slow = slow.next; fast = fast.next; }
return slow;
```

### 4. **Transformation** (Happy number)
```csharp
do {
    slow = Transform(slow);
    fast = Transform(Transform(fast));
} while (slow != fast && fast != target);
```

---

## Comparison with Other Patterns

| Pattern | Data Structure | Space | When to Use |
|---------|----------------|-------|-------------|
| **Fast & Slow** | Linked list, sequences | O(1) | Cycle, middle, timing problems |
| **Two Pointers** | Arrays (sorted) | O(1) | Pair finding, partitioning |
| **Hash Set** | Any | O(n) | When O(n) space acceptable |
| **DFS/BFS** | Graph, tree | O(h) or O(w) | General graph traversal |

---

## Problem-Solving Framework

```
1. Identify if it's a Fast & Slow problem:
   ✓ Linked list structure?
   ✓ Need O(1) space?
   ✓ Mentions "cycle", "middle", "palindrome"?
   
2. Choose the right variant:
   → Detection only? Use basic cycle detection
   → Find position? Use middle-finding approach
   → Find start? Use two-phase approach
   → Transformation? Apply to number/sequence
   
3. Handle edge cases:
   → Empty list or single node
   → No cycle (fast reaches null)
   → Even vs odd length
   
4. Verify correctness:
   → Trace through small example
   → Check boundary conditions
```

---

# Mental Checklist for Fast & Slow Pointers

- [ ] **Is it a linked list or sequence?** → Fast & slow applicable
- [ ] **Need O(1) space?** → Hash set not allowed, use pointers
- [ ] **Detecting cycle?** → Basic fast & slow until they meet
- [ ] **Finding middle?** → Fast & slow until fast reaches end
- [ ] **Finding cycle start?** → Two phases (detect, then find)
- [ ] **Palindrome check?** → Find middle, reverse, compare
- [ ] **Intersection?** → Switch lists when reaching end
- [ ] **Transformation sequence?** → Apply transforms at different rates
- [ ] **Edge cases covered?** → null, single node, no cycle
- [ ] **Fast pointer safety?** → Always check `fast != null && fast.next != null`

**If problem involves linked list timing/speed/phases → Fast & Slow Pointers applies.**
