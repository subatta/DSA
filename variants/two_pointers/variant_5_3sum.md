# Variant: 3Sum

**Pattern:** Two Pointers  
**Difficulty:** Medium  
**LeetCode:** #15

---

## Problem Statement

Given an integer array `nums`, return all triplets `[nums[i], nums[j], nums[k]]` such that `i != j`, `i != k`, and `j != k`, and `nums[i] + nums[j] + nums[k] == 0`.

The solution set must not contain duplicate triplets.

### Input/Output:
- Input: `nums = [-1,0,1,2,-1,-4]`
- Output: `[[-1,-1,2],[-1,0,1]]`

---

## **Step 1: Derive Full State Space from Problem**

### **Problem Visualization:**
```
Input: nums = [-1, 0, 1, 2, -1, -4]

Goal: Find all UNIQUE triplets (a, b, c) where a + b + c = 0

All possible triplets (C(6,3) = 20):
(-1, 0, 1)   sum=0  ✓
(-1, 0, 2)   sum=1
(-1, 0, -1)  sum=-2
(-1, 0, -4)  sum=-5
(-1, 1, 2)   sum=2
(-1, 1, -1)  sum=-1
(-1, 1, -4)  sum=-4
(-1, 2, -1)  sum=0  ✓
(-1, 2, -4)  sum=-3
(-1, -1, -4) sum=-6
(0, 1, 2)    sum=3
(0, 1, -1)   sum=0  ✓ (duplicate of (-1, 0, 1))
(0, 1, -4)   sum=-3
(0, 2, -1)   sum=1
(0, 2, -4)   sum=-2
(0, -1, -4)  sum=-5
(1, 2, -1)   sum=2
(1, 2, -4)   sum=-1
(1, -1, -4)  sum=-4
(2, -1, -4)  sum=-3

Valid with duplicates removed:
  [-1, -1, 2]  (sorted form)
  [-1, 0, 1]   (sorted form)

Strategy: Fix first element, use two pointers for remaining two
  After sorting: [-4, -1, -1, 0, 1, 2]
  
  Fix a=-4: Find b+c=-(-4)=4 in remaining array
  Fix a=-1: Find b+c=-(-1)=1 in remaining array
  etc.
```

### **Core Question:**
How to find all unique triplets without checking all O(n³) combinations?

### **Deriving from First Principles:**

**Observation 1:** Brute force requires three nested loops
- Check all triplets (i, j, k) where i < j < k
- C(n, 3) = n(n-1)(n-2)/6 = O(n³)
- Also need to deduplicate results

**Observation 2:** Reduce to Two Sum
- Fix one element a at index i
- Problem becomes: Find two elements in remaining array that sum to (-a)
- This is the Two Sum problem! Can solve in O(n)
- Total: O(n) choices for a × O(n) for Two Sum = O(n²)

**Observation 3:** Sorting enables two pointers
- Sort array first: O(n log n)
- After fixing a, remaining array is sorted
- Use two pointers (one from left, one from right)
- Move based on current sum:
  - sum < target: move left pointer right (increase sum)
  - sum > target: move right pointer left (decrease sum)
  - sum = target: found triplet!

**Observation 4:** Handling duplicates
- Sort array helps: duplicates are adjacent
- Skip duplicate values when:
  1. Choosing element a (fix)
  2. Moving left pointer (skip same values)
  3. Moving right pointer (skip same values)
- Example: [-1, -1, 0, 1]
  - When a=-1 at index 0, process
  - When a=-1 at index 1, skip (duplicate)

### **Problem Analysis:**
- Find all **unique** triplets that sum to 0
- Three **different** indices required
- No duplicate triplets in result

### **State Space Definition:**
- **Type**: All triplets of three **different** elements
- **Structure**: Triplets (i, j, k) where `0 ≤ i < j < k < n`
- **Cardinality**: C(n,3) = n(n-1)(n-2)/6 triplets = **O(n³)**
- **Generation**: Three nested loops

