# Generate Parentheses

**Difficulty:** Medium  
**LeetCode Problem:** #22  
**Tags:** `Backtracking`, `DFS`, `String`, `Constrained Generation`, `Balanced Structure`

---

## Problem Statement

Given `n` pairs of parentheses, write a function to generate all combinations of well-formed parentheses.

**Constraints:**
- `1 <= n <= 8`

**Example 1:**
```
Input: n = 3
Output: ["((()))","(()())","(())()","()(())","()()()"]
```

**Example 2:**
```
Input: n = 2
Output: ["(())","()()"]
```

**Example 3:**
```
Input: n = 1
Output: ["()"]
```

---

## Step 1: State Space

### Problem Visualization

Input: `n = 3` (3 pairs = 6 characters total)

```
Valid combinations:
  "((()))"    ← nested
  "(()())"    ← nested then sequential
  "(())()"    ← group then pair
  "()(())"    ← pair then nested
  "()()()"    ← all sequential

Total: Catalan number C(n) = C(3) = 5

Invalid examples:
  "((("       ← only opening
  ")))"       ← only closing
  "())(()"    ← closing before enough opening
  "(()(())"   ← unbalanced (4 open, 2 close)
```

**Well-formed rules:**
```
1. Equal number of '(' and ')'
2. At any point, # of ')' <= # of '('
   (Can't close before opening)

Valid prefix examples:
  "("        ✓ (1 open, 0 close)
  "(("       ✓ (2 open, 0 close)
  "(()"      ✓ (2 open, 1 close)
  "()("      ✓ (2 open, 1 close)

Invalid prefix examples:
  ")"        ✗ (0 open, 1 close)
  "())"      ✗ (1 open, 2 close)
```

**Decision tree (backtracking):**

```
n = 2 (generate 4 characters)

                    ""
                    |
                   "("
             /            \
          "(("            "()"
          /               / \
       "(()"          "()("  "())" ✗
        |               |
      "(())"          "()()"
        ✓               ✓

At each step, choose:
  - Add '(' if open < n
  - Add ')' if close < open

Legend:
  ✓ = Valid (open == close == n)
  ✗ = Invalid (close > open, pruned)
```

**Generation process:**

```
For n = 2:

Start: ""
  open=0, close=0
  
  Add '(':
    "(", open=1, close=0
    
    Add '(':
      "((", open=2, close=0
      
      Can't add '(' (open == n)
      Add ')':
        "(()", open=2, close=1
        
        Add ')':
          "(())", open=2, close=2
          Complete! ✓
    
    Add ')':
      "()", open=1, close=1
      
      Add '(':
        "()(", open=2, close=1
        
        Add ')':
          "()()", open=2, close=2
          Complete! ✓
      
      Can't add ')' (close == open)

Result: ["(())", "()()"]
```

### Core Question

**How do we generate all valid parentheses combinations?**

Backtracking with:
1. **Track open/close counts**
2. **Constrain by rules:** open ≤ n, close ≤ open
3. **Build string incrementally**

### Deriving from First Principles

**Observation 1: Catalan number**
```
Number of valid combinations = C(n) = (2n)! / ((n+1)! × n!)

Values:
  n=1: C(1) = 1
  n=2: C(2) = 2
  n=3: C(3) = 5
  n=4: C(4) = 14
  n=8: C(8) = 1430

This is exponential, so backtracking is appropriate.
```

**Observation 2: Constraint-based generation**
```
At each step, we can add:
  1. '(' if open < n
     (Haven't used all opening parens yet)
  
  2. ')' if close < open
     (There's an unmatched opening paren)

These constraints ensure validity!
```

**Observation 3: No need to validate after**
```
Because we enforce constraints during generation,
every complete string is automatically valid.

No need for separate validation step.
```

**Observation 4: When to record**
```
Record when open == n AND close == n
This means we've used all parentheses

Alternative check: current.length == 2 × n
```

**Observation 5: Complexity**
```
Time: O(4^n / √n)
  This is approximately the n-th Catalan number
  Bounded by total number of valid combinations × cost per combination
  More precisely: O(C(n) × n) where C(n) is Catalan number

Space: O(n) - recursion depth
  Each recursive call adds one character
  Maximum depth = 2n
```

**Formula (Backtracking):**
```
result = []

Backtrack(current, open, close):
  if open == n and close == n:
    result.add(current)
    return
  
  if open < n:
    Backtrack(current + '(', open + 1, close)
  
  if close < open:
    Backtrack(current + ')', open, close + 1)

Backtrack("", 0, 0)
return result
```

### State Space Structure

**Type:** Constrained backtracking decision tree.

**Structure:**
- Binary tree (2 choices: '(' or ')')
- Depth: 2n
- Pruned by open ≤ n and close ≤ open
- Leaf nodes: Catalan(n) valid combinations

**Cardinality:**
- Valid solutions: C(n) = (2n)! / ((n+1)! × n!)
- Time: O(4^n / √n) ≈ O(C(n) × n)
- Space: O(n) recursion depth

**Key Property:** Constraints guarantee validity without post-validation.

### Example Computation

Input: `n = 2`

