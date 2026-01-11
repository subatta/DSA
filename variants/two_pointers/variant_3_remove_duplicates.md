# Variant: Remove Duplicates from Sorted Array

**Pattern:** Two Pointers (Fast & Slow)
**Difficulty:** Easy  
**LeetCode:** #26

---

## Problem Statement

Given an integer array `nums` sorted in **ascending order**, remove the duplicates **in-place** such that each unique element appears only once. The **relative order** of elements should be kept the same.

Return `k` after placing the final result in the first `k` slots of `nums`.

### Input/Output:
- Input: `nums = [1,1,2,2,3]`
- Output: `3` (nums becomes `[1,2,3,_,_]`)
- Explanation: First 3 elements are unique

---

## **Step 1: Derive Full State Space from Problem**

### **Problem Analysis:**
- Array is sorted (duplicates are adjacent)
- Remove duplicates **in-place** (O(1) extra space)
- Preserve order of first occurrence
- Return count of unique elements

### **State Space Definition:**
- **Type**: All elements in array, need to identify unique ones
- **Structure**: Linear scan comparing adjacent elements  
- **Cardinality**: n elements to process
- **Generation**: Single pass, compare nums[i] with nums[i-1]

### **Full State Space Enumeration:**
For `nums = [1,1,2,2,3]` (n=5):

```
Elements to process:
Index 0: 1 (no previous, always unique)
Index 1: 1 (same as index 0, duplicate)
Index 2: 2 (different from index 1, unique)
Index 3: 2 (same as index 2, duplicate)  
Index 4: 3 (different from index 3, unique)

Unique elements: [1, 2, 3]
Count: 3
```

---

## **Step 2: Brute Force Solution with Value Tracing**

### **Brute Force Approach:**
1. Create separate list to store unique elements
2. Scan array, add element if different from previous
3. Copy unique elements back to original array
4. Return count

### **Brute Force Code:**
```csharp
int RemoveDuplicatesBruteForce(int[] nums) 
{
    if (nums.Length == 0) return 0;
    
    var unique = new List<int>();
    unique.Add(nums[0]); // First element always unique
    
    // Collect unique elements
    for (int i = 1; i < nums.Length; i++)
    {
        if (nums[i] != nums[i - 1])
        {
            unique.Add(nums[i]);
        }
    }
    
    // Copy back to original array
    for (int i = 0; i < unique.Count; i++)
    {
        nums[i] = unique[i];
    }
    
    return unique.Count;
}
```

### **Brute Force Value Trace (nums = [1,1,2,2,3]):**

**Phase 1: Collect Unique**
| i | nums[i] | nums[i-1] | Different? | unique list |
|---|---------|-----------|------------|-------------|
| 0 | 1       | -         | -          | [1]         |
| 1 | 1       | 1         | ❌         | [1]         |
| 2 | 2       | 1         | ✅         | [1,2]       |
| 3 | 2       | 2         | ❌         | [1,2]       |
| 4 | 3       | 2         | ✅         | [1,2,3]     |

**Phase 2: Copy Back**
```
unique = [1,2,3]
nums becomes [1,2,3,2,3] → only first 3 positions matter
```

**Result: 3**

### **Complexity:**
- **Time**: O(n) - two passes (collect + copy)
- **Space**: O(n) - unique list

---

## **Step 3: Can We Prune the Brute Force?**

### **Key Observations:**

1. **Unnecessary Extra Space**: List stores up to n elements
   ```
   unique = [1,2,3] ← wastes O(n) space
   Could write directly to front of nums
   ```

2. **Two-Pass Problem**: Separate collect + copy phases
   ```
   Pass 1: Build unique list
   Pass 2: Copy back
   Could write in-place during first pass!
   ```

3. **Sorted Property**: Duplicates are always adjacent
   ```
   [1,1,2,2,3]
    ↑ ↑     ← adjacent duplicates
   Only need to compare with immediate predecessor
   ```

4. **Overwrite Pattern**: Can overwrite duplicates safely
   ```
   [1,1,2,2,3]
    ↑ ↑       ← reading fast pointer
    ↑         ← writing slow pointer  
   Safe because slow ≤ fast always
   ```

