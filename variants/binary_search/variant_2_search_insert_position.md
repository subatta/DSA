# Search Insert Position

**Difficulty:** Easy  
**LeetCode Problem:** #35  
**Tags:** `Binary Search`, `Array`, `Insertion Point`

---

## Problem Statement

Given a sorted array of distinct integers and a target value, return the index if the target is found. If not, return the index where it would be if it were inserted in order.

You must write an algorithm with `O(log n)` runtime complexity.

**Constraints:**
- `1 <= nums.length <= 10^4`
- `-10^4 <= nums[i] <= 10^4`
- `nums` contains distinct values sorted in ascending order
- `-10^4 <= target <= 10^4`

**Example 1:**
```
Input: nums = [1,3,5,6], target = 5
Output: 2
```

**Example 2:**
```
Input: nums = [1,3,5,6], target = 2
Output: 1
Explanation: 2 should be inserted at index 1
```

**Example 3:**
```
Input: nums = [1,3,5,6], target = 7
Output: 4
```

---

## Step 1: State Space

### Problem Visualization

For `nums = [1,3,5,6]`:

```
Target = 5 (exists):
[1, 3, 5, 6]
       ↑
    index 2

Target = 2 (insert between 1 and 3):
[1, 3, 5, 6]
    ↑
 insert at 1
Result: [1, 2, 3, 5, 6]

Target = 7 (insert at end):
[1, 3, 5, 6]
            ↑
         insert at 4
Result: [1, 3, 5, 6, 7]

Target = 0 (insert at start):
[1, 3, 5, 6]
 ↑
insert at 0
Result: [0, 1, 3, 5, 6]
```

### Core Question

**What is the leftmost position where target should be placed to maintain sorted order?**

This is the index of the first element ≥ target (or n if all elements < target).

### Deriving from First Principles

**Observation 1: Two cases**
1. Target exists → return its index
2. Target doesn't exist → return insertion index

Both cases can be unified: find leftmost position ≥ target.

**Observation 2: Insertion point definition**
The insertion point is where:
- All elements to the left are < target
- All elements from this point onward are ≥ target

**Observation 3: Binary search finds boundary**
We're looking for the "boundary" between elements < target and elements ≥ target:
```
[1, 3, 5, 6], target = 4
 <  <  ≥ ≥
       ↑
   boundary at index 2
```

**Observation 4: Modified binary search**
Instead of exact match, we seek the leftmost position where we could insert target:
- If `nums[mid] < target`: Answer is to the right, `left = mid + 1`
- If `nums[mid] >= target`: Answer could be here or to the left, `right = mid`

**Observation 5: Loop terminates at answer**
Using `left < right` and `right = mid` (not `mid - 1`), the loop terminates when `left == right`, which is the insertion point.

**Formula:**
```
left = 0, right = n
while left < right:
  mid = left + (right - left) / 2
  if nums[mid] < target:
    left = mid + 1
  else:
    right = mid
return left
```

### State Space Structure

**Type:** Search for boundary/insertion point.

**Structure:**
- Range [left, right) where answer lies
- Invariant: All elements in [0, left) are < target
- Invariant: All elements in [right, n) are ≥ target

**Cardinality:** n+1 possible positions (before first element, after each element).

**Key Property:** Binary search narrows range until left == right.

### Example Computation

Input: `nums = [1,3,5,6]`, `target = 2`

```
Initial: left=0, right=4

Iteration 1:
  mid = 0 + (4-0)/2 = 2
  nums[2] = 5
  5 >= 2 → right = 2
  left=0, right=2

Iteration 2:
  mid = 0 + (2-0)/2 = 1
  nums[1] = 3
  3 >= 2 → right = 1
  left=0, right=1

Iteration 3:
  mid = 0 + (1-0)/2 = 0
  nums[0] = 1
  1 < 2 → left = 1
  left=1, right=1

Loop terminates: return 1
```

Target = 7:
```
Initial: left=0, right=4

Iteration 1:
  mid = 2, nums[2] = 5
  5 < 7 → left = 3
  left=3, right=4

Iteration 2:
  mid = 3, nums[3] = 6
  6 < 7 → left = 4
  left=4, right=4

Loop terminates: return 4
```

### Generation Pattern

**Linear search:**
```csharp
public int SearchInsert(int[] nums, int target) {
    for (int i = 0; i < nums.Length; i++) {
        if (nums[i] >= target) return i;
    }
    return nums.Length;
}
// Time: O(n)
```

---

## Step 2: Brute Force

```csharp
public int SearchInsert(int[] nums, int target) {
    for (int i = 0; i < nums.Length; i++) {
        if (nums[i] >= target) {
            return i;
        }
    }
    // Target is larger than all elements
    return nums.Length;
}
```

**Complexity:**
- Time: O(n)
- Space: O(1)

---

## Step 3: Optimization Ideas

### Key Insights
1. **Find boundary, not exact match:** Leftmost position ≥ target
2. **Binary search template 2:** Uses `left < right` and `right = mid`
3. **Handles both cases:** Whether target exists or not

---

## Step 4: Optimal Solution

```csharp
public int SearchInsert(int[] nums, int target) {
    int left = 0;
    int right = nums.Length;
    
    while (left < right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] < target) {
            left = mid + 1;  // Answer is to the right
        } else {
            right = mid;  // Answer is here or to the left
        }
    }
    
    return left;
}
```

**Complexity:**
- Time: O(log n)
- Space: O(1)

**Alternative (explicit target check):**
```csharp
public int SearchInsert(int[] nums, int target) {
    int left = 0, right = nums.Length - 1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    
    return left;
}
```

Both solutions work. First is cleaner (finds boundary directly), second is more explicit (checks for exact match first).

---

## Edge Cases

1. **Insert at start:** `[3,5,7], target=1` → `0`
2. **Insert at end:** `[3,5,7], target=9` → `3`
3. **Target exists:** `[1,3,5], target=3` → `1`
4. **Single element:** `[5], target=3` → `0`
5. **All elements smaller:** `[1,2,3], target=5` → `3`

---

## Related Problems

1. **Binary Search (LeetCode #704)** - Basic template
2. **Find First and Last Position (LeetCode #34)** - Left/right boundaries
3. **Search in Rotated Array (LeetCode #33)** - Modified binary search

---

## Tags

`#binary-search` `#insertion-point` `#boundary` `#template-2` `#leftmost`
