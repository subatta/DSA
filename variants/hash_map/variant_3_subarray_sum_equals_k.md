# Variant #3: Subarray Sum Equals K

**LeetCode #560 - Medium**

## Problem Statement
Given an array of integers `nums` and an integer `k`, return the total number of continuous subarrays whose sum equals `k`.

**Example:** `nums = [1,1,1]`, `k = 2` → `2` (subarrays: [1,1] at indices 0-1 and 1-2)

## 4-Step Analysis Framework

### Step 1: State Space Derivation

### **Problem Analysis:**
Count all contiguous subarrays whose sum equals k

### **State Space Definition:**

**Reference:** See [All Contiguous Subarrays](../../state_spaces/all_contiguous_subarrays.md) for detailed mathematical derivation

- **Type**: All contiguous subarrays (includes single elements)
- **Structure**: Linear sequences `[start, end]` where `0 ≤ start ≤ end < n`
- **Cardinality**: n(n+1)/2 subarrays = **O(n²)**
- **Generation**: Nested loops or prefix sum optimization

### **Problem Visualization:**
```
Input: nums = [1, 2, 3], k = 3

All subarrays:
[1]       sum=1  ✗
[1,2]     sum=3  ✓ Count = 1
[1,2,3]   sum=6  ✗
[2]       sum=2  ✗
[2,3]     sum=5  ✗
[3]       sum=3  ✓ Count = 2

Total count = 2

Brute force: Check all n(n+1)/2 subarrays → O(n²)

Prefix sum insight:
  prefix[0] = 0
  prefix[1] = 1
  prefix[2] = 1+2 = 3
  prefix[3] = 1+2+3 = 6

  Subarray sum [i, j] = prefix[j] - prefix[i-1]
  
  Want: prefix[j] - prefix[i-1] = k
  Rearrange: prefix[i-1] = prefix[j] - k
  
  Question: "How many times did we see (prefix[j] - k)?"
  
Hash map stores frequency of each prefix sum!

Example trace:
  i=0: prefix=0, add {0:1}
  i=1: prefix=1, check if (1-3=-2) in map? No. Add {0:1, 1:1}
  i=2: prefix=3, check if (3-3=0) in map? Yes! count=1. Add {0:1, 1:1, 3:1}
  i=3: prefix=6, check if (6-3=3) in map? Yes! count=2. Add {0:1, 1:1, 3:1, 6:1}
```

### **Core Question:**
How to count subarrays without checking all O(n²) possibilities?

### **Deriving from First Principles:**

**Observation 1:** Subarray sum formula
- sum(nums[i...j]) = prefix[j] - prefix[i-1]
- Where prefix[j] = sum(nums[0...j])

**Observation 2:** Transform the equation
- Want: prefix[j] - prefix[i-1] = k
- Rearrange: prefix[i-1] = prefix[j] - k
- When processing position j:
  - We know prefix[j]
  - Need to count: how many previous positions had prefix = (prefix[j] - k)?

**Observation 3:** Hash map tracks frequencies
- Store: {prefix_sum → count of occurrences}
- For each position j:
  1. Look up frequency of (prefix[j] - k)
  2. Add that frequency to answer
  3. Update frequency of prefix[j]

**Observation 4:** Why this works with negatives
- Unlike sliding window, this works with negative numbers!
- Sliding window fails: removing element might INCREASE sum
- Prefix sum + hash map: correctly handles any integer values

**Observation 5:** Initialize with {0: 1}
- Handles subarrays starting at index 0
- Example: nums=[3], k=3
- prefix[0] = 3, check (3-3=0), need {0:1} to count this

### **State Space Structure:**

**Type:** Count all contiguous subarrays with specific sum  
**Structure:** All possible subarrays [start, end]  
**Cardinality:** n(n+1)/2 subarrays

**State Space Example:**
For `nums = [1, 1, 1]`, `k = 2`:

```
All subarrays:
[1] sum=1 ❌
[1,1] sum=2 ✅
[1,1,1] sum=3 ❌
[1] sum=1 ❌
[1,1] sum=2 ✅
[1] sum=1 ❌

Valid count: 2
```

---

### Step 2: Brute Force with Value Tracing

**Approach:** Check all O(n²) subarrays

```csharp
public int SubarraySum_BruteForce(int[] nums, int k)
{
    int count = 0;
    int n = nums.Length;
    
    for (int start = 0; start < n; start++)
    {
        int sum = 0;
        for (int end = start; end < n; end++)
        {
            sum += nums[end];
            if (sum == k)
            {
                count++;
            }
        }
    }
    
    return count;
}
```

**Value Trace for:** `nums = [1, 1, 1]`, `k = 2`

| start | end | subarray | sum | sum==k? | count |
|-------|-----|----------|-----|---------|-------|
| 0 | 0 | [1] | 1 | No | 0 |
| 0 | 1 | [1,1] | 2 | Yes | 1 |
| 0 | 2 | [1,1,1] | 3 | No | 1 |
| 1 | 1 | [1] | 1 | No | 1 |
| 1 | 2 | [1,1] | 2 | Yes | 2 |
| 2 | 2 | [1] | 1 | No | 2 |

