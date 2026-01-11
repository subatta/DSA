# Binary Search (Classic)

**Difficulty:** Easy  
**LeetCode Problem:** #704  
**Tags:** `Binary Search`, `Array`, `Search`, `Divide and Conquer`

---

## Problem Statement

Given an array of integers `nums` which is sorted in ascending order, and an integer `target`, write a function to search `target` in `nums`. If `target` exists, return its index. Otherwise, return `-1`.

You must write an algorithm with `O(log n)` runtime complexity.

**Constraints:**
- `1 <= nums.length <= 10^4`
- `-10^4 < nums[i], target < 10^4`
- All integers in `nums` are unique
- `nums` is sorted in ascending order

**Example 1:**
```
Input: nums = [-1,0,3,5,9,12], target = 9
Output: 4
Explanation: 9 exists in nums and its index is 4
```

**Example 2:**
```
Input: nums = [-1,0,3,5,9,12], target = 2
Output: -1
Explanation: 2 does not exist in nums so return -1
```

---

## Step 1: State Space

### Problem Visualization

For `nums = [-1,0,3,5,9,12]`, `target = 9`:

```
Initial array:
Index:  0   1  2  3  4   5
Value: -1   0  3  5  9  12
       └───────────┴───────┘
       left=0  mid=2  right=5

Step 1: mid=2, nums[2]=3 < 9
  Search right half: [5,9,12]
  
       left=3  mid=4  right=5
Value:  5   9  12
           ↑
           
Step 2: mid=4, nums[4]=9 = 9
  Found! Return 4
```

Elimination process:
```
[−1, 0, 3, 5, 9, 12]  mid=3, 5<9 → search right
          └─────────┘
[5, 9, 12]            mid=1, 9=9 → found!
    ↑
```

### Core Question

**How can we eliminate half of the search space in each step to find the target efficiently?**

Binary search leverages the sorted property: if `target > mid`, all elements to the left of mid are also smaller than target (can be eliminated).

### Deriving from First Principles

**Observation 1: Sorted array property**
For sorted array, if `nums[i] < target`, then all `nums[j]` where `j < i` are also `< target`.
Similarly, if `nums[i] > target`, then all `nums[j]` where `j > i` are also `> target`.

**Observation 2: Divide and conquer**
At each step, we can:
- Compare middle element with target
- Eliminate half of the remaining array
- This halving process leads to O(log n) complexity

**Observation 3: Three cases at middle**
```
If nums[mid] == target: Found! Return mid
If nums[mid] < target:  Search right half [mid+1, right]
If nums[mid] > target:  Search left half [left, mid-1]
```

**Observation 4: Loop invariant**
Maintain invariant: "If target exists, it's in range [left, right]"
- Initialize: left=0, right=n-1 (covers entire array)
- Update: Adjust left or right based on comparison
- Terminate: When left > right (range is empty, target not found)

**Observation 5: Integer overflow prevention**
Computing mid as `(left + right) / 2` can overflow.
Better: `mid = left + (right - left) / 2`

**Formula:**
```
while left <= right:
  mid = left + (right - left) / 2
  if nums[mid] == target:
    return mid
  else if nums[mid] < target:
    left = mid + 1    // Search right
  else:
    right = mid - 1   // Search left
return -1             // Not found
```

### State Space Structure

**Type:** Search space represented by range [left, right].

**Structure:**
- Two pointers: left (inclusive), right (inclusive)
- Middle point: mid = left + (right - left) / 2
- Range shrinks by half each iteration

**Cardinality:** 
- Initial size: n
- After k iterations: n / 2^k
- Terminates when size = 0 (left > right)
- O(log n) iterations

**Key Property:** Monotonicity - sorted array allows binary decision at each step.

### Example Computation

Input: `nums = [-1,0,3,5,9,12]`, `target = 9`

```
Initial: left=0, right=5

Iteration 1:
  mid = 0 + (5-0)/2 = 2
  nums[2] = 3
  3 < 9 → search right
  left = 3, right = 5

Iteration 2:
  mid = 3 + (5-3)/2 = 4
  nums[4] = 9
  9 == 9 → Found!
  Return 4
```

Example where target not found: `target = 2`
```
Iteration 1:
  mid = 2, nums[2] = 3
  3 > 2 → search left
  left = 0, right = 1

Iteration 2:
  mid = 0 + (1-0)/2 = 0
  nums[0] = -1
  -1 < 2 → search right
  left = 1, right = 1

Iteration 3:
  mid = 1
  nums[1] = 0
  0 < 2 → search right
  left = 2, right = 1
  left > right → Not found, return -1
```

### Generation Pattern

**Linear Search (brute force):**
```csharp
public int Search(int[] nums, int target) {
    for (int i = 0; i < nums.Length; i++) {
        if (nums[i] == target) return i;
    }
    return -1;
}
// Time: O(n), Space: O(1)
```

