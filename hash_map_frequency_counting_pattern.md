# Pattern Title: Hash Map / Frequency Counting

## Pattern Description:
- What is it?  
  Hash Map / Frequency Counting is a technique that uses a hash map (dictionary) to count, track, or aggregate occurrences of elements while iterating through a collection, enabling O(1) lookups instead of repeated scans.

- What abstract problem does it solve?  
  It converts repeated scanning or pairwise comparison problems (often O(n²) or O(n!)) into single-pass O(n) solutions by trading space for time, maintaining element frequencies or mappings for constant-time access.

- Real-world problem variants (simplest first):  
  1. Valid Anagram
  2. Two Sum
  3. First Non-Repeating Character
  4. Group Anagrams
  5. Subarray Sum Equals K
  6. Longest Substring with K Distinct Characters
  7. Top K Frequent Elements

## Canonical Code Skeleton:

```csharp
Dictionary<T, int> FrequencyCountTemplate(IEnumerable<T> input) {
    var map = new Dictionary<T, int>();
    
    // Build frequency map
    foreach (var item in input)
    {
        map[item] = map.GetValueOrDefault(item) + 1;
    }
    
    // Use map for O(1) lookups
    // Second pass or logic using map
    
    return map;
}
```

<details>
<summary><b>Variant #1: Valid Anagram</b></summary>

## Variant #1: Valid Anagram

### Input/Output:
- Input: `s = "anagram"`, `t = "nagaram"`
- Output: `true`

### Full State Space:
All possible permutations of string `s` to check if any match `t`:
```
"anagram" has 7! = 5,040 permutations
"anagram", "anagrma", "anagamr", "anagarm", "anamgar", "anamgra", ...
```
```csharp
void GenerateAllPermutations(string s)
{
    var allPermutations = new List<string>();
    
    void Backtrack(char[] chars, int start)
    {
        if (start == chars.Length)
        {
            allPermutations.Add(new string(chars));
            return;
        }
        
        for (int end = start; end < chars.Length; end++)
        {
            // Swap
            (chars[start], chars[end]) = (chars[end], chars[start]);
            Backtrack(chars, start + 1);
            // Swap back
            (chars[start], chars[end]) = (chars[end], chars[start]);
        }
    }
    
    Backtrack(s.ToCharArray(), 0);
}
```

### Expected/Pruned State Space:
Only character frequency comparison needed:
```
s: {a:3, n:1, g:1, r:1, m:1}
t: {n:1, a:3, g:1, r:1, m:1}
Frequencies match → anagram
```
```csharp
void CompareFrequencies(string s, string t)
{
    var freqS = new Dictionary<char, int>();
    var freqT = new Dictionary<char, int>();
    
    foreach (char c in s)
        freqS[c] = freqS.GetValueOrDefault(c) + 1;
    
    foreach (char c in t)
        freqT[c] = freqT.GetValueOrDefault(c) + 1;
    
    // Compare: freqS == freqT
}
```

### State Space Leading to Output:
Character frequencies match → Result: `true`

### Brute Force Canonical Skeleton:
```csharp
bool IsAnagramBruteForce(string s, string t) 
{
    if (s.Length != t.Length) return false;
    
    bool[] used = new bool[t.Length];
    
    bool Backtrack(int idx)
    {
        if (idx == s.Length) 
            return true;
        
        for (int i = 0; i < t.Length; i++)
        {
            if (used[i]) continue;
            
            if (s[idx] == t[i])
            {
                used[i] = true;
                if (Backtrack(idx + 1)) 
                    return true;
                used[i] = false;
            }
        }
        
        return false;
    }
    
    return Backtrack(0);
}
```

### Brute Force Code Walkthrough / Variable Trace:
| idx | s[idx] | used[]        | t[i] match | action          |
| --- | ------ | ------------- | ---------- | --------------- |
| 0   | a      | [F,F,F,F,F,F,F] | t[1]=a   | try, used[1]=T  |
| 1   | n      | [F,T,F,F,F,F,F] | t[0]=n   | try, used[0]=T  |
| 2   | a      | [T,T,F,F,F,F,F] | t[3]=a   | try, used[3]=T  |
| 3   | g      | [T,T,F,T,F,F,F] | t[2]=g   | try, used[2]=T  |
| ... | ...    | ...           | ...        | ...             |