### **Full State Space Enumeration:**
For `nums = [-1,0,1,2,-1,-4]` (n=6), there are 6×5×4/6 = **20 triplets**:

```
All triplets:
(-1,0,1), (-1,0,2), (-1,0,-1), (-1,0,-4),
(-1,1,2), (-1,1,-1), (-1,1,-4),
(-1,2,-1), (-1,2,-4),
(-1,-1,-4),
(0,1,2), (0,1,-1), (0,1,-4),
(0,2,-1), (0,2,-4),
(0,-1,-4),
(1,2,-1), (1,2,-4),
(1,-1,-4),
(2,-1,-4)

Valid (sum=0): (-1,0,1), (-1,2,-1), (0,1,-1)
Unique after sorting: [-1,-1,2], [-1,0,1]
```

---

## **Step 2: Brute Force Solution with Value Tracing**

### **Brute Force Code:**
```csharp
List<List<int>> ThreeSumBruteForce(int[] nums) 
{
    var result = new HashSet<string>(); // Use to avoid duplicates
    
    for (int i = 0; i < nums.Length; i++)
    {
        for (int j = i + 1; j < nums.Length; j++)
        {
            for (int k = j + 1; k < nums.Length; k++)
            {
                if (nums[i] + nums[j] + nums[k] == 0)
                {
                    var triplet = new List<int> { nums[i], nums[j], nums[k] };
                    triplet.Sort();
                    result.Add(string.Join(",", triplet));
                }
            }
        }
    }
    
    return result.Select(s => s.Split(',').Select(int.Parse).ToList()).ToList();
}
```

### **Complexity:**
- **Time**: O(n³) - three nested loops
- **Space**: O(n) for result set

---

## **Step 3: Can We Prune the Brute Force?**

### **Key Observations:**

1. **Sorted array helps**: After sorting, can use two pointers for inner two elements
2. **Reduce to Two Sum**: Fix first element, find two elements that sum to `-nums[i]`
3. **Skip duplicates**: Avoid processing same value multiple times

### **Can We Do Better?**
**YES!** Sort array, fix first element, use two pointers for remaining pair

---

## **Step 4: Optimal Solution - Sort + Two Pointers**

### **Key Insight:**
1. Sort array: `[-4,-1,-1,0,1,2]`
2. For each `nums[i]`, find pair that sums to `-nums[i]` using two pointers
3. Skip duplicate values to avoid duplicate triplets

### **Optimized Solution:**
```csharp
List<List<int>> ThreeSum(int[] nums) 
{
    Array.Sort(nums);
    var result = new List<List<int>>();
    
    for (int i = 0; i < nums.Length - 2; i++)
    {
        // Skip duplicates for first element
        if (i > 0 && nums[i] == nums[i - 1]) continue;
        
        int left = i + 1, right = nums.Length - 1;
        int target = -nums[i];
        
        while (left < right)
        {
            int sum = nums[left] + nums[right];
            
            if (sum == target)
            {
                result.Add(new List<int> { nums[i], nums[left], nums[right] });
                
                // Skip duplicates
                while (left < right && nums[left] == nums[left + 1]) left++;
                while (left < right && nums[right] == nums[right - 1]) right--;
                
                left++;
                right--;
            }
            else if (sum < target)
            {
                left++;
            }
            else
            {
                right--;
            }
        }
    }
    
    return result;
}
```

### **Big-O Analysis:**
- **Time**: O(n²) - outer loop O(n) × two pointers O(n)
- **Space**: O(1) excluding result
- **Optimization**: O(n³) → **O(n²)** ✅

### **Key Takeaway:**
**Reduce dimension with fixed anchor**: 3Sum becomes 2Sum by fixing one element. Sort enables two pointers for the remaining pair, reducing O(n³) to O(n²).

---

## **Related Problems:**
- [Two Sum II](variant_1_two_sum_sorted.md) - Base case
- 4Sum - Add another outer loop = O(n³)
- 3Sum Closest - Similar approach, track minimum difference

## **Tags:**
`two-pointers` `array` `sorting` `O(n³)-state-space` `medium` `triplets`
