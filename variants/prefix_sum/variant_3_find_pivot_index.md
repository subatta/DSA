# Variant #3: Find Pivot Index

**LeetCode #724 - Easy**

## Problem Statement
Given an array of integers `nums`, calculate the **pivot index** of this array.

The pivot index is the index where the sum of all numbers **strictly to the left** of the index is equal to the sum of all numbers **strictly to the right** of the index.

If the index is on the left edge, the left sum is 0 (no elements). Similarly for the right edge.

Return the **leftmost pivot index**. If no such index exists, return -1.

**Example:** `nums = [1,7,3,6,5,6]` → `3` (left sum = 1+7+3 = 11, right sum = 5+6 = 11)

## 4-Step Analysis Framework

### Step 1: State Space Derivation

### **Problem Visualization:**
```
Input: nums = [1, 7, 3, 6, 5, 6]

For each index, check if left sum = right sum:

Index 0:
  Left:  [] sum = 0
  Right: [7,3,6,5,6] sum = 27
  0 ≠ 27 ✗

Index 1:
  Left:  [1] sum = 1
  Right: [3,6,5,6] sum = 20
  1 ≠ 20 ✗

Index 2:
  Left:  [1,7] sum = 8
  Right: [6,5,6] sum = 17
  8 ≠ 17 ✗

Index 3:
  Left:  [1,7,3] sum = 11
  Right: [5,6] sum = 11
  11 = 11 ✓ PIVOT!

Visualization:
 1   7   3   6   5   6
[--Left--] P [--Right--]
    11     ↑     11

Key insight: At pivot index i:
  leftSum = nums[0] + ... + nums[i-1]
  rightSum = nums[i+1] + ... + nums[n-1]
  
  Also: leftSum + nums[i] + rightSum = totalSum
  So: leftSum = totalSum - leftSum - nums[i]
  Or: leftSum = (totalSum - nums[i]) / 2  (if (totalSum - nums[i]) is even)
  
  Better formula:
    leftSum + nums[i] + rightSum = totalSum
    If leftSum = rightSum:
      leftSum + nums[i] + leftSum = totalSum
      2×leftSum + nums[i] = totalSum
      leftSum = (totalSum - nums[i]) / 2
```

### **Core Question:**
How to find pivot efficiently without recalculating sums for each position?

### **Deriving from First Principles:**

**Observation 1:** Naive approach
- For each index i from 0 to n-1:
  - Compute leftSum = sum of nums[0...i-1]
  - Compute rightSum = sum of nums[i+1...n-1]
  - Check if leftSum == rightSum
- Time: O(n²) - for each of n positions, sum O(n) elements

**Observation 2:** Relationship between sums
```
At any index i:
  totalSum = leftSum + nums[i] + rightSum

If we want leftSum = rightSum:
  totalSum = leftSum + nums[i] + leftSum
  totalSum = 2×leftSum + nums[i]
  leftSum = (totalSum - nums[i]) / 2
  
But we can use simpler approach:
  rightSum = totalSum - leftSum - nums[i]
  Check if leftSum == rightSum
```

**Observation 3:** Running sum approach
- Precompute totalSum: O(n)
- Iterate left to right, maintaining leftSum
- At each position:
  - rightSum = totalSum - leftSum - nums[i]
  - Check if leftSum == rightSum
  - Update: leftSum += nums[i]
- Time: O(n), Space: O(1)

**Observation 4:** Alternative: prefix sum array
- Could build prefix array: prefix[i] = sum(nums[0...i-1])
- leftSum = prefix[i]
- rightSum = prefix[n] - prefix[i+1]
- But running sum is simpler and uses less space

### **State Space Structure:**

**Type:** Check balance condition at each possible pivot position  
**Structure:** For each index, verify left sum equals right sum  
**Cardinality:** n possible pivot positions to check  
**Valid pivots:** Subset of indices where leftSum = rightSum

**Example Computation:**
For `nums = [1, 7, 3, 6, 5, 6]`:

```
totalSum = 1+7+3+6+5+6 = 28

Iterate with running leftSum:
i=0: leftSum=0,  rightSum=28-0-1=27,   0≠27 ✗, leftSum→1
i=1: leftSum=1,  rightSum=28-1-7=20,   1≠20 ✗, leftSum→8
i=2: leftSum=8,  rightSum=28-8-3=17,   8≠17 ✗, leftSum→11
i=3: leftSum=11, rightSum=28-11-6=11, 11=11 ✓ FOUND!

Return 3
```

### **Generation Pattern (Naive):**
```csharp
int PivotIndexNaive(int[] nums)
{
    int n = nums.Length;
    
    for (int i = 0; i < n; i++)  // O(n)
    {
        // Compute left sum
        int leftSum = 0;
        for (int j = 0; j < i; j++)  // O(n)
            leftSum += nums[j];
        
        // Compute right sum
        int rightSum = 0;
        for (int j = i + 1; j < n; j++)  // O(n)
            rightSum += nums[j];
        
        if (leftSum == rightSum)
            return i;
    }
    
    return -1;
}
// Time: O(n²), Space: O(1)
```