### Optimized Solution from Canonical Skeleton:
```csharp
bool IsAnagram(string s, string t) 
{
    if (s.Length != t.Length) 
        return false;
    
    var freq = new Dictionary<char, int>();
    
    // Count characters in s
    foreach (char c in s)
    {
        freq[c] = freq.GetValueOrDefault(c) + 1;
    }
    
    // Decrement for characters in t
    foreach (char c in t)
    {
        if (!freq.ContainsKey(c) || freq[c] == 0)
            return false;
        
        freq[c]--;
    }
    
    return true;
}
```

### Explanation of Pruning:
- No need to generate permutations
- Character frequency uniquely identifies anagram relationship
- Single pass to build frequency map
- Second pass to validate
- Reduces O(n!) to O(n)

### Optimized Solution Code Walkthrough / Variable Trace:
| step | char | action      | freq map              |
| ---- | ---- | ----------- | --------------------- |
| 1    | a    | add to s    | {a:1}                 |
| 2    | n    | add to s    | {a:1,n:1}             |
| 3    | a    | add to s    | {a:2,n:1}             |
| 4    | g    | add to s    | {a:2,n:1,g:1}         |
| 5    | r    | add to s    | {a:2,n:1,g:1,r:1}     |
| 6    | a    | add to s    | {a:3,n:1,g:1,r:1}     |
| 7    | m    | add to s    | {a:3,n:1,g:1,r:1,m:1} |
| 8    | n    | check t     | {a:3,n:0,g:1,r:1,m:1} |
| 9    | a    | check t     | {a:2,n:0,g:1,r:1,m:1} |
| 10   | g    | check t     | {a:2,n:0,g:0,r:1,m:1} |
| 11   | a    | check t     | {a:1,n:0,g:0,r:1,m:1} |
| 12   | r    | check t     | {a:1,n:0,g:0,r:0,m:1} |
| 13   | a    | check t     | {a:0,n:0,g:0,r:0,m:1} |
| 14   | m    | check t     | {a:0,n:0,g:0,r:0,m:0} |

### Big-O Analysis:
- **Brute Force:** O(n!) → generate and check all permutations
- **Optimized:** O(n) → two passes through strings
- **Space Complexity:** O(1) → at most 26 characters (alphabet bounded)

</details>

<details>
<summary><b>Variant #2: Two Sum</b></summary>

## Variant #2: Two Sum

### Input/Output:
- Input: `nums = [2,7,11,15]`, `target = 9`
- Output: `[0,1]` (indices where 2 + 7 = 9)

### Full State Space:
All possible pairs of indices:
```
(0,1): 2+7=9,
(0,2): 2+11=13,
(0,3): 2+15=17,
(1,2): 7+11=18,
(1,3): 7+15=22,
(2,3): 11+15=26
```
```csharp
void GenerateAllPairs(int[] nums)
{
    var allPairs = new List<(int i, int j, int sum)>();
    
    for (int start = 0; start < nums.Length; start++)
    {
        for (int end = start + 1; end < nums.Length; end++)
        {
            allPairs.Add((start, end, nums[start] + nums[end]));
        }
    }
}
```

### Expected/Pruned State Space:
Only pairs where complement exists:
```
For nums[0]=2: need 7 → found at index 1
Result: [0,1]
```
```csharp
void FindComplementPairs(int[] nums, int target)
{
    var map = new Dictionary<int, int>();
    var validPairs = new List<(int i, int j)>();
    
    for (int i = 0; i < nums.Length; i++)
    {
        int complement = target - nums[i];
        if (map.ContainsKey(complement))
        {
            validPairs.Add((map[complement], i));
        }
        map[nums[i]] = i;
    }
}
```

### State Space Leading to Output:
Complement of 2 is 7, found at index 1 → Result: [0,1]

### Brute Force Canonical Skeleton:
```csharp
int[] TwoSumBruteForce(int[] nums, int target) 
{
    // Check all pairs
    for (int start = 0; start < nums.Length; start++)
    {
        for (int end = start + 1; end < nums.Length; end++)
        {
            if (nums[start] + nums[end] == target)
            {
                return new int[] { start, end };
            }
        }
    }
    
    return new int[0];
}
```

### Brute Force Code Walkthrough / Variable Trace:
| start | end | nums[start] | nums[end] | sum | target? |
| - | - | ------- | ------- | --- | ------- |
| 0 | 1 | 2       | 7       | 9   | Yes!    |

