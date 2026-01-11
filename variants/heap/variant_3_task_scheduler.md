# Task Scheduler

**Difficulty:** Medium  
**LeetCode Problem:** #621  
**Tags:** `Heap`, `Greedy`, `Hash Map`, `Frequency`, `Cooldown`

---

## Problem Statement

Given a characters array `tasks`, representing the tasks a CPU needs to do, where each letter represents a different task. Tasks could be done in any order. Each task is done in one unit of time. For each unit of time, the CPU could complete either one task or just be idle.

However, there is a non-negative integer `n` that represents the cooldown period between two **same** tasks (the same letter in the array), that is, there must be at least `n` units of time between any two same tasks.

Return the least number of units of time that the CPU will take to finish all the given tasks.

**Constraints:**
- `1 <= tasks.length <= 10^4`
- `tasks[i]` is uppercase English letter.
- `0 <= n <= 100`

**Example 1:**
```
Input: tasks = ["A","A","A","B","B","B"], n = 2
Output: 8
Explanation: 
A -> B -> idle -> A -> B -> idle -> A -> B
Cycle 1: A B _
Cycle 2: A B _
Cycle 3: A B
Total: 8 units
```

**Example 2:**
```
Input: tasks = ["A","A","A","B","B","B"], n = 0
Output: 6
Explanation: No cooldown, can do: A A A B B B
```

**Example 3:**
```
Input: tasks = ["A","A","A","A","A","A","B","C","D","E","F","G"], n = 2
Output: 16
Explanation: One possible solution:
A -> B -> C -> A -> D -> E -> A -> F -> G -> A -> idle -> idle -> A -> idle -> idle -> A
```

---

## Step 1: State Space

### Problem Visualization

Tasks: `["A","A","A","B","B","B"]`, n = 2

```
Frequency: {A: 3, B: 3}

Scheduling with cooldown n=2:

Time:  0  1  2  3  4  5  6  7
Task:  A  B  _  A  B  _  A  B

Explanation:
- At t=0: Execute A (1st)
- At t=1: Execute B (1st) - can't do A (cooldown)
- At t=2: Idle - can't do A or B (cooldown)
- At t=3: Execute A (2nd) - cooldown satisfied
- At t=4: Execute B (2nd)
- At t=5: Idle
- At t=6: Execute A (3rd)
- At t=7: Execute B (3rd)

Total: 8 units
```

**Pattern with most frequent task:**
```
Most frequent task: A (count = 3)
Cooldown: n = 2

Frame structure:
[A _ _ ] [A _ _ ] [A]
 cycle1   cycle2   last

Cycles: (3 - 1) = 2 cycles
Cycle length: (n + 1) = 3
Last task: 1

Minimum time if no other tasks:
  cycles * cycle_length + 1 = 2 * 3 + 1 = 7

But we have B (count = 3), fills gaps:
[A B _ ] [A B _ ] [A B]
Total: 8 = max(7 + 1, total_tasks)
```

### Core Question

**What is the minimum time to execute all tasks with cooldown constraints?**

Greedy: Execute most frequent tasks with maximum spacing, fill gaps with other tasks.

### Deriving from First Principles

**Observation 1: Most frequent task dominates**
```
Let max_freq = maximum frequency of any task
This task requires (max_freq - 1) cooldown periods
Each period has (n + 1) slots (1 for task, n for cooldown)

Minimum frames needed: (max_freq - 1) * (n + 1) + count_of_max_freq_tasks
```

**Observation 2: Two cases**
```
Case 1: Many idle slots
  When n is large or few task types
  Time = (max_freq - 1) * (n + 1) + max_count

Case 2: No idle needed
  When many different tasks or n is small
  Time = total_tasks (can fill all slots)

Answer = max(case1_time, total_tasks)
```

**Observation 3: Greedy with heap**
```
Simulate scheduling:
1. Use max-heap for task frequencies
2. Each round, pick up to (n+1) different tasks
3. Execute them, reduce frequencies
4. If fewer than (n+1) tasks available, add idle
5. Add tasks back to heap if frequency > 0
```

**Observation 4: Mathematical formula (optimal)**
```
max_freq = maximum frequency
max_count = number of tasks with max_freq

If only max-freq tasks:
  (max_freq - 1) * (n + 1) + max_count

But other tasks can fill gaps:
  max(formula, total_tasks)
```

