# Climbing Stairs

**Difficulty:** Easy  
**LeetCode Problem:** #70  
**Tags:** `Dynamic Programming`, `Fibonacci`, `State Transition`, `Optimization`

---

## Problem Statement

You are climbing a staircase. It takes `n` steps to reach the top.

Each time you can either climb **1 or 2 steps**. In how many distinct ways can you climb to the top?

**Constraints:**
- `1 <= n <= 45`

**Example 1:**
```
Input: n = 2
Output: 2
Explanation: There are two ways to climb to the top.
1. 1 step + 1 step
2. 2 steps
```

**Example 2:**
```
Input: n = 3
Output: 3
Explanation: There are three ways to climb to the top.
1. 1 step + 1 step + 1 step
2. 1 step + 2 steps
3. 2 steps + 1 step
```

---

## Step 1: State Space

### Problem Visualization

Input: `n = 5`

```
Staircase with 5 steps:

Ground → Step 1 → Step 2 → Step 3 → Step 4 → Step 5 (Top)

From each step, can move:
  +1 step (small jump)
  +2 steps (big jump)

Ways to reach each step:
  Step 0 (ground): 1 way (starting position)
  Step 1: 1 way (from 0: +1)
  Step 2: 2 ways (from 0: +2, from 1: +1)
  Step 3: 3 ways (from 1: +2, from 2: +1)
  Step 4: 5 ways (from 2: +2, from 3: +1)
  Step 5: 8 ways (from 3: +2, from 4: +1)

Pattern: Fibonacci sequence!
  ways[i] = ways[i-1] + ways[i-2]
```

**Visual tree of paths for n=3:**

```
                    Ground (0)
                   /          \
                (0→1)        (0→2)
                 |           /    \
              (1→2)      (2→3)  (already at 3, done)
               |            ✓
            (2→3)
               ✓

Paths:
1. 0 → 1 → 2 → 3  (1+1+1)
2. 0 → 1 → 3      (1+2)
3. 0 → 2 → 3      (2+1)

Total: 3 ways
```

**Why Fibonacci?**

```
To reach step i, must come from:
  - Step i-1 (take 1 step)
  - Step i-2 (take 2 steps)

If we know:
  - ways(i-1) = number of ways to reach step i-1
  - ways(i-2) = number of ways to reach step i-2

Then:
  ways(i) = ways(i-1) + ways(i-2)

This is exactly the Fibonacci recurrence!

Base cases:
  ways(0) = 1 (one way to stay at ground)
  ways(1) = 1 (only way: take 1 step)
```

### Core Question

**How many distinct ways exist to reach step n using 1 or 2-step moves?**

The answer follows Fibonacci sequence: F(n) where F(1)=1, F(2)=2.

### Deriving from First Principles

**Observation 1: Last step analysis**
```
To reach step n, the last move was either:
  - 1 step from position n-1
  - 2 steps from position n-2

No other way to reach n (can't go backwards, can't jump > 2)

Total ways to reach n:
  = (ways to reach n-1) + (ways to reach n-2)
```

**Observation 2: Overlapping subproblems**
```
To compute ways(5):
  need ways(4) and ways(3)

To compute ways(4):
  need ways(3) and ways(2)

ways(3) is computed multiple times!
This is the hallmark of DP - overlapping subproblems.
```

**Observation 3: Optimal substructure**
```
The optimal solution to ways(n) is built from:
  - Optimal solution to ways(n-1)
  - Optimal solution to ways(n-2)

This property allows us to build up the solution bottom-up.
```

**Observation 4: Base cases**
```
ways(0) = 1  (convention: one way to "do nothing")
ways(1) = 1  (only one way: take 1 step)

Alternatively:
ways(1) = 1
ways(2) = 2  (either 1+1 or 2)

Then build from there.
```

**Observation 5: State transition formula**
```
dp[i] = dp[i-1] + dp[i-2]

Where:
  dp[i] = number of ways to reach step i
  i >= 2

Base:
  dp[0] = 1
  dp[1] = 1
  
Result:
  dp[n]
```

**Observation 6: Space optimization**
```
We only need last 2 values to compute current value.
Instead of array: dp[0..n]
Use two variables: prev1, prev2

Space: O(n) → O(1)
```

