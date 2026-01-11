# Variant: Valid Palindrome

**Pattern:** Two Pointers  
**Difficulty:** Easy  
**LeetCode:** #125

---

## Problem Statement

A phrase is a **palindrome** if, after converting all uppercase letters to lowercase and removing all non-alphanumeric characters, it reads the same forward and backward.

Given a string `s`, return `true` if it is a palindrome, or `false` otherwise.

### Input/Output:
- Input: `s = "A man, a plan, a canal: Panama"`
- Output: `true`
- Explanation: "amanaplanacanalpanama" is a palindrome

---

## **Step 1: Derive Full State Space from Problem**

### **Problem Analysis:**
- Check if string reads same forwards and backwards
- Ignore non-alphanumeric characters
- Case-insensitive comparison

### **State Space Definition:**
- **Type**: All mirrored position pairs to compare
- **Structure**: Pairs (i, n-1-i) where `0 ≤ i < n/2`
- **Cardinality**: n/2 pairs = **O(n)**
- **Generation**: Single loop from start to middle, comparing with mirror

### **Full State Space Enumeration:**
For `s = "A man, a plan, a canal: Panama"` (n=30 original), after cleaning we get:
`"amanaplanacanalpanama"` (n=21 cleaned)

```
Mirrored pairs to check (from cleaned string):
Position 0 ↔ Position 20: (a, a)
Position 1 ↔ Position 19: (m, m)
Position 2 ↔ Position 18: (a, a)
Position 3 ↔ Position 17: (n, n)
Position 4 ↔ Position 16: (a, a)
Position 5 ↔ Position 15: (p, p)
Position 6 ↔ Position 14: (l, l)
Position 7 ↔ Position 13: (a, a)
Position 8 ↔ Position 12: (n, n)
Position 9 ↔ Position 11: (a, a)
Position 10: (c) ← middle element, no pair

Total: 10 pairs to check
```

---

## **Step 2: Brute Force Solution with Value Tracing**

### **Brute Force Approach:**
1. Clean string: remove non-alphanumeric, convert to lowercase
2. Compare character at position i with character at position n-1-i
3. If any pair doesn't match, return false

### **Brute Force Code:**
```csharp
bool IsPalindromeBruteForce(string s) 
{
    // Step 1: Clean string
    var cleaned = new StringBuilder();
    foreach (char c in s)
    {
        if (char.IsLetterOrDigit(c))
            cleaned.Append(char.ToLower(c));
    }
    
    string str = cleaned.ToString();
    
    // Step 2: Check all mirrored pairs
    for (int i = 0; i < str.Length / 2; i++)
    {
        int mirrorIdx = str.Length - 1 - i;
        if (str[i] != str[mirrorIdx])
            return false;
    }
    
    return true;
}
```

### **Brute Force Value Trace (s = "A man, a plan, a canal: Panama"):**

**Phase 1: Cleaning**
```
Original: "A man, a plan, a canal: Panama"
Cleaned:  "amanaplanacanalpanama"
```

**Phase 2: Comparison**
| i | str[i] | mirrorIdx | str[mirrorIdx] | Match? |
|---|--------|-----------|----------------|--------|
| 0 | a      | 20        | a              | ✅     |
| 1 | m      | 19        | m              | ✅     |
| 2 | a      | 18        | a              | ✅     |
| 3 | n      | 17        | n              | ✅     |
| 4 | a      | 16        | a              | ✅     |
| 5 | p      | 15        | p              | ✅     |
| 6 | l      | 14        | l              | ✅     |
| 7 | a      | 13        | a              | ✅     |
| 8 | n      | 12        | n              | ✅     |
| 9 | a      | 11        | a              | ✅     |

**Result: true** (all pairs match)

### **Complexity:**
- **Time**: O(n) - one pass to clean, one pass to check
- **Space**: O(n) - cleaned string storage

---

## **Step 3: Can We Prune the Brute Force?**

### **Key Observations:**

1. **Unnecessary String Creation**: Brute force builds entire cleaned string first
   ```
   "A man" → clean → "aman" ← allocates O(n) space
   Could compare on-the-fly without storing
   ```

2. **Two-Pass Problem**: Separate clean + compare steps
   ```
   Pass 1: Clean entire string
   Pass 2: Compare all pairs
   Could do both in single pass!
   ```

3. **Symmetry Property**: Don't need middle character (odd length)
   ```
   "amanaplanacanalpanama"
            ↑
         middle 'c' has no pair, auto-valid
   ```

4. **Early Termination**: Can stop at first mismatch
   ```
   Already present, but can save even more by not building cleaned string
   ```

### **Can We Do Better?**
**YES!** Use two pointers on original string, skip invalid chars on-the-fly, compare without extra space

---

