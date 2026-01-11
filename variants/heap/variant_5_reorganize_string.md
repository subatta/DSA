# Reorganize String

**Difficulty:** Medium  
**LeetCode Problem:** #767  
**Tags:** `Heap`, `Greedy`, `Hash Map`, `String`, `Frequency`

---

## Problem Statement

Given a string `s`, rearrange the characters of `s` so that any two adjacent characters are not the same.

Return any possible rearrangement of `s` or return `""` if not possible.

**Constraints:**
- `1 <= s.length <= 500`
- `s` consists of lowercase English letters.

**Example 1:**
```
Input: s = "aab"
Output: "aba"
```

**Example 2:**
```
Input: s = "aaab"
Output: ""
Explanation: Cannot rearrange so no two 'a' are adjacent.
```

**Example 3:**
```
Input: s = "vvvlo"
Output: "vlvov" (or other valid arrangements)
```

---

## Step 1: State Space

### Problem Visualization

Input: `s = "aab"`

```
Frequency: {a: 2, b: 1}

Greedy approach: Always place most frequent character first
(to maximize spacing between same characters)

Step 1: Most frequent is 'a' (count=2)
  Result: "a"
  Remaining: {a: 1, b: 1}

Step 2: Most frequent is 'a' or 'b' (both count=1)
  But can't use 'a' (just used it)
  Use 'b'
  Result: "ab"
  Remaining: {a: 1}

Step 3: Use 'a'
  Result: "aba"
  Done ✓

Output: "aba"
```

**Impossible case:** `s = "aaab"`

```
Frequency: {a: 3, b: 1}

Observation: 3 'a's need 2 different characters between them
Pattern: a_a_a
But we only have 1 'b': a b a _ a

Cannot fill all gaps → impossible

Mathematical condition:
  If max_freq > (n + 1) / 2, impossible
  Here: 3 > (4 + 1) / 2 = 2.5, so 3 > 2, impossible ✓
```

**Possible case:** `s = "aaabb"`

```
Frequency: {a: 3, b: 2}
n = 5, max_freq = 3
Check: 3 <= (5 + 1) / 2 = 3 ✓ Possible

Greedy with heap:
  Heap: [(a,3), (b,2)]

Step 1: Pop (a,3), use 'a'
  Result: "a"
  Push back (a,2)
  Heap: [(a,2), (b,2)]

Step 2: Pop (a,2) or (b,2)
  Can't use 'a' (just used), use (b,2)
  Result: "ab"
  Push back (b,1)
  Heap: [(a,2), (b,1)]

Step 3: Pop (a,2)
  Result: "aba"
  Push back (a,1)
  Heap: [(a,1), (b,1)]

Step 4: Pop (b,1) (can't use 'a')
  Result: "abab"
  b exhausted
  Heap: [(a,1)]

Step 5: Pop (a,1)
  Result: "ababa"
  Done ✓
```

### Core Question

**Can we rearrange string so no two adjacent characters are the same?**

Greedy: Always place most frequent remaining character that isn't the previous one.

### Deriving from First Principles

**Observation 1: Necessary condition**
```
If one character appears more than ⌈n/2⌉ times:
  Impossible to avoid adjacency
  
Example: n=5, max_freq must be ≤ 3
  Pattern: X _ X _ X (3 X's need 2 other chars)
  
Example: n=6, max_freq must be ≤ 3
  Pattern: X _ X _ X _ (3 X's need 3 other chars)

Condition: max_freq <= ⌈n/2⌉ or equivalently max_freq <= (n+1)/2
```

**Observation 2: Greedy placement**
```
If condition satisfied, greedy works:
1. Always place most frequent remaining character
2. That isn't same as previous character

Why? Maximize spacing by spreading frequent chars early.
```

**Observation 3: Max-heap for frequency**
```
Use max-heap to always get most frequent character
Track previous character to avoid adjacency
```

