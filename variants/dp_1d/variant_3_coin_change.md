# Coin Change

**Difficulty:** Medium  
**LeetCode Problem:** #322  
**Tags:** `Dynamic Programming`, `Unbounded Knapsack`, `Optimization`, `Minimization`

---

## Problem Statement

You are given an integer array `coins` representing coins of different denominations and an integer `amount` representing a total amount of money.

Return the **fewest number of coins** that you need to make up that amount. If that amount of money cannot be made up by any combination of the coins, return `-1`.

You may assume that you have an **infinite number of each kind of coin**.

**Constraints:**
- `1 <= coins.length <= 12`
- `1 <= coins[i] <= 2^31 - 1`
- `0 <= amount <= 10^4`

**Example 1:**
```
Input: coins = [1,2,5], amount = 11
Output: 3
Explanation: 11 = 5 + 5 + 1
```

**Example 2:**
```
Input: coins = [2], amount = 3
Output: -1
```

**Example 3:**
```
Input: coins = [1], amount = 0
Output: 0
```

---

## Step 1: State Space Derivation

### Problem Visualization

**Example:** `coins = [1, 2, 5]`, `amount = 11`

```
Question: What's the MINIMUM number of coins to make 11?

Some ways to make 11:
  • 11 pennies (1+1+1+1+1+1+1+1+1+1+1) = 11 coins ❌ too many
  • 5 nickels + 3 pennies (2+2+2+2+2+1) = 6 coins ❌ still bad
  • 2 fives + 1 penny (5+5+1) = 3 coins ✅ BEST!
  • 1 five + 3 twos (5+2+2+2) = 4 coins ❌ worse

Optimal answer: 3 coins (5 + 5 + 1)
```

**Why is this hard?** We have infinite coins of each type, so we could use:
- Any number of 1s
- Any number of 2s  
- Any number of 5s

That's a HUGE search space!

### Core Question

**How do we systematically find the minimum without checking every single combination?**

### State Space Structure

**What are we exploring?**

For `amount = 11`, we need to consider ALL possible ways to use coins [1, 2, 5]:

```
Tree of choices (each level = decision to use a coin):

                     11 (need to make 11)
                   /   |   \
           use 1 /  use 2|  use 5\
                /       |        \
              10        9         6
            / | \     / | \     / | \
          9  8  5   8  7  4   5  4  1
         ... ... ... ... ... ... ... ...

This tree is MASSIVE! Each node branches into multiple choices.
```

**State Space Cardinality:**

- **Number of amounts to solve:** 0 to 11 → **12 states** total
- **But with recursion:** Each amount can be reached multiple ways → exponential paths!
- **Brute force explores:** All possible coin combinations → **O(2^amount)** in worst case

**Key Insight:** Many paths lead to the same subproblem!
```
To make 11:
  - Use coin 5 → need to make 6
  - Use coin 1 six times, then coin 5 → also need to make 6

We're solving "make 6" multiple times! This is where DP helps.
```

---

## Step 2: Brute Force with Value Tracing

### Recursive Exploration

**Idea:** Try every possible coin and recursively solve for the remaining amount.

```csharp
public int CoinChange_BruteForce(int[] coins, int amount)
{
    // Base cases
    if (amount == 0) return 0;  // No coins needed for 0
    if (amount < 0) return -1;  // Invalid
    
    int minCoins = int.MaxValue;
    
    // Try each coin
    foreach (int coin in coins)
    {
        int subResult = CoinChange_BruteForce(coins, amount - coin);
        
        // If subproblem is solvable
        if (subResult != -1)
        {
            minCoins = Math.Min(minCoins, 1 + subResult);
        }
    }
    
    return minCoins == int.MaxValue ? -1 : minCoins;
}
```

### Value Trace for `coins = [1, 2, 5], amount = 6`