**Observation 5: Why formula works**
```
Layout most frequent tasks first:
[A _ _ ... _] [A _ _ ... _] ... [A]
  n spaces      n spaces          last

This creates (max_freq - 1) frames of size (n+1)
Plus final tasks with max frequency: max_count

If other tasks overflow this structure:
  Then no idle needed, time = total_tasks
```

**Formula:**
```
freq_map = count frequency of each task
max_freq = max(freq_map.values)
max_count = count of tasks with max_freq

min_time = (max_freq - 1) * (n + 1) + max_count
return max(min_time, len(tasks))
```

### State Space Structure

**Type:** Scheduling with cooldown constraints.

**Approaches:**
1. **Mathematical (O(n)):** Count frequencies, apply formula
2. **Heap simulation (O(n log k)):** Simulate greedy scheduling

**Cardinality:**
- Mathematical: O(n) to count, O(1) to compute
- Simulation: O(n log k) where n = tasks, k = unique tasks
- Space: O(k) for frequencies/heap

**Key Property:** Most frequent task determines minimum time structure.

### Example Computation

Tasks: `["A","A","A","B","B","B"]`, n = 2

```
Step 1: Count frequencies
  freq = {A: 3, B: 3}

Step 2: Find max frequency and count
  max_freq = 3
  max_count = 2 (both A and B)

Step 3: Apply formula
  min_time = (3 - 1) * (2 + 1) + 2
           = 2 * 3 + 2
           = 6 + 2
           = 8

Step 4: Compare with total tasks
  total_tasks = 6
  result = max(8, 6) = 8

Verification:
  A B _ A B _ A B
  0 1 2 3 4 5 6 7
  Total: 8 ✓
```

Example 2: Tasks = `["A","A","A","B","B","B"]`, n = 0
```
max_freq = 3
max_count = 2
min_time = (3-1) * (0+1) + 2 = 2 * 1 + 2 = 4

But total_tasks = 6
result = max(4, 6) = 6

Schedule: A A A B B B (no idle needed)
```

### Generation Pattern

**Brute force:**
```csharp
// Try all permutations of tasks
// Check each for cooldown violations
// Find minimum valid length
// Time: Exponential
```

---

## Step 2: Brute Force (Simulation)

```csharp
// Simulate with heap (not optimal but correct)
public int LeastInterval(char[] tasks, int n) {
    var freq = new Dictionary<char, int>();
    foreach (char task in tasks) {
        freq[task] = freq.GetValueOrDefault(task) + 1;
    }
    
    var heap = new PriorityQueue<int, int>(
        Comparer<int>.Create((a, b) => b.CompareTo(a))
    );
    
    foreach (int count in freq.Values) {
        heap.Enqueue(count, count);
    }
    
    int time = 0;
    var cooldown = new Queue<(int count, int availableAt)>();
    
    while (heap.Count > 0 || cooldown.Count > 0) {
        time++;
        
        // Add tasks back from cooldown
        if (cooldown.Count > 0 && cooldown.Peek().availableAt == time) {
            var (count, _) = cooldown.Dequeue();
            heap.Enqueue(count, count);
        }
        
        // Execute task with highest frequency
        if (heap.Count > 0) {
            int count = heap.Dequeue();
            count--;
            if (count > 0) {
                cooldown.Enqueue((count, time + n + 1));
            }
        }
        // Else: idle time
    }
    
    return time;
}
```

**Complexity:**
- Time: O(n log k) - heap operations
- Space: O(k) - heap and queue

---

## Step 3: Optimization Ideas

### Key Insights
1. **Math formula:** Most frequent task creates frame structure
2. **No simulation needed:** Direct calculation
3. **Two cases:** Idle-dominated vs task-dominated
4. **Formula captures both:** max(frame_time, total_tasks)

---

## Step 4: Optimal Solution (Mathematical)

