# Palindrome Partitioning

**Difficulty:** Medium  
**LeetCode Problem:** #131  
**Tags:** `Backtracking`, `DFS`, `String`, `Palindrome`, `Decision Tree`

---

## Problem Statement

Given a string `s`, partition `s` such that every substring of the partition is a **palindrome**. Return all possible palindrome partitioning of `s`.

**Constraints:**
- `1 <= s.length <= 16`
- `s` contains only lowercase English letters.

**Example 1:**
```
Input: s = "aab"
Output: [["a","a","b"],["aa","b"]]
```

**Example 2:**
```
Input: s = "a"
Output: [["a"]]
```

---

## Step 1: State Space

### Problem Visualization

Input: `s = "aab"`

```
Valid partitions (all substrings must be palindromes):
  ["a", "a", "b"]      → "a" ✓, "a" ✓, "b" ✓
  ["aa", "b"]          → "aa" ✓, "b" ✓

Invalid examples:
  ["aab"]              → "aab" is not a palindrome ✗
  ["a", "ab"]          → "ab" is not a palindrome ✗
```

**Decision tree (backtracking):**

```
s = "aab"
     ""
     |
    [0]
   /   \
  |     |
"a"[1] "aa"[2]  "aab"[3]
  |      |        ✗(not palindrome)
 [1]    [2]
 / \     |
|   |    |
"a" "ab" "b"
[2] ✗    [3]
 |       ✓
"b"
[3]
 ✓

Legend:
  [index] = current position in string
  "x" = substring being considered
  ✓ = valid palindrome partition found
  ✗ = not a palindrome, prune

Paths to valid solutions:
  1. "" → "a" → "a" → "b" → ["a","a","b"]
  2. "" → "aa" → "b" → ["aa","b"]
```

**Partitioning explained:**
```
s = "aab", length = 3

At position 0:
  Try partition of length 1: "a" [0:1]
    Is "a" palindrome? Yes
    Recurse from position 1
  
  Try partition of length 2: "aa" [0:2]
    Is "aa" palindrome? Yes
    Recurse from position 2
  
  Try partition of length 3: "aab" [0:3]
    Is "aab" palindrome? No
    Skip

At position 1 (from "a" path):
  Try "a" [1:2]: palindrome → recurse from 2
  Try "ab" [1:3]: not palindrome → skip

At position 2:
  Try "b" [2:3]: palindrome → reached end → valid solution!
```

### Core Question

**How do we partition a string into all-palindrome substrings?**

Backtracking with:
1. **Position tracking** (start index)
2. **Palindrome checking** (validate each partition)
3. **Build partitions incrementally**

### Deriving from First Principles

**Observation 1: Partition decision at each position**
```
At each position i in string:
  Try all possible substring lengths: 1, 2, ..., (n - i)
  For each substring s[i:j]:
    If palindrome: add to current partition, recurse from j
    If not: skip

This explores all possible ways to partition.
```

**Observation 2: Palindrome check**
```
Function IsPalindrome(s, start, end):
  while start < end:
    if s[start] != s[end]:
      return false
    start++, end--
  return true

Time: O(length) for each check

Alternative: Pre-compute with DP
  dp[i][j] = true if s[i:j+1] is palindrome
  Time: O(n²) preprocessing, O(1) lookup
```

**Observation 3: When to record**
```
Record partition when start == n
This means we've successfully partitioned the entire string

Unlike N-Queens, we don't record intermediate states
Only complete partitions are valid.
```

**Observation 4: Complexity**
```
Worst case: Every substring is palindrome (e.g., "aaaa")
  Number of partitions: O(2^n)
  Why? Each position is a decision point: cut or don't cut
  Example: "aaa" → ["a","a","a"], ["a","aa"], ["aa","a"], ["aaa"]
           3 cut points → 2³ = 8... actually fewer due to structure

More precisely:
  Time: O(n × 2^n)
    2^n partitions in worst case
    O(n) to copy each partition
  
  Space: O(n) - recursion depth
```

