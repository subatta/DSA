# Variant #4: Continuous Subarray Sum

**LeetCode #523 - Medium**

## Problem Statement
Given an integer array `nums` and an integer `k`, return `true` if `nums` has a continuous subarray of size **at least two** whose elements sum up to a multiple of `k`, or `false` otherwise.

An integer `x` is a multiple of `k` if there exists an integer `n` such that `x = n × k`. Note that `0` is always a multiple of `k`.

**Example:** `nums = [23,2,4,6,7]`, `k = 6` → `true` (subarray [2,4] sums to 6)

## 4-Step Analysis Framework

### Step 1: State Space Derivation

### **Problem Visualization:**
```
Input: nums = [23, 2, 4, 6, 7], k = 6

Goal: Find subarray of length ≥ 2 with sum divisible by 6

All subarrays with their sums:
[23,2]      = 25 % 6 = 1  ✗
[23,2,4]    = 29 % 6 = 5  ✗
[23,2,4,6]  = 35 % 6 = 5  ✗
[23,2,4,6,7]= 42 % 6 = 0  ✓ Found one!
[2,4]       = 6  % 6 = 0  ✓ Another (shorter)!
[2,4,6]     = 12 % 6 = 0  ✓
[2,4,6,7]   = 18 % 6 = 0  ✓
[4,6]       = 10 % 6 = 4  ✗
...

Brute force: Check all O(n²) subarrays

Prefix sum + modulo insight:
  prefix[0] = 0
  prefix[1] = 23 % 6 = 5
  prefix[2] = 25 % 6 = 1
  prefix[3] = 29 % 6 = 5  ← Same remainder as prefix[1]!
  prefix[4] = 35 % 6 = 5
  prefix[5] = 42 % 6 = 0

Key observation:
  If prefix[j] % k == prefix[i] % k, then:
    (prefix[j] - prefix[i]) % k == 0
    
This means subarray [i+1, j] has sum divisible by k!

At prefix[3]: remainder=5 seen before at prefix[1]
  Subarray [2,3] = nums[2] + nums[3] = 2+4 = 6 ✓
  Distance = 3-1 = 2 ≥ 2 ✓
```

### **Core Question:**
How to find if any subarray (length ≥ 2) sums to multiple of k?

### **Deriving from First Principles:**

**Observation 1:** Modulo arithmetic
```
A subarray sum is divisible by k if:
  sum % k == 0

For subarray [i, j]:
  sum = prefix[j] - prefix[i-1]
  
  We want: (prefix[j] - prefix[i-1]) % k == 0
  
  This happens when:
    prefix[j] % k == prefix[i-1] % k
```

**Observation 2:** Why same remainders work
```
If prefix[a] % k == r and prefix[b] % k == r:
  prefix[a] = n₁×k + r
  prefix[b] = n₂×k + r
  
  prefix[b] - prefix[a] = (n₂×k + r) - (n₁×k + r)
                        = (n₂ - n₁)×k
                        = multiple of k ✓
```

**Observation 3:** Hash map tracks remainders
- Store: {remainder → earliest index with that remainder}
- When we see remainder r at index j:
  - If r seen before at index i:
    - Subarray [i+1, j] has sum divisible by k
    - Check length: j - i ≥ 2?
  - If r not seen: store {r: j}

**Observation 4:** Special case: remainder 0
- If prefix[j] % k == 0, means prefix itself is divisible
- This is a valid subarray from index 0 to j
- Need j ≥ 1 for length ≥ 2 constraint
- Initialize map with {0: -1} to handle this

**Observation 5:** Why store earliest index?
- We want longest possible subarray (or just any valid one)
- To maximize length, keep earliest occurrence
- Example: remainder 5 at indices 1, 3, 4
  - Keep index 1
  - At index 4: distance = 4 - 1 = 3 ≥ 2 ✓

### **State Space Structure:**

**Type:** Check if any subarray (length ≥ 2) has sum ≡ 0 (mod k)  
**Structure:** All possible subarrays [i, j] where j - i + 1 ≥ 2  
**Cardinality:** O(n²) subarrays, but we use O(n) with hash map  
**Key insight:** Track prefix sum remainders, not actual sums

**Example Computation:**
For `nums = [23, 2, 4, 6, 7]`, `k = 6`:

```
Build prefix remainders:
map = {0: -1}  (initialize for prefix[0])

i=0: prefix=23, remainder=23%6=5
     5 not in map, add {0:-1, 5:0}

i=1: prefix=25, remainder=25%6=1
     1 not in map, add {0:-1, 5:0, 1:1}

i=2: prefix=29, remainder=29%6=5
     5 in map at index 0!
     distance = 2-0 = 2 ≥ 2 ✓ return true

(Found: subarray [1,2] = [2,4] sums to 6)
```

### **Generation Pattern (Naive):**
```csharp
bool CheckSubarraySumNaive(int[] nums, int k)
{
    int n = nums.Length;
    
    // Try all subarrays of length ≥ 2
    for (int i = 0; i < n; i++)
    {
        int sum = 0;
        for (int j = i; j < n; j++)
        {
            sum += nums[j];
            int length = j - i + 1;
            
            if (length >= 2 && sum % k == 0)
                return true;
        }
    }
    
    return false;
}
// Time: O(n²), Space: O(1)
```

