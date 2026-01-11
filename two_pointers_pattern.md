# Pattern Title: Two Pointers

## Pattern Description:
- What is it?  
  Two Pointers is a technique where two indices traverse a data structure (usually an array or string) in a coordinated way, often moving toward each other, in the same direction, or at different speeds to solve problems efficiently.

- What abstract problem does it solve?  
  It reduces a large combinatorial or pairwise state space (often O(n²) or O(n³)) by exploiting ordering, symmetry, or monotonicity, collapsing it into a linear or near-linear traversal.

- Real-world problem variants (simplest first):  
  1. Two Sum II (sorted array)
  2. Valid Palindrome
  3. Remove Duplicates from Sorted Array
  4. Container With Most Water
  5. 3Sum
  6. Trapping Rain Water
  7. Partition / In-place rearrangement (Move Zeros)
  8. Sort Colors (Dutch National Flag)

## Canonical Code Skeleton:

```csharp
int TwoPointersTemplate(int[] nums) {
    int left = 0, right = nums.Length - 1;
    int result = 0; // or initialize depending on problem
    
    while (left < right) {
        // Process current pair (nums[left], nums[right])
        
        if (ConditionMet(nums[left], nums[right])) {
            // Record/update result
            left++;
            right--;
        }
        else if (NeedSmaller()) {
            right--;
        }
        else {
            left++;
        }
    }
    
    return result;
}
```

<details>
<summary><b>Variant #1: Two Sum II (Sorted Array)</b></summary>

## Variant #1: Two Sum II (Sorted Array)

### Input/Output:
- Input: `numbers = [2,7,11,15]`, `target = 9`
- Output: `[1,2]` (1-based indices)

### Full State Space:
All possible pairs from the array:
```
(2,7), (2,11), (2,15),
(7,11), (7,15),
(11,15)
```
```csharp
void GenerateAllPairs(int[] nums)
{
    var allPairs = new List<(int, int)>();
    
    // For each starting position
    for (int start = 0; start < nums.Length; start++)
    {
        // For each position after start
        for (int end = start + 1; end < nums.Length; end++)
        {
            allPairs.Add((nums[start], nums[end]));
        }
    }
}
```

### Expected/Pruned State Space:
Only pairs explored by two pointers (exploiting sorted property):
```
(2,15) → sum = 17 (too large)
(2,11) → sum = 13 (too large)
(2,7)  → sum = 9 (match!)
```
```csharp
void GeneratePrunedPairs(int[] nums, int target)
{
    var prunedPairs = new List<(int, int)>();
    int left = 0, right = nums.Length - 1;
    
    while (left < right)
    {
        prunedPairs.Add((nums[left], nums[right]));
        
        int sum = nums[left] + nums[right];
        if (sum == target)
            break;
        else if (sum > target)
            right--;
        else
            left++;
    }
}
```

### State Space Leading to Output:
Three pairs checked instead of 6 → Result: indices [1, 2] (1-based)

### Brute Force Canonical Skeleton:
```csharp
int[] TwoSumBruteForce(int[] numbers, int target) 
{
    // Generate all pairs
    for (int start = 0; start < numbers.Length; start++)
    {
        for (int end = start + 1; end < numbers.Length; end++)
        {
            if (numbers[start] + numbers[end] == target)
            {
                return new int[] { start + 1, end + 1 }; // 1-based
            }
        }
    }
    
    return new int[0];
}
```

### Brute Force Code Walkthrough / Variable Trace:
| start | end | numbers[start] | numbers[end] | sum | match? |
| - | - | ---------- | ---------- | --- | ------ |
| 0 | 1 | 2          | 7          | 9   | Yes    |

### Optimized Solution from Canonical Skeleton:
```csharp
int[] TwoSum(int[] numbers, int target) 
{
    int left = 0, right = numbers.Length - 1;
    
    while (left < right) 
    {
        int sum = numbers[left] + numbers[right];
        
        if (sum == target) 
        {
            return new int[] { left + 1, right + 1 }; // 1-based
        }
        else if (sum > target) 
        {
            right--;
        }
        else 
        {
            left++;
        }
    }
    
    return new int[0];
}
```

### Explanation of Pruning:
- Sorted array allows directional movement
- If sum too large → decrease right (larger values)
- If sum too small → increase left (smaller values)
- Eliminates need to check all O(n²) pairs

### Optimized Solution Code Walkthrough / Variable Trace:
| left | right | numbers[left] | numbers[right] | sum | action      |
| ---- | ----- | ------------- | -------------- | --- | ----------- |
| 0    | 3     | 2             | 15             | 17  | right--     |
| 0    | 2     | 2             | 11             | 13  | right--     |
| 0    | 1     | 2             | 7              | 9   | found [1,2] |

