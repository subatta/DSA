# Variant: Longest Substring with At Most K Distinct Characters

**Pattern:** Sliding Window + Hash Map (HYBRID)  
**Difficulty:** Medium  
**LeetCode:** #340

---

## Problem Statement

Given a string `s` and an integer `k`, return the **length** of the longest substring that contains at most `k` distinct characters.

### Input/Output:
- Input: `s = "eceba"`, `k = 2`
- Output: `3`
- Explanation: Substring `"ece"` has 2 distinct characters, length 3

---

## **Step 1: Derive Full State Space from Problem**

### **Problem Analysis:**
- Find **longest** substring with constraint: ≤ k distinct characters
- Must consider all possible contiguous substrings
- Track distinct character count

### **State Space Definition:**
- **Type**: All non-zero-length contiguous substrings
- **Structure**: Linear sequences `[start, end]` where `0 ≤ start ≤ end < n`
- **Cardinality**: n(n+1)/2 substrings = **O(n²)**
- **Generation**: Nested loops - outer for start, inner for end

### **Full State Space Enumeration:**
For `s = "eceba"` (n=5), there are 5×6/2 = **15 substrings**:

```
Start=0: "e", "ec", "ece", "eceb", "eceba"    (5)
Start=1: "c", "ce", "ceb", "ceba"             (4)
Start=2: "e", "eb", "eba"                     (3)
Start=3: "b", "ba"                            (2)
Start=4: "a"                                  (1)
Total: 15 substrings
```

**With distinct character counts (k=2):**
```
"e"→1 ✅, "ec"→2 ✅, "ece"→2 ✅ len=3, "eceb"→3 ❌, "eceba"→4 ❌
"c"→1 ✅, "ce"→2 ✅, "ceb"→3 ❌, "ceba"→4 ❌
"e"→1 ✅, "eb"→2 ✅, "eba"→3 ❌
"b"→1 ✅, "ba"→2 ✅
"a"→1 ✅
```

**Valid substrings (≤2 distinct):**
- Length 1: "e", "c", "e", "b", "a" (5 substrings)
- Length 2: "ec", "ce", "eb", "ba" (4 substrings)
- Length 3: "ece" (1 substring) ← **Maximum**

---

## **Step 2: Brute Force Solution with Value Tracing**

### **Brute Force Approach:**
1. Generate all contiguous substrings using nested loops
2. For each substring, count distinct characters using a hash set
3. Track maximum length among valid substrings

### **Brute Force Code:**
```csharp
int LengthOfLongestSubstringKDistinctBruteForce(string s, int k) 
{
    int maxLen = 0;
    
    // Generate all substrings
    for (int start = 0; start < s.Length; start++)
    {
        var charSet = new HashSet<char>();
        
        for (int end = start; end < s.Length; end++)
        {
            charSet.Add(s[end]);
            
            // Check if valid
            if (charSet.Count <= k)
            {
                int length = end - start + 1;
                maxLen = Math.Max(maxLen, length);
            }
            else
            {
                break; // More than k distinct, stop extending
            }
        }
    }
    
    return maxLen;
}
```

### **Brute Force Value Trace (s = "eceba", k = 2):**

| start | end | substring | chars | Add | Set           | distinct | ≤ 2? | length | maxLen |
|-------|-----|-----------|-------|-----|---------------|----------|------|--------|--------|
| 0     | 0   | "e"       | -     | e   | {e}           | 1        | ✅   | 1      | 1      |
| 0     | 1   | "ec"      | {e}   | c   | {e,c}         | 2        | ✅   | 2      | 2      |
| 0     | 2   | "ece"     | {e,c} | e   | {e,c}         | 2        | ✅   | 3      | 3      |
| 0     | 3   | "eceb"    | {e,c} | b   | {e,c,b}       | 3        | ❌   | -      | 3      |
| 1     | 1   | "c"       | -     | c   | {c}           | 1        | ✅   | 1      | 3      |
| 1     | 2   | "ce"      | {c}   | e   | {c,e}         | 2        | ✅   | 2      | 3      |
| 1     | 3   | "ceb"     | {c,e} | b   | {c,e,b}       | 3        | ❌   | -      | 3      |
| 2     | 2   | "e"       | -     | e   | {e}           | 1        | ✅   | 1      | 3      |
| 2     | 3   | "eb"      | {e}   | b   | {e,b}         | 2        | ✅   | 2      | 3      |
| 2     | 4   | "eba"     | {e,b} | a   | {e,b,a}       | 3        | ❌   | -      | 3      |
| 3     | 3   | "b"       | -     | b   | {b}           | 1        | ✅   | 1      | 3      |
| 3     | 4   | "ba"      | {b}   | a   | {b,a}         | 2        | ✅   | 2      | 3      |
| 4     | 4   | "a"       | -     | a   | {a}           | 1        | ✅   | 1      | **3**  |