## **Step 4: Optimal Solution - Two Pointers**

### **Key Insight:**
Instead of cleaning first, use two pointers on **original string**:
- Skip non-alphanumeric characters as we encounter them
- Compare valid characters directly (case-insensitive)
- No extra space for cleaned string

### **Why This Works:**
```
"A man, a plan, a canal: Panama"
 ↑                            ↑
 left                      right

Skip non-alphanumeric:
 left skips: space, comma, space, colon
 right skips: same from right side

Compare valid:
 'A' (lowercased) ↔ 'a' ✅
 Move both pointers inward, repeat
```

### **Canonical Skeleton Applied:**
```csharp
int TwoPointersTemplate(int[] nums) {
    int left = 0, right = nums.Length - 1;
    int result = 0;
    
    while (left < right) {
        // Process current pair (nums[left], nums[right])
        
        if (ConditionMet(nums[left], nums[right])) {
            left++;
            right--;
        }
        else if (NeedSmaller()) {
            right--;
        }
        else {
            left++;
        }
    }
    
    return result;
}
```

### **Optimized Solution:**
```csharp
bool IsPalindrome(string s) 
{
    int left = 0, right = s.Length - 1;
    
    while (left < right) 
    {
        // Skip non-alphanumeric from left
        while (left < right && !char.IsLetterOrDigit(s[left]))
            left++;
        
        // Skip non-alphanumeric from right
        while (left < right && !char.IsLetterOrDigit(s[right]))
            right--;
        
        // Compare valid characters (case-insensitive)
        if (char.ToLower(s[left]) != char.ToLower(s[right]))
            return false;
        
        left++;
        right--;
    }
    
    return true;
}
```

### **Changes from Skeleton:**
1. **String instead of array**: Work with characters
2. **Skip invalid**: Nested while loops to advance past non-alphanumeric
3. **ConditionMet**: Characters match (case-insensitive)
4. **Early exit**: Return false immediately on mismatch
5. **Space optimization**: No cleaned string, O(1) space

### **Optimized Value Trace (s = "A man, a plan, a canal: Panama"):**

| Step | left | right | s[left] | s[right] | After skip left | After skip right | Compare | Match? | Action |
|------|------|-------|---------|----------|-----------------|------------------|---------|--------|--------|
| 1    | 0    | 30    | 'A'     | 'a'      | 'A' (pos 0)     | 'a' (pos 30)     | A ↔ a   | ✅     | Advance both |
| 2    | 1    | 29    | ' '     | 'm'      | 'm' (pos 2)     | 'm' (pos 29)     | m ↔ m   | ✅     | Advance both |
| 3    | 3    | 28    | 'a'     | 'a'      | 'a' (pos 3)     | 'a' (pos 28)     | a ↔ a   | ✅     | Advance both |
| 4    | 4    | 27    | 'n'     | 'n'      | 'n' (pos 4)     | 'n' (pos 27)     | n ↔ n   | ✅     | Advance both |
| 5    | 5    | 26    | ','     | 'a'      | 'a' (pos 7)     | 'a' (pos 26)     | a ↔ a   | ✅     | Advance both |
| 6    | 8    | 25    | 'p'     | ' '      | 'p' (pos 8)     | 'P' (pos 23)     | p ↔ P   | ✅     | Advance both |
| ...  | ...  | ...   | ...     | ...      | ...             | ...              | ...     | ...    | ... |

**Result: true**

### **Detailed Step 2 (Skipping):**
```
left=1, right=29
  s[1] = ' ' → not alphanumeric → left++
  s[2] = 'm' → alphanumeric → stop
  s[29] = 'm' → alphanumeric → stop
  Compare: 'm' == 'm' ✅
```

### **State Space Comparison:**

| Approach | String Operations | Space | Comparisons |
|----------|------------------|-------|-------------|
| Brute Force | Build cleaned string | O(n) | n/2 on cleaned string |
| Two Pointers | Skip on-the-fly | O(1) | n/2 on original string |

### **Big-O Analysis:**
- **Time**: O(n) - single pass through string
- **Space**: O(1) - no extra string storage
- **Optimization**: O(n) time maintained, but O(n) space → **O(1) space** ✅

### **Key Takeaway:**
**Two Pointers can work on "dirty" data**: Instead of preprocessing (cleaning string), we can handle invalid elements during traversal. This is a common pattern where **skip logic** is embedded in pointer movement, saving space while maintaining linear time!

---

## **Related Problems:**
- Palindrome Number - Convert to string or use math
- Valid Palindrome II - Allow one character deletion
- Longest Palindromic Substring - Different algorithm (expand around center or DP)

## **Tags:**
`two-pointers` `string` `palindrome` `O(n)-state-space` `easy` `in-place`