**Observation 5: Optimization - DP preprocessing**
```
If n is large or many queries:
  Precompute palindrome DP table
  dp[i][j] = true if s[i:j+1] is palindrome
  
  dp[i][j] = (s[i] == s[j]) && (j - i <= 2 || dp[i+1][j-1])
  
  Base cases:
    Single char: dp[i][i] = true
    Two chars: dp[i][i+1] = (s[i] == s[i+1])
```

**Formula (Backtracking with direct palindrome check):**
```
result = []

Backtrack(start, current):
  if start == n:
    result.add(copy of current)
    return
  
  for end in start+1 to n:
    substring = s[start:end]
    if IsPalindrome(substring):
      current.add(substring)
      Backtrack(end, current)
      current.removeLast()

Backtrack(0, [])
return result
```

**Formula (Backtracking with DP table):**
```
dp = Precompute palindrome table

Backtrack(start, current):
  if start == n:
    result.add(copy of current)
    return
  
  for end in start to n-1:
    if dp[start][end]:
      current.add(s[start:end+1])
      Backtrack(end + 1, current)
      current.removeLast()
```

### State Space Structure

**Type:** Backtracking decision tree with palindrome pruning.

**Structure:**
- Tree depth: varies (number of partitions)
- Branching factor: decreases as we progress (fewer chars left)
- Pruned by palindrome check

**Cardinality:**
- Worst case: O(2^n) partitions (all substrings are palindromes)
- Time: O(n × 2^n)
- Space: O(n) recursion depth

**Key Property:** Only branch on valid palindrome substrings.

### Example Computation

Input: `s = "aab"`

```
Backtrack(start=0, current=[]):
  
  end=1 (substring "a"):
    IsPalindrome("a")? Yes
    current = ["a"]
    
    Backtrack(start=1, current=["a"]):
      
      end=2 (substring "a"):
        IsPalindrome("a")? Yes
        current = ["a", "a"]
        
        Backtrack(start=2, current=["a","a"]):
          
          end=3 (substring "b"):
            IsPalindrome("b")? Yes
            current = ["a", "a", "b"]
            
            Backtrack(start=3, current=["a","a","b"]):
              start == n, add ["a","a","b"] to result ✓
            
            current = ["a", "a"]
        
        current = ["a"]
      
      end=3 (substring "ab"):
        IsPalindrome("ab")? No
        Skip
    
    current = []
  
  end=2 (substring "aa"):
    IsPalindrome("aa")? Yes
    current = ["aa"]
    
    Backtrack(start=2, current=["aa"]):
      
      end=3 (substring "b"):
        IsPalindrome("b")? Yes
        current = ["aa", "b"]
        
        Backtrack(start=3, current=["aa","b"]):
          start == n, add ["aa","b"] to result ✓
        
        current = ["aa"]
    
    current = []
  
  end=3 (substring "aab"):
    IsPalindrome("aab")? No
    Skip

Result: [["a","a","b"], ["aa","b"]]
```

---

## Step 2: Approaches

### Approach 1: Backtracking with Direct Check
Check palindrome during backtracking

### Approach 2: Backtracking with DP Preprocessing
Precompute all palindrome substrings

---

## Step 3: Optimization Ideas

### Key Insights
1. **Try all cut positions:** Backtracking naturally explores this
2. **Prune non-palindromes:** Skip invalid partitions immediately
3. **DP optimization:** O(n²) preprocessing for O(1) palindrome checks
4. **Early termination:** If no palindrome from current position, prune

---

## Step 4: Optimal Solution (Backtracking with Direct Check)

```csharp
public class Solution {
    public IList<IList<string>> Partition(string s) {
        var result = new List<IList<string>>();
        Backtrack(s, 0, new List<string>(), result);
        return result;
    }
    
    private void Backtrack(string s, int start, List<string> current, IList<IList<string>> result) {
        // Base case: reached end of string
        if (start == s.Length) {
            result.Add(new List<string>(current));
            return;
        }
        
        // Try all possible end positions
        for (int end = start + 1; end <= s.Length; end++) {
            string substring = s.Substring(start, end - start);
            
            // Only recurse if current substring is palindrome
            if (IsPalindrome(substring)) {
                // Choose: add palindrome substring
                current.Add(substring);
                
                // Explore: partition rest of string
                Backtrack(s, end, current, result);
                
                // Unchoose: backtrack
                current.RemoveAt(current.Count - 1);
            }
        }
    }
    
    private bool IsPalindrome(string s) {
        int left = 0, right = s.Length - 1;
        
        while (left < right) {
            if (s[left] != s[right]) {
                return false;
            }
            left++;
            right--;
        }
        
        return true;
    }
}
```

