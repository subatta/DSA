# Pattern: Greedy

## Pattern Description
**The Greedy pattern** is used when **local optimal choices lead to a global optimum**.

**What is it?**
- Make the best choice at each step without reconsidering past decisions
- Locally optimal → globally optimal (when greedy choice property holds)
- No backtracking needed
- Efficient when problem has optimal substructure

**What abstract problem does it solve?**
- Optimization problems (minimize/maximize) under constraints
- Selection problems (choose subset satisfying criteria)
- Scheduling and interval problems
- Resource allocation
- Pathfinding with specific properties

**Real-world / interview variants (simplest → harder):**
1. **Activity Selection / Interval Scheduling** - Classic greedy with sorting
2. **Jump Game / Minimum Jumps** - Greedy range extension
3. **Fractional Knapsack** - Ratio-based greedy selection
4. **Minimum Coins / Change Making** - Greedy works for canonical coin systems
5. **Task Scheduling with Deadlines** - Deadline-based greedy ordering
6. **Reorganize String** - Frequency-based greedy with heap
7. **Gas Station / Circuit Problems** - Cumulative sum greedy
8. **Huffman Encoding** - Tree construction with greedy merging

---

## Canonical Skeleton (Greedy)

```csharp
void GreedySolver(List<Item> items)
{
    SortItemsBySomeCriterion(items);
    foreach (var item in items)
    {
        if (CanInclude(item))
            Include(item);
    }
}
```

- **SortItemsBySomeCriterion**: order by “best first”
- **CanInclude / Include**: maintain constraints
- Works for **linear selection problems**


<details>
<summary><b>Variant #1: Activity Selection / Interval Scheduling</b></summary>

## Variant #1: Activity Selection / Interval Scheduling

### Input/Output:
- Input: `activities = [(1,3),(2,5),(4,6),(6,7)]` (start, end times)
- Output: `[(1,3),(4,6),(6,7)]` (maximum non-overlapping activities)

### Full State Space:
All possible subsets of activities.
```
Subsets of 4 activities: 2^4 = 16 combinations
For each subset, check if all activities are non-overlapping
Find subset with maximum size
```
```csharp
void GenerateAllSubsets(List<(int start, int end)> activities)
{
    for (int mask = 0; mask < (1 << activities.Count); mask++)
    {
        List<(int, int)> subset = new();
        for (int i = 0; i < activities.Count; i++)
        {
            if ((mask & (1 << i)) != 0)
                subset.Add(activities[i]);
        }
        if (IsNonOverlapping(subset))
            Console.WriteLine($"Valid subset size {subset.Count}");
    }
}
```

### Expected/Pruned State Space:
Sort by earliest finish time, greedily select non-overlapping activities.
```
Sort: [(1,3),(2,5),(4,6),(6,7)] → by end time
Greedy selection: O(n) scan after sorting
```

### State Space Leading to Output:
Sorted activities processed sequentially, maintaining last end time.

