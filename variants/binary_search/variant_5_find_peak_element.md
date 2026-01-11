# Find Peak Element

**Difficulty:** Medium  
**LeetCode Problem:** #162  
**Tags:** `Binary Search`, `Array`, `Peak Finding`

---

## Problem Statement

A peak element is an element that is strictly greater than its neighbors.

Given a 0-indexed integer array `nums`, find a peak element, and return its index. If the array contains multiple peaks, return the index to any of the peaks.

You may imagine that `nums[-1] = nums[n] = -∞`. In other words, an element is always considered to be strictly greater than a neighbor that is outside the array.

You must write an algorithm that runs in `O(log n)` time.

**Constraints:**
- `1 <= nums.length <= 1000`
- `-2^31 <= nums[i] <= 2^31 - 1`
- `nums[i] != nums[i + 1]` for all valid `i`

**Example 1:**
```
Input: nums = [1,2,3,1]
Output: 2
Explanation: 3 is a peak element and your function should return the index 2
```

**Example 2:**
```
Input: nums = [1,2,1,3,5,6,4]
Output: 5
Explanation: Your function can return either index 1 (peak at 2) or index 5 (peak at 6)
```

---

## Step 1: State Space

### Problem Visualization

For `nums = [1,2,1,3,5,6,4]`:

```
Height
  6           ▲
  5         ▲ █
  4       ▲ █ █ ▲
  3     ▲ █ █ █ █
  2   ▲ █ █ █ █ █
  1 ▲ █ █ █ █ █ █
  0 +─+─+─+─+─+─+─+
    0 1 2 3 4 5 6

Peaks at index 1 (value 2) and index 5 (value 6)
```

Peak definition:
```
Index 1: nums[0]=1 < nums[1]=2 > nums[2]=1  ✓ Peak
Index 5: nums[4]=5 < nums[5]=6 > nums[6]=4  ✓ Peak
```

### Core Question

**How can we find any peak in O(log n) time without checking all elements?**

Key insight: If we're at position where `nums[mid] < nums[mid+1]`, then there must be a peak to the right (array is ascending, and boundary is treated as -∞).

### Deriving from First Principles

**Observation 1: Boundary conditions**
- `nums[-1] = -∞` and `nums[n] = -∞`
- Single element is always a peak
- If array ends with ascending, last element is peak
- If array starts with descending, first element is peak

**Observation 2: Ascending slope guarantees peak**
If `nums[mid] < nums[mid+1]`:
```
   ? ← peak must exist
 ▲
█ ←mid
```
Since we're going up and the right boundary is -∞, we must reach a peak before hitting the boundary.

**Observation 3: Descending slope guarantees peak**
If `nums[mid] > nums[mid+1]`:
```
peak must exist → ?
                  ▼
                  █ ←mid+1
```
Since we're going down and the left boundary is -∞, we must have come from a peak.

**Observation 4: Binary search on slope**
- If ascending (nums[mid] < nums[mid+1]): Search right half
- If descending (nums[mid] > nums[mid+1]): Search left half (including mid)
- This converges to a peak

**Observation 5: Why this works**
At each step, we move toward the "higher" side. Since boundaries are -∞, we're guaranteed to find a peak. We don't need to check both neighbors explicitly—the slope tells us where a peak must exist.

**Formula:**
```
left = 0, right = n - 1
while left < right:
  mid = left + (right - left) / 2
  if nums[mid] < nums[mid+1]:
    left = mid + 1  // Peak is to the right
  else:
    right = mid     // Peak is here or to the left
return left
```

### State Space Structure

**Type:** Finding local maximum (peak).

**Structure:**
- Range [left, right] where peak exists
- Follow ascending slope to guarantee finding peak

**Cardinality:** O(log n) iterations.

**Key Property:** Always move toward higher neighbor; boundary conditions ensure peak exists.

### Example Computation

Input: `nums = [1,2,1,3,5,6,4]`

```
Iteration 1:
  left=0, right=6, mid=3
  nums[3]=3, nums[4]=5
  3 < 5 → ascending, peak to the right
  left = 4
  
Iteration 2:
  left=4, right=6, mid=5
  nums[5]=6, nums[6]=4
  6 > 4 → descending, peak here or left
  right = 5

Iteration 3:
  left=5, right=5
  Loop terminates, return 5
  
nums[5]=6 is a peak
```

Alternative path (if mid=2 initially):
```
Iteration 1:
  mid=2
  nums[2]=1, nums[3]=3
  1 < 3 → search right
  left = 3

Iteration 2:
  left=3, right=6, mid=4
  nums[4]=5, nums[5]=6
  5 < 6 → search right
  left = 5

left=5, right=6, mid=5
  nums[5]=6, nums[6]=4
  6 > 4 → search left
  right = 5

Return 5
```

### Generation Pattern

**Linear search:**
```csharp
public int FindPeakElement(int[] nums) {
    for (int i = 0; i < nums.Length - 1; i++) {
        if (nums[i] > nums[i + 1]) {
            return i;  // First descending point is a peak
        }
    }
    return nums.Length - 1;  // Last element if all ascending
}
// Time: O(n)
```

---

## Step 2: Brute Force

Check each element to see if it's greater than both neighbors: O(n).

---

## Step 3: Optimization Ideas

### Key Insights
1. **Follow the slope:** Move toward higher neighbor
2. **Boundary guarantees peak:** Since boundaries are -∞, ascending always leads to peak
3. **Binary search on slope direction:** Don't need exact peak position, just move in right direction

---

## Step 4: Optimal Solution

```csharp
public int FindPeakElement(int[] nums) {
    int left = 0, right = nums.Length - 1;
    
    while (left < right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] < nums[mid + 1]) {
            // Ascending slope, peak must be to the right
            left = mid + 1;
        } else {
            // Descending slope, peak is here or to the left
            right = mid;
        }
    }
    
    return left;
}
```

**Complexity:**
- Time: O(log n)
- Space: O(1)

**Why it works:**
- We follow the ascending direction
- Boundary conditions ensure we'll hit a peak
- No need to explicitly check both neighbors

**Key Mechanics:**
1. **Compare mid with mid+1** (not mid-1) to determine slope
2. **Move toward higher side**
3. **Loop terminates at peak**

---

## Edge Cases

1. **Single element:** `[1]` → `0`
2. **All ascending:** `[1,2,3,4]` → `3` (last element)
3. **All descending:** `[4,3,2,1]` → `0` (first element)
4. **Multiple peaks:** Can return any
5. **Two elements:** `[1,2]` → `1` or `[2,1]` → `0`

---

## Related Problems

1. **Find Minimum in Rotated Sorted Array (LeetCode #153)** - Similar binary search
2. **Peak Index in a Mountain Array (LeetCode #852)** - Guaranteed single peak
3. **Find in Mountain Array (LeetCode #1095)** - Binary search on mountain

---

## Tags

`#binary-search` `#peak-finding` `#local-maximum` `#slope` `#greedy`
