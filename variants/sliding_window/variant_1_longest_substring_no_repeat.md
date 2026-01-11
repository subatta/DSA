# Variant: Longest Substring Without Repeating Characters

**Pattern:** Sliding Window  
**Difficulty:** Medium  
**LeetCode:** #3

---

## Problem Statement

Given a string `s`, find the length of the longest substring without repeating characters.

### Input/Output:
- Input: `s = "abcabcbb"`
- Output: `3` (substring `"abc"`)

---

## **Step 1: Derive Full State Space from Problem**

### **Problem Analysis:**
- Find **longest substring** (contiguous sequence) with **all distinct characters**
- Need to check **all possible substrings** to find the longest valid one

### **State Space Definition:**

**Reference:** See [All Contiguous Subarrays](../../state_spaces/all_contiguous_subarrays.md) for detailed mathematical derivation

- **Type**: All contiguous substrings (includes single characters)
- **Structure**: Linear sequences `[start, end]` where `0 ≤ start ≤ end < n`
- **Cardinality**: n(n+1)/2 substrings = **O(n²)**
- **Generation**: Nested loops - outer loop for start index, inner for end index

### **Full State Space Enumeration:**
For `s = "abcabcbb"` (n=8), there are 8×9/2 = 36 substrings:

```
Start=0: "a", "ab", "abc", "abca", "abcab", "abcabc", "abcabcb", "abcabcbb"  (8)
Start=1: "b", "bc", "bca", "bcab", "bcabc", "bcabcb", "bcabcbb"              (7)
Start=2: "c", "ca", "cab", "cabc", "cabcb", "cabcbb"                         (6)
Start=3: "a", "ab", "abc", "abcb", "abcbb"                                   (5)
Start=4: "b", "bc", "bcb", "bcbb"                                            (4)
Start=5: "c", "cb", "cbb"                                                    (3)
Start=6: "b", "bb"                                                           (2)
Start=7: "b"                                                                 (1)
Total: 36 substrings
```

---

## **Step 2: Brute Force Solution with Value Tracing**

### **Brute Force Approach:**
1. Generate all substrings using nested loops
2. For each substring, check if all characters are distinct
3. Track maximum length found

### **Brute Force Code:**
```csharp
int LongestSubstringBruteForce(string s) 
{
    int maxLen = 0;
    
    // Generate all substrings
    for (int start = 0; start < s.Length; start++)
    {
        for (int end = start; end < s.Length; end++)
        {
            // Extract substring [start, end]
            string substring = s.Substring(start, end - start + 1);
            
            // Check if all characters are distinct
            if (HasAllDistinct(substring))
            {
                maxLen = Math.Max(maxLen, substring.Length);
            }
        }
    }
    
    return maxLen;
}

bool HasAllDistinct(string str)
{
    var seen = new HashSet<char>();
    foreach (char c in str)
    {
        if (seen.Contains(c))
            return false;
        seen.Add(c);
    }
    return true;
}
```

### **Brute Force Value Trace (s = "abcabcbb"):**

| start | end | substring  | distinct? | length | maxLen |
|-------|-----|------------|-----------|--------|--------|
| 0     | 0   | "a"        | ✅ YES    | 1      | 1      |
| 0     | 1   | "ab"       | ✅ YES    | 2      | 2      |
| 0     | 2   | "abc"      | ✅ YES    | 3      | **3**  |
| 0     | 3   | "abca"     | ❌ NO (a repeats) | - | 3 |
| 0     | 4   | "abcab"    | ❌ NO     | -      | 3      |
| ...   | ... | ...        | ...       | ...    | ...    |
| 1     | 1   | "b"        | ✅ YES    | 1      | 3      |
| 1     | 2   | "bc"       | ✅ YES    | 2      | 3      |
| 1     | 3   | "bca"      | ✅ YES    | 3      | 3      |
| 1     | 4   | "bcab"     | ❌ NO (b repeats) | - | 3 |
| ...   | ... | ...        | ...       | ...    | ...    |
| 3     | 3   | "a"        | ✅ YES    | 1      | 3      |
| 3     | 4   | "ab"       | ✅ YES    | 2      | 3      |
| 3     | 5   | "abc"      | ✅ YES    | 3      | 3      |
| 3     | 6   | "abcb"     | ❌ NO (b repeats) | - | 3 |

**Result: maxLen = 3**

### **Complexity:**
- **Time**: O(n²) to generate substrings × O(n) to check distinctness = **O(n³)**
- **Space**: O(n) for hashset

---

## **Step 3: Can We Prune the Brute Force?**

### **Key Observations:**

1. **Early Termination**: When we find a duplicate at `end`, all extensions beyond `end` from same `start` will also have duplicates
   ```
   "abc" → distinct ✅
   "abca" → 'a' repeats ❌
   "abcab", "abcabc", ... → all will have 'a' repeating ❌
   ```

2. **Redundant Work**: We recompute distinctness from scratch for each substring
   - `"abc"` → check {a, b, c}
   - `"abca"` → check {a, b, c, a} ← recheck a, b, c unnecessarily

3. **Overlapping Windows**: Many substrings share prefixes
   - Both `"abc"` and `"abca"` process 'a', 'b', 'c' from scratch

