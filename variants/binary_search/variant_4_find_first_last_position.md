# Find First and Last Position in Sorted Array

**Difficulty:** Medium  
**LeetCode Problem:** #34  
**Tags:** `Binary Search`, `Array`, `Range Query`, `Boundaries`

---

## Problem Statement

Given an array of integers `nums` sorted in non-decreasing order, find the starting and ending position of a given `target` value.

If `target` is not found in the array, return `[-1, -1]`.

You must write an algorithm with `O(log n)` runtime complexity.

**Constraints:**
- `0 <= nums.length <= 10^5`
- `-10^9 <= nums[i] <= 10^9`
- `nums` is a non-decreasing array
- `-10^9 <= target <= 10^9`

**Example 1:**
```
Input: nums = [5,7,7,8,8,10], target = 8
Output: [3,4]
```

**Example 2:**
```
Input: nums = [5,7,7,8,8,10], target = 6
Output: [-1,-1]
```

**Example 3:**
```
Input: nums = [], target = 0
Output: [-1,-1]
```

---

## Step 1: State Space

### Problem Visualization

For `nums = [5,7,7,8,8,10]`, `target = 8`:

```
Index:  0  1  2  3  4  5
Value:  5  7  7  8  8  10
                ↑  ↑
             first last
           (index 3,4)

Target range: [3, 4]
```

For `target = 7`:
```
Index:  0  1  2  3  4  5
Value:  5  7  7  8  8  10
           ↑  ↑
        first last
       (index 1,2)
```

Boundary visualization:
```
[... < 8, 8, 8, 8, > 8 ...]
         ↑        ↑
      leftmost  rightmost
```

### Core Question

**How do we find the leftmost and rightmost occurrence of target in a sorted array with O(log n) complexity?**

We need two binary searches:
1. **Left boundary:** First position where `nums[i] == target`
2. **Right boundary:** Last position where `nums[i] == target`

### Deriving from First Principles

**Observation 1: Two separate searches**
Finding the range requires finding two boundaries:
- Leftmost position ≥ target (left boundary)
- Rightmost position ≤ target (right boundary)

**Observation 2: Left boundary search**
Find the first position where `nums[i] >= target`:
- If `nums[mid] < target`: Search right, `left = mid + 1`
- If `nums[mid] >= target`: Search left (or mid itself), `right = mid`

**Observation 3: Right boundary search**
Find the last position where `nums[i] <= target`:
- If `nums[mid] <= target`: Search right (or mid itself), `left = mid`
- If `nums[mid] > target`: Search left, `right = mid - 1`

**Observation 4: Validate boundaries**
After finding boundaries, check if target actually exists:
- If `leftBound >= n` or `nums[leftBound] != target`: Target doesn't exist
- Otherwise, return `[leftBound, rightBound]`

**Observation 5: Template variations**
- **Left boundary:** Use template 2 (left < right, right = mid)
- **Right boundary:** Use template 3 (left < right, left = mid with mid rounding up)

**Formula:**
```
findLeft(target):
  left = 0, right = n
  while left < right:
    mid = left + (right - left) / 2
    if nums[mid] < target:
      left = mid + 1
    else:
      right = mid
  return left

findRight(target):
  left = 0, right = n - 1
  while left < right:
    mid = left + (right - left + 1) / 2  // Round up
    if nums[mid] <= target:
      left = mid
    else:
      right = mid - 1
  return left
```

### State Space Structure

**Type:** Finding left and right boundaries of target range.

**Structure:**
- Two binary searches with different conditions
- Left search: Find first position >= target
- Right search: Find last position <= target

**Cardinality:** O(log n) for each search, O(log n) total.

**Key Property:** Sorted array with duplicates requires finding boundaries, not just any occurrence.

### Example Computation

Input: `nums = [5,7,7,8,8,10]`, `target = 8`