### **Can We Do Better?**
**YES!** Use fast/slow two pointers to write unique elements in-place

---

## **Step 4: Optimal Solution - Fast & Slow Pointers**

### **Key Insight:**
Use two pointers:
- **slow**: Write position for next unique element
- **fast**: Scan through array looking for new unique elements

When `nums[fast] != nums[fast-1]`, write to `nums[slow++]`

### **Why This Works:**
```
[1,1,2,2,3]
 slow=1, fast=1
   ↑   ↑
   
nums[1]=1 == nums[0]=1 → skip
slow=1, fast=2
   ↑     ↑
   
nums[2]=2 != nums[1]=1 → write nums[1]=2, slow=2
[1,2,2,2,3]
     ↑   ↑
     slow fast
     
Continue until fast reaches end
```

### **Canonical Skeleton Applied:**
```csharp
int TwoPointersTemplate(int[] nums) {
    int slow = initialPosition;
    
    for (int fast = 0; fast < nums.Length; fast++) {
        if (ShouldInclude(nums[fast])) {
            nums[slow] = nums[fast];
            slow++;
        }
    }
    
    return slow;
}
```

### **Optimized Solution:**
```csharp
int RemoveDuplicates(int[] nums) 
{
    if (nums.Length == 0) return 0;
    
    int slow = 1; // Write position (first element always unique)
    
    for (int fast = 1; fast < nums.Length; fast++) 
    {
        // Found new unique element
        if (nums[fast] != nums[fast - 1]) 
        {
            nums[slow] = nums[fast];
            slow++;
        }
    }
    
    return slow; // Length of unique elements
}
```

### **Changes from Skeleton:**
1. **slow starts at 1**: First element always unique, no need to check
2. **ShouldInclude**: `nums[fast] != nums[fast-1]` (different from previous)
3. **In-place write**: Overwrite duplicate positions
4. **Return slow**: Count of unique elements

### **Optimized Value Trace (nums = [1,1,2,2,3]):**

| Step | fast | slow | nums[fast] | nums[fast-1] | Different? | Action | nums array |
|------|------|------|------------|--------------|------------|--------|------------|
| Init | 1    | 1    | -          | -            | -          | -      | [1,1,2,2,3] |
| 1    | 1    | 1    | 1          | 1            | ❌         | Skip   | [1,1,2,2,3] |
| 2    | 2    | 1    | 2          | 1            | ✅         | Write  | [1,2,2,2,3] |
|      |      | 2    |            |              |            | slow++ | [1,2,2,2,3] |
| 3    | 3    | 2    | 2          | 2            | ❌         | Skip   | [1,2,2,2,3] |
| 4    | 4    | 2    | 3          | 2            | ✅         | Write  | [1,2,3,2,3] |
|      |      | 3    |            |              |            | slow++ | [1,2,3,2,3] |

**Result: 3** (first 3 elements are [1,2,3])

### **Detailed Step 2:**
```
fast=2, slow=1
nums[2]=2, nums[1]=1
2 != 1? YES → unique element found
  Write: nums[slow] = nums[fast] → nums[1] = 2
  Increment: slow++ → slow = 2
  Array becomes: [1,2,2,2,3]
                    ↑ ← wrote here
```

### **State Space Comparison:**

| Approach | Extra Space | Writes | Passes |
|----------|-------------|--------|--------|
| Brute Force | O(n) list | Copy all unique | 2 passes |
| Two Pointers | O(1) | Only unique elements | 1 pass |

### **Big-O Analysis:**
- **Time**: O(n) - single pass
- **Space**: O(1) - in-place modification  
- **Optimization**: O(n) time maintained, O(n) space → **O(1) space** ✅

### **Key Takeaway:**
**Fast & Slow Pointers for in-place filtering**: When we need to remove elements from array while preserving order, fast pointer scans for valid elements while slow pointer marks write position. This is safe because slow ≤ fast always (we never overwrite unread data).

---

## **Related Problems:**
- Remove Element - Similar fast/slow pattern
- Remove Duplicates II - Allow at most 2 duplicates
- Move Zeros - Fast/slow with different condition

## **Tags:**
`two-pointers` `array` `in-place` `sorted` `easy` `fast-slow`