**Complexity:**
- Time: O(n × 2^n)
  - O(2^n) possible partitions in worst case
  - O(n) to check each palindrome and copy result
- Space: O(n) - recursion depth and current partition

---

## Alternative: With DP Preprocessing

```csharp
public class Solution {
    public IList<IList<string>> Partition(string s) {
        int n = s.Length;
        
        // Precompute palindrome DP table
        bool[,] dp = new bool[n, n];
        
        for (int i = 0; i < n; i++) {
            dp[i, i] = true;  // Single character
        }
        
        for (int len = 2; len <= n; len++) {
            for (int i = 0; i <= n - len; i++) {
                int j = i + len - 1;
                
                if (s[i] == s[j]) {
                    if (len == 2) {
                        dp[i, j] = true;
                    } else {
                        dp[i, j] = dp[i + 1, j - 1];
                    }
                }
            }
        }
        
        var result = new List<IList<string>>();
        Backtrack(s, 0, new List<string>(), dp, result);
        return result;
    }
    
    private void Backtrack(string s, int start, List<string> current, bool[,] dp, IList<IList<string>> result) {
        if (start == s.Length) {
            result.Add(new List<string>(current));
            return;
        }
        
        for (int end = start; end < s.Length; end++) {
            if (dp[start, end]) {
                current.Add(s.Substring(start, end - start + 1));
                Backtrack(s, end + 1, current, dp, result);
                current.RemoveAt(current.Count - 1);
            }
        }
    }
}
```

**Complexity:**
- Time: O(n² + n × 2^n)
  - O(n²) DP preprocessing
  - O(n × 2^n) backtracking
- Space: O(n²) - DP table + O(n) recursion

**Trade-off:** Better for multiple queries or when n is small (dp overhead not worth it for single query).

---

## Edge Cases

1. **Single character:** `"a"` → `[["a"]]`
2. **All same:** `"aaa"` → `[["a","a","a"], ["a","aa"], ["aa","a"], ["aaa"]]`
3. **No palindromes > 1:** `"abc"` → `[["a","b","c"]]`
4. **Full string palindrome:** `"aba"` → `[["a","b","a"], ["aba"]]`

---

## Visualization

```
s = "aab"

Decision tree:

                  "" (start=0)
         /            |            \
       "a"           "aa"         "aab"
      (start=1)     (start=2)       ✗
       / \            |
     "a" "ab"        "b"
    (2)   ✗        (start=3)
     |               ✓
    "b"           ["aa","b"]
   (3)
    ✓
  ["a","a","b"]

✓ = Complete partition (start == n)
✗ = Not palindrome, pruned
```

---

## Related Problems

1. **Palindrome Partitioning II (LeetCode #132)** - Minimum cuts (DP problem)
2. **Palindrome Pairs (LeetCode #336)** - Concatenation palindrome
3. **Longest Palindromic Substring (LeetCode #5)** - Single longest
4. **Valid Palindrome (LeetCode #125)** - Simple validation
5. **Restore IP Addresses (LeetCode #93)** - Similar partitioning structure

---

## Pattern Recognition

**Problem Asks For:**
- Partition string into valid pieces
- All partitions satisfying a constraint
- Each piece must be palindrome

**This Suggests:**
- Backtracking with substring enumeration
- Validate each partition
- Record complete partitions only
- → **String Partitioning with Constraint Pattern**

**Key Indicators:**
- "Partition into..." or "Split into..."
- Each piece must satisfy condition
- Return all valid partitions

---

## Tags

`#backtracking` `#string` `#palindrome` `#partitioning` `#decision-tree` `#dp-optimization`