```
CoinChange(6)
├─ Try coin 1: 1 + CoinChange(5)
│  └─ CoinChange(5)
│     ├─ Try coin 1: 1 + CoinChange(4)
│     │  └─ CoinChange(4) → ... → eventually 4
│     ├─ Try coin 2: 1 + CoinChange(3)
│     │  └─ CoinChange(3) → ... → eventually 3
│     └─ Try coin 5: 1 + CoinChange(0) = 1 + 0 = 1 ✓
│     Result: min(4, 3, 1) = 1
│  Result: 1 + 1 = 2
│
├─ Try coin 2: 1 + CoinChange(4)
│  └─ CoinChange(4)
│     ├─ Try coin 1: 1 + CoinChange(3) → 3
│     ├─ Try coin 2: 1 + CoinChange(2) → 2
│     └─ coin 5 > 4, skip
│     Result: min(3, 2) = 2
│  Result: 1 + 2 = 3
│
└─ Try coin 5: 1 + CoinChange(1)
   └─ CoinChange(1)
      └─ Try coin 1: 1 + CoinChange(0) = 1 + 0 = 1
      Result: 1
   Result: 1 + 1 = 2

Final: min(2, 3, 2) = 2 coins (5 + 1 or 2 + 2 + 2)
```

**Why is this slow?**

Notice we compute `CoinChange(4)` multiple times:
- Once when trying coin 1 from amount 5
- Once when trying coin 2 from amount 6
- Many more times in deeper recursion

**Complexity:**
- **Time:** O(amount^coins) - exponential! For amount=11 with 3 coins → thousands of calls
- **Space:** O(amount) - recursion depth

---

## Step 3: Pruning Analysis - Can We Do Better?

### Key Observations

**Observation 1: Overlapping Subproblems**
```
To make 11 with coin 1: need to make 10
To make 11 with coin 2: need to make 9
To make 9 with coin 1: need to make 8
To make 10 with coin 2: need to make 8 ← DUPLICATE!

We solve "make 8" multiple times!
```

**Observation 2: Optimal Substructure**
```
If the best way to make 11 uses a coin 5:
  11 = 5 + best_way_to_make(6)

The solution for 6 must ALSO be optimal.
Why? If there was a better way to make 6, we'd use it!
```

**Observation 3: Bottom-Up Construction**
```
Instead of recursing from 11 down to 0:
  Build solutions from 0 up to 11!

Start with easy problems:
  amount 0: 0 coins
  amount 1: 1 coin (use coin 1)
  amount 2: 1 coin (use coin 2)
  ...
  
Use small solutions to build bigger ones.
```

**Observation 4: State Definition**
```
Let dp[i] = minimum coins to make amount i

Base case:
  dp[0] = 0 (no coins needed for 0)

Impossible case:
  dp[i] = infinity if amount i cannot be made

Recurrence:
  For each amount i, try each coin c:
    if c <= i:
      dp[i] = min(dp[i], 1 + dp[i - c])
      
  Translation: "Use coin c, then optimally solve for remainder"
```

**Example Build Process:**

```
coins = [1, 2, 5], amount = 11

Initialize: dp[0] = 0, all others = ∞

dp[1]: Try coins → use 1 → dp[1] = 1 + dp[0] = 1
dp[2]: Try coins → use 2 → dp[2] = 1 + dp[0] = 1 (better than using two 1s)
dp[3]: Try coins → use 2 → dp[3] = 1 + dp[1] = 2 (2+1)
dp[4]: Try coins → use 2 → dp[4] = 1 + dp[2] = 2 (2+2)
dp[5]: Try coins → use 5 → dp[5] = 1 + dp[0] = 1 (just one 5!)
dp[6]: Try coins → use 5 → dp[6] = 1 + dp[1] = 2 (5+1)
...
dp[11]: Try coins:
  - use 1: 1 + dp[10] = 1 + 2 = 3
  - use 2: 1 + dp[9] = 1 + 3 = 4  
  - use 5: 1 + dp[6] = 1 + 2 = 3
  dp[11] = min(3, 4, 3) = 3
```

**Improvement:** O(amount^coins) → O(amount × coins) - from exponential to polynomial!

---

## Step 4: Optimal Solution

### Bottom-Up Dynamic Programming