### Optimized Solution from Canonical Skeleton:
```csharp
int[] TwoSum(int[] nums, int target) 
{
    var map = new Dictionary<int, int>(); // value -> index
    
    for (int i = 0; i < nums.Length; i++)
    {
        int complement = target - nums[i];
        
        if (map.ContainsKey(complement))
        {
            return new int[] { map[complement], i };
        }
        
        map[nums[i]] = i;
    }
    
    return new int[0];
}
```

### Explanation of Pruning:
- Instead of checking all O(n²) pairs, use hash map for O(1) lookups
- For each element, check if its complement exists
- Store seen elements with their indices
- Single pass through array

### Optimized Solution Code Walkthrough / Variable Trace:
| i | nums[i] | complement | map before     | found? | action      |
| - | ------- | ---------- | -------------- | ------ | ----------- |
| 0 | 2       | 7          | {}             | No     | add {2:0}   |
| 1 | 7       | 2          | {2:0}          | Yes!   | return[0,1] |

### Big-O Analysis:
- **Brute Force:** O(n²) → check all pairs
- **Optimized:** O(n) → single pass with hash map
- **Space Complexity:** O(n) → store up to n elements

</details>

<details>
<summary><b>Variant #3: Group Anagrams</b></summary>

## Variant #3: Group Anagrams

### Input/Output:
- Input: `strs = ["eat","tea","tan","ate","nat","bat"]`
- Output: `[["bat"],["nat","tan"],["ate","eat","tea"]]`

### Full State Space:
Compare every string with every other string for anagram relationship:
```
Compare "eat" with "tea", "tan", "ate", "nat", "bat"
Compare "tea" with "tan", "ate", "nat", "bat"
Compare "tan" with "ate", "nat", "bat"
Compare "ate" with "nat", "bat"
Compare "nat" with "bat"
Total: O(n²) comparisons, each comparison O(k) for anagram check
```
```csharp
void GroupAnagramsBruteForce(string[] strs)
{
    var groups = new List<List<string>>();
    var used = new bool[strs.Length];
    
    for (int start = 0; start < strs.Length; start++)
    {
        if (used[start]) continue;
        
        var group = new List<string> { strs[start] };
        used[start] = true;
        
        for (int end = start + 1; end < strs.Length; end++)
        {
            if (!used[end] && IsAnagram(strs[start], strs[end]))
            {
                group.Add(strs[end]);
                used[end] = true;
            }
        }
        
        groups.Add(group);
    }
}
```

### Expected/Pruned State Space:
Group by canonical representation (sorted string):
```
"eat" → key="aet" → group: ["eat", "tea", "ate"]
"tan" → key="ant" → group: ["tan", "nat"]
"bat" → key="abt" → group: ["bat"]
```
```csharp
void GroupByCanonicalKey(string[] strs)
{
    var groups = new Dictionary<string, List<string>>();
    
    foreach (var str in strs)
    {
        // Create canonical key
        var chars = str.ToCharArray();
        Array.Sort(chars);
        var key = new string(chars);
        
        if (!groups.ContainsKey(key))
            groups[key] = new List<string>();
        
        groups[key].Add(str);
    }
}
```

### State Space Leading to Output:
Three unique keys → Three groups

### Brute Force Canonical Skeleton:
```csharp
IList<IList<string>> GroupAnagramsBruteForce(string[] strs) 
{
    var groups = new List<IList<string>>();
    var used = new bool[strs.Length];
    
    for (int i = 0; i < strs.Length; i++)
    {
        if (used[i]) continue;
        
        var group = new List<string> { strs[i] };
        used[i] = true;
        
        // Find all anagrams of strs[i]
        for (int j = i + 1; j < strs.Length; j++)
        {
            if (!used[j] && IsAnagram(strs[i], strs[j]))
            {
                group.Add(strs[j]);
                used[j] = true;
            }
        }
        
        groups.Add(group);
    }
    
    return groups;
}

bool IsAnagram(string s, string t)
{
    if (s.Length != t.Length) return false;
    var freq = new int[26];
    foreach (char c in s) freq[c - 'a']++;
    foreach (char c in t) freq[c - 'a']--;
    return freq.All(x => x == 0);
}
```