**Result: 3** (substring `"ece"`)

### **Complexity:**
- **Time**: O(n²) to generate substrings × O(n) to track distinct chars = O(n³) worst case
  - But with early termination (break when > k), average case closer to O(n²)
- **Space**: O(k) for character set

---

## **Step 3: Can We Prune the Brute Force?**

### **Key Observations:**

1. **Early Termination**: Already implemented - stop when distinct > k
   ```
   "ece" → 2 distinct ✅
   "eceb" → 3 distinct ❌ → stop, don't try "eceba"
   ```

2. **Overlapping Windows**: Recalculating character sets wastes work
   ```
   Window "ece" → {e, c}
   Window "ce" → rebuild set from scratch ← wasteful!
   ```

3. **Frequency Tracking**: Need counts, not just presence
   ```
   "ece": {e→2, c→1}
   Remove left 'e': {e→1, c→1} ← still 2 distinct
   Remove another 'e': {c→1} ← now 1 distinct
   ```

4. **Shrinking Pattern**: When distinct > k, shrink from left until valid
   ```
   "eceb" → 3 distinct ❌
   Remove 'e': "ceb" → still 3 distinct ❌
   Remove 'c': "eb" → 2 distinct ✅
   ```

### **Can We Do Better?**
**YES!** Use **sliding window with frequency hash map** to track character counts

---

## **Step 4: Optimal Solution - Sliding Window + Hash Map**

### **Key Insight:**
Maintain a **sliding window** with character frequency map:
- **Expand** right pointer to include new characters
- **Track** frequency of each character in hash map
- **Contract** left pointer when distinct count > k
- **Update** maximum length when window is valid

### **Why Frequency Map?**
```
String "ece": {e→2, c→1}
  Remove left 'e': {e→1, c→1} → distinct = 2 (e still present!)
  
vs Set approach:
  Remove 'e': {c} → would incorrectly think e is gone
```

### **Canonical Skeleton Applied:**
```csharp
int SlidingWindowTemplate(string s, int k) {
    int left = 0;
    int maxLen = 0;
    var window = new Dictionary<char, int>();

    for (int right = 0; right < s.Length; right++) {
        // Add right character
        char c = s[right];
        window[c] = window.GetValueOrDefault(c) + 1;
        
        // Shrink while condition violated
        while (window.Count > k) {
            char leftChar = s[left];
            window[leftChar]--;
            if (window[leftChar] == 0)
                window.Remove(leftChar);
            left++;
        }

        // Update result
        maxLen = Math.Max(maxLen, right - left + 1);
    }

    return maxLen;
}
```

### **Optimized Solution:**
```csharp
int LengthOfLongestSubstringKDistinct(string s, int k) 
{
    if (k == 0) return 0;
    
    var window = new Dictionary<char, int>();
    int left = 0, maxLen = 0;
    
    for (int right = 0; right < s.Length; right++)
    {
        // Expand window: add right character
        char c = s[right];
        window[c] = window.GetValueOrDefault(c) + 1;
        
        // Shrink window while distinct > k
        while (window.Count > k)
        {
            char leftChar = s[left];
            window[leftChar]--;
            
            // Remove character if count becomes 0
            if (window[leftChar] == 0)
                window.Remove(leftChar);
                
            left++;
        }
        
        // Update maximum length
        maxLen = Math.Max(maxLen, right - left + 1);
    }
    
    return maxLen;
}
```

