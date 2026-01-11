# Variant: Minimum Window Substring

**Pattern:** Sliding Window + Hash Map (Hybrid)  
**Difficulty:** Hard  
**LeetCode:** #76

---

## Problem Statement

Given two strings `s` and `t`, return the minimum window substring of `s` such that every character in `t` (including duplicates) is included in the window. If there is no such substring, return the empty string `""`.

### Input/Output:
- Input: `s = "ADOBECODEBANC"`, `t = "ABC"`
- Output: `"BANC"`

---

## **Step 1: Derive Full State Space from Problem**

### **Problem Analysis:**
- Find **shortest substring** of `s` that contains **all characters** from `t` (including duplicates)
- Need to check all possible substrings to find the shortest valid one
- Characters can appear in any order within the window

### **State Space Definition:**

**Reference:** See [All Contiguous Subarrays](../../state_spaces/all_contiguous_subarrays.md) for detailed mathematical derivation

- **Type**: All contiguous substrings (includes single characters)
- **Structure**: Linear sequences `[start, end]` where `0 ≤ start ≤ end < n`
- **Cardinality**: n(n+1)/2 substrings = **O(n²)**
- **Generation**: Nested loops - outer loop for start index, inner for end index

### **Full State Space Enumeration:**
For `s = "ADOBECODEBANC"` (n=13), there are 13×14/2 = **91 substrings**:

```
Start=0: "A", "AD", "ADO", "ADOB", "ADOBE", "ADOBEC", "ADOBECO", "ADOBECOD", 
         "ADOBECODE", "ADOBECODEB", "ADOBECODEBA", "ADOBECODEBAN", "ADOBECODEBANC"
Start=1: "D", "DO", "DOB", "DOBE", "DOBEC", "DOBECO", "DOBECOD", "DOBECODE",
         "DOBECODEB", "DOBECODEBA", "DOBECODEBAN", "DOBECODABANC"
...
Start=9: "B", "BA", "BAN", "BANC"
Start=10: "A", "AN", "ANC"
Start=11: "N", "NC"
Start=12: "C"
Total: 91 substrings
```

---

## **Step 2: Brute Force Solution with Value Tracing**

### **Brute Force Approach:**
1. Generate all substrings using nested loops
2. For each substring, check if it contains all characters from `t` (with correct frequencies)
3. Track minimum length substring

### **Brute Force Code:**
```csharp
string MinWindowBruteForce(string s, string t) 
{
    // Build frequency map for target string
    var need = new Dictionary<char, int>();
    foreach (char c in t)
        need[c] = need.GetValueOrDefault(c) + 1;
    
    string minWindow = "";
    int minLen = int.MaxValue;
    
    // Generate all substrings
    for (int start = 0; start < s.Length; start++)
    {
        for (int end = start; end < s.Length; end++)
        {
            // Check if substring [start, end] contains all chars from t
            if (ContainsAllChars(s, start, end, need))
            {
                int len = end - start + 1;
                if (len < minLen)
                {
                    minLen = len;
                    minWindow = s.Substring(start, len);
                }
            }
        }
    }
    
    return minWindow;
}

bool ContainsAllChars(string s, int start, int end, Dictionary<char, int> need)
{
    var window = new Dictionary<char, int>();
    
    for (int i = start; i <= end; i++)
    {
        char c = s[i];
        if (need.ContainsKey(c))
            window[c] = window.GetValueOrDefault(c) + 1;
    }
    
    // Check if window has at least as many of each char as needed
    foreach (var kvp in need)
    {
        if (window.GetValueOrDefault(kvp.Key) < kvp.Value)
            return false;
    }
    
    return true;
}
```

### **Brute Force Value Trace (s = "ADOBECODEBANC", t = "ABC"):**