### **Pruned Brute Force:**
```csharp
int LongestSubstringPruned(string s) 
{
    int maxLen = 0;
    
    for (int start = 0; start < s.Length; start++)
    {
        var seen = new HashSet<char>();
        
        for (int end = start; end < s.Length; end++)
        {
            char c = s[end];
            
            // Early termination - found duplicate
            if (seen.Contains(c))
                break; // Stop extending from this start
                
            seen.Add(c);
            maxLen = Math.Max(maxLen, end - start + 1);
        }
    }
    
    return maxLen;
}
```

**Improvement**: O(n²) time (no O(n) distinctness check per substring)

### **Is This Good Enough?**
**NO!** Still O(n²) - for n=10,000 we do 100 million operations. We can do better!

---

## **Step 4: Optimal Solution - Sliding Window**

### **Key Insight:**
Instead of restarting from each position, maintain a **sliding window** of distinct characters:
- **Expand** right pointer to include new characters
- **Contract** left pointer when duplicate found
- Each character enters and leaves window **at most once** → O(n)

### **Why This Works:**
When we find duplicate at `right`, instead of starting fresh from `start+1`, we can:
- Keep all the work done so far (characters in window)
- Just remove characters from left until duplicate is gone

### **Canonical Skeleton Applied:**
```csharp
int SlidingWindowTemplate(int[] nums, int kOrCondition) 
{
    int left = 0;
    var windowState = new Dictionary<int,int>();
    int result = 0;

    for (int right = 0; right < nums.Length; right++) 
    {
        // Use nums[right]
        
        // Shrink window from left if condition violated
        while (window invalid) {
            // Update windowState by removing nums[left]
            left++;
        }

        // Update result
    }

    return result;
}
```

### **Optimized Solution:**
```csharp
int LengthOfLongestSubstring(string s) 
{
    int left = 0, maxLen = 0;
    var seen = new HashSet<char>(); // Track characters in current window
    
    for (int right = 0; right < s.Length; right++) 
    {
        char c = s[right];
        
        // Shrink window until c is unique
        while (seen.Contains(c))
        {
            seen.Remove(s[left]);
            left++;
        }
        
        seen.Add(c);
        maxLen = Math.Max(maxLen, right - left + 1);
    }
    
    return maxLen;
}
```

### **Changes from Skeleton:**
1. **windowState** → `HashSet<char>` (only need to track existence)
2. **ConditionViolated** → `seen.Contains(c)` (duplicate found)
3. **UpdateResult** → `Math.Max(maxLen, right - left + 1)` (track max window size)
4. Window management: Add `c` after removing duplicates

### **Optimized Value Trace (s = "abcabcbb"):**

| Step | right | c   | left | Action | Window | seen        | maxLen |
|------|-------|-----|------|--------|--------|-------------|--------|
| 1    | 0     | 'a' | 0    | Add a  | "a"    | {a}         | 1      |
| 2    | 1     | 'b' | 0    | Add b  | "ab"   | {a,b}       | 2      |
| 3    | 2     | 'c' | 0    | Add c  | "abc"  | {a,b,c}     | **3**  |
| 4    | 3     | 'a' | 0    | Dup!   |        |             |        |
|      |       |     | 1    | Rm 'a' | "bc"   | {b,c}       | 3      |
|      |       |     | 1    | Add a  | "bca"  | {b,c,a}     | 3      |
| 5    | 4     | 'b' | 1    | Dup!   |        |             |        |
|      |       |     | 2    | Rm 'b' | "ca"   | {c,a}       | 3      |
|      |       |     | 2    | Add b  | "cab"  | {c,a,b}     | 3      |
| 6    | 5     | 'c' | 2    | Dup!   |        |             |        |
|      |       |     | 3    | Rm 'c' | "ab"   | {a,b}       | 3      |
|      |       |     | 3    | Add c  | "abc"  | {a,b,c}     | 3      |
| 7    | 6     | 'b' | 3    | Dup!   |        |             |        |
|      |       |     | 4    | Rm 'a' | "bc"   | {b,c}       | 3      |
|      |       |     | 5    | Rm 'b' | "c"    | {c}         | 3      |
|      |       |     | 5    | Add b  | "cb"   | {c,b}       | 3      |
| 8    | 7     | 'b' | 5    | Dup!   |        |             |        |
|      |       |     | 6    | Rm 'c' | "b"    | {b}         | 3      |
|      |       |     | 7    | Rm 'b' | ""     | {}          | 3      |
|      |       |     | 7    | Add b  | "b"    | {b}         | 3      |

**Result: 3**

### **State Space Comparison:**

| Approach | States Explored | Operations |
|----------|----------------|------------|
| Brute Force | 36 substrings | O(n³) = 512 ops |
| Pruned | ~20 substrings | O(n²) = 64 ops |
| Sliding Window | 8 windows (one per char) | O(n) = 16 ops |

### **Big-O Analysis:**
- **Time**: O(n) - each character enters/exits window once
- **Space**: O(min(n, charset)) for hashset
- **Optimization**: O(n³) → O(n²) → **O(n)** ✅

### **Key Takeaway:**
**Sliding Window optimizes STATE GENERATION**: Instead of enumerating all O(n²) substrings, we maintain a single dynamic window that adapts, visiting each position once!

---

## **Related Problems:**
- [Maximum Sum Subarray of Size K](variant_2_max_sum_size_k.md) - Fixed window size
- [Minimum Window Substring](variant_3_min_window_substring.md) - Variable window with character requirements
- [Longest Substring with K Distinct Characters](variant_7_k_distinct_chars.md) - Constraint on distinct count

## **Tags:**
`sliding-window` `hash-map` `string` `O(n²)-state-space` `variable-window`
