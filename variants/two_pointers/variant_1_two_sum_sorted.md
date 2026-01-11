# Variant: Two Sum II (Sorted Array)

**Pattern:** Two Pointers  
**Difficulty:** Easy  
**LeetCode:** #167

---

## Problem Statement

Given a **1-indexed** array of integers `numbers` that is **sorted in ascending order**, find two numbers that add up to a specific `target` number. Return the indices of the two numbers (1-indexed) as an array `[index1, index2]` where `1 ≤ index1 < index2 ≤ numbers.length`.

### Input/Output:
- Input: `numbers = [2,7,11,15]`, `target = 9`
- Output: `[1,2]`
- Explanation: The sum of 2 and 7 is 9. Therefore, index1 = 1, index2 = 2 (1-indexed)

---

## **Step 1: Derive Full State Space from Problem**

### **Problem Analysis:**
- Find two **different** numbers that sum to target
- Array is sorted in ascending order
- Must return 1-indexed positions

### **State Space Definition:**

**Reference:** See [All Pairs State Space](../../state_spaces/all_pairs.md) for detailed mathematical derivation

- **Type**: All pairs of two **different** elements
- **Structure**: Pairs (i, j) where `0 ≤ i < j < n`
- **Cardinality**: n(n-1)/2 pairs = **O(n²)**
- **Generation**: Nested loops - outer for first element, inner for second

### **Full State Space Enumeration:**
For `numbers = [2,7,11,15]` (n=4), there are 4×3/2 = **6 pairs**:

```
Pairs to check:
(2,7)   at indices (0,1)
(2,11)  at indices (0,2)
(2,15)  at indices (0,3)
(7,11)  at indices (1,2)
(7,15)  at indices (1,3)
(11,15) at indices (2,3)

Total: 6 pairs
```

**With sums:**
```
(2,7)   → 9  ✅ Match!
(2,11)  → 13
(2,15)  → 17
(7,11)  → 18
(7,15)  → 22
(11,15) → 26
```

---

## **Step 2: Brute Force Solution with Value Tracing**

### **Brute Force Approach:**
1. Generate all pairs using nested loops
2. Check if sum equals target
3. Return 1-indexed positions when found

### **Brute Force Code:**
```csharp
int[] TwoSumBruteForce(int[] numbers, int target) 
{
    // Generate all pairs
    for (int i = 0; i < numbers.Length; i++)
    {
        for (int j = i + 1; j < numbers.Length; j++)
        {
            if (numbers[i] + numbers[j] == target)
            {
                return new int[] { i + 1, j + 1 }; // 1-based indices
            }
        }
    }
    
    return new int[0]; // Not found
}
```

### **Brute Force Value Trace (numbers = [2,7,11,15], target = 9):**

| i | j | numbers[i] | numbers[j] | sum | == target? | result |
|---|---|------------|------------|-----|------------|--------|
| 0 | 1 | 2          | 7          | 9   | ✅         | [1,2]  |

**Result: [1,2]** (found immediately, but worst case checks all 6 pairs)

### **Complexity:**
- **Time**: O(n²) - nested loops check all pairs
- **Space**: O(1)

---

## **Step 3: Can We Prune the Brute Force?**

### **Key Observations:**

1. **Sorted Property**: Array is sorted ascending
   ```
   [2, 7, 11, 15]
    ↑          ↑
   smallest  largest
   ```

2. **Directional Information**: When sum is wrong, we know which way to adjust
   ```
   (2, 15) → sum = 17 > 9  → Need smaller sum → decrease right
   (2, 11) → sum = 13 > 9  → Need smaller sum → decrease right
   (2, 7)  → sum = 9 = 9   → Match!
   ```

3. **Monotonic Property**: 
   - Moving `left` right → sum increases (add larger value)
   - Moving `right` left → sum decreases (remove larger value)

4. **Pair Elimination**: Each comparison eliminates many pairs
   ```
   Check (2, 15) → sum too large
   → Can eliminate ALL pairs (2, 15), (7, 15), (11, 15)
   → Move right pointer left
   ```

### **Can We Do Better?**
**YES!** Use two pointers starting from opposite ends, move based on sum comparison

---

## **Step 4: Optimal Solution - Two Pointers**

