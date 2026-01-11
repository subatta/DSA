# Gas Station

**Difficulty:** Medium  
**LeetCode Problem:** #134  
**Tags:** `Greedy`, `Array`, `Circular Array`

---

## Problem Statement

There are `n` gas stations along a circular route, where the amount of gas at the `i-th` station is `gas[i]`.

You have a car with an unlimited gas tank and it costs `cost[i]` of gas to travel from the `i-th` station to its next `(i + 1)-th` station. You begin the journey with an empty tank at one of the gas stations.

Given two integer arrays `gas` and `cost`, return the starting gas station's index if you can travel around the circuit once in the clockwise direction, otherwise return `-1`. If there exists a solution, it is guaranteed to be unique.

**Constraints:**
- `n == gas.length == cost.length`
- `1 <= n <= 10^5`
- `0 <= gas[i], cost[i] <= 10^4`

**Example 1:**
```
Input: gas = [1,2,3,4,5], cost = [3,4,5,1,2]
Output: 3
Explanation:
Start at station 3:
  Tank=0+4=4, move to 4 (cost 1), tank=3
  Tank=3+5=8, move to 0 (cost 2), tank=6
  Tank=6+1=7, move to 1 (cost 3), tank=4
  Tank=4+2=6, move to 2 (cost 4), tank=2
  Tank=2+3=5, move to 3 (cost 5), tank=0
Successfully completes circuit.
```

**Example 2:**
```
Input: gas = [2,3,4], cost = [3,4,3]
Output: -1
Explanation: Cannot complete circuit from any starting point.
```

---

## Step 1: State Space

### Problem Visualization

For `gas = [1,2,3,4,5]`, `cost = [3,4,5,1,2]`:

```
Station:   0  1  2  3  4
Gas:       1  2  3  4  5
Cost:      3  4  5  1  2
Net:      -2 -2 -2 +3 +3  (gas[i] - cost[i])

Cumulative net from station 3:
  Station 3: +3 → tank=3
  Station 4: +3 → tank=6
  Station 0: -2 → tank=4
  Station 1: -2 → tank=2
  Station 2: -2 → tank=0 ✓

Starting from 3 works!
```

Net gain visualization:
```
Net:  -2  -2  -2  +3  +3
Cum:  -2  -4  -6  -3   0  (total sum = 0)
           ↑ min
        
If total >= 0, solution exists.
Start after the most negative point.
```

### Core Question

**From which station should we start to complete the circular route, or is it impossible?**

Key insight: If total gas ≥ total cost, a solution exists. The starting point is after the "lowest tank level" position.

### Deriving from First Principles

**Observation 1: Net gain at each station**
At station i:
- Gain: gas[i]
- Cost: cost[i]
- Net: gas[i] - cost[i]

If we can complete the circuit, sum of all nets must be ≥ 0.

**Observation 2: Necessary condition**
```
If sum(gas) < sum(cost):
  Total net < 0 → Impossible to complete circuit
  Return -1
```

**Observation 3: Sufficient condition**
If sum(gas) >= sum(cost), a solution exists (problem guarantees uniqueness).

**Observation 4: Greedy starting point**
Start from station 0, track running tank:
```
If tank becomes negative at station i:
  Cannot start from 0,1,...,i
  Try starting from i+1
```

Why? If we can't reach station i from station 0, we also can't reach station i from any intermediate station (since we had positive tank before that station).

**Observation 5: Single pass algorithm**
```
totalTank = 0  (total net gain)
currentTank = 0  (net from current start)
start = 0

for i = 0 to n-1:
  totalTank += gas[i] - cost[i]
  currentTank += gas[i] - cost[i]
  
  if currentTank < 0:
    start = i + 1  (reset starting point)
    currentTank = 0  (reset current tank)

return totalTank >= 0 ? start : -1
```

**Formula:**
```
If sum(gas - cost) < 0:
  return -1  (impossible)

Start from index where we reset after tank went negative.
Guaranteed to work if total sum >= 0.
```

### State Space Structure

**Type:** Greedy selection of starting point with single pass.

**Structure:**
- Track total balance (sum of all nets)
- Track current balance (from current start candidate)
- Reset start when current tank goes negative

**Cardinality:**
- O(n) single pass
- O(1) space

**Key Property:** If total net ≥ 0, the last reset starting point completes the circuit.

### Example Computation

Input: `gas = [1,2,3,4,5]`, `cost = [3,4,5,1,2]`