**Observation 4: Two-at-a-time placement**
```
Alternative: Pop 2 most frequent, place both
This ensures they're different
Continue until heap has < 2 elements
If 1 remains, place it (must be valid)
If > 1 remain, impossible (shouldn't happen with right condition)
```

**Observation 5: Validation**
```
Before starting: Check max_freq <= (n+1)/2
If fails: return ""
Else: proceed with greedy heap
```

**Formula (Approach 1: Place one at a time):**
```
if max_freq > (n+1)/2:
  return ""

heap = max-heap of (char, freq)
result = []
prev = null

while heap not empty:
  // Get most frequent that isn't prev
  if heap.top.char == prev and heap.size == 1:
    return ""  // Impossible (shouldn't happen if condition checked)
  
  if heap.top.char == prev:
    second = heap.popSecond()
    use second
    push back if count > 0
  else:
    top = heap.pop()
    use top
    push back if count > 0
  
  prev = used char

return result
```

**Formula (Approach 2: Place two at a time):**
```
if max_freq > (n+1)/2:
  return ""

heap = max-heap of (char, freq)
result = []

while heap.size >= 2:
  first = heap.pop()
  second = heap.pop()
  
  result.append(first.char)
  result.append(second.char)
  
  if first.count > 1: heap.push(first.char, first.count-1)
  if second.count > 1: heap.push(second.char, second.count-1)

if heap.size == 1:
  result.append(heap.pop().char)

return result
```

### State Space Structure

**Type:** Greedy placement with max-heap.

**Structure:**
- Frequency map
- Max-heap ordered by frequency
- Build result greedily

**Cardinality:**
- Count frequencies: O(n)
- Build heap: O(k log k) where k = unique chars (≤ 26)
- Process: O(n log k)
- Space: O(k) for heap

**Key Property:** Greedy placement of most frequent ensures valid arrangement.

### Example Computation

Input: `s = "aaabb"`

```
Step 1: Count frequencies
  freq = {a: 3, b: 2}
  n = 5

Step 2: Check feasibility
  max_freq = 3
  (n+1)/2 = (5+1)/2 = 3
  3 <= 3 ✓ Feasible

Step 3: Build max-heap
  heap = [(a,3), (b,2)]

Step 4: Greedy placement (two-at-a-time)
  
  Iteration 1:
    Pop (a,3), (b,2)
    Place: "ab"
    Push back (a,2), (b,1)
    Heap: [(a,2), (b,1)]
  
  Iteration 2:
    Pop (a,2), (b,1)
    Place: "abab"
    Push back (a,1)
    Heap: [(a,1)]
  
  Final:
    Heap size = 1, append 'a'
    Result: "ababa"

Validation: No adjacent same characters ✓
```

### Generation Pattern

**Brute force:**
```csharp
// Try all permutations of string
// Check each for adjacent same characters
// Return first valid or ""
// Time: O(n! * n) - exponential
```

---

## Step 2: Brute Force

Try all rearrangements, check validity: Exponential time.

---

## Step 3: Optimization Ideas

### Key Insights
1. **Feasibility check:** max_freq <= (n+1)/2
2. **Greedy works:** Place most frequent first
3. **Max-heap:** Get most frequent in O(log k)
4. **Two-at-a-time:** Ensures different adjacency

---

## Step 4: Optimal Solution

```csharp
public string ReorganizeString(string s) {
    int n = s.Length;
    
    // Count frequency of each character
    var freq = new Dictionary<char, int>();
    int maxFreq = 0;
    foreach (char c in s) {
        freq[c] = freq.GetValueOrDefault(c) + 1;
        maxFreq = Math.Max(maxFreq, freq[c]);
    }
    
    // Check feasibility
    if (maxFreq > (n + 1) / 2) {
        return "";
    }
    
    // Build max-heap (use negative for max behavior)
    var heap = new PriorityQueue<(char c, int count), int>();
    foreach (var kvp in freq) {
        heap.Enqueue((kvp.Key, kvp.Value), -kvp.Value);
    }
    
    var result = new StringBuilder();
    
    // Place two characters at a time
    while (heap.Count >= 2) {
        var (c1, count1) = heap.Dequeue();
        var (c2, count2) = heap.Dequeue();
        
        result.Append(c1);
        result.Append(c2);
        
        if (count1 > 1) {
            heap.Enqueue((c1, count1 - 1), -(count1 - 1));
        }
        if (count2 > 1) {
            heap.Enqueue((c2, count2 - 1), -(count2 - 1));
        }
    }
    
    // If one character left, it must be placeable
    if (heap.Count == 1) {
        var (c, count) = heap.Dequeue();
        result.Append(c);
    }
    
    return result.ToString();
}
```

