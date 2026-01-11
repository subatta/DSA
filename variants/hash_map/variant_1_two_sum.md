# Variant #1: Two Sum (Unsorted)

**LeetCode #1 - Easy**

## Problem Statement
Given an array of integers `nums` and an integer `target`, return indices of the two numbers such that they add up to `target`.

You may assume that each input would have **exactly one solution**, and you may not use the same element twice.

**Example:** `nums = [2,7,11,15]`, `target = 9` → `[0,1]` (2 + 7 = 9)

## 4-Step Analysis Framework

### Step 1: State Space Derivation

### **Problem Visualization:**
```
Input: nums = [2, 7, 11, 15], target = 9

Goal: Find indices i, j where nums[i] + nums[j] = 9

All pairs to check:
  nums[0] + nums[1] = 2 + 7  = 9  ✓ Found!
  nums[0] + nums[2] = 2 + 11 = 13
  nums[0] + nums[3] = 2 + 15 = 17
  nums[1] + nums[2] = 7 + 11 = 18
  nums[1] + nums[3] = 7 + 15 = 22
  nums[2] + nums[3] = 11 + 15 = 26

Brute force: Check all 6 pairs → O(n²)

Optimized thinking:
  When processing nums[0] = 2:
    Need complement: 9 - 2 = 7
    Is 7 in array? → O(n) linear search
    
  Better: Use hash map for O(1) lookup
    When processing 2: Check if 7 exists in map
    Store: {value → index} for O(1) complement search
```

### **Core Question:**
Can we avoid checking all O(n²) pairs?

### **Deriving from First Principles:**

**Observation 1:** Equation transformation
- Given: a + b = target
- Rearrange: b = target - a
- New perspective: "Does (target - a) exist in array?"

**Observation 2:** Lookup speed matters
- Nested loops: For each element a, scan remaining array for b → O(n²)
- Sort + binary search: For each a, binary search for b → O(n log n)
- Hash map: For each a, O(1) lookup for b → O(n)

**Observation 3:** Single-pass solution
- Build hash map while scanning array
- At position i processing nums[i]:
  1. Check if (target - nums[i]) already in map → found pair!
  2. Otherwise, add nums[i] to map for future lookups
- Why this works: if answer is (i, j) where i < j, when we reach j we'll find i in the map

### **State Space Structure:**

**Type:** Find two distinct indices where values sum to target  
**Structure:** All possible pairs of indices (i, j) where i < j  
**Cardinality:** n(n-1)/2 pairs (requires two different elements)

**State Space Example:**
For `nums = [2, 7, 11, 15]`, `target = 9`:

```
All pairs:
(0,1): 2+7=9 ✅
(0,2): 2+11=13
(0,3): 2+15=17
(1,2): 7+11=18
(1,3): 7+15=22
(2,3): 11+15=26

Total: 4×3/2 = 6 pairs
```

**Generation Pattern:**
```csharp
void GenerateAllPairs(int[] nums)
{
    for (int i = 0; i < nums.Length; i++)
    {
        for (int j = i + 1; j < nums.Length; j++)
        {
            // Check if nums[i] + nums[j] == target
        }
    }
}
```

---

### Step 2: Brute Force with Value Tracing

**Approach:** Check all pairs with nested loops

```csharp
public int[] TwoSum_BruteForce(int[] nums, int target)
{
    int n = nums.Length;
    
    // Try all pairs
    for (int i = 0; i < n; i++)
    {
        for (int j = i + 1; j < n; j++)
        {
            if (nums[i] + nums[j] == target)
            {
                return new int[] { i, j };
            }
        }
    }
    
    return new int[] { -1, -1 }; // Not found
}
```

**Value Trace for:** `nums = [2, 7, 11, 15]`, `target = 9`

| i | j | nums[i] | nums[j] | sum | target? | Action |
|---|---|---------|---------|-----|---------|--------|
| 0 | 1 | 2 | 7 | 9 | ✅ | **Return [0,1]** |

**Value Trace for:** `nums = [3, 2, 4]`, `target = 6`

| i | j | nums[i] | nums[j] | sum | target? | Action |
|---|---|---------|---------|-----|---------|--------|
| 0 | 1 | 3 | 2 | 5 | ❌ | Continue |
| 0 | 2 | 3 | 4 | 7 | ❌ | Continue |
| 1 | 2 | 2 | 4 | 6 | ✅ | **Return [1,2]** |

**Complexity:**
- **Time:** O(n²) - nested loops check all pairs
- **Space:** O(1)

---

### Step 3: Pruning Analysis - Can We Do Better?

**Key Observations:**

1. **Equation transformation:** `nums[i] + nums[j] = target` → `nums[j] = target - nums[i]`
2. **Lookup problem:** For each `nums[i]`, need to check if `complement = target - nums[i]` exists
3. **Naive lookup:** Linear search through remaining elements = O(n)
4. **Better lookup:** Hash map provides O(1) lookup!

**Strategy:**
```
For each number x in array:
  - Calculate complement = target - x
  - If complement already seen: found pair!
  - Otherwise: store x with its index for future lookups
```