---

### Step 2: Brute Force with Value Tracing

**Approach:** Check all subarrays of length ≥ 2

**Value Trace for:** `nums = [23, 2, 4, 6, 7]`, `k = 6`

| Start | End | Subarray | Sum | Sum % 6 | Length ≥ 2? | Valid? |
|-------|-----|----------|-----|---------|-------------|--------|
| 0 | 1 | [23,2] | 25 | 1 | ✓ | ✗ |
| 0 | 2 | [23,2,4] | 29 | 5 | ✓ | ✗ |
| 0 | 3 | [23,2,4,6] | 35 | 5 | ✓ | ✗ |
| 0 | 4 | [23,2,4,6,7] | 42 | 0 | ✓ | ✓ |
| 1 | 2 | [2,4] | 6 | 0 | ✓ | ✓ |

Return: true (found at [1,2])

**Complexity:**
- Time: O(n²) - nested loops to check all subarrays
- Space: O(1)

---

### Step 3: Pruning Analysis - Can We Do Better?

**Key Insight:** Use prefix sum remainders with hash map

**Why it works:**
```
If two prefix sums have same remainder mod k:
  prefix[j] % k == prefix[i] % k
  
Then:
  (prefix[j] - prefix[i]) % k == 0
  
Which means subarray [i+1, j] has sum divisible by k!

We only need to track remainders, not actual sums.
Hash map gives O(1) lookup.
```

**Modulo properties:**
```
(a + b) % k = ((a % k) + (b % k)) % k
(a - b) % k = ((a % k) - (b % k) + k) % k  (add k to handle negatives)
```

**Transformation:** O(n²) → O(n)
- One pass with running prefix sum
- Hash map tracks first occurrence of each remainder
- O(n) time, O(min(n, k)) space (at most k different remainders)

---

### Step 4: Optimal Solution

**Skeleton Transformation:**

```csharp
// BEFORE: Naive O(n²) approach
bool CheckSubarraySum_BruteForce(int[] nums, int k)
{
    for (int i = 0; i < nums.Length; i++)
    {
        int sum = 0;
        for (int j = i; j < nums.Length; j++)  // Nested loop
        {
            sum += nums[j];
            if (j - i + 1 >= 2 && sum % k == 0)
                return true;
        }
    }
    return false;
}

// AFTER: Prefix sum remainders with hash map O(n)
bool CheckSubarraySum(int[] nums, int k)
{
    // Map: {remainder -> earliest index}
    var remainderMap = new Dictionary<int, int>();
    remainderMap[0] = -1;  // For prefix starting at index 0
    
    int prefixSum = 0;
    
    for (int i = 0; i < nums.Length; i++)
    {
        prefixSum += nums[i];
        int remainder = prefixSum % k;
        
        // Handle negative remainders
        if (remainder < 0)
            remainder += k;
        
        if (remainderMap.ContainsKey(remainder))
        {
            // Found same remainder before
            int prevIndex = remainderMap[remainder];
            if (i - prevIndex >= 2)  // Check length constraint
                return true;
        }
        else
        {
            // First time seeing this remainder
            remainderMap[remainder] = i;
        }
    }
    
    return false;
}
```

**Complexity:**
- **Time:** O(n) - single pass through array
- **Space:** O(min(n, k)) - at most k different remainders

**Execution Trace for [23,2,4,6,7], k=6:**

```
map = {0: -1}

i=0: prefixSum=23, remainder=5
     5 not in map, add {0:-1, 5:0}

i=1: prefixSum=25, remainder=1
     1 not in map, add {0:-1, 5:0, 1:1}

i=2: prefixSum=29, remainder=5
     5 in map at index 0
     distance = 2-0 = 2 ≥ 2 ✓ return true
```

**Why initialize with {0: -1}?**
```
Handles case where prefix sum itself is divisible by k.

Example: nums=[2,4], k=6
  i=0: prefix=2, remainder=2, add {0:-1, 2:0}
  i=1: prefix=6, remainder=0
       0 in map at index -1
       distance = 1-(-1) = 2 ≥ 2 ✓
       
This correctly identifies [2,4] as valid subarray!
```

---

## Edge Cases

1. **Length constraint:** `[5,1]`, `k=6` → `true`
   - sum=6, length=2 ✓

2. **Consecutive zeros:** `[0,0]`, `k=1` → `true`
   - Any sum divisible by 1

3. **Single element:** `[5]`, `k=5` → `false`
   - Need length ≥ 2

4. **Negative numbers:** `[23,-2,4]`, `k=6` → Handle negative remainders

5. **k=0:** Usually undefined, problem states k ≥ 1

6. **Same remainder at i and i+1:** `[5,0,0]`, `k=5`
   - At i=0: remainder=0 at index -1
   - At i=1: remainder=0, distance=1-(-1)=2 ✓

---

## Related Problems

- **LeetCode #560:** Subarray Sum Equals K
- **LeetCode #974:** Subarray Sums Divisible by K
- **LeetCode #525:** Contiguous Array (variant with 0s and 1s)

---

## Tags
`#prefix-sum` `#hash-map` `#modulo-arithmetic` `#subarray` `#medium` `#remainder`
