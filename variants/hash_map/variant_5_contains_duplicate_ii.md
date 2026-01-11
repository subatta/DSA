# Variant #5: Contains Duplicate II

**LeetCode #219 - Easy**

## Problem Statement
Given an integer array `nums` and an integer `k`, return `true` if there are two distinct indices `i` and `j` in the array such that `nums[i] == nums[j]` and `abs(i - j) <= k`.

**Example:** `nums = [1,2,3,1]`, `k = 3` → `true` (indices 0 and 3, distance = 3)

## 4-Step Analysis Framework

### Step 1: State Space Derivation

**Type:** Find duplicate within distance k  
**Structure:** All pairs of indices with same value  
**Cardinality:** Up to n(n-1)/2 pairs to check

**State Space Example:**
For `nums = [1, 2, 3, 1]`, `k = 3`:

```
Pairs with same value:
(0,3): nums[0]=1, nums[3]=1, distance=3-0=3 ≤ k=3 ✅

Check: Does any pair have distance ≤ k?
Result: Yes!
```

---

### Step 2: Brute Force with Value Tracing

**Approach:** Check all pairs

```csharp
public bool ContainsNearbyDuplicate_BruteForce(int[] nums, int k)
{
    int n = nums.Length;
    
    for (int i = 0; i < n; i++)
    {
        for (int j = i + 1; j < n && j <= i + k; j++)
        {
            if (nums[i] == nums[j])
            {
                return true;
            }
        }
    }
    
    return false;
}
```

**Value Trace for:** `[1, 2, 3, 1]`, `k = 3`

| i | j | nums[i] | nums[j] | Same? | Distance | Valid? |
|---|---|---------|---------|-------|----------|--------|
| 0 | 1 | 1 | 2 | No | 1 | - |
| 0 | 2 | 1 | 3 | No | 2 | - |
| 0 | 3 | 1 | 1 | Yes | 3 | ≤ k? Yes! ✅ |

**Complexity:**
- **Time:** O(n × k) or O(n²) if k ≥ n
- **Space:** O(1)

---

### Step 3: Pruning Analysis - Can We Do Better?

**Key Observations:**

1. **What we need:** Check if value seen within last k positions
2. **Sliding window idea:** Maintain window of size k
3. **Hash Map:** Store value → most recent index
4. **Check distance:** When we see a value, check if previous occurrence within k

**Decision:** ✅ Use Hash Map to store most recent index of each value!

---

### Step 4: Optimal Solution with Skeleton Transformation

**Approach 1: Hash Map (Value → Index)**

```csharp
public bool ContainsNearbyDuplicate_Optimal(int[] nums, int k)
{
    var map = new Dictionary<int, int>(); // value -> most recent index
    
    for (int i = 0; i < nums.Length; i++)
    {
        if (map.ContainsKey(nums[i]))
        {
            // Check distance
            if (i - map[nums[i]] <= k)
            {
                return true;
            }
        }
        
        // Update most recent index
        map[nums[i]] = i;
    }
    
    return false;
}
```

**Approach 2: Hash Set (Sliding Window)**

```csharp
public bool ContainsNearbyDuplicate_SlidingWindow(int[] nums, int k)
{
    var window = new HashSet<int>();
    
    for (int i = 0; i < nums.Length; i++)
    {
        // Check if current value in window
        if (window.Contains(nums[i]))
        {
            return true;
        }
        
        // Add current value to window
        window.Add(nums[i]);
        
        // Maintain window size k
        if (window.Count > k)
        {
            window.Remove(nums[i - k]);
        }
    }
    
    return false;
}
```

**Skeleton Transformation:**
```
BRUTE FORCE:
├── for i in range(n):
│   └── for j in range(i+1, min(i+k+1, n)):
│       └── if nums[i] == nums[j]: return true
└── return false

         ↓

OPTIMAL (Hash Map):
├── map = {}
├── for i in range(n):
│   ├── if nums[i] in map:
│   │   └── if i - map[nums[i]] <= k: return true
│   └── map[nums[i]] = i
└── return false

KEY CHANGES:
- Nested loop → Single pass
- O(k) inner checks → O(1) hash lookup
- Track all pairs → Track most recent index only
```

**Value Trace (Hash Map) for:** `[1, 0, 1, 1]`, `k = 1`

| i | nums[i] | map before | Found? | Distance | Valid? | map after |
|---|---------|------------|--------|----------|--------|------------|
| 0 | 1 | {} | No | - | - | {1:0} |
| 1 | 0 | {1:0} | No | - | - | {1:0, 0:1} |
| 2 | 1 | {1:0, 0:1} | Yes | 2-0=2 | 2≤1? No | {1:2, 0:1} |
| 3 | 1 | {1:2, 0:1} | Yes | 3-2=1 | 1≤1? Yes! ✅ | - |

**Value Trace (Sliding Window) for:** `[1, 2, 3, 1]`, `k = 3`

| i | nums[i] | window before | Contains? | Action | window after |
|---|---------|---------------|-----------|--------|---------------|
| 0 | 1 | {} | No | Add 1 | {1} |
| 1 | 2 | {1} | No | Add 2 | {1,2} |
| 2 | 3 | {1,2} | No | Add 3 | {1,2,3} |
| 3 | 1 | {1,2,3} | Yes! ✅ | - | - |

---

## Big-O Analysis

**Brute Force:**
- **Time:** O(n × k)
- **Space:** O(1)

**Optimal (Both Approaches):**
- **Time:** O(n) - single pass
- **Space:** O(min(n, k)) - hash map/set size bounded by k

---

## Pattern Recognition

**When to use Hash Map for Recent Values:**
- ✅ Need to check "seen recently"
- ✅ Distance/window constraint
- ✅ Can track last occurrence index
- ✅ Single pass preferred

**Approach Selection:**
- **Hash Map:** When need actual distance/index
- **Hash Set:** When just need "within k" check (simpler)

---

## Edge Cases

1. **k = 0:** No pairs within distance 0 (always false)
2. **k ≥ n:** Check entire array for duplicates
3. **No duplicates:** Returns false
4. **Immediate duplicates:** `[1,1]`, `k=1` → true

---

## Related Problems

- **Contains Duplicate** (LeetCode #217) - No distance constraint
- **Contains Duplicate III** (LeetCode #220) - Value difference + index difference
- **Valid Anagram** (LeetCode #242) - Different constraint

---

## Tags
`hash-map` `hash-set` `sliding-window` `array` `easy` `distance-constraint`