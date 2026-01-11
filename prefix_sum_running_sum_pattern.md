# Pattern Title: Prefix Sum / Running Sum

## Pattern Description:
- What is it?  
  Prefix Sum (also called Running Sum or Cumulative Sum) precomputes cumulative aggregates so that range queries or subarray calculations can be answered in constant time.

- What abstract problem does it solve?  
  It collapses repeated summation over overlapping ranges into a single pass, transforming O(n²) subarray enumeration problems into O(n) or O(n log n).

- Real-world problem variants (simplest first):  
  1. Range Sum Query (immutable)
  2. Subarray Sum Equals K
  3. Count of Subarrays with Given Sum
  4. Contiguous Array (Binary Array with Sum)
  5. 2D Prefix Sum (conceptual extension)

## Canonical Code Skeleton:

```csharp
int SubarraySumTemplate(int[] nums, int k) {
    int prefixSum = 0, count = 0;
    var map = new Dictionary<int, int>();
    map[0] = 1; // base case for subarrays starting at index 0

    foreach (var num in nums)
    {
        prefixSum += num;

        int need = prefixSum - k;
        if (map.ContainsKey(need))
            count += map[need];

        map[prefixSum] = map.GetValueOrDefault(prefixSum, 0) + 1;
    }
    
    return count;
}
```

<details>
<summary><b>Variant #1: Range Sum Query (Immutable)</b></summary>

## Variant #1: Range Sum Query (Immutable)

### Input/Output:
- Input: `nums = [1,2,3,4]`, queries like `sumRange(1, 3)`
- Output: `9` (sum of elements from index 1 to 3: 2+3+4)

### Full State Space:
All possible range queries `(l, r)` where `0 ≤ l ≤ r < n`:
```
(0,0): sum=1,
(0,1): sum=3,
(0,2): sum=6,
(0,3): sum=10,
(1,1): sum=2,
(1,2): sum=5,
(1,3): sum=9,
(2,2): sum=3,
(2,3): sum=7,
(3,3): sum=4
```
```csharp
void GenerateAllRangeSums(int[] nums)
{
    var allRangeSums = new List<(int l, int r, int sum)>();
    
    for (int start = 0; start < nums.Length; start++)
    {
        for (int end = start; end < nums.Length; end++)
        {
            int sum = 0;
            for (int i = start; i <= end; i++)
            {
                sum += nums[i];
            }
            allRangeSums.Add((start, end, sum));
        }
    }
}
```

### Expected/Pruned State Space:
Use prefix array so each range is answered in O(1):
```
prefix = [0, 1, 3, 6, 10]
sum(1,3) = prefix[4] - prefix[1] = 10 - 1 = 9
```
```csharp
void BuildPrefixArray(int[] nums)
{
    int[] prefix = new int[nums.Length + 1];
    
    for (int i = 0; i < nums.Length; i++)
    {
        prefix[i + 1] = prefix[i] + nums[i];
    }
}
```

### State Space Leading to Output:
For query (1,3): `prefix[3+1] - prefix[1] = 10 - 1 = 9`

### Brute Force Canonical Skeleton:
```csharp
int RangeSumBruteForce(int[] nums, int l, int r)
{
    int sum = 0;
    for (int i = l; i <= r; i++)
    {
        sum += nums[i];
    }
    return sum;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `nums = [1,2,3,4]`, query `sumRange(1, 3)`:

| i | nums[i] | sum |
| - | ------- | --- |
| 1 | 2       | 2   |
| 2 | 3       | 5   |
| 3 | 4       | 9   |

### Optimized Solution from Canonical Skeleton:
```csharp
class NumArray 
{
    private int[] prefix;
    
    public NumArray(int[] nums) 
    {
        prefix = new int[nums.Length + 1];
        for (int i = 0; i < nums.Length; i++)
        {
            prefix[i + 1] = prefix[i] + nums[i];
        }
    }
    