**Observation 7: Complexity**
```
Time: O(n)
  - Single pass through 1 to n
  - Constant work per step

Space: O(n) with array
       O(1) with variables

Naive recursion without memoization: O(2^n) - exponential!
```

**Formula (Bottom-up DP):**
```
dp[0] = 1
dp[1] = 1

for i from 2 to n:
  dp[i] = dp[i-1] + dp[i-2]

return dp[n]
```

**Formula (Space-optimized):**
```
if n == 1: return 1

prev2 = 1  // dp[0]
prev1 = 1  // dp[1]

for i from 2 to n:
  current = prev1 + prev2
  prev2 = prev1
  prev1 = current

return prev1
```

### State Space Structure

**Type:** Linear 1D DP with Fibonacci recurrence.

**Structure:**
- States: 0 to n (number of steps)
- Each state: depends on previous 2 states
- Computation: bottom-up (or top-down with memo)

**Cardinality:**
- States to compute: n+1
- Time: O(n)
- Space: O(n) or O(1)

**Key Property:** Fibonacci sequence starting F(1)=1, F(2)=2.

### Example Computation

Input: `n = 5`

```
Initialize:
  dp[0] = 1
  dp[1] = 1

Compute:

i=2:
  dp[2] = dp[1] + dp[0]
        = 1 + 1
        = 2

i=3:
  dp[3] = dp[2] + dp[1]
        = 2 + 1
        = 3

i=4:
  dp[4] = dp[3] + dp[2]
        = 3 + 2
        = 5

i=5:
  dp[5] = dp[4] + dp[3]
        = 5 + 3
        = 8

Result: dp[5] = 8

Sequence: 1, 1, 2, 3, 5, 8, ... (Fibonacci)
```

**Trace with space optimization:**

```
n = 5

Initial:
  prev2 = 1  (dp[0])
  prev1 = 1  (dp[1])

i=2:
  current = prev1 + prev2 = 1 + 1 = 2
  prev2 = prev1 = 1
  prev1 = current = 2
  State: prev2=1, prev1=2

i=3:
  current = prev1 + prev2 = 2 + 1 = 3
  prev2 = prev1 = 2
  prev1 = current = 3
  State: prev2=2, prev1=3

i=4:
  current = prev1 + prev2 = 3 + 2 = 5
  prev2 = prev1 = 3
  prev1 = current = 5
  State: prev2=3, prev1=5

i=5:
  current = prev1 + prev2 = 5 + 3 = 8
  prev2 = prev1 = 5
  prev1 = current = 8
  State: prev2=5, prev1=8

Result: prev1 = 8
```

### Generation Pattern

**Brute force (recursive):**
```csharp
int ClimbStairs(int n) {
    if (n <= 1) return 1;
    return ClimbStairs(n-1) + ClimbStairs(n-2);
}
// Time: O(2^n) - exponential due to repeated computation
```

**Optimized (DP):**
```csharp
// Time: O(n), Space: O(n) or O(1)
```

---

## Step 2: Approaches

### Approach 1: Recursive (Brute Force)
Directly model the recurrence relation

### Approach 2: Recursive with Memoization (Top-Down DP)
Cache computed results to avoid recomputation

### Approach 3: Bottom-Up DP with Array
Build solution iteratively from base cases

### Approach 4: Bottom-Up with Space Optimization
Use only two variables instead of array

### Approach 5: Matrix Exponentiation
O(log n) time using matrix power (advanced)

---

## Step 3: Optimization Ideas

### Key Insights
1. **Fibonacci pattern:** Recognize the recurrence relation
2. **Overlapping subproblems:** Memoization avoids exponential time
3. **Only need last 2 values:** Space optimization O(n) → O(1)
4. **Base cases matter:** Ensure dp[0]=1, dp[1]=1 for correct sequence

---

## Step 4: Optimal Solution (Space-Optimized DP)

```csharp
public class Solution {
    public int ClimbStairs(int n) {
        // Base cases
        if (n <= 1) return 1;
        
        // Initialize: ways to reach step 0 and step 1
        int prev2 = 1;  // dp[i-2]
        int prev1 = 1;  // dp[i-1]
        
        // Build solution bottom-up
        for (int i = 2; i <= n; i++) {
            int current = prev1 + prev2;
            
            // Slide the window
            prev2 = prev1;
            prev1 = current;
        }
        
        return prev1;
    }
}
```

**Complexity:**
- Time: O(n) - single pass through steps
- Space: O(1) - only two variables

