# Variant: Sort Colors (Dutch National Flag)

**Pattern:** Two Pointers (Three-way Partition)  
**Difficulty:** Medium  
**LeetCode:** #75

---

## Problem Statement

Given an array `nums` with `n` objects colored red (0), white (1), or blue (2), sort them **in-place** so that objects of the same color are adjacent, with colors in order 0, 1, 2.

### Input/Output:
- Input: `nums = [2,0,2,1,1,0]`
- Output: `[0,0,1,1,2,2]`

---

## **Step 1: State Space Derivation**

### **Problem Visualization:**
```
Input: nums = [2,0,2,1,1,0]

Current state (mixed):
[2, 0, 2, 1, 1, 0]
 R  B  R  W  W  B

Goal: Sort into three regions
[0, 0, 1, 1, 2, 2]
 B  B  W  W  R  R

Target structure:
[all 0s | all 1s | all 2s]
```

### **Core Question:**
How to partition array into three sorted regions with only 3 distinct values (0, 1, 2)?

### **Deriving from First Principles:**

**Observation 1:** Only 3 distinct values means we can count
- Count frequency of each value: count[0], count[1], count[2]
- Rewrite array: first count[0] positions get 0, next count[1] get 1, rest get 2
- This works but requires **two passes** through array

**Observation 2:** Can we partition in one pass?
- Maintain three regions: [0s | 1s | unprocessed | 2s]
- Use three pointers to mark boundaries
- Process middle region element by element

**Observation 3:** Dutch National Flag algorithm
- **low**: boundary between 0s and 1s (everything before low is 0)
- **mid**: current element being examined
- **high**: boundary between 1s and 2s (everything after high is 2)
- Invariant: `[0...low-1]=0, [low...mid-1]=1, [mid...high]=unknown, [high+1...n-1]=2`

### **State Space Structure:**
**Type:** Three-way partition problem  
**Structure:** Divide array into three consecutive regions  
**Cardinality:** n elements to classify into 3 buckets  
**Comparison with other approaches:**
- Brute force sort: O(n log n) time
- Counting sort: O(n) time, but **two passes**
- Dutch flag: O(n) time, **one pass**, O(1) space

**Example Computation:**
For `nums = [2,0,2,1,1,0]`:

```
Initial: [2, 0, 2, 1, 1, 0]
         ↑low        ↑high
         ↑mid

Step-by-step classification:
1. mid=0, nums[0]=2: swap with high, high--
   [0, 0, 2, 1, 1, 2]  (moved 2 to end)
         ↑low     ↑high
         ↑mid

2. mid=0, nums[0]=0: swap with low, low++, mid++
   [0, 0, 2, 1, 1, 2]  (0 already in place)
            ↑low  ↑high
            ↑mid

3. mid=1, nums[1]=0: swap with low, low++, mid++
   [0, 0, 2, 1, 1, 2]
               ↑low ↑high
               ↑mid

4. mid=2, nums[2]=2: swap with high, high--
   [0, 0, 1, 1, 2, 2]
               ↑low ↑high
               ↑mid

5. mid=2, nums[2]=1: mid++
   [0, 0, 1, 1, 2, 2]
               ↑low ↑high
                  ↑mid

6. mid=3, nums[3]=1: mid++
   [0, 0, 1, 1, 2, 2]
               ↑low ↑high
                     ↑mid

Done: mid > high
Result: [0, 0, 1, 1, 2, 2]
```

### **Generation Pattern (Counting Sort - Two Pass):**
```csharp
void SortColorsCounting(int[] nums)
{
    // Pass 1: Count frequencies
    int[] count = new int[3];
    foreach (int num in nums)
        count[num]++;
    
    // Pass 2: Rewrite array
    int index = 0;
    for (int color = 0; color < 3; color++)
    {
        for (int i = 0; i < count[color]; i++)
        {
            nums[index++] = color;
        }
    }
}
// Time: O(n), Space: O(1), but TWO passes
```

---

## **Step 2: Brute Force**

```csharp
void SortColorsBruteForce(int[] nums) 
{
    // Count each color
    int zeros = 0, ones = 0, twos = 0;
    
    foreach (int num in nums)
    {
        if (num == 0) zeros++;
        else if (num == 1) ones++;
        else twos++;
    }
    
    // Rewrite array
    int i = 0;
    while (zeros-- > 0) nums[i++] = 0;
    while (ones-- > 0) nums[i++] = 1;
    while (twos-- > 0) nums[i++] = 2;
}
```

**Time**: O(n), but two passes

---

## **Step 3: Pruning**

**Key Insight:** Three-way partitioning with **three pointers**:
- **low**: boundary for 0s (everything before low is 0)
- **mid**: current element being examined
- **high**: boundary for 2s (everything after high is 2)

Invariant: `[0...low-1]=0, [low...mid-1]=1, [mid...high]=unprocessed, [high+1...n-1]=2`

---

## **Step 4: Optimal Solution**

```csharp
void SortColors(int[] nums) 
{
    int low = 0, mid = 0, high = nums.Length - 1;
    
    while (mid <= high)
    {
        if (nums[mid] == 0)
        {
            // Swap to low region
            Swap(nums, low, mid);
            low++;
            mid++;
        }
        else if (nums[mid] == 1)
        {
            // Already in correct region
            mid++;
        }
        else // nums[mid] == 2
        {
            // Swap to high region
            Swap(nums, mid, high);
            high--;
            // Don't increment mid (need to check swapped element)
        }
    }
}

void Swap(int[] nums, int i, int j)
{
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}
```

**Big-O**: O(n) single pass ✅  
**Space**: O(1)

### **Key Takeaway:**
**Three-way partitioning**: Extension of two pointers for 3 regions. Maintain invariants as mid pointer scans, swapping elements to appropriate regions.

---

## **Related Problems:**
- Partition Array - Two-way partition
- Sort Array by Parity - Similar partitioning

## **Tags:**
`two-pointers` `array` `in-place` `medium` `three-way-partition` `dutch-flag`