**Finding left boundary:**
```
left=0, right=6

Iteration 1:
  mid = 3, nums[3] = 8
  8 >= 8 → right = 3
  left=0, right=3

Iteration 2:
  mid = 1, nums[1] = 7
  7 < 8 → left = 2
  left=2, right=3

Iteration 3:
  mid = 2, nums[2] = 7
  7 < 8 → left = 3
  left=3, right=3

leftBound = 3
```

**Finding right boundary:**
```
left=0, right=5

Iteration 1:
  mid = (0+5+1)/2 = 3, nums[3] = 8
  8 <= 8 → left = 3
  left=3, right=5

Iteration 2:
  mid = (3+5+1)/2 = 4, nums[4] = 8
  8 <= 8 → left = 4
  left=4, right=5

Iteration 3:
  mid = (4+5+1)/2 = 5, nums[5] = 10
  10 > 8 → right = 4
  left=4, right=4

rightBound = 4
```

Result: `[3, 4]`

### Generation Pattern

**Linear search:**
```csharp
public int[] SearchRange(int[] nums, int target) {
    int left = -1, right = -1;
    for (int i = 0; i < nums.Length; i++) {
        if (nums[i] == target) {
            if (left == -1) left = i;
            right = i;
        }
    }
    return new int[] { left, right };
}
// Time: O(n)
```

---

## Step 2: Brute Force

Linear scan to find first and last occurrence: O(n).

---

## Step 3: Optimization Ideas

### Key Insights
1. **Two binary searches:** Find left boundary, then right boundary
2. **Different templates:** Left uses template 2, right uses template 3
3. **Rounding matters:** Right boundary search rounds mid up to avoid infinite loop

---

## Step 4: Optimal Solution

```csharp
public int[] SearchRange(int[] nums, int target) {
    if (nums.Length == 0) return new int[] { -1, -1 };
    
    int leftBound = FindLeftBoundary(nums, target);
    
    // If target not found
    if (leftBound == nums.Length || nums[leftBound] != target) {
        return new int[] { -1, -1 };
    }
    
    int rightBound = FindRightBoundary(nums, target);
    
    return new int[] { leftBound, rightBound };
}

private int FindLeftBoundary(int[] nums, int target) {
    int left = 0, right = nums.Length;
    
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            left = mid + 1;
        } else {
            right = mid;
        }
    }
    
    return left;
}

private int FindRightBoundary(int[] nums, int target) {
    int left = 0, right = nums.Length - 1;
    
    while (left < right) {
        // Round up to avoid infinite loop
        int mid = left + (right - left + 1) / 2;
        if (nums[mid] <= target) {
            left = mid;
        } else {
            right = mid - 1;
        }
    }
    
    return left;
}
```

**Complexity:**
- Time: O(log n) - two binary searches
- Space: O(1)

**Alternative (single binary search + expand):**
```csharp
public int[] SearchRange(int[] nums, int target) {
    int index = Array.BinarySearch(nums, target);
    
    if (index < 0) return new int[] { -1, -1 };
    
    // Expand left
    int left = index;
    while (left > 0 && nums[left - 1] == target) left--;
    
    // Expand right
    int right = index;
    while (right < nums.Length - 1 && nums[right + 1] == target) right++;
    
    return new int[] { left, right };
}
```
This is O(n) in worst case (all elements are target), but simpler.

---

## Edge Cases

1. **Empty array:** `[], target=0` → `[-1,-1]`
2. **Single occurrence:** `[1,2,3], target=2` → `[1,1]`
3. **Multiple occurrences:** `[2,2,2,2], target=2` → `[0,3]`
4. **Target not found:** `[1,3,5], target=2` → `[-1,-1]`
5. **Target at boundaries:** `[1,2,2], target=2` → `[1,2]`

---

## Related Problems

1. **Search Insert Position (LeetCode #35)** - Finding left boundary
2. **Binary Search (LeetCode #704)** - Basic template
3. **Count of Range Sum (LeetCode #327)** - Using boundaries

---

## Tags

`#binary-search` `#boundaries` `#left-boundary` `#right-boundary` `#duplicates` `#range`
