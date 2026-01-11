# Variant #4: Longest Consecutive Sequence

**LeetCode #128 - Medium**

## Problem Statement
Given an unsorted array of integers `nums`, return the length of the longest consecutive elements sequence.

You must write an algorithm that runs in **O(n)** time.

**Example:** `nums = [100, 4, 200, 1, 3, 2]` → `4` (sequence: [1, 2, 3, 4])

## 4-Step Analysis Framework

### Step 1: State Space Derivation

### **Problem Visualization:**
```
Input: nums = [100, 4, 200, 1, 3, 2]

Unsorted array visualized:
[100] [4] [200] [1] [3] [2]

After sorting (mental model):
[1, 2, 3, 4, 100, 200]
 └───────┘  └─┘ └──┘
 consecutive  single single
 length=4

But sorting takes O(n log n)... can we do O(n)?

Hash set insight: O(1) existence checks
  Put all numbers in set: {1, 2, 3, 4, 100, 200}
  
  For each number, ask: "Am I the START of a sequence?"
  - 1: Is (1-1=0) in set? No → I'm a start!
  - 2: Is (2-1=1) in set? Yes → Skip (not a start)
  - 3: Is (3-1=2) in set? Yes → Skip
  - 4: Is (4-1=3) in set? Yes → Skip
  - 100: Is (100-1=99) in set? No → I'm a start!
  - 200: Is (200-1=199) in set? No → I'm a start!
  
  For each start, count how long the sequence goes:
  - Start at 1: 1→2→3→4 (length 4)
  - Start at 100: 100 (length 1)
  - Start at 200: 200 (length 1)

Smart iteration: Only process O(n) starts, not O(n²) pairs!
```

### **Core Question:**
How to find consecutive sequences without sorting?

### **Deriving from First Principles:**

**Observation 1:** Sorting approach (baseline)
- Sort array: O(n log n)
- Scan for consecutive runs: O(n)
- Total: O(n log n)
- But problem requires O(n)!

**Observation 2:** What makes numbers consecutive?
- Consecutive: n, n+1, n+2, ...
- For any number x, its neighbors are x-1 and x+1
- Need fast way to check "Is x±1 in array?"

**Observation 3:** Hash set for O(1) lookups
- Put all numbers in hash set
- For any number, check existence of neighbors in O(1)
- Can build sequences by checking n, n+1, n+2, ... until miss

**Observation 4:** Avoid redundant work
- Naive: For each number, try to build sequence → O(n²)
- Example: For 2, build 2→3→4. For 3, build 3→4. For 4, build 4.
  - Lots of redundant work!
- **Smart insight:** Only start sequences where (n-1) NOT in set!
  - 1: start (0 not in set)
  - 2: skip (1 in set)
  - 3: skip (2 in set)
  - 4: skip (3 in set)
  - Only process each element as part of ONE sequence!

**Observation 5:** Time complexity analysis
- Build hash set: O(n)
- Check each number if it's a start: O(n)
- For starts, extend sequence: Each number visited at most twice
  1. Once to check if it's a start
  2. Once as part of a sequence from an actual start
- Total: O(n)

### **State Space Structure:**

**Type:** Find longest consecutive integer sequence  
**Structure:** All possible consecutive sequences in the array  
**Cardinality:** Up to n sequences (each starting point)

**State Space Example:**
For `nums = [100, 4, 200, 1, 3, 2]`:

```
Sorted view: [1, 2, 3, 4, 100, 200]

Consecutive sequences:
- Starting at 1: [1, 2, 3, 4] length=4 ✅
- Starting at 100: [100] length=1
- Starting at 200: [200] length=1

Longest: 4
```

---

### Step 2: Brute Force with Value Tracing

**Approach:** Sort array, then scan for consecutive sequences

```csharp
public int LongestConsecutive_BruteForce(int[] nums)
{
    if (nums.Length == 0) return 0;
    
    Array.Sort(nums);
    
    int maxLen = 1;
    int currentLen = 1;
    
    for (int i = 1; i < nums.Length; i++)
    {
        if (nums[i] == nums[i - 1]) continue; // Duplicate
        
        if (nums[i] == nums[i - 1] + 1)
        {
            currentLen++;
        }
        else
        {
            maxLen = Math.Max(maxLen, currentLen);
            currentLen = 1;
        }
    }
    
    return Math.Max(maxLen, currentLen);
}
```

**Value Trace for:** `[100, 4, 200, 1, 3, 2]`

