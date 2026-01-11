# Remove K Digits

**Difficulty:** Medium  
**LeetCode Problem:** #402  
**Tags:** `Monotonic Stack`, `Greedy`, `String`

---

## Problem Statement

Given string `num` representing a non-negative integer, and an integer `k`, return the smallest possible integer after removing `k` digits from the number.

**Constraints:**
- `1 <= k <= num.length <= 10^5`
- `num` consists of only digits
- `num` does not have leading zeros except for the zero itself

**Example 1:**
```
Input: num = "1432219", k = 3
Output: "1219"
Explanation: Remove digits 4, 3, 2 → "1219" is smallest
```

**Example 2:**
```
Input: num = "10200", k = 1
Output: "200"
Explanation: Remove leading 1 → "0200" → "200" (remove leading zeros)
```

**Example 3:**
```
Input: num = "10", k = 2
Output: "0"
```

---

## Step 1: State Space

### Problem Visualization

For `num = "1432219"`, `k = 3`:

```
Original: 1 4 3 2 2 1 9

Goal: Remove 3 digits to make smallest number

Strategy: Remove digits that make number larger
  - 4 > 3 → remove 4: "132219"
  - 3 > 2 → remove 3: "12219"
  - 2 > 1 → remove 2: "1219"

Result: "1219"
```

Greedy observation:
```
1 4 3 2 2 1 9
  ↑ Remove! (4 > 3, decreasing)
1 3 2 2 1 9
  ↑ Remove! (3 > 2, decreasing)
1 2 2 1 9
    ↑ Remove! (2 > 1, decreasing)
1 2 1 9
```

### Core Question

**Which k digits should we remove to make the resulting number as small as possible?**

Greedy insight: To minimize the number, we want smaller digits toward the front. Remove digits that create "peaks" (where digit is larger than the next one).

### Deriving from First Principles

**Observation 1: Leftmost digits matter most**
A number like "2XXX" is always larger than "1XXX" regardless of X.
Priority: Make leftmost digits as small as possible.

**Observation 2: Remove "decreasing pairs"**
If we see digit[i] > digit[i+1], removing digit[i] makes number smaller:
- "43..." → remove 4 → "3..." (smaller)
- Always better to remove the larger leftmost digit in a decreasing pair

**Observation 3: Greedy removal from left to right**
Process digits left to right:
- If current digit < previous digit, we should have removed previous
- This suggests keeping digits in **non-decreasing (increasing) order**

**Observation 4: Monotonic increasing stack**
Maintain stack of digits in increasing order:
- When we see smaller digit, pop larger digits (remove them)
- This greedily removes peaks
- Continue until we've removed k digits

**Observation 5: Edge cases after k removals**
- If k removals happened before end: continue adding remaining digits
- If fewer than k removals: remove from end (all increasing, remove largest)
- Remove leading zeros from result

**Formula:**
```
For each digit d in num:
  While stack not empty AND stack.top() > d AND removals < k:
    Pop from stack (remove digit)
    removals++
  Push d to stack

If removals < k:
  Remove (k - removals) digits from end

Build result, remove leading zeros
```

### State Space Structure

**Type:** String/stack representing remaining digits after k removals.

**Structure:**
- Stack maintaining increasing digit sequence
- Track number of removals

**Cardinality:** n digits, removing k, result has n-k digits.

**Key Property:** Monotonic increasing stack greedily minimizes result.

### Example Computation

Input: `num = "1432219"`, `k = 3`

```
Stack: [], removals = 0

Digit '1':
  Stack empty, push
  Stack: ['1']

Digit '4':
  Stack top '1' < '4', push
  Stack: ['1', '4']

Digit '3':
  Stack top '4' > '3' AND removals < 3, pop '4'
  removals = 1
  Stack top '1' < '3', push
  Stack: ['1', '3']

Digit '2':
  Stack top '3' > '2' AND removals < 3, pop '3'
  removals = 2
  Stack top '1' < '2', push
  Stack: ['1', '2']

Digit '2':
  Stack top '2' = '2', push
  Stack: ['1', '2', '2']

Digit '1':
  Stack top '2' > '1' AND removals < 3, pop '2'
  removals = 3 (done removing!)
  Stack top '2' > '1' but removals = k, don't pop
  Push '1'
  Stack: ['1', '2', '1']

Digit '9':
  Stack top '1' < '9', push
  Stack: ['1', '2', '1', '9']

removals = 3 = k, done
Result: "1219"
```

### Generation Pattern

**Brute Force (try all combinations):**
```csharp
// Try all C(n,k) combinations of k digits to remove
// For each combination, build resulting number and compare
// Time: O(C(n,k) * n) - exponential
```

---

## Step 2: Brute Force (Conceptual)

Brute force would try all ways to choose k digits to remove:
- Generate all combinations of k positions
- For each, build the resulting string
- Return the lexicographically smallest

This is exponential and impractical.

---

## Step 3: Optimization Ideas

### Key Insights
1. **Greedy is optimal:** Remove peaks from left to right
2. **Monotonic increasing stack:** Naturally removes peaks
3. **One pass:** O(n) solution

### Optimization Strategy
Use monotonic increasing stack to greedily remove larger digits when smaller digit appears.

---

## Step 4: Optimal Solution

```csharp
public string RemoveKdigits(string num, int k) {
    if (k >= num.Length) return "0";
    
    var stack = new Stack<char>();
    int removals = 0;
    
    foreach (char digit in num) {
        // Remove larger digits to make room for smaller digit
        while (stack.Count > 0 && stack.Peek() > digit && removals < k) {
            stack.Pop();
            removals++;
        }
        stack.Push(digit);
    }
    
    // If we haven't removed k digits, remove from end
    while (removals < k) {
        stack.Pop();
        removals++;
    }
    
    // Build result (stack is reversed)
    var result = new char[stack.Count];
    for (int i = stack.Count - 1; i >= 0; i--) {
        result[i] = stack.Pop();
    }
    
    // Remove leading zeros
    int start = 0;
    while (start < result.Length && result[start] == '0') {
        start++;
    }
    
    // If all zeros, return "0"
    if (start == result.Length) return "0";
    
    return new string(result, start, result.Length - start);
}
```

**Complexity:**
- Time: O(n) - each digit processed once
- Space: O(n) - stack

**Key Mechanics:**
1. **Monotonic increasing stack:** Remove peaks greedily
2. **Pop when smaller digit arrives:** Makes number smaller
3. **Handle remaining removals:** Remove from end if needed
4. **Remove leading zeros:** Clean up result

---

## Edge Cases

1. **All increasing digits:** `"123456", k=3` → Remove from end → `"123"`
2. **All same digits:** `"111111", k=3` → `"111"`
3. **All decreasing:** `"54321", k=2` → Remove largest from left → `"321"`
4. **Leading zeros:** `"10200", k=1` → `"200"`
5. **Remove all:** `"123", k=3` → `"0"`
6. **Result is zero:** `"10", k=2` → `"0"`

---

## Related Problems

1. **Create Maximum Number (LeetCode #321)** - Similar greedy with two arrays
2. **Largest Rectangle in Histogram** - Monotonic stack pattern
3. **Next Greater Element** - Stack maintains order

---

## Tags

`#monotonic-stack` `#greedy` `#string` `#increasing-stack` `#digit-removal`
