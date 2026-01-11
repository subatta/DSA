# Search in Rotated Sorted Array

**Difficulty:** Medium  
**LeetCode Problem:** #33  
**Tags:** `Binary Search`, `Array`, `Rotated Array`

---

## Problem Statement

There is an integer array `nums` sorted in ascending order (with distinct values).

Prior to being passed to your function, `nums` is possibly rotated at an unknown pivot index `k` (`1 <= k < nums.length`) such that the resulting array is `[nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]` (0-indexed).

Given the array `nums` after the possible rotation and an integer `target`, return the index of `target` if it is in `nums`, or `-1` if it is not in `nums`.

You must write an algorithm with `O(log n)` runtime complexity.

**Example 1:**
```
Input: nums = [4,5,6,7,0,1,2], target = 0
Output: 4
```

**Example 2:**
```
Input: nums = [4,5,6,7,0,1,2], target = 3
Output: -1
```

**Example 3:**
```
Input: nums = [1], target = 0
Output: -1
```

---

## Step 1: State Space

### Problem Visualization

Original sorted: `[0,1,2,4,5,6,7]`
Rotated at k=4: `[4,5,6,7,0,1,2]`

```
Visualization:
       7
     6
   5
 4
                 2
               1
             0
 └─────────┘ └─────┘
 Sorted part  Sorted part
 (left)       (right)
```

Two sorted subarrays:
```
[4, 5, 6, 7, | 0, 1, 2]
 ←sorted→      ←sorted→
    pivot point here
```

Key property: At least one half is always sorted!

### Core Question

**How do we determine which half to search when the array is rotated?**

Even though the full array isn't sorted, one of the two halves [left, mid] or [mid, right] is always sorted. We can use the sorted half to decide where target might be.

### Deriving from First Principles

**Observation 1: One half is always sorted**
When we pick a mid point:
- If `nums[left] <= nums[mid]`: Left half [left, mid] is sorted
- Else: Right half [mid, right] is sorted

**Observation 2: Check target in sorted half**
If we identify the sorted half, we can check if target lies in that range:
- If yes: Search that half
- If no: Search the other half

**Observation 3: Left half sorted**
If `nums[left] <= nums[mid]` (left half is sorted):
```
If nums[left] <= target < nums[mid]:
  Search left half (target is in sorted range)
Else:
  Search right half
```

**Observation 4: Right half sorted**
If left half is not sorted, then right half must be sorted:
```
If nums[mid] < target <= nums[right]:
  Search right half (target is in sorted range)
Else:
  Search left half
```

**Observation 5: Standard binary search structure**
We still use standard binary search, but with modified condition to handle rotation:
- Check which half is sorted
- Check if target is in sorted half's range
- Adjust left/right accordingly

**Formula:**
```
while left <= right:
  mid = left + (right - left) / 2
  if nums[mid] == target:
    return mid
  
  if nums[left] <= nums[mid]:  // Left half sorted
    if nums[left] <= target < nums[mid]:
      right = mid - 1  // Search left
    else:
      left = mid + 1   // Search right
  else:  // Right half sorted
    if nums[mid] < target <= nums[right]:
      left = mid + 1   // Search right
    else:
      right = mid - 1  // Search left
```

### State Space Structure

**Type:** Modified binary search on rotated sorted array.

**Structure:**
- Range [left, right] where target might be
- At each step, identify sorted half and check if target is there

**Cardinality:** O(log n) iterations, same as binary search.

**Key Property:** Rotation creates two sorted subarrays; at least one half from mid is always sorted.

### Example Computation

Input: `nums = [4,5,6,7,0,1,2]`, `target = 0`

```
Iteration 1:
  left=0, right=6, mid=3
  nums[3] = 7
  7 != 0
  nums[0]=4 <= nums[3]=7 → left half [4,5,6,7] is sorted
  Is 0 in [4, 7)? No (0 < 4)
  Search right half: left = 4
  
Iteration 2:
  left=4, right=6, mid=5
  nums[5] = 1
  1 != 0
  nums[4]=0 > nums[5]=1 → left half NOT sorted, right half is sorted
  Is 0 in (1, 2]? No (0 < 1)
  Search left half: right = 4

Iteration 3:
  left=4, right=4, mid=4
  nums[4] = 0
  0 == 0 → Found! Return 4
```

Example with target not found: `target = 3`
```
Iteration 1:
  left=0, right=6, mid=3
  nums[3] = 7
  Left half sorted: [4,7]
  Is 3 in [4,7)? No (3 < 4)
  Search right: left = 4

Iteration 2:
  left=4, right=6, mid=5
  nums[5] = 1
  Right half sorted: (1,2]
  Is 3 in (1,2]? No (3 > 2)
  Search left: right = 4

Iteration 3:
  left=4, right=4, mid=4
  nums[4] = 0
  0 != 3
  Left half "sorted" (single element)
  Is 3 in [0,0]? No
  Search right: left = 5

left > right → Not found, return -1
```

### Generation Pattern

**Linear search:**
```csharp
public int Search(int[] nums, int target) {
    for (int i = 0; i < nums.Length; i++) {
        if (nums[i] == target) return i;
    }
    return -1;
}
```

---

## Step 2: Brute Force

Linear search works regardless of rotation: O(n).

---

## Step 3: Optimization Ideas

### Key Insights
1. **Half is always sorted:** Identify it using `nums[left] <= nums[mid]`
2. **Range check in sorted half:** Use sorted property to eliminate half
3. **Modified binary search:** Same structure, different conditions

---

## Step 4: Optimal Solution

```csharp
public int Search(int[] nums, int target) {
    int left = 0, right = nums.Length - 1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] == target) {
            return mid;
        }
        
        // Determine which half is sorted
        if (nums[left] <= nums[mid]) {
            // Left half is sorted
            if (nums[left] <= target && target < nums[mid]) {
                right = mid - 1;  // Target in left half
            } else {
                left = mid + 1;   // Target in right half
            }
        } else {
            // Right half is sorted
            if (nums[mid] < target && target <= nums[right]) {
                left = mid + 1;   // Target in right half
            } else {
                right = mid - 1;  // Target in left half
            }
        }
    }
    
    return -1;
}
```

**Complexity:**
- Time: O(log n)
- Space: O(1)

**Key Mechanics:**
1. **Identify sorted half:** `nums[left] <= nums[mid]`
2. **Check if target in sorted half's range**
3. **Adjust pointers based on where target might be**

---

## Edge Cases

1. **No rotation:** `[1,2,3,4,5], target=3` → Works as normal binary search
2. **Rotated by 1:** `[5,1,2,3,4], target=1` → `1`
3. **Single element:** `[1], target=1` → `0`
4. **Target at rotation point:** `[4,5,6,7,0,1,2], target=0` → `4`
5. **All elements rotated to end:** `[2,3,4,5,1], target=1` → `4`

---

## Related Problems

1. **Search in Rotated Sorted Array II (LeetCode #81)** - With duplicates
2. **Find Minimum in Rotated Sorted Array (LeetCode #153)** - Find pivot
3. **Binary Search (LeetCode #704)** - Standard template

---

## Tags

`#binary-search` `#rotated-array` `#modified-binary-search` `#two-sorted-halves`