| start | end | substring       | A | B | C | valid? | len | minLen | minWindow |
|-------|-----|-----------------|---|---|---|--------|-----|--------|-----------|
| 0     | 0   | "A"             | 1 | 0 | 0 | ❌     | -   | ∞      | ""        |
| 0     | 1   | "AD"            | 1 | 0 | 0 | ❌     | -   | ∞      | ""        |
| 0     | 5   | "ADOBEC"        | 1 | 1 | 1 | ✅     | 6   | 6      | "ADOBEC"  |
| 0     | 6   | "ADOBECO"       | 1 | 1 | 1 | ✅     | 7   | 6      | "ADOBEC"  |
| ...   | ... | ...             | . | . | . | ...    | ... | ...    | ...       |
| 5     | 12  | "CODEBANC"      | 1 | 1 | 2 | ✅     | 8   | 6      | "ADOBEC"  |
| 9     | 12  | "BANC"          | 1 | 1 | 1 | ✅     | 4   | **4**  | **"BANC"**|
| 10    | 12  | "ANC"           | 1 | 0 | 1 | ❌     | -   | 4      | "BANC"    |

**Result: "BANC"**

### **Complexity:**
- **Time**: O(n²) to generate substrings × O(n+m) to check validity = **O(n²·(n+m))**
- **Space**: O(m) for frequency maps

---

## **Step 3: Can We Prune the Brute Force?**

### **Key Observations:**

1. **Early Termination**: Once we find all required characters, extending further only makes the window larger
   ```
   "ADOBEC" → valid (contains A, B, C) ✅
   "ADOBECO", "ADOBECOD", ... → all longer, not better ❌
   ```

2. **Redundant Checking**: We recount characters from scratch for overlapping windows
   ```
   "ADOBEC" → count A:1, B:1, C:1
   "DOBEC"  → recount same characters ❌
   ```

3. **Window Contraction**: When we have all required characters, we should try shrinking from left, not extending right

### **Pruned Brute Force:**
```csharp
string MinWindowPruned(string s, string t) 
{
    var need = new Dictionary<char, int>();
    foreach (char c in t)
        need[c] = need.GetValueOrDefault(c) + 1;
    
    string minWindow = "";
    int minLen = int.MaxValue;
    
    for (int start = 0; start < s.Length; start++)
    {
        var window = new Dictionary<char, int>();
        int have = 0;
        
        for (int end = start; end < s.Length; end++)
        {
            char c = s[end];
            if (need.ContainsKey(c))
            {
                window[c] = window.GetValueOrDefault(c) + 1;
                if (window[c] == need[c])
                    have++;
            }
            
            // Found valid window
            if (have == need.Count)
            {
                int len = end - start + 1;
                if (len < minLen)
                {
                    minLen = len;
                    minWindow = s.Substring(start, len);
                }
                break; // No point extending further from this start
            }
        }
    }
    
    return minWindow;
}
```

**Improvement**: O(n²·m) time, early termination when valid

### **Is This Good Enough?**
**NO!** Still O(n²) - we restart from each position and lose all progress.

---

## **Step 4: Optimal Solution - Sliding Window + Hash Map**

### **Key Insight:**
Instead of restarting from each position, maintain a **sliding window** with character frequencies:
- **Expand** right pointer to include more characters until we have all required
- **Contract** left pointer to minimize window while maintaining validity
- Track frequencies incrementally instead of recounting

### **Why This Works:**
When we find a valid window:
1. Try to shrink it from left to find minimum
2. Then expand right to find next valid window
3. Each character processed at most twice (once by right, once by left)

### **Canonical Skeleton Applied:**
```csharp
int SlidingWindowTemplate(int[] nums, int kOrCondition) {
    int left = 0;
    int result = 0;
    var windowState = new Dictionary<int,int>();

    for (int right = 0; right < nums.Length; right++) {
        // Update windowState with nums[right]
        
        // Shrink window if condition violated
        while (ConditionViolated(windowState)) {
            // Update windowState by removing nums[left]
            left++;
        }

        // Update result based on current window
        result = UpdateResult(result, windowState);
    }

    return result;
}
```

