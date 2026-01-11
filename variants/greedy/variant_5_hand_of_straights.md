# Hand of Straights

**Difficulty:** Medium  
**LeetCode Problem:** #846  
**Tags:** `Greedy`, `Hash Map`, `Sorting`, `Grouping`

---

## Problem Statement

Alice has some number of cards and she wants to rearrange the cards into groups so that each group is of size `groupSize`, and consists of `groupSize` consecutive cards.

Given an integer array `hand` where `hand[i]` is the value written on the `i-th` card and an integer `groupSize`, return `true` if she can rearrange the cards, or `false` otherwise.

**Constraints:**
- `1 <= hand.length <= 10^4`
- `0 <= hand[i] <= 10^9`
- `1 <= groupSize <= hand.length`

**Example 1:**
```
Input: hand = [1,2,3,6,2,3,4,7,8], groupSize = 3
Output: true
Explanation: Groups: [1,2,3], [2,3,4], [6,7,8]
```

**Example 2:**
```
Input: hand = [1,2,3,4,5], groupSize = 4
Output: false
Explanation: Cannot make groups of 4 consecutive cards
```

**Example 3:**
```
Input: hand = [1,2,3,4,5,6,7,8], groupSize = 4
Output: true
Explanation: Groups: [1,2,3,4], [5,6,7,8]
```

---

## Step 1: State Space

### Problem Visualization

For `hand = [1,2,3,6,2,3,4,7,8]`, `groupSize = 3`:

```
Sorted: [1,2,2,3,3,4,6,7,8]
Count:  {1:1, 2:2, 3:2, 4:1, 6:1, 7:1, 8:1}

Form groups starting from smallest:

Group 1: Need [1,2,3]
  Use 1 (count: 1→0)
  Use 2 (count: 2→1)
  Use 3 (count: 2→1)
  ✓ [1,2,3]

Group 2: Smallest remaining is 2, need [2,3,4]
  Use 2 (count: 1→0)
  Use 3 (count: 1→0)
  Use 4 (count: 1→0)
  ✓ [2,3,4]

Group 3: Smallest remaining is 6, need [6,7,8]
  Use 6 (count: 1→0)
  Use 7 (count: 1→0)
  Use 8 (count: 1→0)
  ✓ [6,7,8]

All cards used: true
```

Impossible case: `hand = [1,2,3,5]`, `groupSize = 3`:
```
Sorted: [1,2,3,5]
Count:  {1:1, 2:1, 3:1, 5:1}

Group 1: Need [1,2,3]
  Use 1,2,3 ✓

Remaining: {5:1}
Group 2: Need [5,6,7]
  5 exists but 6 doesn't exist ✗

Cannot form groups: false
```

### Core Question

**Can we partition all cards into groups of consecutive cards of size `groupSize`?**

Greedy approach: Start from smallest card, form groups greedily.

### Deriving from First Principles

**Observation 1: Necessary condition**
```
If hand.length % groupSize != 0:
  Cannot form complete groups
  Return false
```

**Observation 2: Greedy starting point**
Always start a new group with the smallest available card.
Why? If card x must be in a group, it must start a group [x, x+1, ..., x+groupSize-1].
No other arrangement works if x is the smallest.

**Observation 3: Count frequency**
Use hash map to count occurrences of each card.
To form group starting at x, we need:
- count[x] >= 1
- count[x+1] >= 1
- ...
- count[x+groupSize-1] >= 1

**Observation 4: Process in sorted order**
Process unique card values in sorted order.
For each card x with count[x] > 0:
- Form count[x] groups starting from x
- Decrement counts for x, x+1, ..., x+groupSize-1

**Observation 5: Validation**
If at any point we need card y but count[y] = 0, return false.
After processing all, all counts should be 0.

**Formula:**
```
If n % groupSize != 0:
  return false

count = frequency map of hand
sorted_keys = sorted unique values

for each key in sorted_keys:
  while count[key] > 0:
    for i = 0 to groupSize-1:
      card = key + i
      if count[card] == 0:
        return false
      count[card]--
      
return true
```

### State Space Structure

**Type:** Greedy grouping with frequency counting.

**Structure:**
- Hash map for frequencies
- Sorted unique values
- Greedy group formation from smallest

**Cardinality:**
- O(n) to build frequency map
- O(k log k) to sort unique values (k = unique cards)
- O(n) to form groups
- Total: O(n log n) worst case

**Key Property:** Greedy starting from smallest card ensures optimal grouping if solution exists.

### Example Computation

Input: `hand = [1,2,3,6,2,3,4,7,8]`, `groupSize = 3`