**Complexity:**
- Time: O(n log k) where k = unique characters (≤ 26)
  - Count frequencies: O(n)
  - Heap operations: O(n log k)
- Space: O(k) - heap and frequency map

**Why Two-at-a-Time Works:**
- Pop 2 most frequent characters
- They're different (from different heap entries)
- Place them adjacent → guaranteed no conflict
- Continue until done

---

## Alternative: Placement with Gap

Fill even positions first, then odd:

```csharp
public string ReorganizeString(string s) {
    int n = s.Length;
    var freq = new int[26];
    int maxFreq = 0;
    char maxChar = 'a';
    
    foreach (char c in s) {
        freq[c - 'a']++;
        if (freq[c - 'a'] > maxFreq) {
            maxFreq = freq[c - 'a'];
            maxChar = c;
        }
    }
    
    if (maxFreq > (n + 1) / 2) {
        return "";
    }
    
    var result = new char[n];
    int idx = 0;
    
    // Place most frequent character at even positions
    while (freq[maxChar - 'a'] > 0) {
        result[idx] = maxChar;
        idx += 2;
        freq[maxChar - 'a']--;
    }
    
    // Place remaining characters
    for (int i = 0; i < 26; i++) {
        while (freq[i] > 0) {
            if (idx >= n) {
                idx = 1;  // Start at odd positions
            }
            result[idx] = (char)('a' + i);
            idx += 2;
            freq[i]--;
        }
    }
    
    return new string(result);
}
```

**Complexity:** Same O(n), but simpler (no heap).

---

## Edge Cases

1. **Empty string:** `s = ""` → `""`
2. **Single character:** `s = "a"` → `"a"`
3. **Two same:** `s = "aa"` → `""` (impossible)
4. **All different:** `s = "abc"` → `"abc"` (any order works)
5. **Exact boundary:** `s = "aaab"` (4 chars, max 3) → `""`, `s = "aabb"` → `"abab"`

---

## Visualization

```
Example: "aaabb"

Frequency: {a:3, b:2}
Max-heap: [(a,3), (b,2)]

Round 1: Pop a,b
  Result: "ab"
  Heap: [(a,2), (b,1)]

Round 2: Pop a,b
  Result: "abab"
  Heap: [(a,1)]

Final: Append a
  Result: "ababa"

Check: a≠b, b≠a, a≠b, b≠a, a (no adjacent same) ✓
```

---

## Related Problems

1. **Task Scheduler (LeetCode #621)** - Similar cooldown spacing
2. **Rearrange String k Distance Apart (LeetCode #358)** - Generalized version
3. **Distant Barcodes (LeetCode #1054)** - Same problem with integers
4. **Longest Happy String (LeetCode #1405)** - Multiple character constraint

---

## Pattern Recognition

**Problem Asks For:**
- Rearrange with adjacency constraint
- No two same characters adjacent
- String reordering

**This Suggests:**
- Greedy placement
- Frequency-based ordering
- Max-heap for most frequent
- → **Greedy + Max-Heap pattern**

**Key Indicators:**
- "No two adjacent same"
- "Rearrange" or "reorganize"
- Feasibility check based on frequency

---

## Tags

`#heap` `#max-heap` `#greedy` `#hash-map` `#string` `#frequency` `#adjacency-constraint`
