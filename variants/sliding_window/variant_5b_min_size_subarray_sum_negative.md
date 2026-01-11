# Variant: Minimum Size Subarray Sum (With Negative Integers)

**Pattern:** Prefix Sum + Monotonic Deque (Hybrid)  
**Difficulty:** Hard  
**Related to:** LeetCode #209 (extended to negative integers)

---

## Problem Statement

Given an array of integers `nums` (which may contain **negative integers**) and an integer `target`, return the **minimal length** of a contiguous subarray whose sum is **greater than or equal to** `target`. If no such subarray exists, return `0`.

**Key Difference from Standard Version:** This variant handles **negative integers**, which breaks the sliding window approach used for positive-only arrays.

### Input/Output:
- Input: `nums = [2, -1, 2, 1, -3, 4]`, `target = 3`
- Output: `2`
- Explanation: Subarray `[2, 1]` has minimal length 2 with sum = 3

---

## **Step 1: Derive Full State Space from Problem**

### **Problem Visualization:**
```
Input: nums = [2, -1, 2, 1, -3, 4], target = 3

Why sliding window fails:
  Window: [2, -1, 2, 1] sum = 4 ≥ 3 ✓
  Remove left (2): [-1, 2, 1] sum = 2 < 3 ✗ Good
  
  But consider:
  Window: [2, -1, 2] sum = 3 ≥ 3 ✓
  Remove left (2): [-1, 2] sum = 1 < 3 ✗ Good
  
  BUT ALSO:
  Window: [5, -3, 2] sum = 4 ≥ 3 ✓
  Remove left (5): [-3, 2] sum = -1 < 3 ✗
  Sum DECREASED by removing! Sliding window assumes removing
  decreases or maintains sum — VIOLATED!

Prefix sum + Monotonic deque approach:

Prefix sums: [0, 2, 1, 3, 4, 1, 5]
              0  1  2  3  4  5  6  (indices)

For target = 3, find smallest window where:
  prefix[j] - prefix[i] ≥ 3
  
At j=3: prefix=3, need prefix[i] ≤ 0
  Deque: [0@idx0]
  3 - 0 = 3 ≥ 3 ✓ length = 3-0 = 3

At j=4: prefix=4, need prefix[i] ≤ 1
  Deque: [0@idx0, 1@idx2]
  4 - 1 = 3 ≥ 3 ✓ length = 4-2 = 2 (better!)

Deque maintains indices with INCREASING prefix values
```

### **Core Question:**
How to handle negative integers where sliding window breaks down?

### **Deriving from First Principles:**

**Observation 1:** Sliding window monotonic property
- Standard sliding window assumes: removing left element can only decrease (or maintain) sum
- This holds for **positive-only arrays**
- **Breaks with negatives:** Removing a large negative increases sum!

**Observation 2:** Why negatives break sliding window
```
Example: nums = [5, -3, 2], target = 4

Window [5, -3, 2]:
  sum = 4 ≥ target ✓
  Should we shrink from left?
  
Remove 5: [-3, 2]
  sum = -1 < target ✗
  We INCREASED the sum by removing!
  
This violates the assumption that shrinking decreases sum.
Sliding window logic breaks down.
```

**Observation 3:** Prefix sum transformation
- Subarray sum [i, j] = prefix[j] - prefix[i-1]
- Want: prefix[j] - prefix[i-1] ≥ target
- Rearrange: prefix[i-1] ≤ prefix[j] - target
- For each j, find smallest i where this holds

**Observation 4:** Monotonic deque maintains candidates
- For position j, we need smallest i where prefix[i] ≤ prefix[j] - target
- Can't use sliding window, but can use monotonic deque!
- Deque property: indices with **increasing** prefix values
- Why increasing? If prefix[a] ≥ prefix[b] and a < b, then a is never optimal
  - For any j: if prefix[j] - prefix[b] ≥ target, then prefix[j] - prefix[a] ≥ target too
  - And b gives shorter subarray!
  