### Brute Force Code Walkthrough / Variable Trace:
| start | strs[start] | end | strs[end] | anagram? | groups                  |
| ----- | ----------- | --- | --------- | -------- | ----------------------- |
| 0     | "eat"       | 1   | "tea"     | Yes      | [["eat","tea"]]         |
| 0     | "eat"       | 2   | "tan"     | No       | [["eat","tea"]]         |
| 0     | "eat"       | 3   | "ate"     | Yes      | [["eat","tea","ate"]]   |
| 0     | "eat"       | 4   | "nat"     | No       | [["eat","tea","ate"]]   |
| 0     | "eat"       | 5   | "bat"     | No       | [["eat","tea","ate"]]   |
| 2     | "tan"       | 4   | "nat"     | Yes      | [[...],["tan","nat"]]   |
| 5     | "bat"       | -   | -         | -        | [[...],[...],["bat"]]   |

### Optimized Solution from Canonical Skeleton:
```csharp
IList<IList<string>> GroupAnagrams(string[] strs) 
{
    var map = new Dictionary<string, List<string>>();
    
    foreach (var str in strs)
    {
        // Create canonical key (sorted string)
        var chars = str.ToCharArray();
        Array.Sort(chars);
        var key = new string(chars);
        
        // Group by key
        if (!map.ContainsKey(key))
        {
            map[key] = new List<string>();
        }
        
        map[key].Add(str);
    }
    
    return map.Values.ToList<IList<string>>();
}
```

### Explanation of Pruning:
- Instead of O(n²) pairwise comparisons, use canonical representation
- Sorted string as key uniquely identifies anagram group
- Single pass through input
- O(1) lookup and insertion in hash map
- Alternative: frequency count array as key

### Optimized Solution Code Walkthrough / Variable Trace:
| str   | sorted key | map after                                          |
| ----- | ---------- | -------------------------------------------------- |
| "eat" | "aet"      | {"aet": ["eat"]}                                   |
| "tea" | "aet"      | {"aet": ["eat","tea"]}                             |
| "tan" | "ant"      | {"aet": ["eat","tea"], "ant": ["tan"]}            |
| "ate" | "aet"      | {"aet": ["eat","tea","ate"], "ant": ["tan"]}     |
| "nat" | "ant"      | {"aet": [...], "ant": ["tan","nat"]}              |
| "bat" | "abt"      | {"aet": [...], "ant": [...], "abt": ["bat"]}      |

### Big-O Analysis:
- **Brute Force:** O(n² × k) → n² comparisons, each takes k time for anagram check
- **Optimized:** O(n × k log k) → n strings, each sorted in k log k time
- **Space Complexity:** O(n × k) → store all strings

</details>

<details>
<summary><b>Variant #4: First Non-Repeating Character</b></summary>

## Variant #4: First Non-Repeating Character

### Input/Output:
- Input: `s = "leetcode"`
- Output: `0` (first non-repeating character is 'l' at index 0)

### Full State Space:
For each character, scan entire string to count occurrences:
```
For 'l': scan entire string → count = 1
For 'e': scan entire string → count = 3
For 'e': scan entire string → count = 3
...
Total: O(n²)
```

### Expected/Pruned State Space:
Build frequency map once, then find first char with count=1:
```
Frequency: {l:1, e:3, t:1, c:1, o:1, d:1}
First occurrence order: l,e,t,c,o,d
First with freq=1: l at index 0
```

### State Space Leading to Output:
First character with frequency 1 → 'l' at index 0

### Brute Force Canonical Skeleton:
```csharp
int FirstUniqCharBruteForce(string s) 
{
    for (int start = 0; start < s.Length; start++)
    {
        int count = 0;
        
        // Count occurrences of s[start]
        for (int end = 0; end < s.Length; end++)
        {
            if (s[end] == s[start])
                count++;
        }
        
        if (count == 1)
            return start;
    }
    
    return -1;
}
```

### Optimized Solution from Canonical Skeleton:
```csharp
int FirstUniqChar(string s) 
{
    var freq = new Dictionary<char, int>();
    
    // Build frequency map
    foreach (char c in s)
    {
        freq[c] = freq.GetValueOrDefault(c) + 1;
    }
    
    // Find first character with frequency 1
    for (int i = 0; i < s.Length; i++)
    {
        if (freq[s[i]] == 1)
            return i;
    }
    
    return -1;
}
```

