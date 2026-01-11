# Pattern Title: Sliding Window

## Pattern Description:
- **What is it?**  
  Sliding Window is a technique used to process contiguous sequences (subarrays/substrings) of a given size or condition efficiently, often using two pointers to maintain a "window" of elements and updating results incrementally.  

- **What abstract problem does it solve?**  
  Efficiently finding subarrays/substrings that satisfy conditions (sum, distinct elements, max/min values, etc.) without recomputing from scratch every time.  

- **Core Optimization:**  
  Transforms O(n²) or O(n³) brute force enumeration of all subarrays into O(n) by maintaining a window that slides through the array, updating incrementally instead of recalculating from scratch.

## State Space Context:
- **Primary State Space**: All Contiguous Subarrays - O(n²) or O(n) for fixed-size windows
- See [State Space Index](../indexes/by_state_space.md#category-2-all-contiguous-subarrays-on²) for full analysis
- **Key Insight**: Most variants start with O(n²) state space but optimize to O(n) traversal through incremental window updates

## Canonical Code Skeleton:

```csharp
int SlidingWindowTemplate(int[] nums, int kOrCondition) {
    int left = 0;
    int result = 0; // or initialize depending on problem
    var windowState = new Dictionary<int,int>(); // or HashSet<char> for substrings

    for (int right = 0; right < nums.Length; right++) {
        // Update windowState with nums[right]
        
        // Shrink window if condition violated
        while (ConditionViolated(windowState)) {
            // Update windowState by removing nums[left]
            left++;
        }

        // Update result based on current window
        result = UpdateResult(result, windowState);
    }

    return result;
}
```

## Pattern Variants:

This pattern has 7 main variants organized by window type and auxiliary data structures:

### Variable-Size Window (Pure Sliding Window):
1. **[Longest Substring Without Repeating Characters](variants/sliding_window/variant_1_longest_substring_no_repeat.md)** - LeetCode #3, Medium
   - Find longest substring with all distinct characters
   - State Space: O(n²) substrings → O(n) with hash set tracking
   
4. **[Subarray Product Less Than K](variants/sliding_window/variant_4_subarray_product_less_k.md)** - LeetCode #713, Medium
   - Count subarrays where product < k
   - Uses counting formula: `right - left + 1`
   
5. **[Minimum Size Subarray Sum](variants/sliding_window/variant_5_min_size_subarray_sum.md)** - LeetCode #209, Medium
   - Find shortest subarray with sum ≥ target
   - "Grow-then-shrink" minimization pattern

### Fixed-Size Window:
2. **[Maximum Sum Subarray of Size K](variants/sliding_window/variant_2_max_sum_size_k.md)** - Easy
   - Find maximum sum in fixed window of size k
   - State Space: O(n) windows (n-k+1)
   - Incremental update: O(1) per slide vs O(k) recalculation

### Hybrid Patterns (Sliding Window + Auxiliary Data Structure):
3. **[Minimum Window Substring](variants/sliding_window/variant_3_min_window_substring.md)** ⭐ - LeetCode #76, Hard
   - Shortest substring containing all target characters
   - **Hybrid**: Sliding Window + Hash Map (frequency tracking)
   - Contract-while-valid strategy
   
6. **[Sliding Window Maximum](variants/sliding_window/variant_6_sliding_window_maximum.md)** ⭐ - LeetCode #239, Hard
   - Maximum value in each fixed-size window
   - **Hybrid**: Sliding Window + Monotonic Deque
   - O(n×k) → O(n) via dominated element removal
   
7. **[Longest Substring with K Distinct Characters](variants/sliding_window/variant_7_k_distinct_characters.md)** ⭐ - LeetCode #340, Medium
   - Longest substring with ≤ k distinct characters
   - **Hybrid**: Sliding Window + Hash Map (frequency counting)

⭐ = Hybrid pattern requiring auxiliary data structure

---

## Pattern Recognition:

**Use Sliding Window when:**
- Problem involves **contiguous subarrays/substrings**
- Brute force would enumerate O(n²) sequences
- Can maintain window state incrementally (sum, count, max, distinct chars, etc.)
- Monotonic property: extending/shrinking window has predictable effect on constraint

**Window Types:**
- **Fixed-size**: Window size k is given → Simple slide with add/remove
- **Variable-size**: Condition-based (sum ≥ target, distinct ≤ k) → Grow/shrink dynamically

**Optimization Patterns:**
- **Pure Sliding Window**: Track simple property (sum, count) with O(1) update
- **+ Hash Map**: Track frequencies or distinct elements
- **+ Monotonic Deque**: Track min/max with dominated element removal
- **+ Two Pointers**: When shrinking is deterministic based on condition

---

## Related Patterns:
- [Two Pointers](two_pointers_pattern.md) - Similar idea but windows may not be contiguous
- [Prefix Sum](prefix_sum_pattern.md) - Alternative for sum-based queries without updates
- [Monotonic Stack](monotonic_stack_pattern.md) - Related to monotonic deque in variant #6

## Practice Progression:
1. Start with **Variant #2** (fixed window, easiest)
2. Progress to **Variant #1, #4, #5** (variable window, core pattern)
3. Master **Variant #3, #6, #7** (hybrid patterns, interview favorites)

---

Each variant linked above contains comprehensive 4-step analysis:
1. **State Space Derivation** - Full enumeration with cardinality calculation
2. **Brute Force Solution** - Canonical implementation with value tracing
3. **Pruning Analysis** - Optimization opportunities and decision making
4. **Optimal Solution** - Skeleton transformation with complexity analysis

---

## When to Use This Pattern:

✅ **Use Sliding Window when:**
- Problem involves contiguous subarrays/substrings
- Need to find optimal window satisfying some condition
- Can maintain window state incrementally (sum, count, frequencies)
- Brute force would be O(n²) or O(n³)

❌ **Don't use when:**
- Need non-contiguous subsequences (use Dynamic Programming)
- Looking for all pairs/combinations (use Two Pointers or Backtracking)
- Window state cannot be maintained incrementally

## Common Mistakes:
1. **Forgetting window validity check** - Always validate window before updating result
2. **Wrong shrinking condition** - Understand when to expand vs. shrink
3. **Off-by-one errors** - Window size is `right - left + 1`, not `right - left`
4. **Not handling edge cases** - Empty array, k=0, k>n, etc.

### Input/Output:
- Input: `s = "abcabcbb"`
- Output: `3` (substring `"abc"`)

---

## **Step 1: Derive Full State Space from Problem**

### **Problem Analysis:**
- Find **longest substring** (contiguous sequence) with **all distinct characters**
- Need to check **all possible substrings** to find the longest valid one

### **State Space Definition:**
- **Type**: All non-zero-length contiguous substrings
- **Structure**: Linear sequences `[start, end]` where `0 ≤ start ≤ end < n`
- **Cardinality**: n(n+1)/2 substrings = **O(n²)**
- **Generation**: Nested loops - outer loop for start index, inner for end index

### **Full State Space Enumeration:**
For `s = "abcabcbb"` (n=8), there are 8×9/2 = 36 substrings:

```
Start=0: "a", "ab", "abc", "abca", "abcab", "abcabc", "abcabcb", "abcabcbb"  (8)
Start=1: "b", "bc", "bca", "bcab", "bcabc", "bcabcb", "bcabcbb"              (7)
Start=2: "c", "ca", "cab", "cabc", "cabcb", "cabcbb"                         (6)
Start=3: "a", "ab", "abc", "abcb", "abcbb"                                   (5)
Start=4: "b", "bc", "bcb", "bcbb"                                            (4)
Start=5: "c", "cb", "cbb"                                                    (3)
Start=6: "b", "bb"                                                           (2)
Start=7: "b"                                                                 (1)
Total: 36 substrings
```

---

## **Step 2: Brute Force Solution with Value Tracing**

### **Brute Force Approach:**
1. Generate all substrings using nested loops
2. For each substring, check if all characters are distinct
3. Track maximum length found

### **Brute Force Code:**
```csharp
int LongestSubstringBruteForce(string s) 
{
    int maxLen = 0;
    
    // Generate all substrings
    for (int start = 0; start < s.Length; start++)
    {
        for (int end = start; end < s.Length; end++)
        {
            // Extract substring [start, end]
            string substring = s.Substring(start, end - start + 1);
            
            // Check if all characters are distinct
            if (HasAllDistinct(substring))
            {
                maxLen = Math.Max(maxLen, substring.Length);
            }
        }
    }
    
    return maxLen;
}

bool HasAllDistinct(string str)
{
    var seen = new HashSet<char>();
    foreach (char c in str)
    {
        if (seen.Contains(c))
            return false;
        seen.Add(c);
    }
    return true;
}
```

### **Brute Force Value Trace (s = "abcabcbb"):**

| start | end | substring  | distinct? | length | maxLen |
|-------|-----|------------|-----------|--------|--------|
| 0     | 0   | "a"        | ✅ YES    | 1      | 1      |
| 0     | 1   | "ab"       | ✅ YES    | 2      | 2      |
| 0     | 2   | "abc"      | ✅ YES    | 3      | **3**  |
| 0     | 3   | "abca"     | ❌ NO (a repeats) | - | 3 |
| 0     | 4   | "abcab"    | ❌ NO     | -      | 3      |
| ...   | ... | ...        | ...       | ...    | ...    |
| 1     | 1   | "b"        | ✅ YES    | 1      | 3      |
| 1     | 2   | "bc"       | ✅ YES    | 2      | 3      |
| 1     | 3   | "bca"      | ✅ YES    | 3      | 3      |
| 1     | 4   | "bcab"     | ❌ NO (b repeats) | - | 3 |
| ...   | ... | ...        | ...       | ...    | ...    |
| 3     | 3   | "a"        | ✅ YES    | 1      | 3      |
| 3     | 4   | "ab"       | ✅ YES    | 2      | 3      |
| 3     | 5   | "abc"      | ✅ YES    | 3      | 3      |
| 3     | 6   | "abcb"     | ❌ NO (b repeats) | - | 3 |

**Result: maxLen = 3**

### **Complexity:**
- **Time**: O(n²) to generate substrings × O(n) to check distinctness = **O(n³)**
- **Space**: O(n) for hashset

---

## **Step 3: Can We Prune the Brute Force?**

### **Key Observations:**

1. **Early Termination**: When we find a duplicate at `end`, all extensions beyond `end` from same `start` will also have duplicates
   ```
   "abc" → distinct ✅
   "abca" → 'a' repeats ❌
   "abcab", "abcabc", ... → all will have 'a' repeating ❌
   ```

2. **Redundant Work**: We recompute distinctness from scratch for each substring
   - `"abc"` → check {a, b, c}
   - `"abca"` → check {a, b, c, a} ← recheck a, b, c unnecessarily

3. **Overlapping Windows**: Many substrings share prefixes
   - Both `"abc"` and `"abca"` process 'a', 'b', 'c' from scratch

### **Pruned Brute Force:**
```csharp
int LongestSubstringPruned(string s) 
{
    int maxLen = 0;
    
    for (int start = 0; start < s.Length; start++)
    {
        var seen = new HashSet<char>();
        
        for (int end = start; end < s.Length; end++)
        {
            char c = s[end];
            
            // Early termination - found duplicate
            if (seen.Contains(c))
                break; // Stop extending from this start
                
            seen.Add(c);
            maxLen = Math.Max(maxLen, end - start + 1);
        }
    }
    
    return maxLen;
}
```

**Improvement**: O(n²) time (no O(n) distinctness check per substring)

### **Is This Good Enough?**
**NO!** Still O(n²) - for n=10,000 we do 100 million operations. We can do better!

---

## **Step 4: Optimal Solution - Sliding Window**

### **Key Insight:**
Instead of restarting from each position, maintain a **sliding window** of distinct characters:
- **Expand** right pointer to include new characters
- **Contract** left pointer when duplicate found
- Each character enters and leaves window **at most once** → O(n)

### **Why This Works:**
When we find duplicate at `right`, instead of starting fresh from `start+1`, we can:
- Keep all the work done so far (characters in window)
- Just remove characters from left until duplicate is gone

### **Canonical Skeleton Applied:**
```csharp
int SlidingWindowTemplate(int[] nums, int kOrCondition) {
    int left = 0;
    int result = 0;
    var windowState = new Dictionary<int,int>();

    for (int right = 0; right < nums.Length; right++) {
        // Update windowState with nums[right]
        
        // Shrink window if condition violated
        while (ConditionViolated(windowState)) {
            // Update windowState by removing nums[left]
            left++;
        }

        // Update result based on current window
        result = UpdateResult(result, windowState);
    }

    return result;
}
```

### **Optimized Solution:**
```csharp
int LengthOfLongestSubstring(string s) 
{
    int left = 0, maxLen = 0;
    var seen = new HashSet<char>(); // Track characters in current window
    
    for (int right = 0; right < s.Length; right++) 
    {
        char c = s[right];
        
        // Shrink window until c is unique
        while (seen.Contains(c))
        {
            seen.Remove(s[left]);
            left++;
        }
        
        seen.Add(c);
        maxLen = Math.Max(maxLen, right - left + 1);
    }
    
    return maxLen;
}
```

### **Changes from Skeleton:**
1. **windowState** → `HashSet<char>` (only need to track existence)
2. **ConditionViolated** → `seen.Contains(c)` (duplicate found)
3. **UpdateResult** → `Math.Max(maxLen, right - left + 1)` (track max window size)
4. Window management: Add `c` after removing duplicates

### **Optimized Value Trace (s = "abcabcbb"):**

| Step | right | c   | left | Action | Window | seen        | maxLen |
|------|-------|-----|------|--------|--------|-------------|--------|
| 1    | 0     | 'a' | 0    | Add a  | "a"    | {a}         | 1      |
| 2    | 1     | 'b' | 0    | Add b  | "ab"   | {a,b}       | 2      |
| 3    | 2     | 'c' | 0    | Add c  | "abc"  | {a,b,c}     | **3**  |
| 4    | 3     | 'a' | 0    | Dup!   |        |             |        |
|      |       |     | 1    | Rm 'a' | "bc"   | {b,c}       | 3      |
|      |       |     | 1    | Add a  | "bca"  | {b,c,a}     | 3      |
| 5    | 4     | 'b' | 1    | Dup!   |        |             |        |
|      |       |     | 2    | Rm 'b' | "ca"   | {c,a}       | 3      |
|      |       |     | 2    | Add b  | "cab"  | {c,a,b}     | 3      |
| 6    | 5     | 'c' | 2    | Dup!   |        |             |        |
|      |       |     | 3    | Rm 'c' | "ab"   | {a,b}       | 3      |
|      |       |     | 3    | Add c  | "abc"  | {a,b,c}     | 3      |
| 7    | 6     | 'b' | 3    | Dup!   |        |             |        |
|      |       |     | 4    | Rm 'a' | "bc"   | {b,c}       | 3      |
|      |       |     | 5    | Rm 'b' | "c"    | {c}         | 3      |
|      |       |     | 5    | Add b  | "cb"   | {c,b}       | 3      |
| 8    | 7     | 'b' | 5    | Dup!   |        |             |        |
|      |       |     | 6    | Rm 'c' | "b"    | {b}         | 3      |
|      |       |     | 7    | Rm 'b' | ""     | {}          | 3      |
|      |       |     | 7    | Add b  | "b"    | {b}         | 3      |

**Result: 3**

### **State Space Comparison:**

| Approach | States Explored | Operations |
|----------|----------------|------------|
| Brute Force | 36 substrings | O(n³) = 512 ops |
| Pruned | ~20 substrings | O(n²) = 64 ops |
| Sliding Window | 8 windows (one per char) | O(n) = 16 ops |

### **Big-O Analysis:**
- **Time**: O(n) - each character enters/exits window once
- **Space**: O(min(n, charset)) for hashset
- **Optimization**: O(n³) → O(n²) → **O(n)** ✅

### **Key Takeaway:**
**Sliding Window optimizes STATE GENERATION**: Instead of enumerating all O(n²) substrings, we maintain a single dynamic window that adapts, visiting each position once!

</details>

<details>
<summary><b>Variant #2: Maximum Sum Subarray of Size k</b></summary>

## Variant #2: Maximum Sum Subarray of Size k

### Input/Output:
- Input: `nums = [2, 1, 5, 1, 3, 2]`, `k = 3`
- Output: `9` (subarray `[5,1,3]`)

---

## **Step 1: Derive Full State Space from Problem**

### **Problem Analysis:**
- Find **maximum sum** among all **size-k contiguous subarrays**
- Fixed window size makes this simpler than variable-length problems
- Must check every possible size-k window

### **State Space Definition:**
- **Type**: All non-zero-length contiguous subarrays with **size = k**
- **Structure**: Linear sequences `[start, start+k-1]` where `0 ≤ start ≤ n-k`
- **Cardinality**: `n - k + 1` subarrays = **O(n)**
- **Generation**: Single loop with fixed window size

### **Full State Space Enumeration:**
For `nums = [2, 1, 5, 1, 3, 2]` (n=6), `k=3`, there are 6-3+1 = **4 subarrays**:

```
[0,2]: [2, 1, 5] → sum = 8
[1,3]: [1, 5, 1] → sum = 7
[2,4]: [5, 1, 3] → sum = 9 ✅
[3,5]: [1, 3, 2] → sum = 6
```

**Note:** Only 4 valid windows, not n(n+1)/2 = 21 total subarrays!

---

## **Step 2: Brute Force Solution with Value Tracing**

### **Brute Force Approach:**
1. For each starting position from `0` to `n-k`
2. Sum the k elements starting from that position
3. Track maximum sum

### **Brute Force Code:**
```csharp
int MaxSumSubarrayBruteForce(int[] nums, int k) 
{ 
    int maxSum = int.MinValue;
    
    // Generate all size-k subarrays
    for (int start = 0; start <= nums.Length - k; start++)
    {
        // Calculate sum of subarray [start, start+k-1]
        int sum = 0;
        for (int i = start; i < start + k; i++)
        {
            sum += nums[i];
        }
        
        maxSum = Math.Max(maxSum, sum);
    }
    
    return maxSum;
}
```

### **Brute Force Value Trace (nums = [2,1,5,1,3,2], k=3):**

| start | Window Range | Elements  | sum calculation | sum | maxSum |
|-------|--------------|-----------|-----------------|-----|--------|
| 0     | [0,2]        | [2,1,5]   | 2+1+5           | 8   | 8      |
| 1     | [1,3]        | [1,5,1]   | 1+5+1           | 7   | 8      |
| 2     | [2,4]        | [5,1,3]   | 5+1+3           | 9   | **9**  |
| 3     | [3,5]        | [1,3,2]   | 1+3+2           | 6   | 9      |

**Result: 9**

### **Complexity:**
- **Time**: O(n) windows × O(k) sum calculation = **O(n·k)**
- **Space**: O(1)

---

## **Step 3: Can We Prune the Brute Force?**

### **Key Observations:**

1. **Redundant Recalculation**: Windows overlap heavily!
   ```
   Window [0,2]: sum(2, 1, 5) = 8
   Window [1,3]: sum(1, 5, 1) = 7
                  ↑  ↑
                  Already calculated in previous window!
   ```

2. **Overlapping Elements**: Adjacent windows share k-1 elements
   - Window 1: `[2, 1, 5]`
   - Window 2: `[1, 5, 1]` ← shares `[1, 5]` with Window 1
   - We recompute sum of `1+5` unnecessarily

3. **Incremental Update**: When sliding from `[a,b,c]` to `[b,c,d]`:
   ```
   New sum = Old sum - a + d
   ```
   Only 2 operations instead of k!

### **Can We Do Better?**
**YES!** Sliding window with incremental updates: O(n) instead of O(n·k)

---

## **Step 4: Optimal Solution - Sliding Window**

### **Key Insight:**
Maintain a **running sum** and update it incrementally:
- **Remove** leftmost element when sliding right
- **Add** new rightmost element
- Each window updated in O(1) instead of O(k)

### **Canonical Skeleton Applied:**
```csharp
int SlidingWindowTemplate(int[] nums, int kOrCondition) {
    int left = 0;
    int result = 0;
    var windowState = new Dictionary<int,int>();

    for (int right = 0; right < nums.Length; right++) {
        // Update windowState with nums[right]
        
        // Shrink window if condition violated
        while (ConditionViolated(windowState)) {
            // Update windowState by removing nums[left]
            left++;
        }

        // Update result based on current window
        result = UpdateResult(result, windowState);
    }

    return result;
}
```

### **Optimized Solution:**
```csharp
int MaxSumSubarray(int[] nums, int k) 
{
    int maxSum = 0, windowSum = 0;
    
    // Build initial window [0, k-1]
    for (int i = 0; i < k; i++)
    {
        windowSum += nums[i];
    }
    maxSum = windowSum;
    
    // Slide window: remove left, add right
    for (int right = k; right < nums.Length; right++)
    {
        int left = right - k; // Element leaving window
        windowSum = windowSum - nums[left] + nums[right];
        maxSum = Math.Max(maxSum, windowSum);
    }
    
    return maxSum;
}
```

### **Changes from Skeleton:**
1. **Fixed window size**: No while loop needed - window always size k
2. **windowState** → `windowSum` (single integer, not dictionary)
3. **Initial window**: Build first window separately
4. **Sliding logic**: `windowSum - nums[left] + nums[right]`
5. **UpdateResult** → `Math.Max(maxSum, windowSum)`

### **Optimized Value Trace (nums = [2,1,5,1,3,2], k=3):**

**Phase 1: Build Initial Window**
| i   | nums[i] | windowSum | maxSum |
|-----|---------|-----------|--------|
| 0   | 2       | 2         | -      |
| 1   | 1       | 3         | -      |
| 2   | 5       | 8         | 8      |

**Phase 2: Slide Window**
| right | nums[right] | left | nums[left] | Operation | windowSum | maxSum |
|-------|-------------|------|------------|-----------|-----------|--------|
| 3     | 1           | 0    | 2          | 8-2+1     | 7         | 8      |
| 4     | 3           | 1    | 1          | 7-1+3     | 9         | **9**  |
| 5     | 2           | 2    | 5          | 9-5+2     | 6         | 9      |

**Result: 9**

### **State Space Comparison:**

| Approach | Windows Processed | Operations per Window | Total Operations |
|----------|-------------------|----------------------|------------------|
| Brute Force | 4 windows | O(k) = 3 adds | O(n·k) = 12 ops |
| Sliding Window | 4 windows | O(1) = 1 subtract + 1 add | O(n) = 2+6 = 8 ops |

### **Big-O Analysis:**
- **Time**: O(k) initial window + O(n-k) slides = **O(n)**
- **Space**: O(1)
- **Optimization**: O(n·k) → **O(n)** ✅

### **Key Takeaway:**
**Fixed-size sliding window** eliminates redundant recalculation by maintaining running state (sum) and updating incrementally. Perfect for problems with **constant window constraints**!

</details>

<details>
<summary><b>Variant #3: Minimum Window Substring</b></summary>

## Variant #3: Minimum Window Substring
    int left = 0, windowSum = 0, maxSum = 0;
    
    for (int right = 0; right < nums.Length; right++) 
    {
        // Expand window: add element at right
        windowSum += nums[right];
        
        // Shrink window from left if size exceeds k
        while (right - left + 1 > k) 
        {
            windowSum -= nums[left];
            left++;
        }
        
        // Update result once we have a full window of size k
        if (right - left + 1 == k)
        {
            maxSum = Math.Max(maxSum, windowSum);
        }
    }
    
    return maxSum;
}
``` 

### Explanation of Pruning:
-   Instead of generating all subarrays, maintain sum of current window of size k.
-   Use explicit left/right pointers with while loop to maintain window size.
-   Expand with right, shrink from left when window exceeds k → O(n) vs O(n*k).

### Optimized Solution Code Walkthrough / Variable Trace:
| right | nums[right] | left | windowSum | window size | maxSum |
| ----- | ----------- | ---- | --------- | ----------- | ------ |
| 0     | 2           | 0    | 2         | 1           | 0      |
| 1     | 1           | 0    | 3         | 2           | 0      |
| 2     | 5           | 0    | 8         | 3           | 8      |
| 3     | 1           | 1    | 7         | 3           | 8      |
| 4     | 3           | 2    | 9         | 3           | 9      |
| 5     | 2           | 3    | 6         | 3           | 9      |

### Big-O Analysis:
-   **Brute Force:** O(n*k) → sum for each subarray
-   **Optimized:** O(n) → sliding window
-   **Space Complexity:** O(1)

</details>

<details>
<summary><b>Variant #3: Minimum Window Substring</b></summary>

## Variant #3: Minimum Window Substring

### Input/Output:
- Input: `s = "ADOBECODEBANC"`, `t = "ABC"`  
- Output: `"BANC"`  

### Full State Space:
All contiguous substrings of `s`:
```
"A", "AD", "ADO", "ADOB", "ADOBE", "ADOBEC", "ADOBECO", "ADOBECOD", "ADOBECODE", ...,
"D", "DO", "DOB", "DOBE", "DOBEC", "DOBECO", "DOBECOD", "DOBECODE", ...,
"O", "OB", "OBE", "OBEC", "OBECO", "OBECOD", "OBECODE", ...,
"B", "BE", "BEC", "BECO", "BECOD", "BECODE", ...,
"E", "EC", "ECO", "ECOD", "ECODE", ...,
"C", "CO", "COD", "CODE", ...,
"O", "OD", "ODE", ...,
"D", "DE", ...,
"E", "EB", "EBA", "EBAN", "EBANC",
"B", "BA", "BAN", "BANC",
"A", "AN", "ANC",
"N", "NC",
"C"
```
```csharp
void GenerateAllSubstrings(string s)
{
    var allSubstrings = new List<string>();
    
    // For each starting position
    for (int start = 0; start < s.Length; start++)
    {
        // For each ending position from start (inclusive)
        for (int end = start; end < s.Length; end++)
        {
            allSubstrings.Add(s.Substring(start, end - start + 1));
        }
    }
}
```

### Expected/Pruned State Space:
Only substrings that contain all characters from `t` (A, B, C):
```
"ADOBEC", "ADOBECO", "ADOBECOD", "ADOBECODE", "ADOBECODEB", "ADOBECODEBA", "ADOBECODEBAN", "ADOBECODEBANC",
"DOBECODEBA", "DOBECODABAN", "DOBECODABANC",
"OBECODEBA", "OBECODEBAN", "OBECODEBANC",
"BECODEBA", "BECODEBAN", "BECODEBANC",
"ECODEBA", "ECODEBAN", "ECODEBANC",
"CODEBA", "CODEBAN", "CODEBANC",
"ODEBAN", "ODEBANC",
"DEBAN", "DEBANC",
"EBANC",
"BANC"
```
```csharp
void GenerateValidSubstrings(string s, string t)
{
    var validSubstrings = new List<string>();
    var need = new Dictionary<char, int>();
    foreach (var c in t) 
        need[c] = need.GetValueOrDefault(c) + 1;
    
    // For each starting position
    for (int start = 0; start < s.Length; start++)
    {
        var window = new Dictionary<char, int>();
        int have = 0;
        
        // For each ending position from start
        for (int end = start; end < s.Length; end++)
        {
            char c = s[end];
            
            // Update window
            if (need.ContainsKey(c))
            {
                window[c] = window.GetValueOrDefault(c) + 1;
                if (window[c] == need[c]) 
                    have++;
            }
            
            // If we have all required characters
            if (have == need.Count)
            {
                validSubstrings.Add(s.Substring(start, end - start + 1));
            }
        }
    }
}
```

### State Space Leading to Output:
Minimum-length substring containing all chars from `t` → `"BANC"` (length 4)

### Brute Force Canonical Skeleton:
```csharp
string MinWindowBruteForce(string s, string t) 
{
    var need = new Dictionary<char, int>();
    foreach (var c in t) 
        need[c] = need.GetValueOrDefault(c) + 1;
    
    string minStr = "";
    int minLen = int.MaxValue;
    
    // Generate all contiguous substrings
    for (int start = 0; start < s.Length; start++)
    {
        var window = new Dictionary<char, int>();
        int have = 0;
        
        for (int end = start; end < s.Length; end++)
        {
            char c = s[end];
            
            // Update window
            if (need.ContainsKey(c))
            {
                window[c] = window.GetValueOrDefault(c) + 1;
                if (window[c] == need[c]) 
                    have++;
            }
            
            // If current substring contains all required characters
            if (have == need.Count)
            {
                int len = end - start + 1;
                if (len < minLen)
                {
                    minLen = len;
                    minStr = s.Substring(start, len);
                }
            }
        }
    }
    
    return minStr;
}
```

### Brute Force Code Walkthrough / Variable Trace:
| start | end | substring      | have | need.Count | minLen | minStr   |
| ----- | --- | -------------- | ---- | ---------- | ------ | -------- |
| 0     | 5   | "ADOBEC"       | 3    | 3          | 6      | "ADOBEC" |
| 0     | 6   | "ADOBECO"      | 3    | 3          | 6      | "ADOBEC" |
| 0     | 7   | "ADOBECOD"     | 3    | 3          | 6      | "ADOBEC" |
| 1     | 9   | "DOBECODEBA"   | 3    | 3          | 6      | "ADOBEC" |
| 9     | 12  | "BANC"         | 3    | 3          | 4      | "BANC"   |
| ...   | ... | ...            | ...  | ...        | ...    | ...      |
### Optimized Solution from Canonical Skeleton:
```csharp
string MinWindow(string s, string t) 
{
    var need = new Dictionary<char, int>();
    foreach (var c in t) 
        need[c] = need.GetValueOrDefault(c) + 1;

    int left = 0, minLen = int.MaxValue, startIdx = 0;
    int have = 0;
    var window = new Dictionary<char, int>();

    for (int right = 0; right < s.Length; right++) 
    {
        char c = s[right];
        
        if (need.ContainsKey(c)) 
        {
            window[c] = window.GetValueOrDefault(c) + 1;
            if (window[c] == need[c]) 
                have++;
        }

        while (have == need.Count) 
        {
            int len = right - left + 1;
            if (len < minLen) 
            {
                minLen = len;
                startIdx = left;
            }
            
            char leftChar = s[left];
            if (need.ContainsKey(leftChar)) 
            {
                window[leftChar]--;
                if (window[leftChar] < need[leftChar]) 
                    have--;
            }
            left++;
        }
    }

    return minLen == int.MaxValue ? "" : s.Substring(startIdx, minLen);
}
```

### Explanation of Pruning:
- Instead of generating all substrings, maintain a sliding window that contains all required characters
- Expand `right` to include more characters until all requirements met
- Shrink from `left` while still satisfying constraints → finds minimal window
- No need to enumerate all valid substrings

### Optimized Solution Code Walkthrough / Variable Trace:
| right | char | window            | have | left | minLen | startIdx |
| ----- | ---- | ----------------- | ---- | ---- | ------ | -------- |
| 0     | A    | {A:1}             | 1    | 0    | ∞      | 0        |
| 3     | B    | {A:1,B:1}         | 2    | 0    | ∞      | 0        |
| 5     | C    | {A:1,B:1,C:1}     | 3    | 0    | 6      | 0        |
| 5     | C    | {D:1,B:1,C:1}     | 3    | 1    | 6      | 0        |
| 9     | A    | {B:1,A:1,C:1}     | 3    | 6    | 6      | 0        |
| 12    | C    | {B:1,A:1,C:1}     | 3    | 9    | 4      | 9        |
| ...   | ...  | ...               | ...  | ...  | ...    | ...      |

### Big-O Analysis:
- **Brute Force:** O(n² × m) → for each substring check if it contains all chars from `t`
- **Optimized:** O(n + m) → sliding window (n) + building need map (m)
- **Space Complexity:** O(m) for `need` map + O(charset) for `window` map

</details>

<details>
<summary><b>Variant #4: Subarray Product Less Than K</b></summary>

## Variant #4: Subarray Product Less Than K

### Input/Output:
- Input: `nums = [10,5,2,6]`, `k = 100`
- Output: `8` → subarrays `[10],[5],[2],[6],[10,5],[5,2],[2,6],[5,2,6]`

### Full State Space:
All contiguous subarrays:
```
[10], [10,5], [10,5,2], [10,5,2,6],
[5], [5,2], [5,2,6],
[2], [2,6],
[6]
```
```csharp
void GenerateAllSubarrays(int[] nums)
{
    var allSubarrays = new List<List<int>>();
    
    // For each starting position
    for (int start = 0; start < nums.Length; start++)
    {
        // For each ending position from start
        for (int end = start; end < nums.Length; end++)
        {
            var subarray = new List<int>();
            for (int i = start; i <= end; i++)
            {
                subarray.Add(nums[i]);
            }
            allSubarrays.Add(subarray);
        }
    }
}
```

### Expected/Pruned State Space:
Only subarrays with product < k (100):
```
[10] (product=10), [5] (product=5), [2] (product=2), [6] (product=6),
[10,5] (product=50), [5,2] (product=10), [2,6] (product=12),
[5,2,6] (product=60)
```
```csharp
void GenerateValidSubarrays(int[] nums, int k)
{
    var validSubarrays = new List<List<int>>();
    
    // For each starting position
    for (int start = 0; start < nums.Length; start++)
    {
        int product = 1;
        
        // For each ending position from start
        for (int end = start; end < nums.Length; end++)
        {
            product *= nums[end];
            
            // Only include if product < k
            if (product < k)
            {
                var subarray = new List<int>();
                for (int i = start; i <= end; i++)
                {
                    subarray.Add(nums[i]);
                }
                validSubarrays.Add(subarray);
            }
            else
            {
                break; // No point extending further from this start
            }
        }
    }
}
```

### State Space Leading to Output:
Count of all valid subarrays → 8

### Brute Force Canonical Skeleton:
```csharp
int NumSubarrayProductLessThanKBruteForce(int[] nums, int k) 
{
    int count = 0;
    
    // Generate all contiguous subarrays
    for (int start = 0; start < nums.Length; start++)
    {
        int product = 1;
        
        for (int end = start; end < nums.Length; end++)
        {
            product *= nums[end];
            
            if (product < k)
            {
                count++;
            }
            else
            {
                break; // Product will only get larger
            }
        }
    }
    
    return count;
}
```

### Brute Force Code Walkthrough / Variable Trace:
| start | end | subarray  | product | count |
| ----- | --- | --------- | ------- | ----- |
| 0     | 0   | [10]      | 10      | 1     |
| 0     | 1   | [10,5]    | 50      | 2     |
| 0     | 2   | [10,5,2]  | 100     | 2     |
| 1     | 1   | [5]       | 5       | 3     |
| 1     | 2   | [5,2]     | 10      | 4     |
| 1     | 3   | [5,2,6]   | 60      | 5     |
| 2     | 2   | [2]       | 2       | 6     |
| 2     | 3   | [2,6]     | 12      | 7     |
| 3     | 3   | [6]       | 6       | 8     |
### Optimized Solution from Canonical Skeleton:
```csharp
int NumSubarrayProductLessThanK(int[] nums, int k) 
{
    if (k <= 1) return 0;

    int prod = 1, left = 0, count = 0;
    
    for (int right = 0; right < nums.Length; right++) 
    {
        prod *= nums[right];
        
        while (prod >= k) 
        {
            prod /= nums[left];
            left++;
        }
        
        // All subarrays ending at right with product < k
        count += right - left + 1;
    }
    
    return count;
}
```

### Explanation of Pruning:
- Maintain a sliding window where product < k
- When product ≥ k, shrink window from left
- Key insight: when window is valid, ALL subarrays ending at `right` within the window are valid
- This means `right - left + 1` new subarrays (no need to enumerate)
- Example: window [5,2,6] adds 3 subarrays: [6], [2,6], [5,2,6]

### Optimized Solution Code Walkthrough / Variable Trace:
| right | nums[right] | prod (before) | prod (after) | left | right-left+1 | count |
| ----- | ----------- | ------------- | ------------ | ---- | ------------ | ----- |
| 0     | 10          | 1             | 10           | 0    | 1            | 1     |
| 1     | 5           | 10            | 50           | 0    | 2            | 3     |
| 2     | 2           | 50            | 10 (÷10)     | 1    | 2            | 5     |
| 3     | 6           | 10            | 60           | 1    | 3            | 8     |

### Big-O Analysis:
- **Brute Force:** O(n²) → check every contiguous subarray
- **Optimized:** O(n) → each element visited at most twice (once by right, once by left)
- **Space Complexity:** O(1)

</details>

<details>
<summary><b>Variant #5: Minimum Size Subarray with Sum ≥ S</b></summary>

## Variant #5: Minimum Size Subarray with Sum ≥ S

### Input/Output:
- Input: `nums = [2,3,1,2,4,3]`, `s = 7`  
- Output: `2` → subarray `[4,3]` is minimal length  

### Full State Space:
All contiguous subarrays:
```
[2], [2,3], [2,3,1], [2,3,1,2], [2,3,1,2,4], [2,3,1,2,4,3],
[3], [3,1], [3,1,2], [3,1,2,4], [3,1,2,4,3],
[1], [1,2], [1,2,4], [1,2,4,3],
[2], [2,4], [2,4,3],
[4], [4,3],
[3]
```
```csharp
void GenerateAllSubarrays(int[] nums)
{
    var allSubarrays = new List<List<int>>();
    
    // For each starting position
    for (int start = 0; start < nums.Length; start++)
    {
        // For each ending position from start
        for (int end = start; end < nums.Length; end++)
        {
            var subarray = new List<int>();
            for (int i = start; i <= end; i++)
            {
                subarray.Add(nums[i]);
            }
            allSubarrays.Add(subarray);
        }
    }
}
```

### Expected/Pruned State Space:
Only subarrays with sum ≥ s (7):
```
[2,3,1,2] (sum=8), [2,3,1,2,4] (sum=12), [2,3,1,2,4,3] (sum=15),
[3,1,2,4] (sum=10), [3,1,2,4,3] (sum=13),
[1,2,4] (sum=7), [1,2,4,3] (sum=10),
[2,4,3] (sum=9),
[4,3] (sum=7)
```
```csharp
void GenerateValidSubarrays(int[] nums, int s)
{
    var validSubarrays = new List<List<int>>();
    
    // For each starting position
    for (int start = 0; start < nums.Length; start++)
    {
        int sum = 0;
        
        // For each ending position from start
        for (int end = start; end < nums.Length; end++)
        {
            sum += nums[end];
            
            // Only include if sum >= s
            if (sum >= s)
            {
                var subarray = new List<int>();
                for (int i = start; i <= end; i++)
                {
                    subarray.Add(nums[i]);
                }
                validSubarrays.Add(subarray);
            }
        }
    }
}
```

### State Space Leading to Output:
Minimal length among valid subarrays → `[4,3]` and `[1,2,4]` both have length 2

### Brute Force Canonical Skeleton:
```csharp
int MinSubArrayLenBruteForce(int s, int[] nums) 
{
    int minLen = int.MaxValue;
    
    // Generate all contiguous subarrays
    for (int start = 0; start < nums.Length; start++)
    {
        int sum = 0;
        
        for (int end = start; end < nums.Length; end++)
        {
            sum += nums[end];
            
            if (sum >= s)
            {
                minLen = Math.Min(minLen, end - start + 1);
            }
        }
    }
    
    return minLen == int.MaxValue ? 0 : minLen;
}
```

### Brute Force Code Walkthrough / Variable Trace:
| start | end | subarray      | sum | minLen |
| ----- | --- | ------------- | --- | ------ |
| 0     | 3   | [2,3,1,2]     | 8   | 4      |
| 0     | 4   | [2,3,1,2,4]   | 12  | 4      |
| 1     | 4   | [3,1,2,4]     | 10  | 4      |
| 2     | 4   | [1,2,4]       | 7   | 3      |
| 3     | 5   | [2,4,3]       | 9   | 3      |
| 4     | 5   | [4,3]         | 7   | 2      |
| ...   | ... | ...           | ... | ...    |

### Optimized Solution from Canonical Skeleton:
```csharp
int MinSubArrayLen(int s, int[] nums) 
{
    int left = 0, sum = 0, minLen = int.MaxValue;
    
    for (int right = 0; right < nums.Length; right++) 
    {
        sum += nums[right];
        
        while (sum >= s) 
        {
            minLen = Math.Min(minLen, right - left + 1);
            sum -= nums[left];
            left++;
        }
    }
    
    return minLen == int.MaxValue ? 0 : minLen;
}
```

### Explanation of Pruning:
- Maintain a sliding window with current sum
- Expand window by moving `right` to include more elements
- When sum ≥ s, try to minimize window by moving `left`
- Only track minimum length, not all valid subarrays

### Optimized Solution Code Walkthrough / Variable Trace:
| right | nums[right] | sum | left | minLen |
| ----- | ----------- | --- | ---- | ------ |
| 0     | 2           | 2   | 0    | ∞      |
| 1     | 3           | 5   | 0    | ∞      |
| 2     | 1           | 6   | 0    | ∞      |
| 3     | 2           | 8   | 0    | 4      |
| 3     | 2           | 6   | 1    | 4      |
| 4     | 4           | 10  | 1    | 4      |
| 4     | 4           | 7   | 2    | 3      |
| 5     | 3           | 10  | 2    | 3      |
| 5     | 3           | 9   | 3    | 3      |
| 5     | 3           | 7   | 4    | 2      |

### Big-O Analysis:
- **Brute Force:** O(n²) → check every contiguous subarray
- **Optimized:** O(n) → each element visited at most twice
- **Space Complexity:** O(1)

</details>

<details>
<summary><b>Variant #6: Sliding Window Maximum</b></summary>

## Variant #6: Sliding Window Maximum (Max in Every Subarray of Size K)

### Input/Output:
- Input: `nums = [1,3,-1,-3,5,3,6,7]`, `k = 3`
- Output: `[3,3,5,5,6,7]`

### Full State Space:
All contiguous subarrays of size k:
```
[1,3,-1], [3,-1,-3], [-1,-3,5], [-3,5,3], [5,3,6], [3,6,7]
```
```csharp
void GenerateAllSubarraysOfSizeK(int[] nums, int k)
{
    var allSubarrays = new List<List<int>>();
    
    for (int start = 0; start <= nums.Length - k; start++)
    {
        var subarray = new List<int>();
        for (int i = start; i < start + k; i++)
        {
            subarray.Add(nums[i]);
        }
        allSubarrays.Add(subarray);
    }
}
```

### Expected/Pruned State Space:
Maximum value of each window:
```
[1,3,-1] → 3
[3,-1,-3] → 3
[-1,-3,5] → 5
[-3,5,3] → 5
[5,3,6] → 6
[3,6,7] → 7
```
```csharp
void GenerateMaxOfEachWindow(int[] nums, int k)
{
    var maxValues = new List<int>();
    
    for (int start = 0; start <= nums.Length - k; start++)
    {
        int maxVal = int.MinValue;
        for (int i = start; i < start + k; i++)
        {
            maxVal = Math.Max(maxVal, nums[i]);
        }
        maxValues.Add(maxVal);
    }
}
```

### State Space Leading to Output:
Array of maximum values → `[3,3,5,5,6,7]`

### Brute Force Canonical Skeleton:
```csharp
int[] MaxSlidingWindowBruteForce(int[] nums, int k) 
{
    int n = nums.Length;
    var result = new List<int>();

    // For each window of size k
    for (int start = 0; start <= n - k; start++) 
    {
        int maxVal = int.MinValue;
        
        // Find max in current window
        for (int i = start; i < start + k; i++) 
        {
            maxVal = Math.Max(maxVal, nums[i]);
        }
        
        result.Add(maxVal);
    }
    
    return result.ToArray();
}
```

### Brute Force Code Walkthrough / Variable Trace:
| start | window      | maxVal | result          |
| ----- | ----------- | ------ | --------------- |
| 0     | [1,3,-1]    | 3      | [3]             |
| 1     | [3,-1,-3]   | 3      | [3,3]           |
| 2     | [-1,-3,5]   | 5      | [3,3,5]         |
| 3     | [-3,5,3]    | 5      | [3,3,5,5]       |
| 4     | [5,3,6]     | 6      | [3,3,5,5,6]     |
| 5     | [3,6,7]     | 7      | [3,3,5,5,6,7]   |

### Optimized Solution from Canonical Skeleton:
```csharp
int[] MaxSlidingWindow(int[] nums, int k) 
{
    int n = nums.Length;
    if (n * k == 0) return new int[0];
    
    var result = new int[n - k + 1];
    var deque = new LinkedList<int>(); // Stores indices

    for (int i = 0; i < n; i++) 
    {
        // Remove indices outside current window
        if (deque.Count > 0 && deque.First.Value <= i - k)
            deque.RemoveFirst();
        
        // Remove smaller elements from back (they'll never be max)
        while (deque.Count > 0 && nums[i] >= nums[deque.Last.Value])
            deque.RemoveLast();
        
        // Add current index
        deque.AddLast(i);
        
        // Start recording once we have a full window
        if (i >= k - 1)
            result[i - k + 1] = nums[deque.First.Value];
    }
    
    return result;
}
```

### Explanation of Pruning:
- **Note:** This is a **hybrid problem** - Fixed-size Sliding Window + Monotonic Deque
- **Why not left/right pointers?** The window size is fixed (always k), so we don't have the typical expand/shrink pattern
- **Why LinkedList (deque)?** We need O(1) operations at both ends to maintain a monotonic decreasing queue
- **Monotonic Deque strategy:**
  - Store **indices** (not values) in decreasing order of their corresponding values
  - Front of deque always contains index of current window maximum
  - Remove indices outside current window from front: `if (deque.First.Value <= i - k)`
  - Remove smaller elements from back before adding new element (they can never be maximum)
  - Each element added/removed at most once → O(n) total

### Optimized Solution Code Walkthrough / Variable Trace:
| i | nums[i] | deque (indices) | nums[deque] | result          |
| - | ------- | --------------- | ----------- | --------------- |
| 0 | 1       | [0]             | [1]         | []              |
| 1 | 3       | [1]             | [3]         | []              |
| 2 | -1      | [1,2]           | [3,-1]      | [3]             |
| 3 | -3      | [1,2,3]         | [3,-1,-3]   | [3,3]           |
| 4 | 5       | [4]             | [5]         | [3,3,5]         |
| 5 | 3       | [4,5]           | [5,3]       | [3,3,5,5]       |
| 6 | 6       | [6]             | [6]         | [3,3,5,5,6]     |
| 7 | 7       | [7]             | [7]         | [3,3,5,5,6,7]   |

### Big-O Analysis:
- **Brute Force:** O(n × k) → for each window, find max in O(k)
- **Optimized:** O(n) → each element added and removed from deque at most once
- **Space Complexity:** O(k) → deque size at most k

</details>

<details>
<summary><b>Variant #7: Variable-Length Window with K Distinct Characters</b></summary>

## Variant #7: Variable-Length Window Problems with Conditions

### Input/Output:
- Input: `s = "eceba"`, `k = 2` (at most 2 distinct characters)
- Output: `"ece"` (length 3)

### Full State Space:
All contiguous substrings of s:
```
"e", "ec", "ece", "eceb", "eceba",
"c", "ce", "ceb", "ceba",
"e", "eb", "eba",
"b", "ba",
"a"
```
```csharp
void GenerateAllSubstrings(string s)
{
    var allSubstrings = new List<string>();
    
    // For each starting position
    for (int start = 0; start < s.Length; start++)
    {
        // For each ending position from start (inclusive)
        for (int end = start; end < s.Length; end++)
        {
            allSubstrings.Add(s.Substring(start, end - start + 1));
        }
    }
}
```

### Expected/Pruned State Space:
Only substrings with ≤ 2 distinct characters:
```
"e", "ec", "ece",
"c", "ce",
"e", "eb",
"b", "ba",
"a"
```
```csharp
void GenerateValidSubstrings(string s, int k)
{
    var validSubstrings = new List<string>();
    
    // For each starting position
    for (int start = 0; start < s.Length; start++)
    {
        var map = new Dictionary<char, int>();
        
        // For each ending position from start
        for (int end = start; end < s.Length; end++)
        {
            char c = s[end];
            map[c] = map.GetValueOrDefault(c) + 1;
            
            // Only include if distinct count <= k
            if (map.Count <= k)
            {
                validSubstrings.Add(s.Substring(start, end - start + 1));
            }
            else
            {
                break; // No point extending further
            }
        }
    }
}
```

### State Space Leading to Output:
Longest substring with ≤ 2 distinct characters → `"ece"` (length 3)

### Brute Force Canonical Skeleton:
```csharp
string LongestSubstringKDistinctBruteForce(string s, int k) 
{
    int maxLen = 0;
    string result = "";
    
    // Generate all contiguous substrings
    for (int start = 0; start < s.Length; start++) 
    {
        var map = new Dictionary<char, int>();
        
        for (int end = start; end < s.Length; end++) 
        {
            char c = s[end];
            map[c] = map.GetValueOrDefault(c) + 1;
            
            // Check if valid
            if (map.Count <= k) 
            {
                int len = end - start + 1;
                if (len > maxLen) 
                {
                    maxLen = len;
                    result = s.Substring(start, len);
                }
            }
            else
            {
                break; // Too many distinct characters
            }
        }
    }
    
    return result;
}
```

### Brute Force Code Walkthrough / Variable Trace:
| start | end | substring | distinct | maxLen | result |
| ----- | --- | --------- | -------- | ------ | ------ |
| 0     | 0   | "e"       | 1        | 1      | "e"    |
| 0     | 1   | "ec"      | 2        | 2      | "ec"   |
| 0     | 2   | "ece"     | 2        | 3      | "ece"  |
| 0     | 3   | "eceb"    | 3        | 3      | "ece"  |
| 1     | 1   | "c"       | 1        | 3      | "ece"  |
| 1     | 2   | "ce"      | 2        | 3      | "ece"  |
| ...   | ... | ...       | ...      | ...    | ...    |

### Optimized Solution from Canonical Skeleton:
```csharp
string LongestSubstringKDistinct(string s, int k) 
{
    int left = 0, maxLen = 0, start = 0;
    var map = new Dictionary<char, int>();

    for (int right = 0; right < s.Length; right++) 
    {
        char c = s[right];
        map[c] = map.GetValueOrDefault(c) + 1;

        while (map.Count > k) 
        {
            char leftChar = s[left];
            map[leftChar]--;
            if (map[leftChar] == 0) 
                map.Remove(leftChar);
            left++;
        }

        if (right - left + 1 > maxLen) 
        {
            maxLen = right - left + 1;
            start = left;
        }
    }

    return s.Substring(start, maxLen);
}
```

### Explanation of Pruning:
- Maintain a sliding window with at most k distinct characters
- Expand window by moving `right` to include more characters
- When distinct count > k, shrink from `left` until valid again
- Track maximum length window seen

### Optimized Solution Code Walkthrough / Variable Trace:
| right | char | map        | left | maxLen | start |
| ----- | ---- | ---------- | ---- | ------ | ----- |
| 0     | e    | {e:1}      | 0    | 1      | 0     |
| 1     | c    | {e:1,c:1}  | 0    | 2      | 0     |
| 2     | e    | {e:2,c:1}  | 0    | 3      | 0     |
| 3     | b    | {e:2,c:1,b:1} | 0 | 3      | 0     |
| 3     | b    | {e:1,b:1}  | 2    | 3      | 0     |
| 4     | a    | {e:1,b:1,a:1} | 2 | 3      | 0     |
| 4     | a    | {b:1,a:1}  | 3    | 3      | 0     |

### Big-O Analysis:
- **Brute Force:** O(n²) → check every contiguous substring
- **Optimized:** O(n) → each character visited at most twice
- **Space Complexity:** O(k) → at most k distinct characters in map

</details>