**Observation 5:** Algorithm steps
1. Compute prefix sums
2. Maintain deque of indices with increasing prefix values
3. For each position j:
   - While prefix[j] - prefix[deque.front] ≥ target:
     - Update minimum length
     - Pop front (used up, won't be optimal for future positions)
   - While prefix[j] ≤ prefix[deque.back]:
     - Pop back (current j is better candidate)
   - Add j to deque

### **Problem Analysis:**
- Find **minimum length** subarray where sum ≥ target
- Array contains **negative integers** - sliding window won't work!
- Must still consider all contiguous subarrays

### **Why Sliding Window Fails:**

**Problem:** With negative integers, removing elements from left doesn't guarantee sum decreases!

Example: `nums = [5, -3, 2]`, `target = 4`
```
Window [5, -3, 2], sum = 4 ≥ target ✅
Remove left (5): [-3, 2], sum = -1 < target ❌

We INCREASED the window sum by removing an element!
This violates sliding window's monotonic property.
```

### **State Space Definition:**

**Reference:** See [All Contiguous Subarrays](../../state_spaces/all_contiguous_subarrays.md) for detailed mathematical derivation

- **Type**: All contiguous subarrays (includes single elements)
- **Structure**: Linear sequences `[start, end]` where `0 ≤ start ≤ end < n`
- **Cardinality**: n(n+1)/2 subarrays = **O(n²)**
- **Generation**: Need smarter approach than nested loops

### **Full State Space Example:**
For `nums = [2, -1, 2, 1]`, `target = 3`:

```
All subarrays with sums:
[2]→2, [2,-1]→1, [2,-1,2]→3 ✅, [2,-1,2,1]→4 ✅
[-1]→-1, [-1,2]→1, [-1,2,1]→2
[2]→2, [2,1]→3 ✅
[1]→1

Valid candidates (sum ≥ 3): 
- [2,-1,2] length 3
- [2,-1,2,1] length 4
- [2,1] length 2 ← minimum!
```

---

## **Step 2: Brute Force with Trace**

### **Brute Force Approach:**
Check all O(n²) subarrays:

```csharp
public int MinSubArrayLen_BruteForce(int target, int[] nums)
{
    int n = nums.Length;
    int minLen = int.MaxValue;
    
    // Try all starting positions
    for (int start = 0; start < n; start++)
    {
        int sum = 0;
        
        // Try all ending positions from start
        for (int end = start; end < n; end++)
        {
            sum += nums[end];
            
            // Check if sum meets target
            if (sum >= target)
            {
                minLen = Math.Min(minLen, end - start + 1);
            }
        }
    }
    
    return minLen == int.MaxValue ? 0 : minLen;
}
```

### **Value Trace:**
For `nums = [2, -1, 2, 1]`, `target = 3`:

| start | end | subarray | sum | sum≥3? | length | minLen |
|-------|-----|----------|-----|---------|--------|--------|
| 0 | 0 | [2] | 2 | No | - | ∞ |
| 0 | 1 | [2,-1] | 1 | No | - | ∞ |
| 0 | 2 | [2,-1,2] | 3 | Yes | 3 | 3 |
| 0 | 3 | [2,-1,2,1] | 4 | Yes | 4 | 3 |
| 1 | 1 | [-1] | -1 | No | - | 3 |
| 1 | 2 | [-1,2] | 1 | No | - | 3 |
| 1 | 3 | [-1,2,1] | 2 | No | - | 3 |
| 2 | 2 | [2] | 2 | No | - | 3 |
| 2 | 3 | [2,1] | 3 | Yes | 2 | **2** ✅ |
| 3 | 3 | [1] | 1 | No | - | 2 |

**Complexity:**
- **Time**: O(n²) - nested loops
- **Space**: O(1)

---

## **Step 3: Pruning Analysis - Can We Do Better?**

### **Key Observations:**

1. **Prefix Sum Transform:** `sum[i:j] = prefix[j] - prefix[i-1]`
2. **Reframe Problem:** For each position `j`, find smallest `i` where `prefix[j] - prefix[i] ≥ target`
3. **Equivalent:** Find largest `prefix[i] ≤ prefix[j] - target`
4. **Monotonic Deque Insight:** Maintain candidates for `prefix[i]` in increasing order

### **Why Monotonic Deque Works:**

For position `j`, we want: `prefix[j] - prefix[i] ≥ target`
→ `prefix[i] ≤ prefix[j] - target`

**Key insight:** If `prefix[a] ≥ prefix[b]` and `a < b`, then position `a` is **useless**!
- If `b` works, it gives shorter length (larger index)
- We only keep strictly increasing prefix values

**Deque maintains:** Indices with increasing prefix sums (candidates for optimal `i`)

### **Decision:** ✅ Use Prefix Sum + Monotonic Deque for O(n) solution!

---

## **Step 4: Optimal Solution with Skeleton Transformation**

### **Algorithm: Prefix Sum + Monotonic Deque**

```csharp
public int MinSubArrayLen_Optimal(int target, int[] nums)
{
    int n = nums.Length;
    int minLen = int.MaxValue;
    
    // Compute prefix sums
    long[] prefix = new long[n + 1];
    for (int i = 0; i < n; i++)
    {
        prefix[i + 1] = prefix[i] + nums[i];
    }
    
    // Deque stores indices with increasing prefix values
    var deque = new LinkedList<int>();
    deque.AddLast(0); // Start with prefix[0] = 0
    
    for (int j = 1; j <= n; j++)
    {
        // Remove indices where prefix[i] is too large
        // We want: prefix[j] - prefix[i] >= target
        // So: prefix[i] <= prefix[j] - target
        while (deque.Count > 0 && prefix[deque.First.Value] <= prefix[j] - target)
        {
            int i = deque.First.Value;
            minLen = Math.Min(minLen, j - i);
            deque.RemoveFirst(); // Can't be answer for future j's
        }
        
        // Maintain increasing order: remove larger prefix values
        // If prefix[back] >= prefix[j], back is useless (worse position, not smaller prefix)
        while (deque.Count > 0 && prefix[deque.Last.Value] >= prefix[j])
        {
            deque.RemoveLast();
        }
        
        deque.AddLast(j);
    }
    
    return minLen == int.MaxValue ? 0 : minLen;
}
```

### **Skeleton Transformation:**

```
BRUTE FORCE SKELETON:
├── for start = 0 to n:
│   ├── sum = 0
│   └── for end = start to n:
│       ├── sum += nums[end]
│       └── if sum >= target: update minLen
└── return minLen

                    ↓ TRANSFORM ↓

OPTIMAL SKELETON (Prefix Sum + Deque):
├── Compute prefix[0..n]
├── deque = [0] (indices with increasing prefix values)
├── for j = 1 to n:
│   ├── while prefix[deque.front] <= prefix[j] - target:
│   │   └── update minLen, remove front
│   ├── while prefix[deque.back] >= prefix[j]:
│   │   └── remove back (maintain increasing)
│   └── deque.add(j)
└── return minLen

KEY CHANGES:
- Nested loops → Single pass with deque
- Recompute sums → Prefix sum O(1) range query
- Check all pairs → Deque maintains only useful candidates
- O(n²) → O(n)
```

### **Value Trace:**
For `nums = [2, -1, 2, 1]`, `target = 3`:

**Prefix array:** `[0, 2, 1, 3, 4]`

| j | prefix[j] | deque (indices) | prefix values in deque | Action | minLen |
|---|-----------|----------------|----------------------|---------|--------|
| 0 | 0 | [0] | [0] | Initialize | ∞ |
| 1 | 2 | [0,1] | [0,2] | Add 1 (increasing) | ∞ |
| 2 | 1 | [0,2] | [0,1] | Remove 1 (≥1), Add 2 | ∞ |
| 3 | 3 | [2,3] | [1,3] | Check: 0≤3-3? Yes! len=3-0=3, Remove 0. Add 3 | 3 |
| 4 | 4 | [3,4] | [3,4] | Check: 1≤4-3? Yes! len=4-2=2, Remove 2. Add 4 | **2** ✅ |

**Why deque works:**
- At j=3: Check if prefix[0]=0 ≤ 3-3=0? Yes → subarray [0:3) has sum ≥ 3
- At j=4: Check if prefix[2]=1 ≤ 4-3=1? Yes → subarray [2:4) has sum ≥ 3, length 2!

---

## **Big-O Analysis**

**Brute Force:**
- **Time**: O(n²) - all subarrays
- **Space**: O(1)

**Optimal (Prefix Sum + Deque):**
- **Time**: O(n) - each index enters/exits deque once
- **Space**: O(n) - prefix array + deque

**Key Advantage:** O(n²) → O(n) time while handling negative integers!

---

## **Pattern Recognition**

**When to use Prefix Sum + Monotonic Deque:**
- ✅ Subarray sum problems with **negative integers**
- ✅ Need minimum/maximum window size
- ✅ Sliding window fails due to non-monotonic property
- ✅ Can transform to "find optimal prefix value"

**Why not Sliding Window?**
- Sliding window requires: removing left → sum changes predictably
- Negative integers break this (removing negative increases sum)
- Deque maintains only "useful" prefix positions

---

## **Comparison with Positive-Only Version**

| Aspect | Positive Integers | With Negatives |
|--------|-------------------|----------------|
| **Pattern** | Sliding Window | Prefix Sum + Deque |
| **Why it works** | Sum increases monotonically | Prefix values can decrease |
| **Time** | O(n) | O(n) |
| **Space** | O(1) | O(n) |
| **Complexity** | Easier | Harder (deque logic) |
| **LeetCode** | #209 standard | #209 follow-up |

**See also:** [Minimum Size Subarray Sum (Positive Only)](variant_5_min_size_subarray_sum.md) for the simpler sliding window approach.

---

## **Edge Cases**

1. **All negative:** May have no valid subarray if target > 0
2. **Single large element:** Works if element ≥ target
3. **Mix of large positives and negatives:** Deque handles efficiently
4. **Target = 0:** Any subarray with sum ≥ 0 qualifies

---

## **Related Problems**

- [Minimum Size Subarray Sum](variant_5_min_size_subarray_sum.md) - Positive integers version (simpler)
- **Shortest Subarray with Sum at Least K** (LeetCode #862) - Same technique
- **Sliding Window Maximum** (LeetCode #239) - Similar deque pattern
- **Longest Subarray Sum Equals K** - Related prefix sum problem

---

## **Tags:**
`prefix-sum` `monotonic-deque` `hard` `hybrid-pattern` `negative-integers` `O(n²)-state-space` `advanced`