**Algorithm: Build solutions from amount 0 to target amount**

```csharp
public int CoinChange(int[] coins, int amount)
{
    // dp[i] = minimum coins needed to make amount i
    int[] dp = new int[amount + 1];
    
    // Initialize all amounts as impossible (use amount+1 as "infinity")
    Array.Fill(dp, amount + 1);
    
    // Base case: 0 coins needed to make amount 0
    dp[0] = 0;
    
    // Build table for each amount from 1 to target
    for (int i = 1; i <= amount; i++)
    {
        // Try each coin
        foreach (int coin in coins)
        {
            // Can we use this coin?
            if (coin <= i)
            {
                // Option: use this coin + optimal solution for remainder
                dp[i] = Math.Min(dp[i], 1 + dp[i - coin]);
            }
        }
    }
    
    // If still "impossible", return -1
    return dp[amount] > amount ? -1 : dp[amount];
}
```

### Complete Value Trace

**Input:** `coins = [1, 2, 5]`, `amount = 11`

**Step-by-step table construction:**

| i | Try coin 1 | Try coin 2 | Try coin 5 | dp[i] | How to make |
|---|------------|------------|------------|-------|-------------|
| 0 | - | - | - | **0** | (no coins) |
| 1 | 1+dp[0]=1 | skip (2>1) | skip (5>1) | **1** | 1 |
| 2 | 1+dp[1]=2 | 1+dp[0]=1 | skip (5>2) | **1** | 2 |
| 3 | 1+dp[2]=2 | 1+dp[1]=2 | skip (5>3) | **2** | 2+1 |
| 4 | 1+dp[3]=3 | 1+dp[2]=2 | skip (5>4) | **2** | 2+2 |
| 5 | 1+dp[4]=3 | 1+dp[3]=3 | 1+dp[0]=1 | **1** | 5 |
| 6 | 1+dp[5]=2 | 1+dp[4]=3 | 1+dp[1]=2 | **2** | 5+1 |
| 7 | 1+dp[6]=3 | 1+dp[5]=2 | 1+dp[2]=2 | **2** | 5+2 |
| 8 | 1+dp[7]=3 | 1+dp[6]=3 | 1+dp[3]=3 | **3** | 5+2+1 |
| 9 | 1+dp[8]=4 | 1+dp[7]=3 | 1+dp[4]=3 | **3** | 5+2+2 |
| 10 | 1+dp[9]=4 | 1+dp[8]=4 | 1+dp[5]=2 | **2** | 5+5 |
| 11 | 1+dp[10]=3 | 1+dp[9]=4 | 1+dp[6]=3 | **3** | 5+5+1 |

**Final answer:** `dp[11] = 3` → Use coins: 5 + 5 + 1

### Why This Works

**Key Insight:** At each amount `i`, we've already solved all smaller amounts.

```
To make amount 11:
  - Option 1: Use coin 1, then optimally make 10 (we know dp[10]=2)
              Total: 1 + 2 = 3 coins
  
  - Option 2: Use coin 2, then optimally make 9 (we know dp[9]=3)
              Total: 1 + 3 = 4 coins
  
  - Option 3: Use coin 5, then optimally make 6 (we know dp[6]=2)
              Total: 1 + 2 = 3 coins

  Best: min(3, 4, 3) = 3
```

Each subproblem is solved once and reused → massive time savings!

**Complexity:**
- **Time:** O(amount × n) where n = coins.length  
  - Outer loop: amount iterations
  - Inner loop: n coins per iteration
- **Space:** O(amount) for the dp array

**Space Optimization:** Already optimal! We only store one value per amount.

---

## Related Problems

1. **Coin Change II (LeetCode #518)** - Count number of ways
2. **Perfect Squares (LeetCode #279)** - Minimum squares summing to n
3. **Minimum Cost For Tickets (LeetCode #983)** - Similar DP
4. **Number of Dice Rolls (LeetCode #1155)** - Count combinations

---

## Tags

`#dynamic-programming` `#unbounded-knapsack` `#optimization` `#minimization` `#medium`