    public int SumRange(int left, int right) 
    {
        return prefix[right + 1] - prefix[left];
    }
}
```

### Explanation of Pruning:
- Instead of computing sum for each query, precompute all cumulative sums
- Use difference between two prefix values to get range sum
- Transforms O(n) per query to O(1) per query

### Optimized Solution Code Walkthrough / Variable Trace:
**Build phase** for `nums = [1,2,3,4]`:

| i | nums[i] | prefix[i+1] |
| - | ------- | ----------- |
| 0 | 1       | 1           |
| 1 | 2       | 3           |
| 2 | 3       | 6           |
| 3 | 4       | 10          |

**Query phase** for `sumRange(1, 3)`:
- `prefix[4] - prefix[1] = 10 - 1 = 9`

### Big-O Analysis:
- **Brute Force:** O(n) per query
- **Optimized:** O(n) preprocessing, O(1) per query
- **Space Complexity:** O(n) for prefix array

</details>

<details>
<summary><b>Variant #2: Subarray Sum Equals K</b></summary>

## Variant #2: Subarray Sum Equals K

### Input/Output:
- Input: `nums = [1,2,1]`, `k = 3`
- Output: `2` (two subarrays: [1,2] and [2,1])

### Full State Space:
All contiguous subarrays:
```
[1]: sum=1,
[1,2]: sum=3,
[1,2,1]: sum=4,
[2]: sum=2,
[2,1]: sum=3,
[1]: sum=1
```
```csharp
void GenerateAllSubarrays(int[] nums, int k)
{
    var allSubarrays = new List<(int start, int end, int sum)>();
    
    for (int start = 0; start < nums.Length; start++)
    {
        int sum = 0;
        for (int end = start; end < nums.Length; end++)
        {
            sum += nums[end];
            allSubarrays.Add((start, end, sum));
        }
    }
}
```

### Expected/Pruned State Space:
Use prefix sum frequency map:
```
For each position, check if (prefixSum - k) exists in map
If prefix[j] - prefix[i] = k, then subarray (i+1, j) is valid
```
```csharp
void FindValidSubarrays(int[] nums, int k)
{
    var map = new Dictionary<int, int>();
    map[0] = 1;
    int prefixSum = 0;
    var validSubarrays = new List<(int count)>();
    
    for (int end = 0; end < nums.Length; end++)
    {
        prefixSum += nums[end];
        int need = prefixSum - k;
        
        if (map.ContainsKey(need))
        {
            validSubarrays.Add((map[need]));
        }
        
        map[prefixSum] = map.GetValueOrDefault(prefixSum, 0) + 1;
    }
}
```

### State Space Leading to Output:
Two subarrays with sum=3 → count = 2

### Brute Force Canonical Skeleton:
```csharp
int SubarraySumBruteForce(int[] nums, int k)
{
    int count = 0;
    
    for (int start = 0; start < nums.Length; start++)
    {
        int sum = 0;
        for (int end = start; end < nums.Length; end++)
        {
            sum += nums[end];
            if (sum == k)
            {
                count++;
            }
        }
    }
    
    return count;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `nums = [1,2,1]`, `k = 3`:

| start | end | sum | k? | count |
| ----- | --- | --- | -- | ----- |
| 0     | 0   | 1   | No | 0     |
| 0     | 1   | 3   | Yes| 1     |
| 0     | 2   | 4   | No | 1     |
| 1     | 1   | 2   | No | 1     |
| 1     | 2   | 3   | Yes| 2     |
| 2     | 2   | 1   | No | 2     |

### Optimized Solution from Canonical Skeleton:
```csharp
int SubarraySum(int[] nums, int k)
{
    int count = 0, prefixSum = 0;
    var map = new Dictionary<int, int>();
    map[0] = 1; // Handle subarrays starting at index 0

    foreach (var num in nums)
    {
        prefixSum += num;
        
        // Check if there's a prefix sum that makes current subarray sum to k
        if (map.ContainsKey(prefixSum - k))
        {
            count += map[prefixSum - k];
        }
        
        // Add current prefix sum to map
        map[prefixSum] = map.GetValueOrDefault(prefixSum, 0) + 1;
    }
    
    return count;
}
```

### Explanation of Pruning:
- Instead of checking all O(n²) subarrays, use prefix sum with hash map
- For each position, check if (prefixSum - k) has occurred before
- If yes, those positions form valid subarrays ending at current position
- `map[0] = 1` handles subarrays starting from index 0

### Optimized Solution Code Walkthrough / Variable Trace:
For `nums = [1,2,1]`, `k = 3`:

| idx | num | prefixSum | prefixSum-k | found? | count | map                        |
| --- | --- | --------- | ----------- | ------ | ----- | -------------------------- |
| 0   | 1   | 1         | -2          | No     | 0     | {0:1, 1:1}                 |
| 1   | 2   | 3         | 0           | Yes    | 1     | {0:1, 1:1, 3:1}            |
| 2   | 1   | 4         | 1           | Yes    | 2     | {0:1, 1:1, 3:1, 4:1}       |

### Big-O Analysis:
- **Brute Force:** O(n²) → check all subarrays
- **Optimized:** O(n) → single pass with hash map
- **Space Complexity:** O(n) → store prefix sums in map

</details>

# Key Pattern Takeaways
- Prefix sum converts range problems into **difference lookups**.
- Hash map tracks *how many times* a prefix sum has occurred.
- Always initialize `map[0] = 1` to handle subarrays starting at index 0.

---

**End of Prefix Sum / Running Sum Pattern**