**Binary Search:**
```csharp
public int Search(int[] nums, int target) {
    int left = 0, right = nums.Length - 1;
    
    while (left <= right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] == target) return mid;
        else if (nums[mid] < target) left = mid + 1;
        else right = mid - 1;
    }
    
    return -1;
}
// Time: O(log n), Space: O(1)
```

---

## Step 2: Brute Force

```csharp
public int Search(int[] nums, int target) {
    // Linear scan
    for (int i = 0; i < nums.Length; i++) {
        if (nums[i] == target) {
            return i;
        }
    }
    return -1;
}
```

**Complexity:**
- Time: O(n) - scan entire array
- Space: O(1)

**Why this works:**
Check each element until we find target or exhaust array.

**Why it's slow:**
Doesn't use the sorted property. Checks every element even though many can be eliminated.

---

## Step 3: Optimization Ideas

### Key Insights
1. **Sorted array allows elimination:** Compare with middle element to eliminate half
2. **Logarithmic reduction:** n → n/2 → n/4 → ... → 1 takes O(log n) steps
3. **Iterative approach:** Use two pointers to track search range

### Why Binary Search Works
- **Monotonicity:** Sorted array has the property that comparison at any point tells us which half to search
- **Halving:** Each comparison eliminates half the remaining elements
- **Termination:** Range eventually becomes empty (left > right)

---

## Step 4: Optimal Solution

```csharp
public int Search(int[] nums, int target) {
    int left = 0;
    int right = nums.Length - 1;
    
    while (left <= right) {
        // Prevent integer overflow
        int mid = left + (right - left) / 2;
        
        if (nums[mid] == target) {
            return mid;  // Found target
        }
        else if (nums[mid] < target) {
            left = mid + 1;  // Search right half
        }
        else {
            right = mid - 1;  // Search left half
        }
    }
    
    return -1;  // Target not found
}
```

**Complexity:**
- Time: O(log n) - halve search space each iteration
- Space: O(1) - only use a few variables

**Why it's faster:**
Each comparison eliminates half of the remaining elements. With n elements, we need at most log₂(n) comparisons.

**Key Mechanics:**
1. **Initialization:** `left = 0, right = n - 1` covers entire array
2. **Loop condition:** `left <= right` ensures we check all valid ranges
3. **Mid calculation:** `left + (right - left) / 2` avoids overflow
4. **Three-way decision:** Equal (found), less than (go right), greater (go left)
5. **Pointer update:** `mid + 1` or `mid - 1` excludes mid itself

**Binary Search Template:**
This is the fundamental template. Variations differ in:
- Loop condition (`<=` vs `<`)
- Pointer update (`mid+1` vs `mid`)
- What to return (index, element, insertion point)

---

## Edge Cases

1. **Empty array:** (Not possible per constraints, but would return -1)
2. **Single element - found:** `[5], target=5` → `0`
3. **Single element - not found:** `[5], target=3` → `-1`
4. **Target at start:** `[1,2,3], target=1` → `0`
5. **Target at end:** `[1,2,3], target=3` → `2`
6. **Target not in range:** `[1,3,5], target=4` → `-1`

---

## Binary Search Template Variations

### Template 1: Classic (used above)
```csharp
while (left <= right) {
    int mid = left + (right - left) / 2;
    if (nums[mid] == target) return mid;
    else if (nums[mid] < target) left = mid + 1;
    else right = mid - 1;
}
return -1;
```
- **When:** Searching for exact value
- **Loop:** `left <= right`
- **Update:** `mid ± 1`
- **Return:** Index or -1

### Template 2: Left boundary
```csharp
while (left < right) {
    int mid = left + (right - left) / 2;
    if (nums[mid] < target) left = mid + 1;
    else right = mid;
}
return left;
```
- **When:** Finding insertion point or leftmost occurrence
- **Loop:** `left < right`
- **Update:** `right = mid` (keep mid as candidate)

### Template 3: Right boundary
```csharp
while (left < right) {
    int mid = left + (right - left) / 2 + 1;  // Round up
    if (nums[mid] <= target) left = mid;
    else right = mid - 1;
}
return left;
```
- **When:** Finding rightmost occurrence

---

## Related Problems

1. **Search Insert Position (LeetCode #35)** - Find insertion point
2. **First Bad Version (LeetCode #278)** - Binary search on version numbers
3. **Search in Rotated Sorted Array (LeetCode #33)** - Modified binary search
4. **Find Peak Element (LeetCode #162)** - Binary search on peaks
5. **Sqrt(x) (LeetCode #69)** - Binary search on answer space

---

## Pattern Recognition

**Problem Asks For:**
- Search in sorted array
- O(log n) complexity required
- Find target index

**This Suggests:**
- Sorted or monotonic property present
- Can make binary decision to eliminate half
- → **Binary Search pattern**

**Key Indicators:**
- Input is sorted
- Need to find specific value or position
- O(log n) hint or large input size (up to 10^6)

---

## Tags

`#binary-search` `#sorted-array` `#divide-and-conquer` `#template-1` `#classic` `#logarithmic`