```
n = 9, 9 % 3 = 0 ✓

Frequency map:
{1:1, 2:2, 3:2, 4:1, 6:1, 7:1, 8:1}

Sorted keys: [1,2,3,4,6,7,8]

Process key=1 (count=1):
  Form 1 group starting at 1:
    [1,2,3]: count[1]--=0, count[2]--=1, count[3]--=1

Process key=2 (count=1):
  Form 1 group starting at 2:
    [2,3,4]: count[2]--=0, count[3]--=0, count[4]--=0

Process key=3 (count=0): Skip

Process key=4 (count=0): Skip

Process key=6 (count=1):
  Form 1 group starting at 6:
    [6,7,8]: count[6]--=0, count[7]--=0, count[8]--=0

All processed successfully: return true
```

### Generation Pattern

**Brute force:**
```csharp
// Try all possible groupings
// Check if each grouping has consecutive cards
// Time: Exponential
```

---

## Step 2: Brute Force

Try all possible groupings, validate each: Exponential time.

---

## Step 3: Optimization Ideas

### Key Insights
1. **Frequency map:** Count occurrences in O(n)
2. **Sort unique values:** Process in order
3. **Greedy group formation:** Start with smallest available
4. **Validation while forming:** Check availability of consecutive cards

---

## Step 4: Optimal Solution

```csharp
public bool IsNStraightHand(int[] hand, int groupSize) {
    int n = hand.Length;
    
    // Check if total cards divisible by groupSize
    if (n % groupSize != 0) {
        return false;
    }
    
    // Count frequency of each card
    var count = new Dictionary<int, int>();
    foreach (int card in hand) {
        count[card] = count.GetValueOrDefault(card) + 1;
    }
    
    // Sort unique card values
    var sortedCards = count.Keys.OrderBy(x => x).ToList();
    
    // Try to form groups greedily
    foreach (int card in sortedCards) {
        while (count[card] > 0) {
            // Try to form a group starting at 'card'
            for (int i = 0; i < groupSize; i++) {
                int needed = card + i;
                
                if (!count.ContainsKey(needed) || count[needed] == 0) {
                    return false;  // Cannot form consecutive group
                }
                
                count[needed]--;
            }
        }
    }
    
    return true;
}
```

**Complexity:**
- Time: O(n log n)
  - Build frequency map: O(n)
  - Sort unique values: O(k log k) where k ≤ n
  - Form groups: O(n) total decrements
- Space: O(n) - frequency map

**Alternative (using SortedDictionary for auto-sort):**
```csharp
public bool IsNStraightHand(int[] hand, int groupSize) {
    if (hand.Length % groupSize != 0) return false;
    
    // SortedDictionary maintains sorted order
    var count = new SortedDictionary<int, int>();
    foreach (int card in hand) {
        count[card] = count.GetValueOrDefault(card) + 1;
    }
    
    while (count.Count > 0) {
        // Get smallest card
        int start = count.First().Key;
        
        // Try to form group [start, start+1, ..., start+groupSize-1]
        for (int i = 0; i < groupSize; i++) {
            int card = start + i;
            
            if (!count.ContainsKey(card)) {
                return false;
            }
            
            count[card]--;
            if (count[card] == 0) {
                count.Remove(card);
            }
        }
    }
    
    return true;
}
```

---

## Edge Cases

1. **Single group:** `[1,2,3], groupSize=3` → `true`
2. **Cannot divide:** `[1,2,3,4,5], groupSize=2` → `false` (odd length)
3. **Gap in sequence:** `[1,2,4,5], groupSize=2` → `false`
4. **Multiple same cards:** `[1,1,2,2,3,3], groupSize=3` → `true` ([1,2,3], [1,2,3])
5. **Single card:** `[1], groupSize=1` → `true`

---

## Why Greedy Works

**Claim:** Starting groups from smallest card is optimal.

**Proof:**
- Suppose smallest available card is x.
- Card x must be in some group.
- Since groups are consecutive, x must be in group [x, x+1, ..., x+k-1].
- No other group contains x (since x is smallest).
- Therefore, greedy choice of forming group starting at x is safe.

---

## Related Problems

1. **Divide Array in Sets of K Consecutive Numbers (LeetCode #1296)** - Same problem
2. **Maximum Length of Pair Chain (LeetCode #646)** - Greedy intervals
3. **Find the Duplicate Number (LeetCode #287)** - Array grouping

---

## Pattern Recognition

**Problem Asks For:**
- Group elements by consecutive values
- Fixed group size
- Validate possibility

**This Suggests:**
- Frequency counting
- Sort and process in order
- Greedy group formation
- → **Greedy + Hash Map pattern**

**Key Indicators:**
- "Consecutive" or "straight" grouping
- Fixed size groups
- Check if rearrangement possible

---

## Tags

`#greedy` `#hash-map` `#sorting` `#consecutive` `#grouping` `#frequency-count`
