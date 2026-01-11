# Pattern: Binary Search

## Pattern Description
Binary Search is a divide-and-conquer technique used to efficiently search or optimize over a **monotonic search space**.

### What is it?
- A method that repeatedly halves the search space based on a condition that is monotonic (false → true or true → false)
- Divides the problem space in half at each step until the target is found or space is exhausted
- Requires a way to determine which half to eliminate

### What abstract problem does it solve?
- Searching in sorted data
- Finding a boundary / transition point in a monotonic space
- Searching for an answer in a monotonic function (a.k.a. "Binary Search on Answer")
- Optimizing a value subject to a feasibility constraint

### Real-world / interview variants (simplest → harder)
1. Classic Binary Search (find exact value in sorted array)
2. First / Last Occurrence (find boundary in duplicates)
3. Search Insert Position (find insertion point)
4. Peak Element (find local maximum)
5. Rotated Sorted Array (search in partially sorted data)
6. Binary Search on Answer (minimize/maximize feasible value)
7. Search in 2D Matrix (2D binary search)
8. Minimum in Rotated Sorted Array (find pivot)

---

## Canonical Binary Search Skeleton

```csharp
int BinarySearch(int[] nums, int target)
{
    int left = 0, right = nums.Length - 1;

    while (left <= right)
    {
        int mid = left + (right - left) / 2;

        if (nums[mid] == target)
            return mid;
        else if (nums[mid] < target)
            left = mid + 1;
        else
            right = mid - 1;
    }

    return -1;
}
```

---

<details>
<summary><b>Variant #1: Classic Binary Search</b></summary>

## Variant #1: Classic Binary Search

### Input/Output:
- Input: Sorted array `nums`, integer `target`
- Output: Index of `target` or `-1`

## Full State Space
For `nums = [1,3,5,7,9]`, target = 7

Possible indices checked:
```
[0..4] → mid=2
[3..4] → mid=3
```

## Expected / Pruned State Space
Only log₂(n) mid-points are explored.

## State Space → Output
We discard half the array at every step based on comparison.