**Why Hash Map?**
- **Goal:** Fast "have we seen this value?" check
- **Brute force:** O(n) per lookup via linear scan
- **Hash Map:** O(1) average per lookup
- **Trade-off:** Use O(n) space to gain O(n) time

**Decision:** ✅ Use Hash Map to eliminate inner loop!

---

### Step 4: Optimal Solution with Skeleton Transformation

**Algorithm: Single-Pass Hash Map**

```csharp
public int[] TwoSum_Optimal(int[] nums, int target)
{
    // Map: value -> index
    var map = new Dictionary<int, int>();
    
    for (int i = 0; i < nums.Length; i++)
    {
        int complement = target - nums[i];
        
        // Check if complement exists in map
        if (map.ContainsKey(complement))
        {
            return new int[] { map[complement], i };
        }
        
        // Store current number with its index
        map[nums[i]] = i;
    }
    
    return new int[] { -1, -1 }; // Not found
}
```

**Skeleton Transformation:**

```
BRUTE FORCE SKELETON:
├── for i = 0 to n-1:
│   └── for j = i+1 to n-1:
│       └── if nums[i] + nums[j] == target:
│           └── return [i, j]
└── return [-1, -1]

                    ↓ TRANSFORM ↓

OPTIMAL SKELETON (Hash Map):
├── map = {}
├── for i = 0 to n-1:
│   ├── complement = target - nums[i]
│   ├── if complement in map:
│   │   └── return [map[complement], i]
│   └── map[nums[i]] = i
└── return [-1, -1]

KEY CHANGES:
- Nested loop → Single loop
- Inner O(n) search → O(1) hash map lookup
- Check all pairs → Check complement only
- O(n²) → O(n)
```

**Value Trace for:** `nums = [3, 2, 4]`, `target = 6`

| i | nums[i] | complement | map before | Found? | Action | map after |
|---|---------|------------|------------|--------|--------|-----------|
| 0 | 3 | 6-3=3 | {} | No | Store 3→0 | {3:0} |
| 1 | 2 | 6-2=4 | {3:0} | No | Store 2→1 | {3:0, 2:1} |
| 2 | 4 | 6-4=2 | {3:0, 2:1} | Yes! 2 at index 1 | **Return [1,2]** ✅ | - |

**Value Trace for:** `nums = [2, 7, 11, 15]`, `target = 9`

| i | nums[i] | complement | map before | Found? | Action |
|---|---------|------------|------------|--------|--------|
| 0 | 2 | 9-2=7 | {} | No | Store 2→0 |
| 1 | 7 | 9-7=2 | {2:0} | Yes! 2 at index 0 | **Return [0,1]** ✅ |

---

## Big-O Analysis

**Brute Force:**
- **Time:** O(n²) - all pairs checked
- **Space:** O(1)

**Optimal (Hash Map):**
- **Time:** O(n) - single pass through array
- **Space:** O(n) - hash map stores up to n elements

**Key Advantage:** O(n²) → O(n) time via space-time tradeoff!

---

## Pattern Recognition

**When to use Hash Map:**
- ✅ Need fast lookup of "have we seen X?"
- ✅ Can afford O(n) extra space
- ✅ Values need to be found/counted quickly
- ✅ Transform equation to lookup problem

**Hash Map Operations:**
- `ContainsKey(x)`: O(1) - check existence
- `map[x] = y`: O(1) - store value
- `map[x]`: O(1) - retrieve value

**Common Transformations:**
- `a + b = target` → "Is (target - a) in map?"
- `a - b = diff` → "Is (a - diff) in map?"
- `count(x)` → "map[x]++"

---

## Edge Cases

1. **Duplicate values:** `[3, 3]`, `target = 6` → Works (different indices)
2. **No solution:** Should not happen per problem constraint
3. **Negative numbers:** Works fine (hash map handles any integer)
4. **Zero:** `[0, 4, 3, 0]`, `target = 0` → Returns indices of two zeros
5. **Single pair only:** Problem guarantees exactly one solution

---

## Comparison with Two Sum II (Sorted)

| Aspect | Two Sum (Unsorted) | Two Sum II (Sorted) |
|--------|-------------------|---------------------|
| **Input** | Unsorted array | Sorted array |
| **Pattern** | Hash Map | Two Pointers |
| **Time** | O(n) | O(n) |
| **Space** | O(n) | O(1) |
| **When to use** | Unsorted data | Sorted data |

**See also:** [Two Sum II](../two_pointers/variant_1_two_sum_sorted.md) for the sorted version using two pointers.

---

## Related Problems

- **Two Sum II** (LeetCode #167) - Sorted array version with two pointers
- **3Sum** (LeetCode #15) - Find triplets, uses sorting + two pointers
- **4Sum** (LeetCode #18) - Find quadruplets
- **Two Sum IV - BST** (LeetCode #653) - Two sum in binary search tree
- **Subarray Sum Equals K** (LeetCode #560) - Prefix sum + hash map

---

## Tags
`hash-map` `array` `O(1)-lookup` `space-time-tradeoff` `easy` `classic-interview`