**Complexity:**
- **Time:** O(n²)
- **Space:** O(1)

---

### Step 3: Pruning Analysis - Can We Do Better?

**Key Observations:**

1. **Prefix Sum Transform:** `sum[i:j] = prefix[j] - prefix[i-1]`
2. **Reframe:** For each position j, count how many positions i have: `prefix[j] - prefix[i] = k`
3. **Rearrange:** `prefix[i] = prefix[j] - k`
4. **Hash Map:** Count occurrences of each prefix sum!

**Mathematical Insight:**
```
If sum[i:j] = k, then:
prefix[j] - prefix[i-1] = k
prefix[i-1] = prefix[j] - k

So count how many times (prefix[j] - k) appeared before position j!
```

**Decision:** ✅ Use Prefix Sum + Hash Map to count in O(n)!

---

### Step 4: Optimal Solution with Skeleton Transformation

```csharp
public int SubarraySum_Optimal(int[] nums, int k)
{
    var prefixCount = new Dictionary<int, int>();
    prefixCount[0] = 1; // Empty prefix sum = 0
    
    int prefixSum = 0;
    int count = 0;
    
    foreach (int num in nums)
    {
        prefixSum += num;
        
        // Check if (prefixSum - k) exists
        int target = prefixSum - k;
        if (prefixCount.ContainsKey(target))
        {
            count += prefixCount[target];
        }
        
        // Store current prefix sum
        if (!prefixCount.ContainsKey(prefixSum))
        {
            prefixCount[prefixSum] = 0;
        }
        prefixCount[prefixSum]++;
    }
    
    return count;
}
```

**Skeleton Transformation:**
```
BRUTE FORCE:
├── for start:
│   ├── sum = 0
│   └── for end from start:
│       ├── sum += nums[end]
│       └── if sum == k: count++

         ↓

OPTIMAL (Prefix Sum + Hash Map):
├── prefixCount = {0: 1}
├── prefixSum = 0
├── for num in nums:
│   ├── prefixSum += num
│   ├── if (prefixSum - k) in prefixCount:
│   │   └── count += prefixCount[prefixSum - k]
│   └── prefixCount[prefixSum]++
└── return count

KEY CHANGES:
- Nested loops → Single pass
- Recompute sums → Cumulative prefix sum
- Check all pairs → Hash map lookup
- O(n²) → O(n)
```

**Value Trace for:** `nums = [1, 1, 1]`, `k = 2`

| Step | num | prefixSum | target=prefixSum-k | prefixCount before | Found? | count | prefixCount after |
|------|-----|-----------|-------------------|-------------------|---------|-------|-------------------|
| Init | - | 0 | - | {0:1} | - | 0 | {0:1} |
| 1 | 1 | 1 | 1-2=-1 | {0:1} | No | 0 | {0:1, 1:1} |
| 2 | 1 | 2 | 2-2=0 | {0:1, 1:1} | Yes! count=1 | 1 | {0:1, 1:1, 2:1} |
| 3 | 1 | 3 | 3-2=1 | {0:1, 1:1, 2:1} | Yes! count=1 | 2 | {0:1, 1:1, 2:1, 3:1} |

**Why it works:**
- At step 2: prefixSum=2, target=0. Found 0 once → subarray [0:2) has sum 2
- At step 3: prefixSum=3, target=1. Found 1 once → subarray [1:3) has sum 2

---

## Big-O Analysis

**Brute Force:**
- **Time:** O(n²)
- **Space:** O(1)

**Optimal:**
- **Time:** O(n) - single pass
- **Space:** O(n) - hash map stores prefix sums

**Key Advantage:** O(n²) → O(n) via prefix sum + hash map technique!

---

## Pattern Recognition

**When to use Prefix Sum + Hash Map:**
- ✅ Subarray sum equals target
- ✅ Count subarrays (not just find one)
- ✅ Need O(n) solution
- ✅ Can work with negative numbers

**Why Hash Map?**
- Stores frequency of each prefix sum
- O(1) lookup to find matching prefix
- Handles multiple subarrays with same sum

---

## Edge Cases

1. **k = 0:** Count subarrays with sum 0
2. **Negative numbers:** Works fine (unlike sliding window)
3. **All elements sum to k:** Count includes whole array
4. **No subarrays:** Returns 0

---

## Related Problems

- **Subarray Sum Divisible by K** (LeetCode #974) - Similar prefix sum + hash map
- **Continuous Subarray Sum** (LeetCode #523) - Multiple of k
- **Contiguous Array** (LeetCode #525) - Equal 0s and 1s (transform to sum=0)
- [Minimum Size Subarray Sum](../sliding_window/variant_5_min_size_subarray_sum.md) - Different goal (minimum length)

---

## Tags
`hash-map` `prefix-sum` `array` `counting` `medium` `negative-numbers-ok`