```
totalTank = 0, currentTank = 0, start = 0

i=0: gas=1, cost=3, net=-2
  totalTank = 0 + (-2) = -2
  currentTank = 0 + (-2) = -2
  currentTank < 0:
    start = 1
    currentTank = 0

i=1: gas=2, cost=4, net=-2
  totalTank = -2 + (-2) = -4
  currentTank = 0 + (-2) = -2
  currentTank < 0:
    start = 2
    currentTank = 0

i=2: gas=3, cost=5, net=-2
  totalTank = -4 + (-2) = -6
  currentTank = 0 + (-2) = -2
  currentTank < 0:
    start = 3
    currentTank = 0

i=3: gas=4, cost=1, net=+3
  totalTank = -6 + 3 = -3
  currentTank = 0 + 3 = 3
  currentTank >= 0 (no reset)

i=4: gas=5, cost=2, net=+3
  totalTank = -3 + 3 = 0
  currentTank = 3 + 3 = 6
  currentTank >= 0 (no reset)

totalTank = 0 >= 0 → Return start = 3
```

### Generation Pattern

**Brute force:**
```csharp
public int CanCompleteCircuit(int[] gas, int[] cost) {
    int n = gas.Length;
    
    for (int start = 0; start < n; start++) {
        int tank = 0;
        bool success = true;
        
        for (int i = 0; i < n; i++) {
            int station = (start + i) % n;
            tank += gas[station] - cost[station];
            if (tank < 0) {
                success = false;
                break;
            }
        }
        
        if (success) return start;
    }
    
    return -1;
}
// Time: O(n²), Space: O(1)
```

---

## Step 2: Brute Force

Try each station as starting point, simulate circuit: O(n²).

---

## Step 3: Optimization Ideas

### Key Insights
1. **Total balance determines possibility:** If sum(gas) < sum(cost), impossible
2. **Greedy starting point:** Reset start when tank goes negative
3. **Single pass sufficient:** Don't need to try all starts
4. **Why greedy works:** If can't reach station i from 0, can't from any j in (0,i)

### Mathematical Proof
If total net ≥ 0, a solution exists.
The greedy algorithm finds it by:
- Eliminating impossible starting points (where tank goes negative)
- Last reset point is guaranteed to work (since total net ≥ 0)

---

## Step 4: Optimal Solution

```csharp
public int CanCompleteCircuit(int[] gas, int[] cost) {
    int n = gas.Length;
    int totalTank = 0;
    int currentTank = 0;
    int start = 0;
    
    for (int i = 0; i < n; i++) {
        int net = gas[i] - cost[i];
        totalTank += net;
        currentTank += net;
        
        // If current tank negative, can't start from 0..i
        if (currentTank < 0) {
            start = i + 1;  // Try starting from next station
            currentTank = 0;  // Reset current tank
        }
    }
    
    // If total net is negative, impossible
    return totalTank >= 0 ? start : -1;
}
```

**Complexity:**
- Time: O(n) - single pass
- Space: O(1)

**Why this works:**
1. **totalTank < 0:** Impossible to complete (not enough gas overall)
2. **totalTank >= 0:** Solution exists
3. **start:** Last position where we reset after going negative
   - All stations before start cannot be starting points
   - Starting from `start` is guaranteed to work (since total ≥ 0)

**Key Mechanics:**
- **totalTank:** Tracks if solution possible
- **currentTank:** Tracks feasibility from current start
- **start:** Reset when infeasible, final value is answer

---

## Edge Cases

1. **Single station:** `gas=[5], cost=[3]` → `0` (if net >= 0)
2. **All gains:** `gas=[5,5,5], cost=[1,1,1]` → `0` (any start works, return first)
3. **Impossible:** `gas=[1,2], cost=[3,4]` → `-1`
4. **Exact match:** `gas=[2,3,4], cost=[3,4,3]` → `-1` (total=0 but local negative)
5. **Start at end:** `gas=[1,1,1,10], cost=[2,2,2,1]` → `3`

---

## Why Greedy Works: Proof

**Claim:** If total gas ≥ total cost, the last reset starting point completes circuit.

**Proof:**
- Let `start` be the last position where we reset (tank went negative).
- For all positions i in [start, n-1], currentTank >= 0 (otherwise we'd have reset later).
- For positions [0, start-1], we know previous starts failed.
- Since total net >= 0, starting from `start` accumulates positive total.
- When we loop back to [0, start-1], we have positive balance from [start, n-1].
- This positive balance compensates for any negative sections in [0, start-1].

---

## Related Problems

1. **Jump Game (LeetCode #55)** - Similar greedy reachability
2. **Maximum Subarray (LeetCode #53)** - Kadane's algorithm (similar reset logic)
3. **Best Time to Buy and Sell Stock II (LeetCode #122)** - Greedy accumulation

---

## Pattern Recognition

**Problem Asks For:**
- Starting point in circular array
- Feasibility with resource constraints
- Unique solution guaranteed

**This Suggests:**
- Check total balance for possibility
- Greedy selection of starting point
- Reset strategy when infeasible
- → **Greedy pattern**

**Key Indicators:**
- Circular array/route
- Resource gain/cost at each position
- Find starting point or determine impossibility

---

## Tags

`#greedy` `#circular-array` `#gas-station` `#resource-management` `#single-pass` `#reset-strategy`