### Brute Force Canonical Skeleton:
```csharp
int ActivitySelectionBruteForce(List<(int start, int end)> activities)
{
    int maxCount = 0;
    
    void Backtrack(int idx, List<(int, int)> current)
    {
        maxCount = Math.Max(maxCount, current.Count);
        
        for (int i = idx; i < activities.Count; i++)
        {
            // Check if current activity overlaps with any selected
            bool overlap = false;
            foreach (var act in current)
            {
                if (!(activities[i].end <= act.start || activities[i].start >= act.end))
                {
                    overlap = true;
                    break;
                }
            }
            
            if (!overlap)
            {
                current.Add(activities[i]);
                Backtrack(i + 1, current);
                current.RemoveAt(current.Count - 1);
            }
        }
    }
    
    Backtrack(0, new List<(int, int)>());
    return maxCount;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `activities = [(1,3),(2,5),(4,6),(6,7)]`:

| Recursion | idx | current | overlap check | action | maxCount |
|-----------|-----|---------|---------------|--------|----------|
| 1 | 0 | [] | - | add (1,3) | 1 |
| 2 | 1 | [(1,3)] | (2,5) overlaps | skip | 1 |
| 2 | 2 | [(1,3)] | (4,6) no overlap | add | 2 |
| 3 | 3 | [(1,3),(4,6)] | (6,7) no overlap | add | 3 |
| 4 | 4 | [(1,3),(4,6),(6,7)] | done | return | 3 |
| ... | ... | ... | ... | backtrack explores more | 3 |

### Optimized Solution from Canonical Skeleton:
```csharp
int ActivitySelection(List<(int start, int end)> activities)
{
    // Sort by end time (earliest finish first)
    activities.Sort((a, b) => a.end.CompareTo(b.end));
    
    List<(int, int)> selected = new();
    int lastEnd = int.MinValue;
    
    foreach (var act in activities)
    {
        // If activity starts after last selected ends
        if (act.start >= lastEnd)
        {
            selected.Add(act);
            lastEnd = act.end;
        }
    }
    
    return selected.Count;
}
```

### Explanation of Pruning:
- **Greedy choice property:** Always pick activity that finishes earliest
- **Why it works:** Choosing earliest finish leaves maximum room for future activities
- **Proof sketch:** If optimal solution differs, we can swap to use earliest finish without reducing count
- **Sorting criterion:** End time ascending (finish earliest first)
- **Pruning:** O(n) scan instead of O(2^n) subset exploration

### Optimized Solution Code Walkthrough / Variable Trace:
For `activities = [(1,3),(2,5),(4,6),(6,7)]`:

**After sorting by end time:** `[(1,3),(2,5),(4,6),(6,7)]` (already sorted)

| activity | start | end | lastEnd | start >= lastEnd | action | selected |
|----------|-------|-----|---------|------------------|--------|----------|
| (1,3) | 1 | 3 | -∞ | yes | select | [(1,3)] |
| (2,5) | 2 | 5 | 3 | no (2<3) | skip | [(1,3)] |
| (4,6) | 4 | 6 | 3 | yes | select | [(1,3),(4,6)] |
| (6,7) | 6 | 7 | 6 | yes | select | [(1,3),(4,6),(6,7)] |

**Result:** 3 activities selected

### Big-O Analysis:
- **Brute Force:** O(2^n) → explore all subsets
- **Optimized:** O(n log n) → sorting dominates, then O(n) scan
- **Space Complexity:** O(n) → storing selected activities

</details>


<details>
<summary><b>Variant #2: Jump Game / Minimum Jumps</b></summary>

## Variant #2: Jump Game / Minimum Jumps

### Input/Output:
- Input: `nums = [2,3,1,1,4]`
- Output: `2` (minimum jumps: index 0→1→4)

### Full State Space:
All possible jump sequences from start to end.
```
From index 0 (value 2): can jump to 1 or 2
From index 1 (value 3): can jump to 2, 3, or 4
...
Total sequences: exponential (tree of all paths)
```

### Expected/Pruned State Space:
Greedy range extension: track farthest reachable, jump when range exhausted.
```
At each position, update farthest reachable
When reaching end of current jump range, increment jumps
O(n) single pass
```

### State Space Leading to Output:
Track current jump range and farthest reachable position.

### Brute Force Canonical Skeleton:
```csharp
int MinJumpsBruteForce(int[] nums)
{
    int minJumps = int.MaxValue;
    
    void Backtrack(int idx, int jumps)
    {
        if (idx >= nums.Length - 1)
        {
            minJumps = Math.Min(minJumps, jumps);
            return;
        }
        
        // Try all possible jumps from current position
        for (int i = 1; i <= nums[idx] && idx + i < nums.Length; i++)
        {
            Backtrack(idx + i, jumps + 1);
        }
    }
    
    Backtrack(0, 0);
    return minJumps;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `nums = [2,3,1,1,4]`:

| Recursion | idx | jumps | nums[idx] | next jumps | minJumps |
|-----------|-----|-------|-----------|------------|----------|
| 1 | 0 | 0 | 2 | try 1,2 | ∞ |
| 2 | 1 | 1 | 3 | try 1,2,3 | ∞ |
| 3 | 2 | 2 | 1 | try 1 | ∞ |
| 4 | 3 | 3 | 1 | try 1 | ∞ |
| 5 | 4 | 4 | (end) | - | 4 |
| ... | ... | ... | ... | backtrack | ... |
| 3 | 4 | 2 | (end) | - | 2 |

**Optimal path found:** 0→1→4 (2 jumps)

### Optimized Solution from Canonical Skeleton:
```csharp
int MinJumps(int[] nums)
{
    if (nums.Length <= 1) return 0;
    
    int jumps = 0;
    int currentEnd = 0;    // End of current jump range
    int farthest = 0;      // Farthest position reachable
    
    for (int i = 0; i < nums.Length - 1; i++)
    {
        // Update farthest reachable from current position
        farthest = Math.Max(farthest, i + nums[i]);
        
        // If reached end of current jump range
        if (i == currentEnd)
        {
            jumps++;
            currentEnd = farthest;
            
            // Early exit if we can reach the end
            if (currentEnd >= nums.Length - 1)
                break;
        }
    }
    
    return jumps;
}
```

### Explanation of Pruning:
- **Greedy insight:** At each jump, we can reach any position within the current range
- **Optimal choice:** Jump to position that maximizes next range
- **BFS-like levels:** Each jump represents a "level" of BFS
- **currentEnd:** Marks boundary of current jump range
- **farthest:** Best position reachable within current range
- **Why it works:** We always extend range as far as possible, minimizing jumps

### Optimized Solution Code Walkthrough / Variable Trace:
For `nums = [2,3,1,1,4]`:

| i | nums[i] | farthest before | i+nums[i] | farthest after | i==currentEnd | jumps | currentEnd |
|---|---------|-----------------|-----------|----------------|---------------|-------|-----------|
| 0 | 2 | 0 | 0+2=2 | 2 | yes (0==0) | 1 | 2 |
| 1 | 3 | 2 | 1+3=4 | 4 | no | 1 | 2 |
| 2 | 1 | 4 | 2+1=3 | 4 | yes (2==2) | 2 | 4 |
| 3 | 1 | 4 | 3+1=4 | 4 | no | 2 | 4 |
| 4 | (end) | - | - | - | exit | 2 | - |

**Explanation of jumps:**
- Jump 1: From index 0, can reach indices 1-2, choose range ending at 2
- Jump 2: From range 1-2, can reach up to index 4 (end)
- Total: 2 jumps

### Big-O Analysis:
- **Brute Force:** O(k^n) → branching factor k (max jump length), depth n
- **Optimized:** O(n) → single pass through array
- **Space Complexity:** O(1) → constant extra space

</details>


<details>
<summary><b>Variant #3: Fractional Knapsack</b></summary>

## Variant #3: Fractional Knapsack

### Input/Output:
- Input: `items = [(value:60,weight:10), (100,20), (120,30)]`, `capacity = 50`
- Output: `240` (max value with fractions allowed)

### Full State Space:
All possible combinations of items (with fractions).
```
For each item, decide how much to take: 0 to 100%
Continuous space → infinite combinations
Discrete approximation: try all fractions in steps
```

### Expected/Pruned State Space:
Greedy by value/weight ratio (highest first).
```
Sort by ratio descending
Take items greedily until capacity full
O(n log n) for sorting + O(n) for selection
```

### State Space Leading to Output:
Sorted items processed by ratio, taking maximum possible from each.

### Brute Force Canonical Skeleton:
```csharp
double FractionalKnapsackBruteForce(List<(int value, int weight)> items, int capacity)
{
    double maxValue = 0;
    
    // Try all possible fraction combinations (simplified: 0%, 50%, 100%)
    void Backtrack(int idx, int remainingCapacity, double currentValue)
    {
        if (idx == items.Count || remainingCapacity == 0)
        {
            maxValue = Math.Max(maxValue, currentValue);
            return;
        }
        
        // Try different fractions: 0%, 50%, 100%
        for (int fraction = 0; fraction <= 100; fraction += 50)
        {
            int take = Math.Min(items[idx].weight * fraction / 100, remainingCapacity);
            double value = take * (items[idx].value / (double)items[idx].weight);
            
            Backtrack(idx + 1, remainingCapacity - take, currentValue + value);
        }
    }
    
    Backtrack(0, capacity, 0);
    return maxValue;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `items = [(60,10),(100,20),(120,30)]`, `capacity = 50` (simplified with 3 fraction choices):

| idx | item | fraction | take | value | remainingCap | currentValue | maxValue |
|-----|------|----------|------|-------|--------------|--------------|----------|
| 0 | (60,10) | 100% | 10 | 60 | 40 | 60 | 60 |
| 1 | (100,20) | 100% | 20 | 100 | 20 | 160 | 160 |
| 2 | (120,30) | 50% | 15 | 60 | 5 | 220 | 220 |
| ... | ... | ... | ... | ... | ... | ... | ... |

**Note:** Brute force with continuous fractions is exponential/infinite.

### Optimized Solution from Canonical Skeleton:
```csharp
double FractionalKnapsack(List<(int value, int weight)> items, int capacity)
{
    // Sort by value/weight ratio (descending)
    items.Sort((a, b) => 
        (b.value / (double)b.weight).CompareTo(a.value / (double)a.weight));
    
    double totalValue = 0;
    int remaining = capacity;
    
    foreach (var item in items)
    {
        if (remaining == 0) break;
        
        // Take as much as possible from current item
        int take = Math.Min(item.weight, remaining);
        totalValue += take * (item.value / (double)item.weight);
        remaining -= take;
    }
    
    return totalValue;
}
```

### Explanation of Pruning:
- **Greedy choice property:** Always take item with highest value/weight ratio
- **Why it works:** Maximizing value per unit weight is optimal for fractional knapsack
- **Proof:** Any other selection can be improved by swapping to higher ratio items
- **Sorting criterion:** value/weight ratio descending
- **Fractions allowed:** Take partial items to fill capacity exactly

### Optimized Solution Code Walkthrough / Variable Trace:
For `items = [(60,10),(100,20),(120,30)]`, `capacity = 50`:

**Calculate ratios:**
- (60,10): ratio = 6.0
- (100,20): ratio = 5.0
- (120,30): ratio = 4.0

**After sorting by ratio:** `[(60,10), (100,20), (120,30)]`

| item | value | weight | ratio | remaining | take | value added | totalValue |
|------|-------|--------|-------|-----------|------|-------------|------------|
| (60,10) | 60 | 10 | 6.0 | 50 | 10 | 60 | 60 |
| (100,20) | 100 | 20 | 5.0 | 40 | 20 | 100 | 160 |
| (120,30) | 120 | 30 | 4.0 | 20 | 20 | 80 | 240 |

**Calculation for last item:** take=20, value = 20 × (120/30) = 20 × 4 = 80

### Big-O Analysis:
- **Brute Force:** O(k^n) or exponential → trying many fraction combinations
- **Optimized:** O(n log n) → sorting by ratio
- **Space Complexity:** O(1) → constant extra space

</details>


<details>
<summary><b>Variant #4: Minimum Coins / Change Making</b></summary>

## Variant #4: Minimum Coins / Change Making (Greedy Feasible)

### Input/Output:
- Input: `coins = [25, 10, 5, 1]`, `amount = 41`
- Output: `4` (coins: 25 + 10 + 5 + 1)

### Full State Space:
All combinations of coins that sum to amount.
```
For amount=41 with coins [25,10,5,1]:
- 41 pennies
- 1 quarter + 16 pennies
- 4 dimes + 1 penny
- Many more combinations...
Total: exponential combinations
```

### Expected/Pruned State Space:
Greedy: pick largest coin ≤ remaining amount.
```
Sort coins descending
Repeatedly pick largest valid coin
Works ONLY for canonical coin systems
```

### State Space Leading to Output:
Greedy selection from largest to smallest coins.

### Brute Force Canonical Skeleton:
```csharp
int MinCoinsBruteForce(int[] coins, int amount)
{
    int minCoins = int.MaxValue;
    
    void Backtrack(int remaining, int coinCount)
    {
        if (remaining == 0)
        {
            minCoins = Math.Min(minCoins, coinCount);
            return;
        }
        
        if (remaining < 0) return;
        
        foreach (int coin in coins)
        {
            Backtrack(remaining - coin, coinCount + 1);
        }
    }
    
    Backtrack(amount, 0);
    return minCoins == int.MaxValue ? -1 : minCoins;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `coins = [25,10,5,1]`, `amount = 41` (showing one path):

| remaining | coinCount | try coin | next remaining | action |
|-----------|-----------|----------|----------------|--------|
| 41 | 0 | 25 | 16 | recurse |
| 16 | 1 | 10 | 6 | recurse |
| 6 | 2 | 5 | 1 | recurse |
| 1 | 3 | 1 | 0 | found: 4 coins |

**Many other paths explored...**

### Optimized Solution from Canonical Skeleton:
```csharp
int MinCoinsGreedy(int[] coins, int amount)
{
    // Sort coins in descending order
    Array.Sort(coins);
    Array.Reverse(coins);
    
    int coinCount = 0;
    int remaining = amount;
    
    foreach (int coin in coins)
    {
        if (remaining == 0) break;
        
        // Take as many of this coin as possible
        int count = remaining / coin;
        coinCount += count;
        remaining -= count * coin;
    }
    
    return remaining == 0 ? coinCount : -1;
}
```

### Explanation of Pruning:
- **Greedy choice:** Always use largest coin that fits
- **IMPORTANT:** Greedy works ONLY for canonical coin systems (e.g., US: 1,5,10,25)
- **Counter-example:** coins=[1,3,4], amount=6 → greedy gives 4+1+1=3 coins, optimal is 3+3=2 coins
- **Why canonical works:** Each larger coin is perfectly divisible or strategically valued
- **For non-canonical:** Use dynamic programming instead

### Optimized Solution Code Walkthrough / Variable Trace:
For `coins = [25,10,5,1]`, `amount = 41`:

**After sorting descending:** `[25,10,5,1]`

| coin | remaining | count = remaining/coin | coinCount | remaining after |
|------|-----------|------------------------|-----------|----------------|
| 25 | 41 | 1 | 1 | 16 |
| 10 | 16 | 1 | 2 | 6 |
| 5 | 6 | 1 | 3 | 1 |
| 1 | 1 | 1 | 4 | 0 |

**Result:** 4 coins (25 + 10 + 5 + 1)

### Big-O Analysis:
- **Brute Force:** O(k^n) → k coin types, depth n (amount)
- **Optimized (Greedy):** O(k log k) → sorting coins, then O(k) scan
- **Space Complexity:** O(1) → constant extra space
- **Note:** For non-canonical, use DP: O(amount × k)

</details>


<details>
<summary><b>Variant #5: Task Scheduling with Deadlines</b></summary>

## Variant #5: Task Scheduling with Deadlines

### Input/Output:
- Input: `tasks = [(profit:50,deadline:2), (60,1), (100,2), (120,3)]`
- Output: `[task3, task0, task2]` (max profit = 270, scheduled within deadlines)

### Full State Space:
All permutations of tasks, checking deadline constraints.
```
Permutations: n! orderings
For each, check if tasks meet deadlines
Find ordering with maximum profit
Time: O(n! × n)
```

### Expected/Pruned State Space:
Greedy by profit (highest first), schedule in earliest available slot.
```
Sort by profit descending
For each task, assign to latest available slot before deadline
O(n log n) sorting + O(n²) scheduling
```

### State Space Leading to Output:
Sorted tasks by profit, scheduled in slots array.

### Brute Force Canonical Skeleton:
```csharp
int MaxProfitBruteForce(List<(int profit, int deadline)> tasks)
{
    int maxProfit = 0;
    int n = tasks.Count;
    
    void Backtrack(List<int> schedule)
    {
        // Calculate profit if valid schedule
        int profit = 0;
        bool valid = true;
        
        for (int i = 0; i < schedule.Count; i++)
        {
            int taskIdx = schedule[i];
            if (i + 1 > tasks[taskIdx].deadline)
            {
                valid = false;
                break;
            }
            profit += tasks[taskIdx].profit;
        }
        
        if (valid)
            maxProfit = Math.Max(maxProfit, profit);
        
        if (schedule.Count == n) return;
        
        // Try adding each remaining task
        for (int i = 0; i < n; i++)
        {
            if (!schedule.Contains(i))
            {
                schedule.Add(i);
                Backtrack(schedule);
                schedule.RemoveAt(schedule.Count - 1);
            }
        }
    }
    
    Backtrack(new List<int>());
    return maxProfit;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `tasks = [(50,2),(60,1),(100,2),(120,3)]` (showing one path):

| schedule | time slots | profits | deadlines | valid | profit |
|----------|------------|---------|-----------|-------|--------|
| [3] | [1] | [120] | [3] | yes | 120 |
| [3,0] | [1,2] | [120,50] | [3,2] | yes | 170 |
| [3,0,2] | [1,2,3] | [120,50,100] | [3,2,2] | no (slot 3 > deadline 2) | - |
| [3,2] | [1,2] | [120,100] | [3,2] | yes | 220 |
| [3,2,0] | [1,2,3] | [120,100,50] | [3,2,2] | no | - |

### Optimized Solution from Canonical Skeleton:
```csharp
int TaskSchedulingMaxProfit(List<(int profit, int deadline)> tasks)
{
    // Sort by profit descending
    tasks.Sort((a, b) => b.profit.CompareTo(a.profit));
    
    int maxDeadline = tasks.Max(t => t.deadline);
    int[] slots = new int[maxDeadline + 1]; // 0 = empty
    Array.Fill(slots, -1);
    
    int totalProfit = 0;
    
    foreach (var task in tasks)
    {
        // Find latest available slot before deadline
        for (int slot = task.deadline; slot >= 1; slot--)
        {
            if (slots[slot] == -1)
            {
                slots[slot] = task.profit;
                totalProfit += task.profit;
                break;
            }
        }
    }
    
    return totalProfit;
}
```

### Explanation of Pruning:
- **Greedy choice:** Schedule highest profit tasks first
- **Latest slot strategy:** Assign each task to latest available slot before deadline
- **Why it works:** Higher profit tasks get priority; scheduling late maximizes future options
- **Disjoint set optimization:** Can use union-find for O(n log n) total
- **Key insight:** Profit matters more than deadline order

### Optimized Solution Code Walkthrough / Variable Trace:
For `tasks = [(50,2),(60,1),(100,2),(120,3)]`:

**After sorting by profit:** `[(120,3), (100,2), (60,1), (50,2)]`

| task | profit | deadline | check slots | assign slot | slots | totalProfit |
|------|--------|----------|-------------|-------------|-------|-------------|
| (120,3) | 120 | 3 | 3,2,1 | slot 3 | [-,_,_,120] | 120 |
| (100,2) | 100 | 2 | 2,1 | slot 2 | [-,_,100,120] | 220 |
| (60,1) | 60 | 1 | 1 | slot 1 | [-,60,100,120] | 280 |
| (50,2) | 50 | 2 | 2,1 | (all full) | [-,60,100,120] | 280 |

**Result:** Total profit = 280 (tasks with profits 120, 100, 60)

### Big-O Analysis:
- **Brute Force:** O(n! × n) → all permutations with validation
- **Optimized:** O(n² + n log n) → O(n log n) sorting + O(n²) slot finding
- **With Union-Find:** O(n log n) → both sorting and slot assignment
- **Space Complexity:** O(max_deadline) → slots array

</details>


<details>
<summary><b>Variant #6: Reorganize String</b></summary>

## Variant #6: Reorganize String

### Input/Output:
- Input: `s = "aab"`
- Output: `"aba"` (no two adjacent same characters)

### Full State Space:
All permutations of characters.
```
For "aab": permutations = aab, aba, baa
Check each for adjacent duplicates
Find any valid arrangement
Time: O(n!)
```

### Expected/Pruned State Space:
Greedy with max heap: always pick most frequent character.
```
Heap by frequency (max first)
Alternate between top two frequencies
O(n log k) where k = unique characters
```

### State Space Leading to Output:
Max heap ensures most frequent characters are placed first.

### Brute Force Canonical Skeleton:
```csharp
string ReorganizeStringBruteForce(string s)
{
    // Count frequencies
    Dictionary<char, int> freq = new();
    foreach (char c in s)
        freq[c] = freq.GetValueOrDefault(c, 0) + 1;
    
    // Check if possible
    int maxFreq = freq.Values.Max();
    if (maxFreq > (s.Length + 1) / 2)
        return "";
    
    // Try backtracking
    char[] result = new char[s.Length];
    
    bool Backtrack(int idx, char prev)
    {
        if (idx == s.Length) return true;
        
        foreach (var kvp in freq.Where(x => x.Value > 0 && x.Key != prev))
        {
            result[idx] = kvp.Key;
            freq[kvp.Key]--;
            
            if (Backtrack(idx + 1, kvp.Key))
                return true;
            
            freq[kvp.Key]++;
        }
        return false;
    }
    
    return Backtrack(0, '\0') ? new string(result) : "";
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `s = "aab"`:

| idx | prev | try | freq after | result | backtrack |
|-----|------|-----|------------|--------|------------|
| 0 | \0 | 'a' | {a:1,b:1} | [a] | continue |
| 1 | 'a' | 'b' | {a:1,b:0} | [a,b] | continue |
| 2 | 'b' | 'a' | {a:0,b:0} | [a,b,a] | success! |

### Optimized Solution from Canonical Skeleton:
```csharp
string ReorganizeString(string s)
{
    // Count frequencies
    Dictionary<char, int> freq = new();
    foreach (char c in s)
        freq[c] = freq.GetValueOrDefault(c, 0) + 1;
    
    // Check if possible
    int maxFreq = freq.Values.Max();
    if (maxFreq > (s.Length + 1) / 2)
        return "";
    
    // Max heap by frequency
    PriorityQueue<char, int> maxHeap = new();
    foreach (var kvp in freq)
        maxHeap.Enqueue(kvp.Key, -kvp.Value);
    
    StringBuilder result = new();
    char prev = '\0';
    int prevFreq = 0;
    
    while (maxHeap.Count > 0)
    {
        char current = maxHeap.Dequeue();
        result.Append(current);
        
        // Put previous back if still has frequency
        if (prevFreq > 0)
            maxHeap.Enqueue(prev, -prevFreq);
        
        prev = current;
        prevFreq = --freq[current];
    }
    
    return result.ToString();
}
```

### Explanation of Pruning:
- **Greedy strategy:** Use most frequent character first
- **Alternating:** Hold previous character, pick next most frequent
- **Why it works:** Prevents accumulation of same character
- **Impossibility:** If maxFreq > ⌈n/2⌉, no valid arrangement
- **Heap advantage:** O(log k) to find next best instead of O(k) scan

### Optimized Solution Code Walkthrough / Variable Trace:
For `s = "aab"`:

| step | maxHeap | dequeue | result | prev | prevFreq | put back |
|------|---------|---------|--------|------|----------|----------|
| init | [a:2,b:1] | - | "" | \0 | 0 | - |
| 1 | [b:1] | a | "a" | a | 1 | none |
| 2 | [a:1] | b | "ab" | b | 0 | a:1 |
| 3 | [] | a | "aba" | a | 0 | none |

### Big-O Analysis:
- **Brute Force:** O(n!) → try all permutations
- **Optimized:** O(n log k) → heap operations for k unique chars
- **Space Complexity:** O(k) → k ≤ 26 for lowercase letters

</details>


<details>
<summary><b>Variant #7: Gas Station Circuit</b></summary>

## Variant #7: Gas Station Circuit

### Input/Output:
- Input: `gas = [1,2,3,4,5]`, `cost = [3,4,5,1,2]`
- Output: `3` (start at index 3 to complete circuit)

### Full State Space:
Try starting from each station.
```
For each start index:
  Simulate driving the circuit
  Check if we can complete without running out
Time: O(n²) → n starts × n stations
```

### Expected/Pruned State Space:
Greedy with cumulative sum tracking.
```
If total_gas ≥ total_cost → solution exists
Track cumulative gas while finding start point
O(n) single pass
```

### State Space Leading to Output:
Cumulative gas tracking identifies valid start index.

### Brute Force Canonical Skeleton:
```csharp
int GasStationBruteForce(int[] gas, int[] cost)
{
    int n = gas.Length;
    
    for (int start = 0; start < n; start++)
    {
        int tank = 0;
        bool canComplete = true;
        
        for (int i = 0; i < n; i++)
        {
            int station = (start + i) % n;
            tank += gas[station] - cost[station];
            
            if (tank < 0)
            {
                canComplete = false;
                break;
            }
        }
        
        if (canComplete)
            return start;
    }
    
    return -1;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `gas = [1,2,3,4,5]`, `cost = [3,4,5,1,2]`:

| start | i | station | tank | gas[i]-cost[i] | tank after | canComplete |
|-------|---|---------|------|----------------|------------|-------------|
| 0 | 0 | 0 | 0 | 1-3=-2 | -2 | no (stop) |
| 1 | 0 | 1 | 0 | 2-4=-2 | -2 | no (stop) |
| 2 | 0 | 2 | 0 | 3-5=-2 | -2 | no (stop) |
| 3 | 0 | 3 | 0 | 4-1=3 | 3 | continue |
| 3 | 1 | 4 | 3 | 5-2=3 | 6 | continue |
| 3 | 2 | 0 | 6 | 1-3=-2 | 4 | continue |
| 3 | 3 | 1 | 4 | 2-4=-2 | 2 | continue |
| 3 | 4 | 2 | 2 | 3-5=-2 | 0 | yes! |

### Optimized Solution from Canonical Skeleton:
```csharp
int GasStation(int[] gas, int[] cost)
{
    int totalGas = 0;
    int totalCost = 0;
    int tank = 0;
    int start = 0;
    
    for (int i = 0; i < gas.Length; i++)
    {
        totalGas += gas[i];
        totalCost += cost[i];
        tank += gas[i] - cost[i];
        
        // If tank negative, cannot start before i
        if (tank < 0)
        {
            start = i + 1;
            tank = 0;
        }
    }
    
    // Solution exists only if total gas ≥ total cost
    return totalGas >= totalCost ? start : -1;
}
```

### Explanation of Pruning:
- **Key insight 1:** If total_gas < total_cost, no solution exists
- **Key insight 2:** If starting at A fails at B, any station between A and B also fails
- **Greedy choice:** When tank goes negative, reset start to next station
- **Why it works:** If we can't reach station i from any j < i, start from i+1
- **Proof:** Cumulative deficit from j to i means j is invalid start

### Optimized Solution Code Walkthrough / Variable Trace:
For `gas = [1,2,3,4,5]`, `cost = [3,4,5,1,2]`:

| i | gas[i] | cost[i] | diff | tank | start | action |
|---|--------|---------|------|------|-------|--------|
| 0 | 1 | 3 | -2 | -2 | 0 | reset: start=1, tank=0 |
| 1 | 2 | 4 | -2 | -2 | 1 | reset: start=2, tank=0 |
| 2 | 3 | 5 | -2 | -2 | 2 | reset: start=3, tank=0 |
| 3 | 4 | 1 | 3 | 3 | 3 | continue |
| 4 | 5 | 2 | 3 | 6 | 3 | continue |

**Final check:** totalGas=15, totalCost=15 → 15≥15 → return start=3

### Big-O Analysis:
- **Brute Force:** O(n²) → n starts × n simulation
- **Optimized:** O(n) → single pass
- **Space Complexity:** O(1) → constant extra space

</details>


<details>
<summary><b>Variant #8: Huffman Encoding</b></summary>

## Variant #8: Huffman Encoding

### Input/Output:
- Input: `freq = {a:5, b:9, c:12, d:13, e:16, f:45}`
- Output: Huffman tree with optimal prefix codes

### Full State Space:
All possible binary tree structures.
```
For n characters, number of binary trees = Catalan number
C(n) = (2n)! / ((n+1)! × n!)
Exponential growth
```

### Expected/Pruned State Space:
Greedy: repeatedly merge two least frequent nodes.
```
Min heap for frequencies
Repeatedly: dequeue 2 min, merge, enqueue sum
O(n log n)
```

### State Space Leading to Output:
Min heap ensures lowest frequency nodes are combined first.

### Brute Force Canonical Skeleton:
```csharp
// Brute force: try all binary tree structures (exponential)
int HuffmanBruteForce(Dictionary<char, int> freq)
{
    // Generate all possible tree structures
    // For each tree, calculate total encoding cost
    // Return minimum cost
    
    // Implementation omitted due to complexity
    // Cost = sum of (freq[char] × depth[char])
    
    return -1; // Placeholder
}
```

### Brute Force Code Walkthrough / Variable Trace:
**Too complex to enumerate** - exponential tree structures

### Optimized Solution from Canonical Skeleton:
```csharp
class HuffmanNode
{
    public int Freq { get; set; }
    public char? Char { get; set; }
    public HuffmanNode Left { get; set; }
    public HuffmanNode Right { get; set; }
}

HuffmanNode BuildHuffmanTree(Dictionary<char, int> freq)
{
    // Min heap by frequency
    PriorityQueue<HuffmanNode, int> minHeap = new();
    
    // Initialize with leaf nodes
    foreach (var kvp in freq)
    {
        var node = new HuffmanNode { Freq = kvp.Value, Char = kvp.Key };
        minHeap.Enqueue(node, kvp.Value);
    }
    
    // Build tree bottom-up
    while (minHeap.Count > 1)
    {
        var left = minHeap.Dequeue();
        var right = minHeap.Dequeue();
        
        var parent = new HuffmanNode
        {
            Freq = left.Freq + right.Freq,
            Left = left,
            Right = right
        };
        
        minHeap.Enqueue(parent, parent.Freq);
    }
    
    return minHeap.Dequeue();
}

void GenerateCodes(HuffmanNode node, string code, Dictionary<char, string> codes)
{
    if (node == null) return;
    
    if (node.Char.HasValue)
    {
        codes[node.Char.Value] = code;
        return;
    }
    
    GenerateCodes(node.Left, code + "0", codes);
    GenerateCodes(node.Right, code + "1", codes);
}
```

### Explanation of Pruning:
- **Greedy choice:** Combine two lowest frequency nodes first
- **Why it works:** Lower frequency nodes should be deeper (longer codes)
- **Optimal substructure:** Optimal tree for subset leads to optimal overall tree
- **Proof:** Exchange argument - swapping frequencies increases cost
- **Prefix-free:** No code is prefix of another (tree structure guarantees)

### Optimized Solution Code Walkthrough / Variable Trace:
For `freq = {a:5, b:9, c:12, d:13, e:16, f:45}`:

| step | heap | dequeue | merge | freq | enqueue |
|------|------|---------|-------|------|----------|
| init | [a:5,b:9,c:12,d:13,e:16,f:45] | - | - | - | - |
| 1 | [c:12,d:13,e:16,f:45,(a+b):14] | a:5, b:9 | a+b | 14 | (a+b):14 |
| 2 | [d:13,(a+b):14,e:16,f:45,(c+12):25] | c:12, d:13 | c+d | 25 | (c+d):25 |
| 3 | [e:16,f:45,(c+d):25,(a+b+e):30] | (a+b):14, e:16 | (a+b)+e | 30 | (a+b+e):30 |
| 4 | [f:45,((a+b)+e+(c+d)):55] | (c+d):25, (a+b+e):30 | merge | 55 | left subtree |
| 5 | [root:100] | f:45, left:55 | root | 100 | root |

**Resulting codes (example):**
- f: 0 (most frequent → shortest)
- c: 100
- d: 101
- a: 1100
- b: 1101
- e: 111

### Big-O Analysis:
- **Brute Force:** O(Catalan(n)) → exponential in tree structures
- **Optimized:** O(n log n) → n heap operations
- **Space Complexity:** O(n) → heap and tree nodes

</details>

---

# Key Takeaways

## Pattern Recognition
- **Greedy** = make locally optimal choice at each step
- Typical signals: "maximize", "minimize", "optimal", scheduling, selection problems
- **Critical requirement:** Problem must have **greedy choice property** and **optimal substructure**

## When Greedy Works
1. **Greedy Choice Property:** Local optimum leads to global optimum
2. **Optimal Substructure:** Optimal solution contains optimal solutions to subproblems
3. **No need to reconsider:** Once a choice is made, it's final

## When Greedy Fails
- **0/1 Knapsack:** Greedy by ratio doesn't work (need DP)
- **Non-canonical coins:** Greedy fails (e.g., coins=[1,3,4], amount=6)
- **Graph shortest path with negative weights:** Dijkstra fails (need Bellman-Ford)

## Common Greedy Strategies
1. **Sort + Select:** Activity selection, fractional knapsack
2. **Priority Queue/Heap:** Huffman encoding, merge K sorted lists
3. **Cumulative Sum:** Gas station, jump game
4. **Exchange Argument:** Prove greedy by showing swaps don't improve
5. **Stay Ahead:** Show greedy is always at least as good as optimal

## Proof Techniques
1. **Exchange Argument:** If optimal differs, swap to greedy choice without loss
2. **Greedy Stays Ahead:** Show greedy maintains advantage at each step
3. **Structural Induction:** Prove optimal solution has greedy structure

## Core Template
```csharp
// General greedy pattern
void GreedySolver<T>(List<T> items)
{
    // Sort by greedy criterion
    items.Sort((a, b) => GreedyCriterion(a, b));
    
    // Iterate and make greedy choices
    foreach (var item in items)
    {
        if (CanInclude(item))
        {
            Include(item);
        }
    }
}
```

## Time Complexity Patterns
- **With sorting:** O(n log n)
- **With heap:** O(n log k) where k = heap size
- **Simple scan:** O(n)
- **Always better than:** Brute force O(2^n) or O(n!)

---

# Mental Checklist for Greedy

- Does local optimum lead to global optimum?
- Can I prove greedy choice property?
- Is there optimal substructure?
- Can I sort by some criterion?
- Would backtracking reconsider choices? (If yes, maybe not greedy)
- Does the problem ask for optimization under constraints?

If yes → Greedy applies (if greedy choice property holds).
