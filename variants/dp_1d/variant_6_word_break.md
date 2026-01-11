# Word Break

**Difficulty:** Medium  
**LeetCode Problem:** #139  
**Tags:** `Dynamic Programming`, `String`, `Dictionary`, `Memoization`

---

## Problem Statement

Given a string `s` and a dictionary of strings `wordDict`, return `true` if `s` can be segmented into a space-separated sequence of one or more dictionary words.

**Note:** The same word in the dictionary may be reused multiple times in the segmentation.

**Constraints:**
- `1 <= s.length <= 300`
- `1 <= wordDict.length <= 1000`
- `1 <= wordDict[i].length <= 20`
- `s` and `wordDict[i]` consist of only lowercase English letters.
- All strings of `wordDict` are **unique**.

**Example 1:**
```
Input: s = "leetcode", wordDict = ["leet","code"]
Output: true
Explanation: "leetcode" can be segmented as "leet code".
```

**Example 2:**
```
Input: s = "applepenapple", wordDict = ["apple","pen"]
Output: true
Explanation: "applepenapple" can be segmented as "apple pen apple".
```

**Example 3:**
```
Input: s = "catsandog", wordDict = ["cats","dog","sand","and","cat"]
Output: false
```

---

## Core Concepts

### State Definition
```
dp[i] = true if s[0..i-1] can be segmented into words from dictionary

dp[i] represents: "Can we form first i characters using dictionary?"
```

### Recurrence Relation
```
dp[i] = true if there exists j < i such that:
  1. dp[j] is true (first j chars can be formed)
  2. s[j..i-1] is in dictionary (remaining chars form a word)

Base case: dp[0] = true (empty string)
```

### Example Computation

Input: `s = "leetcode"`, `wordDict = ["leet", "code"]`

```
String: l e e t c o d e
Index:  0 1 2 3 4 5 6 7 8

DP Array:
dp[0] = true (empty string)

dp[4]:
  Check j=0: dp[0]=true, s[0:4]="leet" in dict? YES
  dp[4] = true

dp[8]:
  Check j=0: dp[0]=true, s[0:8]="leetcode" in dict? NO
  Check j=4: dp[4]=true, s[4:8]="code" in dict? YES
  dp[8] = true ✓

Result: true
```

---

## Step 4: Optimal Solution (Bottom-Up DP)

```csharp
public class Solution {
    public bool WordBreak(string s, IList<string> wordDict) {
        int n = s.Length;
        var wordSet = new HashSet<string>(wordDict);
        
        // dp[i] = true if s[0..i-1] can be segmented
        bool[] dp = new bool[n + 1];
        dp[0] = true;  // Empty string
        
        for (int i = 1; i <= n; i++) {
            for (int j = 0; j < i; j++) {
                // Check if s[0..j-1] can be formed AND s[j..i-1] is a word
                if (dp[j] && wordSet.Contains(s.Substring(j, i - j))) {
                    dp[i] = true;
                    break;  // Found one valid segmentation
                }
            }
        }
        
        return dp[n];
    }
}
```

**Complexity:**
- Time: O(n² × m) where n = s.length, m = average word length
  - O(n²) for nested loops
  - O(m) for substring and HashSet lookup
- Space: O(n) for DP array + O(w×m) for HashSet

---

## Alternative: Top-Down with Memoization

```csharp
public class Solution {
    private Dictionary<int, bool> memo;
    private HashSet<string> wordSet;
    private string s;
    
    public bool WordBreak(string s, IList<string> wordDict) {
        this.s = s;
        this.wordSet = new HashSet<string>(wordDict);
        this.memo = new Dictionary<int, bool>();
        
        return CanBreak(0);
    }
    
    private bool CanBreak(int start) {
        // Base case: reached end
        if (start == s.Length) return true;
        
        // Check memo
        if (memo.ContainsKey(start)) return memo[start];
        
        // Try all possible words starting at 'start'
        for (int end = start + 1; end <= s.Length; end++) {
            string word = s.Substring(start, end - start);
            
            if (wordSet.Contains(word) && CanBreak(end)) {
                memo[start] = true;
                return true;
            }
        }
        
        memo[start] = false;
        return false;
    }
}
```

**Complexity:**
- Time: O(n² × m)
- Space: O(n) for recursion + memo

---

## Optimization: Trie-Based Approach

```csharp
public class Solution {
    class TrieNode {
        public Dictionary<char, TrieNode> Children = new Dictionary<char, TrieNode>();
        public bool IsWord = false;
    }
    
    public bool WordBreak(string s, IList<string> wordDict) {
        // Build Trie
        TrieNode root = new TrieNode();
        foreach (string word in wordDict) {
            TrieNode node = root;
            foreach (char c in word) {
                if (!node.Children.ContainsKey(c)) {
                    node.Children[c] = new TrieNode();
                }
                node = node.Children[c];
            }
            node.IsWord = true;
        }
        
        // DP with Trie
        int n = s.Length;
        bool[] dp = new bool[n + 1];
        dp[0] = true;
        
        for (int i = 0; i < n; i++) {
            if (!dp[i]) continue;
            
            TrieNode node = root;
            for (int j = i; j < n; j++) {
                char c = s[j];
                if (!node.Children.ContainsKey(c)) break;
                
                node = node.Children[c];
                if (node.IsWord) {
                    dp[j + 1] = true;
                }
            }
        }
        
        return dp[n];
    }
}
```

**Benefit:** More efficient for large dictionaries.

---

## Edge Cases

1. **Empty string:** Return true (base case)
2. **Single character:** Check if in dictionary
3. **No valid segmentation:** Return false
4. **All of s is one word:** Direct dictionary lookup
5. **Repeated words:** "aaaaaaa" with dict=["a", "aa"]

---

## Related Problems

1. **Word Break II (LeetCode #140)** - Return all possible segmentations
2. **Concatenated Words (LeetCode #472)** - Words formed from other words
3. **Extra Characters in a String (LeetCode #2707)** - Minimize extra chars

---

## Tags

`#dynamic-programming` `#string` `#dictionary` `#memoization` `#trie` `#medium`
