# Decode Ways

**Difficulty:** Medium  
**LeetCode Problem:** #91  
**Tags:** `Dynamic Programming`, `String`, `Count Ways`, `Digit Decoding`

---

## Problem Statement

A message containing letters from `A-Z` can be **encoded** into numbers using the following mapping:

```
'A' -> "1"
'B' -> "2"
...
'Z' -> "26"
```

To **decode** an encoded message, all the digits must be grouped then mapped back into letters using the reverse of the mapping above (there may be multiple ways). For example, `"11106"` can be mapped into:
- `"AAJF"` with the grouping `(1 1 10 6)`
- `"KJF"` with the grouping `(11 10 6)`

Note that the grouping `(1 11 06)` is invalid because `"06"` cannot be mapped into `'F'` since `"6"` is different from `"06"`.

Given a string `s` containing only digits, return the **number** of ways to **decode** it.

**Constraints:**
- `1 <= s.length <= 100`
- `s` contains only digits and may contain leading zero(s).

**Example 1:**
```
Input: s = "12"
Output: 2
Explanation: "12" could be decoded as "AB" (1 2) or "L" (12).
```

**Example 2:**
```
Input: s = "226"
Output: 3
Explanation: "226" could be decoded as "BZ" (2 26), "VF" (22 6), or "BBF" (2 2 6).
```

**Example 3:**
```
Input: s = "06"
Output: 0
Explanation: "06" cannot be mapped to "F" because of the leading zero.
```

---

## Core Concepts

### State Definition
```
dp[i] = number of ways to decode s[0..i-1]

Represents: "How many valid decodings exist for first i characters?"
```

### Recurrence Relation
```
dp[i] depends on:

1. Single digit decode: s[i-1]
   If s[i-1] is '1'-'9' (valid single digit):
     dp[i] += dp[i-1]

2. Two digit decode: s[i-2:i]
   If s[i-2:i] is '10'-'26' (valid two digits):
     dp[i] += dp[i-2]

Base cases:
  dp[0] = 1 (empty string)
  dp[1] = 1 if s[0] != '0', else 0
```

### Example Computation

Input: `s = "226"`

```
String: 2 2 6
Index:  0 1 2

Initialize:
  dp[0] = 1 (empty)
  dp[1] = 1 (s[0]='2' is valid)

dp[2] (decode "22"):
  Single: s[1]='2' is valid → dp[2] += dp[1] = 1
  Double: s[0:2]="22" is in [10,26] → dp[2] += dp[0] = 1
  dp[2] = 2

dp[3] (decode "226"):
  Single: s[2]='6' is valid → dp[3] += dp[2] = 2
  Double: s[1:3]="26" is in [10,26] → dp[3] += dp[1] = 1
  dp[3] = 3

Result: 3 ways
  1. "2" "2" "6" (BBF)
  2. "22" "6" (VF)
  3. "2" "26" (BZ)
```

---

## Step 4: Optimal Solution (DP)

```csharp
public class Solution {
    public int NumDecodings(string s) {
        int n = s.Length;
        if (n == 0 || s[0] == '0') return 0;
        
        // dp[i] = number of ways to decode s[0..i-1]
        int[] dp = new int[n + 1];
        dp[0] = 1;  // Empty string
        dp[1] = 1;  // First character (already checked not '0')
        
        for (int i = 2; i <= n; i++) {
            // Single digit decode
            int oneDigit = s[i - 1] - '0';
            if (oneDigit >= 1 && oneDigit <= 9) {
                dp[i] += dp[i - 1];
            }
            
            // Two digit decode
            int twoDigits = (s[i - 2] - '0') * 10 + (s[i - 1] - '0');
            if (twoDigits >= 10 && twoDigits <= 26) {
                dp[i] += dp[i - 2];
            }
        }
        
        return dp[n];
    }
}
```

**Complexity:**
- Time: O(n) - single pass
- Space: O(n) for DP array

---

## Space-Optimized Solution

```csharp
public class Solution {
    public int NumDecodings(string s) {
        int n = s.Length;
        if (n == 0 || s[0] == '0') return 0;
        
        int prev2 = 1;  // dp[i-2]
        int prev1 = 1;  // dp[i-1]
        
        for (int i = 2; i <= n; i++) {
            int current = 0;
            
            // Single digit
            int oneDigit = s[i - 1] - '0';
            if (oneDigit >= 1 && oneDigit <= 9) {
                current += prev1;
            }
            
            // Two digits
            int twoDigits = (s[i - 2] - '0') * 10 + (s[i - 1] - '0');
            if (twoDigits >= 10 && twoDigits <= 26) {
                current += prev2;
            }
            
            prev2 = prev1;
            prev1 = current;
        }
        
        return prev1;
    }
}
```

**Complexity:**
- Time: O(n)
- Space: O(1)

---

## Key Edge Cases

1. **Leading zero:** `"06"` → 0 (invalid)
2. **Single zero:** `"0"` → 0
3. **Multiple zeros:** `"100"` → 0 (can't decode "00")
4. **Valid two digits:** `"10"`, `"26"` → Valid
5. **Invalid two digits:** `"27"`, `"99"` → Only single digit decode
6. **All single digits:** `"111"` → Multiple ways

### Handling Zeros
```
"0" alone: Invalid
"10" or "20": Valid two-digit code
"30", "40", ..., "90": Invalid
"01", "02", ..., "09": Invalid (leading zero)
```

---

## Alternative: Top-Down with Memoization

```csharp
public class Solution {
    private Dictionary<int, int> memo;
    private string s;
    
    public int NumDecodings(string s) {
        this.s = s;
        this.memo = new Dictionary<int, int>();
        return Decode(0);
    }
    
    private int Decode(int index) {
        // Base case
        if (index == s.Length) return 1;
        if (s[index] == '0') return 0;
        
        // Check memo
        if (memo.ContainsKey(index)) return memo[index];
        
        int ways = 0;
        
        // Single digit
        ways += Decode(index + 1);
        
        // Two digits
        if (index + 1 < s.Length) {
            int twoDigits = (s[index] - '0') * 10 + (s[index + 1] - '0');
            if (twoDigits <= 26) {
                ways += Decode(index + 2);
            }
        }
        
        memo[index] = ways;
        return ways;
    }
}
```

---

## Visualization

```
s = "226"

        ""
       / \
      2   (start)
     / \
    2   22
   /     \
  6      6
 /        \
✓         ✓
(BBF)    (VF)

Also: 2-26 (BZ)

Total: 3 ways
```

---

## Related Problems

1. **Decode Ways II (LeetCode #639)** - With wildcards '*'
2. **Unique Paths (LeetCode #62)** - Similar counting structure
3. **Climbing Stairs (LeetCode #70)** - Similar recurrence
4. **Fibonacci Number (LeetCode #509)** - Base pattern

---

## Tags

`#dynamic-programming` `#string` `#count-ways` `#digit-decoding` `#medium`