```csharp
public int LeastInterval(char[] tasks, int n) {
    // Count frequency of each task
    var freq = new int[26];
    foreach (char task in tasks) {
        freq[task - 'A']++;
    }
    
    // Find maximum frequency
    int maxFreq = 0;
    foreach (int f in freq) {
        maxFreq = Math.Max(maxFreq, f);
    }
    
    // Count how many tasks have maximum frequency
    int maxCount = 0;
    foreach (int f in freq) {
        if (f == maxFreq) {
            maxCount++;
        }
    }
    
    // Calculate minimum time
    // (maxFreq - 1) full cycles of length (n + 1)
    // Plus maxCount tasks at the end
    int minTime = (maxFreq - 1) * (n + 1) + maxCount;
    
    // If other tasks fill all gaps, no idle needed
    return Math.Max(minTime, tasks.Length);
}
```

**Complexity:**
- Time: O(n) - count frequencies
- Space: O(1) - fixed size array (26 letters)

**Why It Works:**

1. **Frame structure:**
   ```
   Most frequent task appears maxFreq times
   Layout: [X _ _ ... _] [X _ _ ... _] ... [X X ... X]
           n+1 slots      n+1 slots         maxCount tasks
   ```

2. **Other tasks fill gaps:**
   ```
   If enough other tasks exist, they fill all gaps
   Then time = total tasks (no idle)
   ```

3. **Formula handles both:**
   ```
   Case 1 (idle needed): (maxFreq-1)*(n+1) + maxCount > tasks.Length
   Case 2 (no idle): tasks.Length >= formula
   
   max() selects correct case
   ```

---

## Alternative: Heap Simulation

For clarity or when modifications needed:

```csharp
public int LeastInterval(char[] tasks, int n) {
    if (n == 0) return tasks.Length;
    
    var freq = new Dictionary<char, int>();
    foreach (char task in tasks) {
        freq[task] = freq.GetValueOrDefault(task) + 1;
    }
    
    var heap = new PriorityQueue<int, int>(
        Comparer<int>.Create((a, b) => b.CompareTo(a))
    );
    foreach (int count in freq.Values) {
        heap.Enqueue(count, count);
    }
    
    int time = 0;
    
    while (heap.Count > 0) {
        var temp = new List<int>();
        
        // Execute up to (n+1) different tasks
        for (int i = 0; i <= n; i++) {
            if (heap.Count > 0) {
                int count = heap.Dequeue();
                count--;
                if (count > 0) {
                    temp.Add(count);
                }
            }
        }
        
        // Add tasks back to heap
        foreach (int count in temp) {
            heap.Enqueue(count, count);
        }
        
        // Add time: (n+1) if more tasks remain, else actual tasks executed
        time += heap.Count > 0 ? n + 1 : temp.Count + (n + 1 - temp.Count - (heap.Count == 0 ? 1 : 0));
    }
    
    return time;
}
```

---

## Edge Cases

1. **No cooldown:** `n = 0` → return `tasks.Length`
2. **Single task type:** `["A","A","A"], n=2` → `(3-1)*3+1 = 7`
3. **Many different tasks:** No idle needed
4. **All same task:** Maximum idle time
5. **Large n:** More idle slots

---

## Visualization

```
Example: ["A","A","A","B","B","B"], n = 2

Frequency: A=3, B=3
max_freq = 3, max_count = 2

Frame layout (maxFreq - 1 = 2 frames):
Frame 1: A _ _ 
Frame 2: A _ _
Final:   A B

Fill with B:
Frame 1: A B _
Frame 2: A B _
Final:   A B

Total: 3 + 3 + 2 = 8

Formula: (3-1) * (2+1) + 2 = 2*3 + 2 = 8 ✓
```

---

## Related Problems

1. **Rearrange String k Distance Apart (LeetCode #358)** - Similar cooldown
2. **Reorganize String (LeetCode #767)** - Adjacent constraint (n=1)
3. **CPU Scheduling** - Classic OS problem
4. **Task Scheduler II (LeetCode #2365)** - Different cooldown rules

---

## Pattern Recognition

**Problem Asks For:**
- Schedule tasks with constraints
- Cooldown between same tasks
- Minimize total time

**This Suggests:**
- Greedy based on frequency
- Most frequent task dominates
- Math formula or heap simulation
- → **Greedy + Frequency + Heap**

**Key Indicators:**
- "Cooldown" or "waiting time"
- "Same task cannot be adjacent within n"
- Minimize completion time

---

## Tags

`#heap` `#greedy` `#hash-map` `#frequency` `#scheduling` `#cooldown` `#math-formula`