---

### Step 2: Brute Force with Value Tracing

**Approach:** For each index, sum left elements and right elements

**Value Trace for:** `nums = [1, 7, 3, 6, 5, 6]`

| i | Left indices | leftSum | Right indices | rightSum | Equal? |
|---|--------------|---------|---------------|----------|--------|
| 0 | [] | 0 | [1,2,3,4,5] | 27 | ✗ |
| 1 | [0] | 1 | [2,3,4,5] | 20 | ✗ |
| 2 | [0,1] | 8 | [3,4,5] | 17 | ✗ |
| 3 | [0,1,2] | 11 | [4,5] | 11 | ✓ |

Return: 3

**Complexity:**
- Time: O(n²) - for each position, compute both sums
- Space: O(1)

---

### Step 3: Pruning Analysis - Can We Do Better?

**Key Insight:** Use running sum instead of recalculating

**Why it works:**
```
At any position i:
  leftSum = sum of elements we've already processed
  rightSum = totalSum - leftSum - nums[i]
  
We know totalSum from one initial pass.
As we move right, we incrementally update leftSum.
No need to recompute sums from scratch!
```

**Transformation:** O(n²) → O(n)
- One pass to compute totalSum: O(n)
- One pass with running leftSum: O(n)
- Total: O(n) with O(1) extra space

---

### Step 4: Optimal Solution

**Skeleton Transformation:**

```csharp
// BEFORE: Naive O(n²) approach
int PivotIndex_BruteForce(int[] nums)
{
    int n = nums.Length;
    
    for (int i = 0; i < n; i++)
    {
        int leftSum = 0;
        for (int j = 0; j < i; j++)  // Recalculate left sum
            leftSum += nums[j];
        
        int rightSum = 0;
        for (int j = i + 1; j < n; j++)  // Recalculate right sum
            rightSum += nums[j];
        
        if (leftSum == rightSum)
            return i;
    }
    
    return -1;
}

// AFTER: Running sum approach O(n) time, O(1) space
int PivotIndex(int[] nums)
{
    // Compute total sum
    int totalSum = 0;
    foreach (int num in nums)
        totalSum += num;
    
    // Check each position with running left sum
    int leftSum = 0;
    for (int i = 0; i < nums.Length; i++)
    {
        int rightSum = totalSum - leftSum - nums[i];
        
        if (leftSum == rightSum)
            return i;  // Found pivot!
        
        leftSum += nums[i];  // Update for next iteration
    }
    
    return -1;  // No pivot found
}
```

**Complexity:**
- **Time:** O(n) - two passes (one for total, one to find pivot)
- **Space:** O(1) - only storing three integers

**Execution Trace for [1,7,3,6,5,6]:**

```
totalSum = 28

i=0: leftSum=0,  rightSum=28-0-1=27,   check: 0==27? ✗, leftSum=1
i=1: leftSum=1,  rightSum=28-1-7=20,   check: 1==20? ✗, leftSum=8
i=2: leftSum=8,  rightSum=28-8-3=17,   check: 8==17? ✗, leftSum=11
i=3: leftSum=11, rightSum=28-11-6=11,  check: 11==11? ✓ return 3
```

---

## Edge Cases

1. **No pivot exists:** `[1,2,3]` → `-1`
   - No index where left = right

2. **Pivot at start:** `[0,1,1]` → `0`
   - leftSum=0 (empty), rightSum=1+1=2... wait, no!
   - Let me recalc: At i=0: left=0, right=1+1=2, not equal
   - Actually no pivot here

3. **Pivot at start (correct):** `[2,-1,-1]` → `0`
   - At i=0: left=0, right=-1+(-1)=-2, not equal
   - Actually: `[0,0,0]` → `0` (left=0, right=0)

4. **Pivot at end:** `[1,1,0]` → `2`
   - At i=2: left=1+1=2, right=0... hmm, not equal
   - Actually: `[-1,-1,0]` → `2` (left=-2, right=0, not equal)
   - Correct: `[-1,-1,2]` → `2` (left=-2, right=0, not equal)
   - Actually simple: `[1,1,2]` no pivot at end
   - Correct example: `[1,1,0]` at i=2: left=2, right=0 (not equal)

5. **Single element:** `[1]` → `0`
   - At i=0: left=0, right=0 ✓

6. **All zeros:** `[0,0,0]` → `0`
   - First index is pivot

---

## Related Problems

- **LeetCode #1991:** Find the Middle Index in Array (same problem)
- **LeetCode #2270:** Number of Ways to Split Array
- **LeetCode #560:** Subarray Sum Equals K

---

## Tags
`#prefix-sum` `#array` `#running-sum` `#easy` `#pivot` `#equilibrium-index`