### Big-O Analysis:
- **Brute Force:** O(n²) → check all pairs
- **Optimized:** O(n) → each pointer moves at most n times
- **Space Complexity:** O(1)

</details>

<details>
<summary><b>Variant #2: Valid Palindrome</b></summary>

## Variant #2: Valid Palindrome

### Input/Output:
- Input: `s = "A man, a plan, a canal: Panama"`
- Output: `true`

### Full State Space:
All mirrored character pairs to compare:
```
(A, a), (m, m), (a, a), (n, n), (a, a), (p, p), (l, l), (a, a), (n, n)
(ignoring non-alphanumeric characters)
```
```csharp
void GenerateAllMirroredPairs(string s)
{
    var allPairs = new List<(char, char)>();
    
    // For each position from start
    for (int i = 0; i < s.Length; i++)
    {
        // Match with mirror position
        for (int j = s.Length - 1; j > i; j--)
        {
            allPairs.Add((s[i], s[j]));
        }
    }
}
```

### Expected/Pruned State Space:
Only one comparison path checking valid alphanumeric characters:
```
(A,a), (m,m), (a,a), (n,n), (a,a), (p,p), (l,l), (a,a), (n,n)
```
```csharp
void GeneratePrunedPairs(string s)
{
    var prunedPairs = new List<(char, char)>();
    int left = 0, right = s.Length - 1;
    
    while (left < right)
    {
        // Skip non-alphanumeric
        while (left < right && !char.IsLetterOrDigit(s[left]))
            left++;
        while (left < right && !char.IsLetterOrDigit(s[right]))
            right--;
            
        if (left < right)
        {
            prunedPairs.Add((char.ToLower(s[left]), char.ToLower(s[right])));
            left++;
            right--;
        }
    }
}
```

### State Space Leading to Output:
All mirrored pairs match → Result: `true`

### Brute Force Canonical Skeleton:
```csharp
bool IsPalindromeBruteForce(string s) 
{
    // Clean string first
    var cleaned = new StringBuilder();
    foreach (char c in s)
    {
        if (char.IsLetterOrDigit(c))
            cleaned.Append(char.ToLower(c));
    }
    
    string str = cleaned.ToString();
    
    // Check all mirrored pairs
    for (int i = 0; i < str.Length / 2; i++)
    {
        if (str[i] != str[str.Length - 1 - i])
            return false;
    }
    
    return true;
}
```

### Brute Force Code Walkthrough / Variable Trace:
| i | str[i] | mirror_idx | str[mirror] | match? |
| - | ------ | ---------- | ----------- | ------ |
| 0 | a      | 8          | a           | Yes    |
| 1 | m      | 7          | m           | Yes    |
| 2 | a      | 6          | a           | Yes    |
| 3 | n      | 5          | n           | Yes    |
| 4 | a      | 4          | a           | Yes    |

### Optimized Solution from Canonical Skeleton:
```csharp
bool IsPalindrome(string s) 
{
    int left = 0, right = s.Length - 1;
    
    while (left < right) 
    {
        // Skip non-alphanumeric characters
        while (left < right && !char.IsLetterOrDigit(s[left]))
            left++;
        while (left < right && !char.IsLetterOrDigit(s[right]))
            right--;
        
        // Compare characters
        if (char.ToLower(s[left]) != char.ToLower(s[right]))
            return false;
        
        left++;
        right--;
    }
    
    return true;
}
```

### Explanation of Pruning:
- No need to create cleaned string (saves space)
- Skip invalid characters on the fly
- Compare directly from both ends
- Single pass through string

### Optimized Solution Code Walkthrough / Variable Trace:
| left | right | s[left] | s[right] | valid? | match? |
| ---- | ----- | ------- | -------- | ------ | ------ |
| 0    | 30    | A       | a        | Yes    | Yes    |
| 2    | 28    | m       | m        | Yes    | Yes    |
| 3    | 27    | a       | a        | Yes    | Yes    |
| 4    | 26    | n       | n        | Yes    | Yes    |
| ...  | ...   | ...     | ...      | ...    | ...    |

### Big-O Analysis:
- **Brute Force:** O(n) → create cleaned string + compare
- **Optimized:** O(n) → single pass, but with O(1) space
- **Space Complexity:** O(1) vs O(n) for brute force

</details>

<details>
<summary><b>Variant #3: Remove Duplicates from Sorted Array</b></summary>

## Variant #3: Remove Duplicates from Sorted Array

### Input/Output:
- Input: `nums = [1,1,2,2,3]`
- Output: `3` (modified array: `[1,2,3,_,_]`)

