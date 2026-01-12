# Variant: Move Zeros

**Pattern:** Two Pointers (Fast & Slow)  
**Difficulty:** Easy  
**LeetCode:** #283

---

## Problem Statement

Given an integer array `nums`, move all `0`'s to the end while maintaining the relative order of non-zero elements. Must be done **in-place**.

### Input/Output:
- Input: `nums = [0,1,0,3,12]`
- Output: `[1,3,12,0,0]`

---

## **Step 1: State Space Derivation**

### **Problem Visualization:**
```
Input: nums = [0,1,0,3,12]

Current state:
[0, 1, 0, 3, 12]
 ↑  ↑  ↑  ↑   ↑
Goal: Move all zeros to end while preserving order of non-zeros

Target state:
[1, 3, 12, 0, 0]
 ↑  ↑   ↑  ↑  ↑
```

### **Core Question:**
How to partition array into [non-zeros | zeros] while preserving relative order of non-zeros?

### **Deriving from First Principles:**

**Observation 1:** We need to identify two groups
- Non-zero elements: must keep their relative order
- Zero elements: position doesn't matter, just move to end

**Observation 2:** Two-pass approach
- First pass: collect all non-zeros in order
- Second pass: fill remaining positions with zeros

**Observation 3:** In-place requirement
- Cannot use extra array (would be O(n) space)
- Must rearrange elements within original array
- Similar to partitioning problem

### **State Space Structure:**
**Type:** Partition array elements into two groups  
**Structure:** Process each of n elements, decide where to place it  
**Cardinality:** n elements to process = **O(n)**  
**Generation:** Single pass with two-pointer approach

**Example Computation:**
For `nums = [0,1,0,3,12]`:

```
Elements to process:
Index 0: value=0  → skip (zero)
Index 1: value=1  → write at position 0
Index 2: value=0  → skip (zero)
Index 3: value=3  → write at position 1
Index 4: value=12 → write at position 2

After placing non-zeros: [1, 3, 12, ?, ?]
Fill remaining with zeros: [1, 3, 12, 0, 0]
```

### **Generation Pattern (Two-Pass):**
```csharp
void MoveZerosTwoPass(int[] nums)
{
    // Pass 1: Write non-zeros to front
    int writePos = 0;
    for (int i = 0; i < nums.Length; i++)
    {
        if (nums[i] != 0)
        {
            nums[writePos++] = nums[i];
        }
    }
    
    // Pass 2: Fill remaining with zeros
    for (int i = writePos; i < nums.Length; i++)
    {
        nums[i] = 0;
    }
}
// Time: O(n), Space: O(1)
```

---

## **Step 2: Brute Force**

```csharp
void MoveZeroesBruteForce(int[] nums) 
{
    var nonZeros = new List<int>();
    int zeroCount = 0;
    
    // Collect non-zeros
    foreach (int num in nums)
    {
        if (num != 0)
            nonZeros.Add(num);
        else
            zeroCount++;
    }
    
    // Copy back
    for (int i = 0; i < nonZeros.Count; i++)
        nums[i] = nonZeros[i];
    
    // Fill zeros
    for (int i = nonZeros.Count; i < nums.Length; i++)
        nums[i] = 0;
}
```

**Time**: O(n), **Space**: O(n)

---

## **Step 3: Pruning**

**Key Insight:** Use fast/slow pointers:
- **slow**: write position for next non-zero
- **fast**: scan for non-zero elements
- Fill remaining positions with zeros

---

## **Step 4: Optimal Solution**

### **Optimal: One-Pass with Swap**

Instead of copy-then-fill, **swap** elements to avoid the second loop:

```csharp
void MoveZeroes(int[] nums) 
{
    int slow = 0; // Write position for non-zeros
    
    for (int fast = 0; fast < nums.Length; fast++)
    {
        if (nums[fast] != 0)
        {
            // Swap non-zero to front
            int temp = nums[slow];
            nums[slow] = nums[fast];
            nums[fast] = temp;
            slow++;
        }
    }
}
```

**Why swapping works:**
- When `slow == fast`: Swaps element with itself (no-op, no zeros encountered yet) ✓
- When `slow < fast`: Swaps non-zero forward, zero backward ✓
- **Single pass** - no need for separate fill loop!

**Value Trace for [0,1,0,3,12]:**

| Step | fast | slow | nums[fast] | Action | Array After |
|------|------|------|------------|--------|-------------|
| Init | 0    | 0    | -          | -      | [0,1,0,3,12] |
| 1    | 0    | 0    | 0          | Skip (zero) | [0,1,0,3,12] |
| 2    | 1    | 0    | 1          | Swap(0,1) | [1,0,0,3,12] |
|      |      | 1    |            | slow++ | [1,0,0,3,12] |
| 3    | 2    | 1    | 0          | Skip (zero) | [1,0,0,3,12] |
| 4    | 3    | 1    | 3          | Swap(1,3) | [1,3,0,0,12] |
|      |      | 2    |            | slow++ | [1,3,0,0,12] |
| 5    | 4    | 2    | 12         | Swap(2,4) | [1,3,12,0,0] |
|      |      | 3    |            | slow++ | [1,3,12,0,0] |

**Result: [1,3,12,0,0]** ✓ Single pass!

**Value Trace for [1,2,3] (no zeros):**

| Step | fast | slow | nums[fast] | Action | Array After |
|------|------|------|------------|--------|-------------|
| Init | 0    | 0    | -          | -      | [1,2,3] |
| 1    | 0    | 0    | 1          | Swap(0,0) = no-op | [1,2,3] |
|      |      | 1    |            | slow++ | [1,2,3] |
| 2    | 1    | 1    | 2          | Swap(1,1) = no-op | [1,2,3] |
|      |      | 2    |            | slow++ | [1,2,3] |
| 3    | 2    | 2    | 3          | Swap(2,2) = no-op | [1,2,3] |
|      |      | 3    |            | slow++ | [1,2,3] |

**Result: [1,2,3]** ✓ Unchanged (swaps with self)

### **Alternative: Two-Pass (Copy + Fill)**

A simpler but less optimal approach:

```csharp
void MoveZeroesTwoPass(int[] nums) 
{
    int slow = 0;
    
    // Pass 1: Copy non-zeros to front
    for (int fast = 0; fast < nums.Length; fast++)
    {
        if (nums[fast] != 0)
        {
            nums[slow] = nums[fast];
            slow++;
        }
    }
    
    // Pass 2: Fill remaining with zeros
    for (int i = slow; i < nums.Length; i++)
    {
        nums[i] = 0;
    }
}
```

**Comparison:**

| Approach | Passes | Advantages | Use When |
|----------|--------|------------|----------|
| **Swap** | 1 | Single pass, elegant | **Preferred** |
| Copy + Fill | 2 | Simpler logic | Teaching basics |

**Big-O**: O(n) time, O(1) space (both approaches)

### **Key Takeaway:**
**Fast/slow with swap for in-place partitioning**: Swapping naturally handles the case when pointers coincide (no-op swap) and eliminates the need for a separate fill loop. This is the optimal single-pass solution.

---

## **Related Problems:**
- [Remove Duplicates](variant_3_remove_duplicates.md) - Same pattern
- Remove Element - Generalize to any value

## **Tags:**
`two-pointers` `array` `in-place` `easy` `fast-slow` `partition`