### Brute Force Canonical Skeleton:
```csharp
int LinearSearchBruteForce(int[] nums, int target)
{
    // Check every element sequentially
    for (int i = 0; i < nums.Length; i++)
    {
        if (nums[i] == target)
            return i;
    }
    return -1; // Not found
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `nums = [1,3,5,7,9], target = 7`:

| i | nums[i] | match | action |
|---|--------|-------|--------|
| 0 | 1 | no | continue |
| 1 | 3 | no | continue |
| 2 | 5 | no | continue |
| 3 | 7 | yes | return 3 |

### Optimized Solution from Canonical Skeleton:
```csharp
int BinarySearch(int[] nums, int target)
{
    int left = 0, right = nums.Length - 1;

    while (left <= right)
    {
        int mid = left + (right - left) / 2;

        if (nums[mid] == target)
            return mid; // Found
        else if (nums[mid] < target)
            left = mid + 1; // Search right half
        else
            right = mid - 1; // Search left half
    }

    return -1; // Not found
}
```

### Explanation of Pruning:
- Compare mid element with target to determine which half to eliminate
- If `nums[mid] < target`, target must be in right half → eliminate left half
- If `nums[mid] > target`, target must be in left half → eliminate right half
- Each comparison eliminates half the search space
- Loop terminates when `left > right` (search space exhausted)

### Optimized Solution Code Walkthrough / Variable Trace:
For `nums = [1,3,5,7,9], target = 7`:

| left | right | mid | nums[mid] | comparison | action |
|-----|------|-----|-----------|------------|--------|
| 0 | 4 | 2 | 5 | 5 < 7 | left = 3 |
| 3 | 4 | 3 | 7 | 7 == 7 | found, return 3 |

### Big-O Analysis:
- **Brute Force:** O(n) → check every element
- **Optimized:** O(log n) → halve search space each iteration
- **Space Complexity:** O(1) → only a few variables

</details>

<details>
<summary><b>Variant #2: First Occurrence of Target</b></summary>

## Variant #2: First Occurrence of Target

### Input/Output:
- Input: `nums = [1,2,2,2,3], target = 2`
- Output: `1` (first occurrence of 2)

### Full State Space:
All occurrences of the target need to be considered.
```
For nums = [1,2,2,2,3], target = 2
All indices of 2: {1, 2, 3}
We want the leftmost: 1
```

### Expected/Pruned State Space:
We want the **leftmost valid index**, not all occurrences.
```
Binary search with modification: continue searching left even after finding target
```

### State Space Leading to Output:
When target is found, don't return immediately - continue searching left half to find earlier occurrence.

### Brute Force Canonical Skeleton:
```csharp
int FindFirstBruteForce(int[] nums, int target)
{
    for (int i = 0; i < nums.Length; i++)
    {
        if (nums[i] == target)
            return i; // First occurrence from left
    }
    return -1;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `nums = [1,2,2,2,3], target = 2`:

| i | nums[i] | match | action |
|---|--------|-------|--------|
| 0 | 1 | no | continue |
| 1 | 2 | yes | return 1 |

### Optimized Solution from Canonical Skeleton:
```csharp
int FindFirst(int[] nums, int target)
{
    int left = 0, right = nums.Length - 1;
    int result = -1;

    while (left <= right)
    {
        int mid = left + (right - left) / 2;

        if (nums[mid] == target)
        {
            result = mid;     // Record potential answer
            right = mid - 1;  // Keep searching left
        }
        else if (nums[mid] < target)
            left = mid + 1;
        else
            right = mid - 1;
    }

    return result;
}
```

### Explanation of Pruning:
- **Key difference:** When target is found, don't return immediately
- Store current index as potential result and continue searching left half
- This finds the leftmost occurrence in O(log n) time
- The `result` variable tracks the best (leftmost) answer found so far

### Optimized Solution Code Walkthrough / Variable Trace:
For `nums = [1,2,2,2,3], target = 2`:

| left | right | mid | nums[mid] | action | result |
|-----|------|-----|-----------|--------|--------|
| 0 | 4 | 2 | 2 | found, search left | 2 |
| 0 | 1 | 0 | 1 | 1 < 2, search right | 2 |
| 1 | 1 | 1 | 2 | found, search left | 1 |
| 1 | 0 | - | - | left > right, done | 1 |

### Big-O Analysis:
- **Brute Force:** O(n) → worst case check all elements
- **Optimized:** O(log n) → binary search with continuation
- **Space Complexity:** O(1) → only variables

</details>

<details>
<summary><b>Variant #3: Search Insert Position</b></summary>

## Variant #3: Search Insert Position

### Input/Output:
- Input: `nums = [1,3,5,6], target = 2`
- Output: `1` (insert at index 1 to maintain sorted order)

### Full State Space:
All possible insertion positions in the array.
```
For nums = [1,3,5,6], target = 2
Possible positions: {0, 1, 2, 3, 4}
Correct position: 1 (between 1 and 3)
```

### Expected/Pruned State Space:
Only one correct insertion position exists to maintain sorted order.
```
Binary search naturally finds this position when left > right
```

### State Space Leading to Output:
The `left` pointer ends up at the correct insertion position after search terminates.

### Brute Force Canonical Skeleton:
```csharp
int SearchInsertBruteForce(int[] nums, int target)
{
    for (int i = 0; i < nums.Length; i++)
    {
        if (nums[i] >= target)
            return i; // Insert here
    }
    return nums.Length; // Insert at end
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `nums = [1,3,5,6], target = 2`:

| i | nums[i] | nums[i] >= 2 | action |
|---|--------|--------------|--------|
| 0 | 1 | no | continue |
| 1 | 3 | yes | return 1 |

### Optimized Solution from Canonical Skeleton:
```csharp
int SearchInsert(int[] nums, int target)
{
    int left = 0, right = nums.Length - 1;

    while (left <= right)
    {
        int mid = left + (right - left) / 2;

        if (nums[mid] == target)
            return mid; // Found exact position
        else if (nums[mid] < target)
            left = mid + 1;
        else
            right = mid - 1;
    }

    return left; // Insert position
}
```

### Explanation of Pruning:
- Use standard binary search to find target or where it should be
- **Key insight:** When loop terminates, `left` points to insertion position
- If target exists, return its index
- If target doesn't exist, `left` is where it should be inserted
- `left` naturally ends up at the first position where `nums[left] >= target`

### Optimized Solution Code Walkthrough / Variable Trace:
For `nums = [1,3,5,6], target = 2`:

| left | right | mid | nums[mid] | comparison | action |
|-----|------|-----|-----------|------------|--------|
| 0 | 3 | 1 | 3 | 3 > 2 | right = 0 |
| 0 | 0 | 0 | 1 | 1 < 2 | left = 1 |
| 1 | 0 | - | - | left > right | return left=1 |

### Big-O Analysis:
- **Brute Force:** O(n) → check each element
- **Optimized:** O(log n) → binary search
- **Space Complexity:** O(1) → only variables

</details>

<details>
<summary><b>Variant #4: Peak Element</b></summary>

## Variant #4: Peak Element

### Input/Output:
- Input: `nums = [1,2,3,1]`
- Output: `2` (index of peak element 3)

### Full State Space:
Any element that is greater than its neighbors is a valid peak.
```
For nums = [1,2,3,1]
Possible peaks: index 2 (value 3)
```

### Expected/Pruned State Space:
Binary search can find a peak in O(log n) by always moving toward higher elements.
```
If nums[mid] < nums[mid+1], peak must be on right
If nums[mid] > nums[mid+1], peak could be mid or on left
```

### State Space Leading to Output:
Always move toward the ascending side to guarantee finding a peak.

### Brute Force Canonical Skeleton:
```csharp
int FindPeakElementBruteForce(int[] nums)
{
    for (int i = 0; i < nums.Length; i++)
    {
        bool isPeak = true;
        if (i > 0 && nums[i] <= nums[i - 1]) isPeak = false;
        if (i < nums.Length - 1 && nums[i] <= nums[i + 1]) isPeak = false;
        if (isPeak) return i;
    }
    return 0;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `nums = [1,2,3,1]`:

| i | nums[i] | left neighbor | right neighbor | isPeak |
|---|--------|---------------|----------------|--------|
| 0 | 1 | none | 2 | no |
| 1 | 2 | 1 | 3 | no |
| 2 | 3 | 2 | 1 | yes, return 2 |

### Optimized Solution from Canonical Skeleton:
```csharp
int FindPeakElement(int[] nums)
{
    int left = 0, right = nums.Length - 1;

    while (left < right)
    {
        int mid = left + (right - left) / 2;

        if (nums[mid] < nums[mid + 1])
            left = mid + 1;  // Peak is on right
        else
            right = mid;     // Peak is mid or on left
    }

    return left; // left == right at peak
}
```

### Explanation of Pruning:
- **Key insight:** Always move toward higher ground
- If ascending on right (`nums[mid] < nums[mid+1]`), peak must be on right
- If descending on right (`nums[mid] >= nums[mid+1]`), mid could be peak or peak is on left
- This guarantees finding a peak because we always climb upward
- Use `left < right` (not `<=`) and `right = mid` (not `mid-1`) for this variant

### Optimized Solution Code Walkthrough / Variable Trace:
For `nums = [1,2,3,1]`:

| left | right | mid | nums[mid] | nums[mid+1] | comparison | action |
|-----|------|-----|-----------|-------------|------------|--------|
| 0 | 3 | 1 | 2 | 3 | 2 < 3 | left = 2 |
| 2 | 3 | 2 | 3 | 1 | 3 > 1 | right = 2 |
| 2 | 2 | - | - | - | left == right | return 2 |

### Big-O Analysis:
- **Brute Force:** O(n) → check every element
- **Optimized:** O(log n) → binary search
- **Space Complexity:** O(1) → only variables

</details>

<details>
<summary><b>Variant #5: Rotated Sorted Array</b></summary>

## Variant #5: Rotated Sorted Array

### Input/Output:
- Input: `nums = [4,5,6,7,0,1,2], target = 0`
- Output: `4` (index of target)

### Full State Space:
Array is sorted but rotated at an unknown pivot point.
```
Original: [0,1,2,4,5,6,7]
Rotated:  [4,5,6,7,0,1,2]
Pivot at index 4
```

### Expected/Pruned State Space:
At least one half of the array is always sorted - use this to determine which half to search.
```
At each mid point:
- Check which half is sorted
- Check if target is in sorted half
- Eliminate appropriate half
```

### State Space Leading to Output:
Determine sorted half, check if target is in range, then eliminate wrong half.

### Brute Force Canonical Skeleton:
```csharp
int SearchRotatedBruteForce(int[] nums, int target)
{
    for (int i = 0; i < nums.Length; i++)
    {
        if (nums[i] == target)
            return i;
    }
    return -1;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `nums = [4,5,6,7,0,1,2], target = 0`:

| i | nums[i] | match | action |
|---|--------|-------|--------|
| 0 | 4 | no | continue |
| 1 | 5 | no | continue |
| 2 | 6 | no | continue |
| 3 | 7 | no | continue |
| 4 | 0 | yes | return 4 |

### Optimized Solution from Canonical Skeleton:
```csharp
int SearchRotated(int[] nums, int target)
{
    int left = 0, right = nums.Length - 1;

    while (left <= right)
    {
        int mid = left + (right - left) / 2;

        if (nums[mid] == target)
            return mid;

        // Determine which half is sorted
        if (nums[left] <= nums[mid]) // Left half is sorted
        {
            if (nums[left] <= target && target < nums[mid])
                right = mid - 1; // Target in left half
            else
                left = mid + 1;  // Target in right half
        }
        else // Right half is sorted
        {
            if (nums[mid] < target && target <= nums[right])
                left = mid + 1;  // Target in right half
            else
                right = mid - 1; // Target in left half
        }
    }

    return -1;
}
```

### Explanation of Pruning:
- **Key insight:** At least one half is always sorted
- Check `nums[left] <= nums[mid]` to determine which half is sorted
- If left half sorted: check if `target` is in range `[nums[left], nums[mid]]`
- If right half sorted: check if `target` is in range `[nums[mid], nums[right]]`
- Eliminate the half that doesn't contain target

### Optimized Solution Code Walkthrough / Variable Trace:
For `nums = [4,5,6,7,0,1,2], target = 0`:

| left | right | mid | nums[mid] | sorted half | target in range? | action |
|-----|------|-----|-----------|-------------|------------------|--------|
| 0 | 6 | 3 | 7 | left [4..7] | no (0 not in [4..7]) | left = 4 |
| 4 | 6 | 5 | 1 | right [1..2] | no (0 not in [1..2]) | right = 4 |
| 4 | 4 | 4 | 0 | - | found | return 4 |

### Big-O Analysis:
- **Brute Force:** O(n) → linear search
- **Optimized:** O(log n) → modified binary search
- **Space Complexity:** O(1) → only variables

</details>

<details>
<summary><b>Variant #6: Binary Search on Answer</b></summary>

## Variant #6: Binary Search on Answer

### Input/Output:
- Input: Problem with feasibility check (e.g., min speed to eat bananas, capacity to ship packages)
- Output: Minimum/maximum feasible value

Example: Koko Eating Bananas
- Input: `piles = [3,6,7,11], h = 8`
- Output: `4` (minimum eating speed)

### Full State Space:
All possible answer values from minimum to maximum.
```
For eating speed: [1, 2, 3, ..., max(piles)]
For this example: [1, 2, 3, ..., 11]
```
```csharp
void GenerateAllSpeeds(int[] piles, int h)
{
    int maxPile = piles.Max();
    for (int speed = 1; speed <= maxPile; speed++)
    {
        Console.WriteLine($"Testing speed {speed}: {(CanEatAll(piles, h, speed) ? "feasible" : "not feasible")}");
    }
}
```

### Expected/Pruned State Space:
Only O(log(max-min)) speeds need to be tested.
```
If speed k is feasible, all speeds > k are also feasible
If speed k is not feasible, all speeds < k are also not feasible
Monotonic property → binary search applicable
```

### State Space Leading to Output:
Find the minimum feasible value by binary searching on the answer space.

### Brute Force Canonical Skeleton:
```csharp
int MinEatingSpeedBruteForce(int[] piles, int h)
{
    int maxPile = piles.Max();
    
    // Try every speed from 1 to max
    for (int speed = 1; speed <= maxPile; speed++)
    {
        if (CanEatAll(piles, h, speed))
            return speed; // First feasible speed
    }
    
    return maxPile;
}

bool CanEatAll(int[] piles, int h, int speed)
{
    int hours = 0;
    foreach (int pile in piles)
    {
        hours += (pile + speed - 1) / speed; // Ceiling division
        if (hours > h) return false;
    }
    return true;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `piles = [3,6,7,11], h = 8`:

| speed | hours for [3,6,7,11] | total hours | feasible (<=8) |
|-------|---------------------|-------------|----------------|
| 1 | [3,6,7,11] | 27 | no |
| 2 | [2,3,4,6] | 15 | no |
| 3 | [1,2,3,4] | 10 | no |
| 4 | [1,2,2,3] | 8 | yes, return 4 |

### Optimized Solution from Canonical Skeleton:
```csharp
int MinEatingSpeed(int[] piles, int h)
{
    int left = 1, right = piles.Max();

    while (left < right)
    {
        int mid = left + (right - left) / 2;

        if (CanEatAll(piles, h, mid))
            right = mid;     // Try smaller speed
        else
            left = mid + 1;  // Need larger speed
    }

    return left; // Minimum feasible speed
}

bool CanEatAll(int[] piles, int h, int speed)
{
    int hours = 0;
    foreach (int pile in piles)
    {
        hours += (pile + speed - 1) / speed;
        if (hours > h) return false;
    }
    return true;
}
```

### Explanation of Pruning:
- **Key insight:** Binary search on the answer space, not the input array
- The answer space [1..max(piles)] has a monotonic property:
  - If speed k works, all speeds > k also work
  - If speed k fails, all speeds < k also fail
- Use feasibility check to determine which half to eliminate
- Find minimum feasible value using `left < right` and `right = mid`

### Optimized Solution Code Walkthrough / Variable Trace:
For `piles = [3,6,7,11], h = 8`:

| left | right | mid | CanEatAll(mid) | hours at mid | action |
|-----|------|-----|----------------|--------------|--------|
| 1 | 11 | 6 | yes | 7 | right = 6 |
| 1 | 6 | 3 | no | 10 | left = 4 |
| 4 | 6 | 5 | yes | 7 | right = 5 |
| 4 | 5 | 4 | yes | 8 | right = 4 |
| 4 | 4 | - | - | - | return 4 |

### Big-O Analysis:
- **Brute Force:** O(n × max(piles)) → try every speed, each taking O(n)
- **Optimized:** O(n × log(max(piles))) → binary search on answer space
- **Space Complexity:** O(1) → only variables

</details>

<details>
<summary><b>Variant #7: Search in 2D Matrix</b></summary>

## Variant #7: Search in 2D Matrix

### Input/Output:
- Input: `matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 3`
- Output: `true`

### Full State Space:
All elements in the m×n matrix need to be considered.
```
For 3×4 matrix: 12 elements
Brute force checks all elements
```

### Expected/Pruned State Space:
Treat 2D matrix as 1D sorted array and use binary search.
```
Matrix[i][j] maps to index = i * cols + j
Index k maps to matrix[k / cols][k % cols]
```

### State Space Leading to Output:
Convert 2D coordinates to 1D index for binary search, then convert back.

### Brute Force Canonical Skeleton:
```csharp
bool SearchMatrix2DBruteForce(int[][] matrix, int target)
{
    for (int row = 0; row < matrix.Length; row++)
    {
        for (int col = 0; col < matrix[0].Length; col++)
        {
            if (matrix[row][col] == target)
                return true;
        }
    }
    return false;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 3`:

| row | col | matrix[row][col] | match |
|-----|-----|-----------------|-------|
| 0 | 0 | 1 | no |
| 0 | 1 | 3 | yes, return true |

### Optimized Solution from Canonical Skeleton:
```csharp
bool SearchMatrix2D(int[][] matrix, int target)
{
    if (matrix.Length == 0) return false;
    
    int rows = matrix.Length;
    int cols = matrix[0].Length;
    int left = 0, right = rows * cols - 1;

    while (left <= right)
    {
        int mid = left + (right - left) / 2;
        int midValue = matrix[mid / cols][mid % cols];

        if (midValue == target)
            return true;
        else if (midValue < target)
            left = mid + 1;
        else
            right = mid - 1;
    }

    return false;
}
```

### Explanation of Pruning:
- **Key insight:** Treat 2D matrix as flattened 1D sorted array
- Convert between 1D index and 2D coordinates:
  - 1D to 2D: `row = index / cols, col = index % cols`
  - 2D to 1D: `index = row * cols + col`
- Apply standard binary search on the 1D representation
- No actual flattening needed - just coordinate conversion

### Optimized Solution Code Walkthrough / Variable Trace:
For `matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 3`:

| left | right | mid | row=mid/4 | col=mid%4 | matrix[row][col] | action |
|-----|------|-----|-----------|-----------|------------------|--------|
| 0 | 11 | 5 | 1 | 1 | 11 | 11 > 3, right = 4 |
| 0 | 4 | 2 | 0 | 2 | 5 | 5 > 3, right = 1 |
| 0 | 1 | 0 | 0 | 0 | 1 | 1 < 3, left = 1 |
| 1 | 1 | 1 | 0 | 1 | 3 | found, return true |

### Big-O Analysis:
- **Brute Force:** O(m × n) → check every element
- **Optimized:** O(log(m × n)) → binary search on flattened view
- **Space Complexity:** O(1) → only variables

</details>

<details>
<summary><b>Variant #8: Minimum in Rotated Sorted Array</b></summary>

## Variant #8: Minimum in Rotated Sorted Array

### Input/Output:
- Input: `nums = [3,4,5,1,2]`
- Output: `1` (minimum element)

### Full State Space:
All elements in the rotated sorted array.
```
Original: [1,2,3,4,5]
Rotated:  [3,4,5,1,2]
Pivot/minimum at index 3
```

### Expected/Pruned State Space:
Use binary search to find the pivot point (minimum element).
```
Compare mid with right to determine which half contains minimum
```

### State Space Leading to Output:
The minimum element is where the rotation occurred (the pivot point).

### Brute Force Canonical Skeleton:
```csharp
int FindMinBruteForce(int[] nums)
{
    int min = nums[0];
    for (int i = 1; i < nums.Length; i++)
    {
        if (nums[i] < min)
            min = nums[i];
    }
    return min;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `nums = [3,4,5,1,2]`:

| i | nums[i] | current min | update |
|---|--------|-------------|--------|
| 0 | 3 | 3 | initial |
| 1 | 4 | 3 | no |
| 2 | 5 | 3 | no |
| 3 | 1 | 1 | yes |
| 4 | 2 | 1 | no |

### Optimized Solution from Canonical Skeleton:
```csharp
int FindMin(int[] nums)
{
    int left = 0, right = nums.Length - 1;

    while (left < right)
    {
        int mid = left + (right - left) / 2;

        if (nums[mid] > nums[right])
            left = mid + 1;  // Minimum in right half
        else
            right = mid;     // Minimum in left half or at mid
    }

    return nums[left];
}
```

### Explanation of Pruning:
- **Key insight:** Compare `nums[mid]` with `nums[right]` to find inflection point
- If `nums[mid] > nums[right]`, array is rotated and minimum is in right half
- If `nums[mid] <= nums[right]`, minimum is in left half or at mid
- Use `left < right` (not `<=`) and `right = mid` (not `mid-1`)
- Loop terminates when `left == right` at minimum element

### Optimized Solution Code Walkthrough / Variable Trace:
For `nums = [3,4,5,1,2]`:

| left | right | mid | nums[mid] | nums[right] | comparison | action |
|-----|------|-----|-----------|-------------|------------|--------|
| 0 | 4 | 2 | 5 | 2 | 5 > 2 | left = 3 |
| 3 | 4 | 3 | 1 | 2 | 1 < 2 | right = 3 |
| 3 | 3 | - | - | - | left == right | return nums[3]=1 |

### Big-O Analysis:
- **Brute Force:** O(n) → scan all elements
- **Optimized:** O(log n) → binary search
- **Space Complexity:** O(1) → only variables

</details>


---

## Key Takeaways

### Binary Search Essentials
Always check:
- **Is the data monotonic?** (sorted or has a monotonic property)
- **Can I define a clear yes/no condition?**
- **Can I discard half the space based on comparison?**

### Problem Types
1. **Direct search:** Find exact value or boundary in sorted array
2. **Rotated arrays:** Handle partially sorted data with pivot
3. **Binary search on answer:** Search answer space with feasibility check
4. **2D problems:** Convert to 1D or search rows/columns

### Loop Variants
```csharp
// Standard: left <= right, return -1 if not found
while (left <= right) { ... return mid; }

// Finding boundary: left < right, return left
while (left < right) { ... right = mid or left = mid + 1; }
```

### Avoid Off-by-One Errors
- Use `mid = left + (right - left) / 2` to prevent overflow
- For finding minimum: use `left < right` and `right = mid`
- For finding maximum: use `left < right` and `left = mid + 1`
- Standard search: use `left <= right` with `left = mid + 1` and `right = mid - 1`

---

_End of Binary Search Pattern_