```
Backtrack("", 0, 0):
  open=0, close=0
  
  open < n (0 < 2):
    Backtrack("(", 1, 0):
      open=1, close=0
      
      open < n (1 < 2):
        Backtrack("((", 2, 0):
          open=2, close=0
          
          open < n? No (2 == 2)
          
          close < open (0 < 2):
            Backtrack("(()", 2, 1):
              open=2, close=1
              
              open < n? No
              
              close < open (1 < 2):
                Backtrack("(())", 2, 2):
                  open=2, close=2
                  open == n and close == n
                  Add "(())" to result ✓
      
      close < open (0 < 1):
        Backtrack("()", 1, 1):
          open=1, close=1
          
          open < n (1 < 2):
            Backtrack("()(", 2, 1):
              open=2, close=1
              
              open < n? No
              
              close < open (1 < 2):
                Backtrack("()()", 2, 2):
                  open=2, close=2
                  Add "()()" to result ✓
          
          close < open? No (1 == 1)

Result: ["(())", "()()"]
```

---

## Step 2: Approaches

### Approach 1: Backtracking with Counts
Track open and close counts

### Approach 2: Backtracking with String Builder
Use StringBuilder for efficiency

### Approach 3: DP (Advanced)
Build from smaller solutions (less intuitive)

---

## Step 3: Optimization Ideas

### Key Insights
1. **Two constraints:** open ≤ n, close ≤ open
2. **No validation needed:** Constraints guarantee validity
3. **Catalan number:** Know expected count in advance
4. **StringBuilder:** More efficient than string concatenation

---

## Step 4: Optimal Solution (Backtracking)

```csharp
public class Solution {
    public IList<string> GenerateParenthesis(int n) {
        var result = new List<string>();
        Backtrack(new StringBuilder(), 0, 0, n, result);
        return result;
    }
    
    private void Backtrack(StringBuilder current, int open, int close, int n, IList<string> result) {
        // Base case: used all parentheses
        if (open == n && close == n) {
            result.Add(current.ToString());
            return;
        }
        
        // Add '(' if we haven't used all opening parens
        if (open < n) {
            current.Append('(');
            Backtrack(current, open + 1, close, n, result);
            current.Length--;  // Backtrack (remove last char)
        }
        
        // Add ')' if we have unmatched opening parens
        if (close < open) {
            current.Append(')');
            Backtrack(current, open, close + 1, n, result);
            current.Length--;  // Backtrack
        }
    }
}
```

**Complexity:**
- Time: O(4^n / √n) ≈ O(C(n) × n) where C(n) is Catalan number
  - Generate all valid combinations
  - Each takes O(n) to build string
- Space: O(n) - recursion depth

**Why two conditions?**
```
open < n:   Ensures we use exactly n opening parens
close < open: Ensures valid structure (close after open)

Together: Generate all and only valid combinations
```

---

## Alternative: With String Concatenation

```csharp
public class Solution {
    public IList<string> GenerateParenthesis(int n) {
        var result = new List<string>();
        Backtrack("", 0, 0, n, result);
        return result;
    }
    
    private void Backtrack(string current, int open, int close, int n, IList<string> result) {
        if (open == n && close == n) {
            result.Add(current);
            return;
        }
        
        if (open < n) {
            Backtrack(current + "(", open + 1, close, n, result);
        }
        
        if (close < open) {
            Backtrack(current + ")", open, close + 1, n, result);
        }
    }
}
```

**Trade-off:** Simpler code, but string concatenation creates new objects (less efficient).

---

## Edge Cases

1. **n=1:** `["()"]` - single pair
2. **n=0:** (Not in constraints, but would be `[""]`)
3. **n=8:** Maximum constraint, 1430 combinations

---

## Visualization

```
Decision tree for n=2:

                    ""
                   (o=0,c=0)
                     |
                    "("
                   (o=1,c=0)
             /                \
          "(("               "()"
        (o=2,c=0)          (o=1,c=1)
           |                  |
         "(()"              "()("
       (o=2,c=1)          (o=2,c=1)
           |                  |
        "(())"             "()()"
       (o=2,c=2)✓        (o=2,c=2)✓

o = open count
c = close count
✓ = Complete valid combination
```

---

## Related Problems

1. **Valid Parentheses (LeetCode #20)** - Validate given string
2. **Longest Valid Parentheses (LeetCode #32)** - Longest valid substring
3. **Remove Invalid Parentheses (LeetCode #301)** - Minimum removals
4. **Different Ways to Add Parentheses (LeetCode #241)** - Expression parsing
5. **Score of Parentheses (LeetCode #856)** - Compute score

---

## Pattern Recognition

**Problem Asks For:**
- Generate all valid combinations
- Balanced structure (matching pairs)
- Constrained generation rules

**This Suggests:**
- Backtracking with constraints
- Track state (open/close counts)
- No post-validation needed
- → **Constrained Generation Backtracking Pattern**

**Key Indicators:**
- "Generate all valid..."
- Balanced structures (parens, brackets, etc.)
- Rules that can be enforced during generation
- Exponential number of solutions (Catalan, etc.)

---

## Tags

`#backtracking` `#string` `#parentheses` `#catalan-number` `#constrained-generation` `#balanced-structure`