### **Key Insight:**
Start with **smallest + largest**:
- If sum **too large** → decrease by moving `right` left (use smaller value)
- If sum **too small** → increase by moving `left` right (use larger value)
- If sum **matches** → found answer!

Each move eliminates O(n) pairs from consideration.

### **Why This Works:**
```
Array: [2, 7, 11, 15], target = 9

Start: left=0 (2), right=3 (15)
  sum = 17 > 9 → Too large
  Can't use 15 with anything left of current left!
  → Move right to 11

Next: left=0 (2), right=2 (11)
  sum = 13 > 9 → Still too large
  Can't use 11 with anything left of current left!
  → Move right to 7

Next: left=0 (2), right=1 (7)
  sum = 9 = 9 → Found!
```

### **Canonical Skeleton Applied:**
```csharp
int TwoPointersTemplate(int[] nums) {
    int left = 0, right = nums.Length - 1;
    int result = 0;
    
    while (left < right) {
        // Process current pair (nums[left], nums[right])
        
        if (ConditionMet(nums[left], nums[right])) {
            // Record/update result
            left++;
            right--;
        }
        else if (NeedSmaller()) {
            right--;
        }
        else {
            left++;
        }
    }
    
    return result;
}
```

### **Optimized Solution:**
```csharp
int[] TwoSum(int[] numbers, int target) 
{
    int left = 0, right = numbers.Length - 1;
    
    while (left < right) 
    {
        int sum = numbers[left] + numbers[right];
        
        if (sum == target) 
        {
            return new int[] { left + 1, right + 1 }; // 1-based
        }
        else if (sum > target) 
        {
            right--; // Sum too large, need smaller value
        }
        else 
        {
            left++; // Sum too small, need larger value
        }
    }
    
    return new int[0]; // Not found (problem guarantees solution exists)
}
```

### **Changes from Skeleton:**
1. **Initialization**: `left = 0`, `right = n-1` (opposite ends)
2. **ConditionMet**: `sum == target` (found answer)
3. **NeedSmaller**: `sum > target` → move `right` left
4. **NeedLarger**: `sum < target` → move `left` right
5. **Return**: 1-based indices `[left+1, right+1]`

### **Optimized Value Trace (numbers = [2,7,11,15], target = 9):**

| Step | left | right | numbers[left] | numbers[right] | sum | Comparison | Action |
|------|------|-------|---------------|----------------|-----|------------|--------|
| 1    | 0    | 3     | 2             | 15             | 17  | 17 > 9     | right--|
| 2    | 0    | 2     | 2             | 11             | 13  | 13 > 9     | right--|
| 3    | 0    | 1     | 2             | 7              | 9   | 9 == 9     | **Found [1,2]** |

**Result: [1,2]**

### **State Space Comparison:**

| Approach | Pairs Checked | Example Path | Total Comparisons |
|----------|---------------|--------------|-------------------|
| Brute Force | All 6 pairs | (2,7), (2,11), (2,15), (7,11), (7,15), (11,15) | O(n²) = 6 |
| Two Pointers | 3 pairs | (2,15), (2,11), (2,7) | O(n) = 3 |

**Pairs Eliminated per Move:**
```
Step 1: Check (2,15) → sum too large
  Eliminates: (2,15), (7,15), (11,15) - ALL pairs with 15
  
Step 2: Check (2,11) → sum too large  
  Eliminates: (2,11), (7,11) - ALL remaining pairs with 11
  
Step 3: Check (2,7) → Found!
```

### **Big-O Analysis:**
- **Time**: O(n) - each pointer moves at most n times
- **Space**: O(1)
- **Optimization**: O(n²) → **O(n)** ✅

### **Key Takeaway:**
**Two Pointers exploits sorted array monotonicity**: Each comparison eliminates O(n) candidates by understanding that if `sum > target` with the largest remaining element, it will be too large with ALL remaining elements. This collapses O(n²) pair space into O(n) linear scan!

---

## **Related Problems:**
- Two Sum (unsorted) - Use hash map instead, O(n) time O(n) space
- 3Sum - Add outer loop, use two pointers for inner pair
- 4Sum - Two nested loops + two pointers

## **Tags:**
`two-pointers` `array` `sorted` `O(n²)-state-space` `easy` `pairs`