### Full State Space:
All possible ways to select unique elements while preserving order:
```
[1], [1,2], [1,2,3], [1,2,2], [1,2,2,3], [1,1,2], [1,1,2,3], etc.
```
```csharp
void GenerateAllSubsequences(int[] nums)
{
    var allSubsequences = new List<List<int>>();
    
    void Backtrack(int start, List<int> current)
    {
        allSubsequences.Add(new List<int>(current));
        
        for (int end = start; end < nums.Length; end++)
        {
            current.Add(nums[end]);
            Backtrack(end + 1, current);
            current.RemoveAt(current.Count - 1);
        }
    }
    
    Backtrack(0, new List<int>());
}
```

### Expected/Pruned State Space:
Only unique elements in order:
```
[1, 2, 3]
```
```csharp
void GenerateUniqueElements(int[] nums)
{
    var unique = new List<int>();
    
    for (int i = 0; i < nums.Length; i++)
    {
        if (i == 0 || nums[i] != nums[i - 1])
        {
            unique.Add(nums[i]);
        }
    }
}
```

### State Space Leading to Output:
Three unique elements found → Length = 3

### Brute Force Canonical Skeleton:
```csharp
int RemoveDuplicatesBruteForce(int[] nums) 
{
    if (nums.Length == 0) return 0;
    
    var unique = new List<int>();
    
    // Generate unique elements
    for (int i = 0; i < nums.Length; i++)
    {
        if (i == 0 || nums[i] != nums[i - 1])
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

### Brute Force Code Walkthrough / Variable Trace:
| i | nums[i] | unique list | action          |
| - | ------- | ----------- | --------------- |
| 0 | 1       | [1]         | add (first)     |
| 1 | 1       | [1]         | skip (dup)      |
| 2 | 2       | [1,2]       | add (new)       |
| 3 | 2       | [1,2]       | skip (dup)      |
| 4 | 3       | [1,2,3]     | add (new)       |

### Optimized Solution from Canonical Skeleton:
```csharp
int RemoveDuplicates(int[] nums) 
{
    if (nums.Length == 0) return 0;
    
    int slow = 1; // Write position
    
    for (int fast = 1; fast < nums.Length; fast++) 
    {
        if (nums[fast] != nums[fast - 1]) 
        {
            nums[slow] = nums[fast];
            slow++;
        }
    }
    
    return slow;
}
```

### Explanation of Pruning:
- `slow` pointer marks where to write next unique element
- `fast` pointer scans through array
- No need for extra space (in-place modification)
- Skip duplicates without copying them

### Optimized Solution Code Walkthrough / Variable Trace:
| fast | nums[fast] | nums[fast-1] | different? | slow | nums[slow] |
| ---- | ---------- | ------------ | ---------- | ---- | ---------- |
| 1    | 1          | 1            | No         | 1    | -          |
| 2    | 2          | 1            | Yes        | 1    | 2          |
| 3    | 2          | 2            | No         | 2    | -          |
| 4    | 3          | 2            | Yes        | 2    | 3          |

### Big-O Analysis:
- **Brute Force:** O(n) time, O(n) space → extra list
- **Optimized:** O(n) time, O(1) space → in-place
- **Space Complexity:** O(1)

</details>

<details>
<summary><b>Variant #4: Container With Most Water</b></summary>

## Variant #4: Container With Most Water

### Input/Output:
- Input: `height = [1,8,6,2,5,4,8,3,7]`
- Output: `49` (area between indices 1 and 8)

### Full State Space:
All possible pairs of lines (containers):
```
(0,1), (0,2), (0,3), ..., (0,8),
(1,2), (1,3), ..., (1,8),
(2,3), ..., (2,8),
...
(7,8)
```
```csharp
void GenerateAllContainers(int[] height)
{
    var allContainers = new List<(int i, int j, int area)>();
    
    for (int start = 0; start < height.Length; start++)
    {
        for (int end = start + 1; end < height.Length; end++)
        {
            int area = Math.Min(height[start], height[end]) * (end - start);
            allContainers.Add((start, end, area));
        }
    }
}
```

### Expected/Pruned State Space:
Only containers checked by two pointers:
```
(0,8) → area = 1*8 = 8
(1,8) → area = 7*7 = 49
(1,7) → area = 3*6 = 18
...
```
```csharp
void GeneratePrunedContainers(int[] height)
{
    var prunedContainers = new List<(int i, int j, int area)>();
    int left = 0, right = height.Length - 1;
    
    while (left < right)
    {
        int area = Math.Min(height[left], height[right]) * (right - left);
        prunedContainers.Add((left, right, area));
        
        // Move pointer at shorter line
        if (height[left] < height[right])
            left++;
        else
            right--;
    }
}
```

### State Space Leading to Output:
Maximum area found = 49

### Brute Force Canonical Skeleton:
```csharp
int MaxAreaBruteForce(int[] height) 
{
    int maxArea = 0;
    
    // Try all pairs of lines
    for (int start = 0; start < height.Length; start++)
    {
        for (int end = start + 1; end < height.Length; end++)
        {
            int area = Math.Min(height[start], height[end]) * (end - start);
            maxArea = Math.Max(maxArea, area);
        }
    }
    
    return maxArea;
}
```

### Brute Force Code Walkthrough / Variable Trace:
| start | end | height[start] | height[end] | width | min_height | area | maxArea |
| - | - | --------- | --------- | ----- | ---------- | ---- | ------- |
| 0 | 1 | 1         | 8         | 1     | 1          | 1    | 1       |
| 0 | 2 | 1         | 6         | 2     | 1          | 2    | 2       |
| 1 | 8 | 8         | 7         | 7     | 7          | 49   | 49      |
| ... | ... | ... | ... | ... | ... | ... | ...     |

### Optimized Solution from Canonical Skeleton:
```csharp
int MaxArea(int[] height) 
{
    int left = 0, right = height.Length - 1;
    int maxArea = 0;
    
    while (left < right) 
    {
        int width = right - left;
        int currentArea = Math.Min(height[left], height[right]) * width;
        maxArea = Math.Max(maxArea, currentArea);
        
        // Move pointer at shorter line
        if (height[left] < height[right]) 
        {
            left++;
        }
        else 
        {
            right--;
        }
    }
    
    return maxArea;
}
```

### Explanation of Pruning:
- Start with maximum width (widest container)
- Area limited by shorter line
- Moving shorter pointer might find taller line
- Moving taller pointer only decreases width and keeps same/smaller height
- Eliminates O(n²) comparisons

### Optimized Solution Code Walkthrough / Variable Trace:
| left | right | height[l] | height[r] | width | area | maxArea | move   |
| ---- | ----- | --------- | --------- | ----- | ---- | ------- | ------ |
| 0    | 8     | 1         | 7         | 8     | 8    | 8       | left++ |
| 1    | 8     | 8         | 7         | 7     | 49   | 49      | right--|
| 1    | 7     | 8         | 3         | 6     | 18   | 49      | right--|
| 1    | 6     | 8         | 8         | 5     | 40   | 49      | right--|
| ...  | ...   | ...       | ...       | ...   | ...  | ...     | ...    |

### Big-O Analysis:
- **Brute Force:** O(n²) → check all pairs
- **Optimized:** O(n) → each pointer moves at most n times
- **Space Complexity:** O(1)

</details>

<details>
<summary><b>Variant #5: 3Sum</b></summary>

## Variant #5: 3Sum

### Input/Output:
- Input: `nums = [-1,0,1,2,-1,-4]`
- Output: `[[-1,-1,2],[-1,0,1]]`

### Full State Space:
All possible triplets:
```
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
```
```csharp
void GenerateAllTriplets(int[] nums)
{
    var allTriplets = new List<List<int>>();
    
    for (int i = 0; i < nums.Length; i++)
    {
        for (int j = i + 1; j < nums.Length; j++)
        {
            for (int k = j + 1; k < nums.Length; k++)
            {
                allTriplets.Add(new List<int> { nums[i], nums[j], nums[k] });
            }
        }
    }
}
```

### Expected/Pruned State Space:
After sorting: `[-4,-1,-1,0,1,2]`
Only triplets checked using fixed index + two pointers:
```
Fix -4: check pairs in [-1,-1,0,1,2]
Fix -1: check pairs in [-1,0,1,2] → found (-1,-1,2)
Fix -1: skip (duplicate)
Fix 0: check pairs in [1,2] → found (-1,0,1)
Fix 1: check pairs in [2] → sum always positive
```
```csharp
void GeneratePrunedTriplets(int[] nums)
{
    Array.Sort(nums);
    var prunedTriplets = new List<List<int>>();
    
    for (int i = 0; i < nums.Length - 2; i++)
    {
        if (i > 0 && nums[i] == nums[i - 1]) continue; // Skip duplicates
        
        int left = i + 1, right = nums.Length - 1;
        while (left < right)
        {
            prunedTriplets.Add(new List<int> { nums[i], nums[left], nums[right] });
            
            int sum = nums[i] + nums[left] + nums[right];
            if (sum == 0)
            {
                left++;
                right--;
            }
            else if (sum < 0)
                left++;
            else
                right--;
        }
    }
}
```

### State Space Leading to Output:
Found 2 unique triplets that sum to 0

### Brute Force Canonical Skeleton:
```csharp
IList<IList<int>> ThreeSumBruteForce(int[] nums) 
{
    var result = new HashSet<string>(); // Use set to avoid duplicates
    var triplets = new List<IList<int>>();
    
    // Generate all triplets
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
                    string key = string.Join(",", triplet);
                    
                    if (!result.Contains(key))
                    {
                        result.Add(key);
                        triplets.Add(triplet);
                    }
                }
            }
        }
    }
    
    return triplets;
}
```

### Brute Force Code Walkthrough / Variable Trace:
| i | j | k | nums[i] | nums[j] | nums[k] | sum | valid? |
| - | - | - | ------- | ------- | ------- | --- | ------ |
| 0 | 1 | 2 | -1      | 0       | 1       | 0   | Yes    |
| 0 | 1 | 4 | -1      | 0       | -1      | -2  | No     |
| 0 | 2 | 4 | -1      | 1       | -1      | -1  | No     |
| 1 | 2 | 3 | 0       | 1       | 2       | 3   | No     |
| 2 | 4 | 5 | 1       | -1      | -4      | -4  | No     |
| ... | ... | ... | ... | ... | ... | ... | ...    |

### Optimized Solution from Canonical Skeleton:
```csharp
IList<IList<int>> ThreeSum(int[] nums) 
{
    Array.Sort(nums);
    var result = new List<IList<int>>();
    
    for (int i = 0; i < nums.Length - 2; i++) 
    {
        // Skip duplicates for first element
        if (i > 0 && nums[i] == nums[i - 1]) 
            continue;
        
        int left = i + 1, right = nums.Length - 1;
        int target = -nums[i];
        
        while (left < right) 
        {
            int sum = nums[left] + nums[right];
            
            if (sum == target) 
            {
                result.Add(new List<int> { nums[i], nums[left], nums[right] });
                
                // Skip duplicates for second element
                while (left < right && nums[left] == nums[left + 1]) 
                    left++;
                // Skip duplicates for third element
                while (left < right && nums[right] == nums[right - 1]) 
                    right--;
                
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

### Explanation of Pruning:
- Sort array first to enable two pointers and skip duplicates
- Fix one element, use two pointers for remaining two
- Reduces O(n³) to O(n²)
- Skip duplicate values to avoid duplicate triplets
- Sorted order allows directional movement

### Optimized Solution Code Walkthrough / Variable Trace:
| i | nums[i] | left | right | nums[l] | nums[r] | sum | action        |
| - | ------- | ---- | ----- | ------- | ------- | --- | ------------- |
| 0 | -4      | 1    | 5     | -1      | 2       | 1   | left++        |
| 1 | -1      | 2    | 5     | -1      | 2       | 1   | left++        |
| 1 | -1      | 3    | 5     | 0       | 2       | 2   | right--       |
| 1 | -1      | 3    | 4     | 0       | 1       | 1   | right--       |
| 1 | -1      | 3    | 3     | 0       | 0       | 0   | found, done   |
| 2 | -1      | -    | -     | -       | -       | -   | skip (dup)    |
| 3 | 0       | 4    | 5     | 1       | 2       | 3   | left++        |
| ...| ...     | ...  | ...   | ...     | ...     | ... | ...           |

### Big-O Analysis:
- **Brute Force:** O(n³) → three nested loops
- **Optimized:** O(n²) → O(n log n) sort + O(n) outer loop × O(n) two pointers
- **Space Complexity:** O(1) or O(n) depending on sorting implementation

</details>

<details>
<summary><b>Variant #6: Trapping Rain Water</b></summary>

## Variant #6: Trapping Rain Water

### Input/Output:
- Input: `height = [0,1,0,2,1,0,1,3,2,1,2,1]`
- Output: `6` (trapped water units)

### Full State Space:
For each position, need to compute water trapped:
```
For each i: water[i] = min(maxLeft[i], maxRight[i]) - height[i]
where maxLeft[i] = max of all elements to the left
      maxRight[i] = max of all elements to the right
```
```csharp
void ComputeWaterBruteForce(int[] height)
{
    int n = height.Length;
    var waterAtEach = new int[n];
    
    for (int i = 0; i < n; i++)
    {
        // Find max on left
        int maxLeft = 0;
        for (int j = 0; j <= i; j++)
            maxLeft = Math.Max(maxLeft, height[j]);
        
        // Find max on right
        int maxRight = 0;
        for (int j = i; j < n; j++)
            maxRight = Math.Max(maxRight, height[j]);
        
        // Compute water at this position
        waterAtEach[i] = Math.Min(maxLeft, maxRight) - height[i];
    }
}
```

### Expected/Pruned State Space:
Use two pointers to track maxLeft and maxRight dynamically:
```
Position 0: maxL=0, maxR=3, water=0
Position 1: maxL=1, maxR=3, water=0
Position 2: maxL=1, maxR=3, water=1
Position 3: maxL=2, maxR=3, water=0
...
```

### State Space Leading to Output:
Total water trapped = 6 units

### Brute Force Canonical Skeleton:
```csharp
int TrapBruteForce(int[] height) 
{
    int n = height.Length;
    if (n == 0) return 0;
    
    int totalWater = 0;
    
    // For each position
    for (int i = 0; i < n; i++)
    {
        // Find max height on left
        int maxLeft = 0;
        for (int j = 0; j <= i; j++)
        {
            maxLeft = Math.Max(maxLeft, height[j]);
        }
        
        // Find max height on right
        int maxRight = 0;
        for (int j = i; j < n; j++)
        {
            maxRight = Math.Max(maxRight, height[j]);
        }
        
        // Water at this position
        totalWater += Math.Min(maxLeft, maxRight) - height[i];
    }
    
    return totalWater;
}
```

### Brute Force Code Walkthrough / Variable Trace:
| i | height[i] | maxLeft | maxRight | min(L,R) | water | totalWater |
| - | --------- | ------- | -------- | -------- | ----- | ---------- |
| 0 | 0         | 0       | 3        | 0        | 0     | 0          |
| 1 | 1         | 1       | 3        | 1        | 0     | 0          |
| 2 | 0         | 1       | 3        | 1        | 1     | 1          |
| 3 | 2         | 2       | 3        | 2        | 0     | 1          |
| 4 | 1         | 2       | 3        | 2        | 1     | 2          |
| 5 | 0         | 2       | 3        | 2        | 2     | 4          |
| ... | ...     | ...     | ...      | ...      | ...   | ...        |

### Optimized Solution from Canonical Skeleton:
```csharp
int Trap(int[] height) 
{
    if (height.Length == 0) return 0;
    
    int left = 0, right = height.Length - 1;
    int leftMax = 0, rightMax = 0;
    int water = 0;
    
    while (left < right) 
    {
        if (height[left] < height[right]) 
        {
            if (height[left] >= leftMax) 
            {
                leftMax = height[left];
            }
            else 
            {
                water += leftMax - height[left];
            }
            left++;
        }
        else 
        {
            if (height[right] >= rightMax) 
            {
                rightMax = height[right];
            }
            else 
            {
                water += rightMax - height[right];
            }
            right--;
        }
    }
    
    return water;
}
```

### Explanation of Pruning:
- Maintain leftMax and rightMax as we go
- Process side with smaller height first
- Water at position limited by smaller of two maxes
- No need to scan entire array for each position
- Single pass O(n) instead of O(n²)

### Optimized Solution Code Walkthrough / Variable Trace:
| left | right | h[l] | h[r] | leftMax | rightMax | water_added | total |
| ---- | ----- | ---- | ---- | ------- | -------- | ----------- | ----- |
| 0    | 11    | 0    | 1    | 0       | 0        | 0           | 0     |
| 1    | 11    | 1    | 1    | 1       | 0        | 0           | 0     |
| 2    | 11    | 0    | 1    | 1       | 1        | 1           | 1     |
| 3    | 11    | 2    | 1    | 2       | 1        | 0           | 1     |
| 3    | 10    | 2    | 2    | 2       | 2        | 0           | 1     |
| 4    | 10    | 1    | 2    | 2       | 2        | 1           | 2     |
| 5    | 10    | 0    | 2    | 2       | 2        | 2           | 4     |
| ... | ...   | ...  | ...  | ...     | ...      | ...         | ...   |

### Big-O Analysis:
- **Brute Force:** O(n²) → for each position, scan left and right
- **Optimized:** O(n) → single pass with two pointers
- **Space Complexity:** O(1)

</details>

<details>
<summary><b>Variant #7: Move Zeros</b></summary>

## Variant #7: Move Zeros

### Input/Output:
- Input: `nums = [0,1,0,3,12]`
- Output: `[1,3,12,0,0]`

### Full State Space:
All possible arrangements preserving non-zero order:
```
[0,1,0,3,12], [0,1,3,0,12], [0,1,3,12,0],
[1,0,0,3,12], [1,0,3,0,12], [1,0,3,12,0],
[1,3,0,0,12], [1,3,0,12,0], [1,3,12,0,0], ...
```
```csharp
void GenerateAllArrangements(int[] nums)
{
    // All permutations preserving relative order of non-zeros
    var arrangements = new List<List<int>>();
    
    // Extract non-zeros and count zeros
    var nonZeros = nums.Where(x => x != 0).ToList();
    int zeroCount = nums.Length - nonZeros.Count;
    
    // Generate all positions for zeros
    void PlaceZeros(List<int> current, int zerosLeft, int nonZeroIdx)
    {
        if (zerosLeft == 0 && nonZeroIdx == nonZeros.Count)
        {
            arrangements.Add(new List<int>(current));
            return;
        }
        
        // Place a zero
        if (zerosLeft > 0)
        {
            current.Add(0);
            PlaceZeros(current, zerosLeft - 1, nonZeroIdx);
            current.RemoveAt(current.Count - 1);
        }
        
        // Place a non-zero
        if (nonZeroIdx < nonZeros.Count)
        {
            current.Add(nonZeros[nonZeroIdx]);
            PlaceZeros(current, zerosLeft, nonZeroIdx + 1);
            current.RemoveAt(current.Count - 1);
        }
    }
    
    PlaceZeros(new List<int>(), zeroCount, 0);
}
```

### Expected/Pruned State Space:
Only one arrangement: all non-zeros first, then zeros:
```
[1,3,12,0,0]
```
```csharp
void GenerateTarget(int[] nums)
{
    var result = new List<int>();
    
    // Add all non-zeros in order
    for (int i = 0; i < nums.Length; i++)
    {
        if (nums[i] != 0)
            result.Add(nums[i]);
    }
    
    // Fill with zeros
    while (result.Count < nums.Length)
        result.Add(0);
}
```

### State Space Leading to Output:
Non-zeros: [1,3,12], Zeros: [0,0] → Result: [1,3,12,0,0]

### Brute Force Canonical Skeleton:
```csharp
void MoveZeroesBruteForce(int[] nums) 
{
    var nonZeros = new List<int>();
    int zeroCount = 0;
    
    // Collect non-zeros and count zeros
    for (int i = 0; i < nums.Length; i++)
    {
        if (nums[i] != 0)
            nonZeros.Add(nums[i]);
        else
            zeroCount++;
    }
    
    // Write back: non-zeros first
    int idx = 0;
    foreach (int num in nonZeros)
    {
        nums[idx++] = num;
    }
    
    // Then zeros
    while (idx < nums.Length)
    {
        nums[idx++] = 0;
    }
}
```

### Brute Force Code Walkthrough / Variable Trace:
| i | nums[i] | nonZeros  | zeroCount |
| - | ------- | --------- | --------- |
| 0 | 0       | []        | 1         |
| 1 | 1       | [1]       | 1         |
| 2 | 0       | [1]       | 2         |
| 3 | 3       | [1,3]     | 2         |
| 4 | 12      | [1,3,12]  | 2         |

### Optimized Solution from Canonical Skeleton:
```csharp
void MoveZeroes(int[] nums) 
{
    int slow = 0; // Position to write next non-zero
    
    // Move all non-zeros to front
    for (int fast = 0; fast < nums.Length; fast++) 
    {
        if (nums[fast] != 0) 
        {
            nums[slow] = nums[fast];
            slow++;
        }
    }
    
    // Fill remaining with zeros
    while (slow < nums.Length) 
    {
        nums[slow] = 0;
        slow++;
    }
}
```

### Explanation of Pruning:
- `slow` pointer marks write position for non-zeros
- `fast` pointer scans through array
- In-place modification, no extra space
- Single pass to collect non-zeros, then fill zeros

### Optimized Solution Code Walkthrough / Variable Trace:
| fast | nums[fast] | nums[slow] before | slow | nums after move |
| ---- | ---------- | ----------------- | ---- | --------------- |
| 0    | 0          | -                 | 0    | [0,1,0,3,12]    |
| 1    | 1          | 0                 | 0    | [1,1,0,3,12]    |
| 2    | 0          | -                 | 1    | [1,1,0,3,12]    |
| 3    | 3          | 1                 | 1    | [1,3,0,3,12]    |
| 4    | 12         | 0                 | 2    | [1,3,12,3,12]   |

After filling zeros: [1,3,12,0,0]

### Big-O Analysis:
- **Brute Force:** O(n) time, O(n) space → extra array
- **Optimized:** O(n) time, O(1) space → in-place
- **Space Complexity:** O(1)

## Variant #8: Sort Colors (Dutch National Flag)

### Input/Output:
- Input: Array `[2,0,2,1,1,0]` representing colors (0=red, 1=white, 2=blue)
- Output: `[0,0,1,1,2,2]` (sorted in-place)

### Full State Space:
Count each color, then overwrite array:
```
Count: {0:2, 1:2, 2:2}
Write: [0,0,1,1,2,2]
```
```csharp
void SortColorsBruteForce(int[] nums)
{
    int zeros = 0, ones = 0, twos = 0;
    
    // Count each color
    foreach (int num in nums)
    {
        if (num == 0) zeros++;
        else if (num == 1) ones++;
        else twos++;
    }
    
    // Overwrite array
    int i = 0;
    while (zeros-- > 0) nums[i++] = 0;
    while (ones-- > 0) nums[i++] = 1;
    while (twos-- > 0) nums[i++] = 2;
}
```

### Expected/Pruned State Space:
Three pointers partitioning in one pass:
```
[2,0,2,1,1,0]
 low=0, mid=0, high=5

Process:
- All 0s go to low region
- All 2s go to high region
- All 1s stay in middle
```

### State Space Leading to Output:
Single pass with three-way partitioning → `[0,0,1,1,2,2]`

### Brute Force Canonical Skeleton:
```csharp
void SortColorsBruteForce(int[] nums) 
{
    // Two-pass solution
    int[] count = new int[3];
    
    // Pass 1: Count
    foreach (int num in nums)
    {
        count[num]++;
    }
    
    // Pass 2: Overwrite
    int index = 0;
    for (int color = 0; color < 3; color++)
    {
        for (int i = 0; i < count[color]; i++)
        {
            nums[index++] = color;
        }
    }
}
```

### Brute Force Code Walkthrough / Variable Trace:
| step | action | count | array |
| ---- | ------ | ----- | ----- |
| 0 | Initial | {0:0,1:0,2:0} | [2,0,2,1,1,0] |
| 1 | Count | {0:2,1:2,2:2} | [2,0,2,1,1,0] |
| 2 | Write 0s | - | [0,0,2,1,1,0] |
| 3 | Write 1s | - | [0,0,1,1,1,0] |
| 4 | Write 2s | - | [0,0,1,1,2,2] |

### Optimized Solution from Canonical Skeleton:
```csharp
void SortColors(int[] nums) 
{
    int low = 0;                    // Boundary for 0s
    int mid = 0;                    // Current element
    int high = nums.Length - 1;     // Boundary for 2s
    
    // Dutch National Flag algorithm
    while (mid <= high) 
    {
        if (nums[mid] == 0) 
        {
            // Swap with low boundary, move both forward
            Swap(nums, low, mid);
            low++;
            mid++;
        }
        else if (nums[mid] == 1) 
        {
            // Already in correct position
            mid++;
        }
        else // nums[mid] == 2
        {
            // Swap with high boundary, move high back
            Swap(nums, mid, high);
            high--;
            // Don't move mid - need to process swapped element
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

### Explanation of Pruning:
- **Dutch National Flag Algorithm** (by Edsger Dijkstra)
- Three regions: [0...low-1] = 0s, [low...mid-1] = 1s, [high+1...n-1] = 2s
- **Invariants:**
  - Everything before `low` is 0
  - Everything from `low` to `mid-1` is 1
  - Everything after `high` is 2
  - Elements from `mid` to `high` are unprocessed
- **One pass:** O(n) vs two passes
- **In-place:** O(1) space

### Optimized Solution Code Walkthrough / Variable Trace:
For `nums = [2,0,2,1,1,0]`:

| iter | low | mid | high | nums[mid] | action | array |
| ---- | --- | --- | ---- | --------- | ------ | ----- |
| 0 | 0 | 0 | 5 | 2 | Swap(0,5)→[0,0,2,1,1,2], high=4 | [0,0,2,1,1,2] |
| 1 | 0 | 0 | 4 | 0 | Swap(0,0)→same, low=1, mid=1 | [0,0,2,1,1,2] |
| 2 | 1 | 1 | 4 | 0 | Swap(1,1)→same, low=2, mid=2 | [0,0,2,1,1,2] |
| 3 | 2 | 2 | 4 | 2 | Swap(2,4)→[0,0,1,1,2,2], high=3 | [0,0,1,1,2,2] |
| 4 | 2 | 2 | 3 | 1 | mid=3 | [0,0,1,1,2,2] |
| 5 | 2 | 3 | 3 | 1 | mid=4 | [0,0,1,1,2,2] |
| 6 | 2 | 4 | 3 | - | mid>high, stop | [0,0,1,1,2,2] |

### Big-O Analysis:
- **Brute Force:** O(n) time (two passes), O(1) space
- **Optimized:** O(n) time (one pass), O(1) space
- **Key advantage:** Single pass, elegant partitioning

</details>