**Why this works:**
```
At each step i, we only need:
  - Ways to reach i-1 (take 1 step)
  - Ways to reach i-2 (take 2 steps)

Don't need to remember all previous values.
Keep sliding window of size 2.
```

---

## Alternative: Bottom-Up DP with Array

```csharp
public class Solution {
    public int ClimbStairs(int n) {
        if (n <= 1) return 1;
        
        int[] dp = new int[n + 1];
        dp[0] = 1;
        dp[1] = 1;
        
        for (int i = 2; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        
        return dp[n];
    }
}
```

**Complexity:**
- Time: O(n)
- Space: O(n) - array storage

**Trade-off:** More space but clearer for beginners learning DP.

---

## Alternative: Top-Down DP (Memoization)

```csharp
public class Solution {
    private Dictionary<int, int> memo = new Dictionary<int, int>();
    
    public int ClimbStairs(int n) {
        // Base cases
        if (n <= 1) return 1;
        
        // Check memo
        if (memo.ContainsKey(n)) {
            return memo[n];
        }
        
        // Compute and store
        int result = ClimbStairs(n - 1) + ClimbStairs(n - 2);
        memo[n] = result;
        
        return result;
    }
}
```

**Complexity:**
- Time: O(n) - each state computed once
- Space: O(n) - recursion stack + memo

**Trade-off:** More intuitive (matches recursive thinking), but recursion overhead.

---

## Advanced: Matrix Exponentiation (O(log n))

```csharp
public class Solution {
    public int ClimbStairs(int n) {
        if (n <= 1) return 1;
        
        long[,] matrix = {{1, 1}, {1, 0}};
        long[,] result = MatrixPower(matrix, n);
        
        return (int)result[0, 0];
    }
    
    private long[,] MatrixPower(long[,] matrix, int n) {
        if (n == 1) return matrix;
        
        if (n % 2 == 0) {
            long[,] half = MatrixPower(matrix, n / 2);
            return MatrixMultiply(half, half);
        } else {
            return MatrixMultiply(matrix, MatrixPower(matrix, n - 1));
        }
    }
    
    private long[,] MatrixMultiply(long[,] a, long[,] b) {
        long[,] result = new long[2, 2];
        for (int i = 0; i < 2; i++) {
            for (int j = 0; j < 2; j++) {
                for (int k = 0; k < 2; k++) {
                    result[i, j] += a[i, k] * b[k, j];
                }
            }
        }
        return result;
    }
}
```

**Complexity:**
- Time: O(log n)
- Space: O(log n)

**Use case:** When n is extremely large (> 10^9).

---

## Edge Cases

1. **n=1:** Returns 1 (single step)
2. **n=2:** Returns 2 (1+1 or 2)
3. **n=45:** Maximum constraint, result fits in int
4. **Large n with long:** May overflow, use modulo if needed

---

## Visualization

```
DP Table for n=5:

Step  | 0 | 1 | 2 | 3 | 4 | 5 |
Ways  | 1 | 1 | 2 | 3 | 5 | 8 |
      ↑   ↑   ↑
     base base computed from 0+1

Fibonacci: 1, 1, 2, 3, 5, 8, 13, 21, ...

Space-optimized (sliding window):
  At i=4: prev2=2, prev1=3 → current=5
  At i=5: prev2=3, prev1=5 → current=8
```

---

## Related Problems

1. **Min Cost Climbing Stairs (LeetCode #746)** - Variant with costs
2. **Fibonacci Number (LeetCode #509)** - Same recurrence
3. **N-th Tribonacci Number (LeetCode #1137)** - 3-step variant
4. **House Robber (LeetCode #198)** - Similar DP structure
5. **Jump Game (LeetCode #55)** - Reachability variant

---

## Pattern Recognition

**Problem Asks For:**
- Count number of ways
- Choices at each step (1 or 2)
- Reach a target position

**This Suggests:**
- Dynamic Programming
- Fibonacci-like recurrence
- Bottom-up or top-down
- → **Classic 1D DP Pattern**

**Key Indicators:**
- "How many ways..."
- "Distinct ways..."
- Choices lead to recurrence relation
- Small constraints (n ≤ 45)

---

## Tags

`#dynamic-programming` `#fibonacci` `#1d-dp` `#state-transition` `#space-optimization` `#easy`