| Step | sorted | i | nums[i] | nums[i-1] | Consecutive? | currentLen | maxLen |
|------|--------|---|---------|-----------|--------------|------------|--------|
| Sort | [1,2,3,4,100,200] | - | - | - | - | 1 | 1 |
| 1 | - | 1 | 2 | 1 | Yes | 2 | 1 |
| 2 | - | 2 | 3 | 2 | Yes | 3 | 1 |
| 3 | - | 3 | 4 | 3 | Yes | 4 | 1 |
| 4 | - | 4 | 100 | 4 | No | 1 | 4 |
| 5 | - | 5 | 200 | 100 | No | 1 | 4 |

**Complexity:**
- **Time:** O(n log n) - dominated by sorting
- **Space:** O(1) or O(n) depending on sort

---

### Step 3: Pruning Analysis - Can We Do Better?

**Key Observations:**

1. **Sorting bottleneck:** O(n log n) time
2. **What we really need:** Fast "does X exist?" queries
3. **Hash Set:** O(1) existence check!
4. **Smart iteration:** Only start sequences at "beginning" numbers

**Key Insight:**
```
For each number X:
  If (X-1) doesn't exist → X is sequence start
  If (X-1) exists → X is middle of sequence, skip!
  
This ensures we count each sequence once!
```

**Decision:** ✅ Use Hash Set for O(1) lookups + smart sequence starting!

---

### Step 4: Optimal Solution with Skeleton Transformation

```csharp
public int LongestConsecutive_Optimal(int[] nums)
{
    if (nums.Length == 0) return 0;
    
    var numSet = new HashSet<int>(nums);
    int maxLen = 0;
    
    foreach (int num in numSet)
    {
        // Only start sequence if num is the beginning
        if (!numSet.Contains(num - 1))
        {
            int currentNum = num;
            int currentLen = 1;
            
            // Count consecutive numbers
            while (numSet.Contains(currentNum + 1))
            {
                currentNum++;
                currentLen++;
            }
            
            maxLen = Math.Max(maxLen, currentLen);
        }
    }
    
    return maxLen;
}
```

**Skeleton Transformation:**
```
BRUTE FORCE:
├── Sort array: O(n log n)
├── Scan for consecutive: O(n)
└── return maxLen

         ↓

OPTIMAL (Hash Set):
├── numSet = HashSet(nums)
├── for num in numSet:
│   ├── if (num-1) not in set:  // Sequence start only
│   │   ├── currentLen = 1
│   │   └── while (num+1) in set:
│   │       └── currentLen++, num++
│   └── maxLen = max(maxLen, currentLen)
└── return maxLen

KEY CHANGES:
- Sort O(n log n) → Hash set O(n)
- Sequential scan → Smart sequence detection
- Check consecutive → O(1) existence checks
```

**Value Trace for:** `[100, 4, 200, 1, 3, 2]`

| num | num-1 in set? | Start sequence? | Consecutive numbers | Length | maxLen |
|-----|---------------|-----------------|---------------------|--------|--------|
| 100 | No | Yes | 100 (101 absent) | 1 | 1 |
| 4 | Yes (3 in set) | No | Skip | - | 1 |
| 200 | No | Yes | 200 (201 absent) | 1 | 1 |
| 1 | No | Yes | 1,2,3,4 (5 absent) | 4 | 4 |
| 3 | Yes (2 in set) | No | Skip | - | 4 |
| 2 | Yes (1 in set) | No | Skip | - | 4 |

**Why O(n)?**
- Each number visited at most twice (once as potential start, once during sequence building)
- All hash set operations are O(1)

---

## Big-O Analysis

**Brute Force:**
- **Time:** O(n log n) - sorting
- **Space:** O(1)

**Optimal:**
- **Time:** O(n) - hash set construction + clever iteration
- **Space:** O(n) - hash set

**Key Advantage:** O(n log n) → O(n) by avoiding sort!

---

## Pattern Recognition

**When to use Hash Set:**
- ✅ Need fast "contains" checks
- ✅ Order doesn't matter
- ✅ Can afford O(n) space
- ✅ Want to avoid sorting

**Smart Iteration Pattern:**
- Only process "start" elements
- Skip elements that are part of existing sequences
- Ensures each element counted once

---

## Edge Cases

1. **Empty array:** Returns 0
2. **Single element:** Returns 1
3. **All consecutive:** Returns n
4. **Duplicates:** `[1,2,0,1]` → 3 (sequence [0,1,2])
5. **Negative numbers:** Works fine

---

## Related Problems

- **Binary Tree Longest Consecutive Sequence** (LeetCode #298) - Tree variant
- **Consecutive Numbers Sum** (LeetCode #829) - Different problem
- **Longest Arithmetic Subsequence** (LeetCode #1027) - Similar idea, different constraint

---

## Tags
`hash-set` `array` `sequence` `O(n)-time` `medium` `clever-iteration`