### Big-O Analysis:
- **Brute Force:** O(n²)
- **Optimized:** O(n)
- **Space Complexity:** O(1) → at most 26 characters

</details>

<details>
<summary><b>Variant #5: Subarray Sum Equals K</b></summary>

## Variant #5: Subarray Sum Equals K

### Input/Output:
- Input: `nums = [1,1,1]`, `k = 2`
- Output: `2` (subarrays: [1,1] at index 0-1 and [1,1] at index 1-2)

### Full State Space:
All possible subarrays:
```
[1], [1,1], [1,1,1],
[1], [1,1],
[1]
```

### Expected/Pruned State Space:
Use prefix sum + hash map:
```
Prefix sums: [0,1,2,3]
For each position, check if (prefixSum - k) exists
```

### State Space Leading to Output:
Two subarrays with sum=2

### Optimized Solution from Canonical Skeleton:
```csharp
int SubarraySum(int[] nums, int k) 
{
    var prefixSumCount = new Dictionary<int, int>();
    prefixSumCount[0] = 1; // Empty prefix
    
    int sum = 0, count = 0;
    
    foreach (int num in nums)
    {
        sum += num;
        
        // Check if (sum - k) exists
        if (prefixSumCount.ContainsKey(sum - k))
        {
            count += prefixSumCount[sum - k];
        }
        
        // Add current sum to map
        prefixSumCount[sum] = prefixSumCount.GetValueOrDefault(sum) + 1;
    }
    
    return count;
}
```

### Big-O Analysis:
- **Brute Force:** O(n²)
- **Optimized:** O(n)
- **Space Complexity:** O(n)

</details>

<details>
<summary><b>Variant #6: Longest Substring with K Distinct Characters</b></summary>

## Variant #6: Longest Substring with K Distinct Characters

### Input/Output:
- Input: `s = "eceba"`, `k = 2`
- Output: `3` (substring "ece")

### Optimized Solution from Canonical Skeleton:
```csharp
int LengthOfLongestSubstringKDistinct(string s, int k) 
{
    var freq = new Dictionary<char, int>();
    int left = 0, maxLen = 0;
    
    for (int right = 0; right < s.Length; right++)
    {
        char c = s[right];
        freq[c] = freq.GetValueOrDefault(c) + 1;
        
        // Shrink window if too many distinct chars
        while (freq.Count > k)
        {
            char leftChar = s[left];
            freq[leftChar]--;
            if (freq[leftChar] == 0)
                freq.Remove(leftChar);
            left++;
        }
        
        maxLen = Math.Max(maxLen, right - left + 1);
    }
    
    return maxLen;
}
```

### Big-O Analysis:
- **Optimized:** O(n)
- **Space Complexity:** O(k)

</details>

<details>
<summary><b>Variant #7: Top K Frequent Elements</b></summary>

## Variant #7: Top K Frequent Elements

### Input/Output:
- Input: `nums = [1,1,1,2,2,3]`, `k = 2`
- Output: `[1,2]` (most frequent elements)

### Optimized Solution from Canonical Skeleton:
```csharp
int[] TopKFrequent(int[] nums, int k) 
{
    // Build frequency map
    var freq = new Dictionary<int, int>();
    foreach (int num in nums)
    {
        freq[num] = freq.GetValueOrDefault(num) + 1;
    }
    
    // Use bucket sort or heap
    var buckets = new List<int>[nums.Length + 1];
    foreach (var pair in freq)
    {
        int count = pair.Value;
        if (buckets[count] == null)
            buckets[count] = new List<int>();
        buckets[count].Add(pair.Key);
    }
    
    // Collect top k from highest frequency
    var result = new List<int>();
    for (int i = buckets.Length - 1; i >= 0 && result.Count < k; i--)
    {
        if (buckets[i] != null)
            result.AddRange(buckets[i]);
    }
    
    return result.Take(k).ToArray();
}
```

### Big-O Analysis:
- **Optimized:** O(n)
- **Space Complexity:** O(n)

</details>

---

## Key Pattern Takeaways
- Hash maps collapse **exponential or quadratic state spaces** into linear scans.
- Always ask: *"What property uniquely identifies equivalence?"*
- This pattern frequently combines with **sliding window**, **prefix sum**, and **two pointers**.

---

**End of Hash Map / Frequency Counting Pattern**