### **Optimized Solution:**
```csharp
string MinWindow(string s, string t) 
{
    // Build frequency map for target
    var need = new Dictionary<char, int>();
    foreach (char c in t)
        need[c] = need.GetValueOrDefault(c) + 1;
    
    var window = new Dictionary<char, int>();
    int have = 0; // Count of unique chars in window that meet frequency requirement
    int left = 0, minLen = int.MaxValue, startIdx = 0;
    
    for (int right = 0; right < s.Length; right++)
    {
        char c = s[right];
        
        // Expand window: add character at right
        if (need.ContainsKey(c))
        {
            window[c] = window.GetValueOrDefault(c) + 1;
            if (window[c] == need[c])
                have++;
        }
        
        // Contract window: try to minimize
        while (have == need.Count)
        {
            // Update result if this window is smaller
            int len = right - left + 1;
            if (len < minLen)
            {
                minLen = len;
                startIdx = left;
            }
            
            // Shrink from left
            char leftChar = s[left];
            if (need.ContainsKey(leftChar))
            {
                window[leftChar]--;
                if (window[leftChar] < need[leftChar])
                    have--;
            }
            left++;
        }
    }
    
    return minLen == int.MaxValue ? "" : s.Substring(startIdx, minLen);
}
```

### **Changes from Skeleton:**
1. **windowState** → `window` (Dictionary tracking char frequencies)
2. **Additional tracking**: `have` counter for valid unique chars
3. **ConditionViolated** → inverted: `while (have == need.Count)` shrinks when valid
4. **UpdateResult** → Store `startIdx` and `minLen` separately
5. **Two-phase**: Expand to find valid, then contract to minimize

### **Optimized Value Trace (s = "ADOBECODEBANC", t = "ABC"):**

| Step | right | c   | window        | have | left | Action      | minLen | startIdx |
|------|-------|-----|---------------|------|------|-------------|--------|----------|
| 1    | 0     | A   | {A:1}         | 1    | 0    | Expand      | ∞      | 0        |
| 2    | 1     | D   | {A:1}         | 1    | 0    | Expand      | ∞      | 0        |
| 3    | 2     | O   | {A:1}         | 1    | 0    | Expand      | ∞      | 0        |
| 4    | 3     | B   | {A:1,B:1}     | 2    | 0    | Expand      | ∞      | 0        |
| 5    | 4     | E   | {A:1,B:1}     | 2    | 0    | Expand      | ∞      | 0        |
| 6    | 5     | C   | {A:1,B:1,C:1} | 3    | 0    | **Valid!**  | ∞      | 0        |
|      |       |     | {A:1,B:1,C:1} | 3    | 0    | Contract    | 6      | 0        |
|      |       |     | {A:0,B:1,C:1} | 2    | 1    | Invalid     | 6      | 0        |
| 7    | 6     | O   | {B:1,C:1}     | 2    | 1    | Expand      | 6      | 0        |
| ...  | ...   | ... | ...           | ...  | ...  | ...         | ...    | ...      |
| 12   | 12    | C   | {A:1,B:1,C:1} | 3    | 9    | **Valid!**  | 6      | 0        |
|      |       |     | {A:1,B:1,C:1} | 3    | 9    | Contract    | 4      | 9        |
|      |       |     | {A:1,C:1}     | 2    | 10   | Invalid     | 4      | 9        |

**Result: "BANC" (from index 9, length 4)**

### **State Space Comparison:**

| Approach | States Explored | Operations |
|----------|----------------|------------|
| Brute Force | 91 substrings | O(n²·(n+m)) = thousands |
| Pruned | ~50 substrings | O(n²·m) = reduced |
| Sliding Window | 13 chars × 2 passes | O(n+m) = 26 ops |

### **Big-O Analysis:**
- **Time**: O(n + m) - each char in s visited twice, m for building need map
- **Space**: O(m + charset) for frequency maps
- **Optimization**: O(n²·(n+m)) → **O(n+m)** ✅

### **Key Takeaway:**
**Sliding Window + Hash Map** is a hybrid pattern - the window provides efficient state generation, while the hash map tracks complex frequency constraints. Perfect for substring problems with character requirements!

---

## **Related Problems:**
- [Longest Substring Without Repeating Characters](variant_1_longest_substring_no_repeat.md) - Simpler constraint (no duplicates)
- [Longest Substring with K Distinct Characters](variant_7_k_distinct_chars.md) - Different constraint type
- Find All Anagrams in String - Similar technique, find all instead of shortest

## **Tags:**
`sliding-window` `hash-map` `hybrid` `string` `hard` `O(n²)-state-space` `variable-window`
