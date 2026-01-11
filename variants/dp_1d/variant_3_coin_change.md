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

## Step 1: State Space

### Problem Visualization

Input: `coins = [1,2,5]`, `amount = 11`

```
Goal: Make 11 using fewest coins

Ways to make 11:
  11 × 1 = 11 coins
  5 + 5 + 1 = 3 coins ✓ (minimum)
  5 + 2 + 2 + 2 = 4 coins
  5 + 2 + 2 + 1 + 1 = 5 coins
  ... many more

Optimal: 3 coins (5 + 5 + 1)
```

**Building table for amounts 0 to 11:**

```
Amount  | 0  1  2  3  4  5  6  7  8  9  10  11
Coins   | 0  1  1  2  2  1  2  2  3  3   2   3
        |    ↑  ↑        ↑              ↑   ↑
        |   1×1 1×2     1×5            2×5  2×5+1

For amount=11:
  Try coin 1: 1 + dp[11-1] = 1 + dp[10] = 1 + 2 = 3
  Try coin 2: 1 + dp[11-2] = 1 + dp[9] = 1 + 3 = 4
  Try coin 5: 1 + dp[11-5] = 1 + dp[6] = 1 + 2 = 3
  
  Minimum: 3
```

### Core Question

**What's the minimum number of coins needed to make amount?**

DP state: dp[i] = minimum coins to make amount i.

### Deriving from First Principles

**Observation 1: Unbounded knapsack**
```
Each coin can be used unlimited times
For amount i, try each coin c:
  If c <= i:
    Option: use coin c, then solve for (i - c)
    Cost: 1 + dp[i - c]

Take minimum across all coins.
```

**Observation 2: State definition**
```
dp[i] = minimum coins needed to make amount i

Base case:
  dp[0] = 0 (zero coins for zero amount)

Invalid:
  dp[i] = infinity if amount i impossible
```

**Observation 3: Recurrence**
```
dp[i] = min over all coins c where c <= i {
  1 + dp[i - c]
}

If no coin works: dp[i] = infinity (impossible)
```

**Observation 4: Bottom-up construction**
```
Build dp[0], dp[1], dp[2], ..., dp[amount]
For each amount i, try all applicable coins

Time: O(amount × coins)
Space: O(amount)
```

**Formula:**
```
dp[0] = 0
dp[1..amount] = infinity

for i from 1 to amount:
  for coin in coins:
    if coin <= i:
      dp[i] = min(dp[i], 1 + dp[i - coin])

return dp[amount] if dp[amount] != infinity else -1
```

### State Space Structure

**Type:** 1D DP with unbounded knapsack.

**Cardinality:**
- States: amount + 1
- Time: O(amount × n) where n = len(coins)
- Space: O(amount)

### Example Computation

Input: `coins = [1,2,5]`, `amount = 11`

```
Initialize:
  dp[0] = 0
  dp[1..11] = ∞

Build:

i=1:
  Try coin 1: dp[1] = min(∞, 1 + dp[0]) = min(∞, 1) = 1
  Try coin 2: 2 > 1, skip
  Try coin 5: 5 > 1, skip
  dp[1] = 1

i=2:
  Try coin 1: dp[2] = min(∞, 1 + dp[1]) = min(∞, 2) = 2
  Try coin 2: dp[2] = min(2, 1 + dp[0]) = min(2, 1) = 1
  dp[2] = 1

i=5:
  Try coin 1: 1 + dp[4] = 1 + 2 = 3
  Try coin 2: 1 + dp[3] = 1 + 2 = 3
  Try coin 5: 1 + dp[0] = 1 + 0 = 1
  dp[5] = 1

...

i=11:
  Try coin 1: 1 + dp[10] = 1 + 2 = 3
  Try coin 2: 1 + dp[9] = 1 + 3 = 4
  Try coin 5: 1 + dp[6] = 1 + 2 = 3
  dp[11] = 3

Result: 3
```

---

## Step 4: Optimal Solution

```csharp
public class Solution {
    public int CoinChange(int[] coins, int amount) {
        // DP array: dp[i] = min coins for amount i
        int[] dp = new int[amount + 1];
        Array.Fill(dp, amount + 1);  // Use amount+1 as "infinity"
        dp[0] = 0;  // Base case
        
        // Build table for each amount
        for (int i = 1; i <= amount; i++) {
            // Try each coin
            foreach (int coin in coins) {
                if (coin <= i) {
                    dp[i] = Math.Min(dp[i], 1 + dp[i - coin]);
                }
            }
        }
        
        // Return result or -1 if impossible
        return dp[amount] > amount ? -1 : dp[amount];
    }
}
```

**Complexity:**
- Time: O(amount × n) where n = coins.length
- Space: O(amount)

---

## Related Problems

1. **Coin Change II (LeetCode #518)** - Count number of ways
2. **Perfect Squares (LeetCode #279)** - Minimum squares summing to n
3. **Minimum Cost For Tickets (LeetCode #983)** - Similar DP
4. **Number of Dice Rolls (LeetCode #1155)** - Count combinations

---

## Tags

`#dynamic-programming` `#unbounded-knapsack` `#optimization` `#minimization` `#medium`