### **Changes from Skeleton:**
1. **windowState** → `Dictionary<char, int>` (character → frequency)
2. **ConditionViolated** → `window.Count > k` (too many distinct)
3. **Remove logic** → Decrement count, remove key if count=0
4. **UpdateResult** → Track maximum window length
5. **Early exit**: Check `k == 0` upfront

### **Optimized Value Trace (s = "eceba", k = 2):**

| Step | right | s[right] | window (after add)    | distinct | > k? | Shrink | left | window (after shrink) | len | maxLen |
|------|-------|----------|-----------------------|----------|------|-----------|------|-----------------------|-----|--------|
| 1    | 0     | 'e'      | {e:1}                 | 1        | ❌   | -         | 0    | {e:1}                 | 1   | 1      |
| 2    | 1     | 'c'      | {e:1, c:1}            | 2        | ❌   | -         | 0    | {e:1, c:1}            | 2   | 2      |
| 3    | 2     | 'e'      | {e:2, c:1}            | 2        | ❌   | -         | 0    | {e:2, c:1}            | 3   | **3**  |
| 4    | 3     | 'b'      | {e:2, c:1, b:1}       | 3        | ✅   | YES       | 0    | -                     | -   | 3      |
|      |       |          |                       |          |      | Remove 'e'| 1    | {e:1, c:1, b:1}       | -   | 3      |
|      |       |          |                       | 3        | ✅   | YES       | -    | -                     | -   | 3      |
|      |       |          |                       |          |      | Remove 'c'| 2    | {e:1, b:1}            | 2   | 3      |
| 5    | 4     | 'a'      | {e:1, b:1, a:1}       | 3        | ✅   | YES       | 2    | -                     | -   | 3      |
|      |       |          |                       |          |      | Remove 'e'| 3    | {b:1, a:1}            | 2   | 3      |

**Detailed Step 4 Shrinking (right=3, s[right]='b'):**
```
After add: window = {e:2, c:1, b:1}, distinct = 3, left = 0

While distinct(3) > k(2):
  Iteration 1:
    leftChar = s[0] = 'e'
    window['e']-- → {e:1, c:1, b:1}
    e count = 1 (not 0, keep key)
    left = 1
    distinct = 3 → still > 2, continue
  
  Iteration 2:
    leftChar = s[1] = 'c'
    window['c']-- → {e:1, c:0, b:1}
    c count = 0 → remove 'c' → {e:1, b:1}
    left = 2
    distinct = 2 → ≤ 2, stop

Final window: {e:1, b:1} at [2,3] = "eb", length = 2
```

**Result: 3**

### **State Space Comparison:**

| Approach | Substrings Explored | Character Tracking | Total Operations |
|----------|--------------------|--------------------|------------------|
| Brute Force | 15 substrings | O(n) set ops per substring | O(n²) or O(n³) |
| Sliding Window | 5 windows | O(1) map ops per step | O(n) ≈ 10 ops |

**Why O(n)?**
- Each character enters window once → n additions
- Each character exits window at most once → n removals
- Total map operations: 2n = O(n) ✅

### **Big-O Analysis:**
- **Time**: O(n) - each character processed twice (enter + exit)
- **Space**: O(k) - map stores at most k+1 distinct characters (during shrinking)
- **Optimization**: O(n²) or O(n³) → **O(n)** ✅

### **Key Takeaway:**
**Sliding Window + Hash Map for Constraint Counting**:
- Use **frequency map** (not set) when elements can repeat
- **Shrink while invalid** → restore constraint
- **Track maximization** during valid states
- Pattern applies to: distinct count, character frequency, subset problems

This is the inverse of [Variant #3: Minimum Window Substring](variant_3_min_window_substring.md):
- **This problem**: Maximize length, constraint on distinct count
- **Variant #3**: Minimize length, constraint on character coverage

---

## **Related Problems:**
- [Longest Substring Without Repeating Characters](variant_1_longest_substring_no_repeat.md) - Special case where k = all distinct
- [Minimum Window Substring](variant_3_min_window_substring.md) - Minimization with character coverage
- Longest Substring with At Least K Repeating Characters - Different constraint (frequency)
- Fruit Into Baskets - Same problem with k=2 and different story

## **Tags:**
`sliding-window` `hash-map` `hybrid` `string` `O(n²)-state-space` `variable-window` `medium` `distinct-counting`
