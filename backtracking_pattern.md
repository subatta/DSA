# Pattern Title: Backtracking

## Pattern Description:
- What is it?  
  Backtracking is a systematic way to explore all possible solutions by building candidates incrementally and abandoning (backtracking from) partial candidates as soon as it is clear they cannot lead to a valid solution.

- What abstract problem does it solve?  
  It solves **combinatorial search problems** where the solution space can be represented as a decision tree, and constraints allow early pruning of invalid branches.

- Real-world problem variants (simplest first):  
  1. Generate all subsets (power set)
  2. Generate all permutations
  3. Combination Sum
  4. Letter Combinations of a Phone Number
  5. Palindrome Partitioning
  6. Word Search
  7. N-Queens
  8. Sudoku Solver

## Canonical Code Skeleton:

```csharp
void BacktrackTemplate(int start, List<T> current, State state) {
    // Base case: valid solution found
    if (IsValidSolution(current, state)) {
        result.Add(new List<T>(current));
        return;
    }

    // Early termination: prune invalid branches
    if (ShouldPrune(current, state)) {
        return;
    }

    // Explore choices
    for (int i = start; i < choices.Length; i++) {
        // Choose
        current.Add(choices[i]);
        UpdateState(state, choices[i]);

        // Explore
        BacktrackTemplate(i + 1, current, state);

        // Unchoose (backtrack)
        current.RemoveAt(current.Count - 1);
        RevertState(state, choices[i]);
    }
}
```

<details>
<summary><b>Variant #1: Generate All Subsets (Power Set)</b></summary>

## Variant #1: Generate All Subsets

### Input/Output:
- Input: `nums = [1,2,3]`
- Output: `[[],[1],[2],[3],[1,2],[1,3],[2,3],[1,2,3]]`

### Full State Space:
Decision tree where each element has two choices: include or exclude.
```
Total nodes: 2^n where each path from root to leaf = one subset
                    []
            /include1    \exclude1
           [1]            []
         /     \        /     \
      [1,2]   [1]    [2]      []
      /   \   / \    / \      / \
   [1,2,3][1,2][1,3][1] [2,3][2][3][]
```
```csharp
void GenerateAllSubsets(int[] nums)
{
    var allSubsets = new List<List<int>>();
    
    void Generate(int index, List<int> current)
    {
        // Every state is a valid subset
        allSubsets.Add(new List<int>(current));
        
        for (int start = index; start < nums.Length; start++)
        {
            current.Add(nums[start]);
            Generate(start + 1, current);
            current.RemoveAt(current.Count - 1);
        }
    }
    
    Generate(0, new List<int>());
}
```

### Expected/Pruned State Space:
No pruning needed - all 2^n subsets are valid.
```
Same as full state space - 8 subsets for n=3
```

### State Space Leading to Output:
Every node in the decision tree represents a valid subset → 2^n total subsets

### Brute Force Canonical Skeleton:
```csharp
IList<IList<int>> SubsetsBruteForce(int[] nums) 
{
    var result = new List<IList<int>>();
    
    void Backtrack(int start, List<int> current) 
    {
        // Add current subset to result
        result.Add(new List<int>(current));
        
        // Try including each remaining element
        for (int i = start; i < nums.Length; i++) 
        {
            // Choose: include nums[i]
            current.Add(nums[i]);
            
            // Explore: recurse with i+1
            Backtrack(i + 1, current);
            
            // Unchoose: backtrack
            current.RemoveAt(current.Count - 1);
        }
    }
    
    Backtrack(0, new List<int>());
    return result;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `nums = [1,2,3]`:

| Call | start | current | action | result so far |
| ---- | ----- | ------- | ------ | ------------- |
| 1    | 0     | []      | add [] | [[]]          |
| 2    | 0     | [1]     | choose 1, add [1] | [[],[1]] |
| 3    | 1     | [1,2]   | choose 2, add [1,2] | [[],[1],[1,2]] |
| 4    | 2     | [1,2,3] | choose 3, add [1,2,3] | [[],[1],[1,2],[1,2,3]] |
| -    | -     | [1,2]   | backtrack from 3 | - |
| 5    | 2     | [1,3]   | choose 3, add [1,3] | [[...],[1,3]] |
| -    | -     | [1]     | backtrack | - |
| 6    | 1     | [2]     | choose 2, add [2] | [[...],[2]] |
| 7    | 2     | [2,3]   | choose 3, add [2,3] | [[...],[2,3]] |
| -    | -     | []      | backtrack | - |
| 8    | 2     | [3]     | choose 3, add [3] | [[...],[3]] |

### Optimized Solution from Canonical Skeleton:
```csharp
IList<IList<int>> Subsets(int[] nums) 
{
    var result = new List<IList<int>>();
    
    void Backtrack(int start, List<int> current) 
    {
        result.Add(new List<int>(current));
        
        for (int i = start; i < nums.Length; i++) 
        {
            current.Add(nums[i]);
            Backtrack(i + 1, current);
            current.RemoveAt(current.Count - 1);
        }
    }
    
    Backtrack(0, new List<int>());
    return result;
}
```

### Explanation of Pruning:
- No pruning needed - all subsets are valid
- The `start` parameter ensures we don't generate duplicate subsets
- By starting from `i` instead of 0, we maintain order and avoid [1,2] and [2,1]

### Optimized Solution Code Walkthrough / Variable Trace:
Same as brute force - this problem has no optimization opportunity as all 2^n subsets must be generated.

### Big-O Analysis:
- **Time Complexity:** O(n × 2^n) → 2^n subsets, each taking O(n) to copy
- **Space Complexity:** O(n) → recursion depth
- **Output Space:** O(n × 2^n) → storing all subsets

</details>

<details>
<summary><b>Variant #2: Generate All Permutations</b></summary>

## Variant #2: Permutations

### Input/Output:
- Input: `nums = [1,2,3]`
- Output: `[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]`

### Full State Space:
Decision tree where each level chooses one unused element.
```
Total nodes: n! where each path from root to leaf = one permutation
                     []
           /          |          \
        [1]          [2]          [3]
       /   \        /   \        /   \
    [1,2] [1,3]  [2,1] [2,3]  [3,1] [3,2]
     |     |      |     |      |     |
  [1,2,3][1,3,2][2,1,3][2,3,1][3,1,2][3,2,1]
```
```csharp
void GenerateAllPermutations(int[] nums)
{
    var allPermutations = new List<List<int>>();
    
    void Generate(List<int> current, bool[] used)
    {
        if (current.Count == nums.Length)
        {
            allPermutations.Add(new List<int>(current));
            return;
        }
        
        for (int i = 0; i < nums.Length; i++)
        {
            if (!used[i])
            {
                current.Add(nums[i]);
                used[i] = true;
                Generate(current, used);
                used[i] = false;
                current.RemoveAt(current.Count - 1);
            }
        }
    }
    
    Generate(new List<int>(), new bool[nums.Length]);
}
```

### Expected/Pruned State Space:
No pruning needed - all n! permutations are valid.
```
Same as full state space - 6 permutations for n=3
```

### State Space Leading to Output:
Every leaf node (depth = n) represents a valid permutation → n! total permutations

### Brute Force Canonical Skeleton:
```csharp
IList<IList<int>> PermuteBruteForce(int[] nums) 
{
    var result = new List<IList<int>>();
    
    void Backtrack(List<int> current, bool[] used) 
    {
        // Base case: found complete permutation
        if (current.Count == nums.Length) 
        {
            result.Add(new List<int>(current));
            return;
        }
        
        // Try each unused element
        for (int i = 0; i < nums.Length; i++) 
        {
            if (used[i]) continue;
            
            // Choose: include nums[i]
            current.Add(nums[i]);
            used[i] = true;
            
            // Explore: recurse
            Backtrack(current, used);
            
            // Unchoose: backtrack
            used[i] = false;
            current.RemoveAt(current.Count - 1);
        }
    }
    
    Backtrack(new List<int>(), new bool[nums.Length]);
    return result;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `nums = [1,2,3]`:

| Call | current | used | i | action | result so far |
| ---- | ------- | ---- | - | ------ | ------------- |
| 1    | []      | [F,F,F] | 0 | choose 1 | [] |
| 2    | [1]     | [T,F,F] | 0 | skip 1 (used) | [] |
| 2    | [1]     | [T,F,F] | 1 | choose 2 | [] |
| 3    | [1,2]   | [T,T,F] | 0 | skip 1,2 | [] |
| 3    | [1,2]   | [T,T,F] | 2 | choose 3 | [] |
| 4    | [1,2,3] | [T,T,T] | - | complete, add [1,2,3] | [[1,2,3]] |
| -    | [1,2]   | [T,T,F] | - | backtrack | - |
| 3    | [1,3]   | [T,F,T] | 1 | choose 2 | [[1,2,3]] |
| 4    | [1,3,2] | [T,T,T] | - | complete, add [1,3,2] | [[1,2,3],[1,3,2]] |
| ...  | ...     | ...  | - | continues | all 6 permutations |

### Optimized Solution from Canonical Skeleton:
```csharp
IList<IList<int>> Permute(int[] nums) 
{
    var result = new List<IList<int>>();
    
    void Backtrack(List<int> current, bool[] used) 
    {
        if (current.Count == nums.Length) 
        {
            result.Add(new List<int>(current));
            return;
        }
        
        for (int i = 0; i < nums.Length; i++) 
        {
            if (used[i]) continue;
            
            current.Add(nums[i]);
            used[i] = true;
            Backtrack(current, used);
            used[i] = false;
            current.RemoveAt(current.Count - 1);
        }
    }
    
    Backtrack(new List<int>(), new bool[nums.Length]);
    return result;
}
```

### Explanation of Pruning:
- No pruning needed - all permutations are valid
- The `used` array prevents using the same element twice
- This is constraint satisfaction, not optimization

### Optimized Solution Code Walkthrough / Variable Trace:
Same as brute force - this problem has no optimization opportunity as all n! permutations must be generated.

### Big-O Analysis:
- **Time Complexity:** O(n × n!) → n! permutations, each taking O(n) to copy
- **Space Complexity:** O(n) → recursion depth + used array
- **Output Space:** O(n × n!) → storing all permutations

</details>

<details>
<summary><b>Variant #3: Combination Sum (Reusable Elements)</b></summary>

## Variant #3: Combination Sum

### Input/Output:
- Input: `candidates = [2,3,6,7], target = 7`
- Output: `[[2,2,3],[7]]`

### Full State Space:
Decision tree where each element can be reused indefinitely until target is reached or exceeded.
```
Infinite depth tree pruned when sum >= target
                          []sum=0
              /2           /3          /6        \7
         [2]sum=2      [3]sum=3    [6]sum=6    [7]sum=7✓
      /2  /3 /6 \7    ...          ...
   [2,2] [2,3]...
  sum=4  sum=5
  /2 /3
[2,2,2] [2,2,3]✓
sum=6   sum=7
```
```csharp
void GenerateAllCombinations(int[] candidates, int target)
{
    var allCombinations = new List<List<int>>();
    
    void Generate(int start, List<int> current, int sum)
    {
        if (sum == target)
        {
            allCombinations.Add(new List<int>(current));
            return;
        }
        if (sum > target) return; // Pruning: exceeded target
        
        for (int i = start; i < candidates.Length; i++)
        {
            current.Add(candidates[i]);
            Generate(i, current, sum + candidates[i]); // i not i+1: reuse allowed
            current.RemoveAt(current.Count - 1);
        }
    }
    
    Generate(0, new List<int>(), 0);
}
```

### Expected/Pruned State Space:
Pruned when sum exceeds target or when sorted order is violated.
```
For [2,3,6,7] target=7:
Valid combinations: [[2,2,3], [7]]
Pruned: All paths where sum > 7
```

### State Space Leading to Output:
Nodes where sum == target represent valid combinations → variable count depending on target and candidates

### Brute Force Canonical Skeleton:
```csharp
IList<IList<int>> CombinationSumBruteForce(int[] candidates, int target) 
{
    var result = new List<IList<int>>();
    
    void Backtrack(int start, List<int> current, int sum) 
    {
        // Base cases
        if (sum == target) 
        {
            result.Add(new List<int>(current));
            return;
        }
        if (sum > target) return; // Exceeded target
        
        // Try each candidate (can reuse)
        for (int i = start; i < candidates.Length; i++) 
        {
            // Choose: include candidates[i]
            current.Add(candidates[i]);
            
            // Explore: recurse with i (not i+1) to allow reuse
            Backtrack(i, current, sum + candidates[i]);
            
            // Unchoose: backtrack
            current.RemoveAt(current.Count - 1);
        }
    }
    
    Backtrack(0, new List<int>(), 0);
    return result;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `candidates = [2,3,6,7], target = 7`:

| Call | start | current | sum | action | result so far |
| ---- | ----- | ------- | --- | ------ | ------------- |
| 1    | 0     | []      | 0   | try 2  | [] |
| 2    | 0     | [2]     | 2   | try 2  | [] |
| 3    | 0     | [2,2]   | 4   | try 2  | [] |
| 4    | 0     | [2,2,2] | 6   | try 2  | [] |
| 5    | 0     | [2,2,2,2] | 8 | sum>7, prune | [] |
| 4    | 0     | [2,2,2] | 6   | try 3  | [] |
| 5    | 1     | [2,2,2,3] | 9 | sum>7, prune | [] |
| 3    | 0     | [2,2]   | 4   | try 3  | [] |
| 4    | 1     | [2,2,3] | 7   | sum==7, add | [[2,2,3]] |
| 1    | 0     | []      | 0   | try 3  | [[2,2,3]] |
| 2    | 1     | [3]     | 3   | try 3  | [[2,2,3]] |
| 3    | 1     | [3,3]   | 6   | try 3  | [[2,2,3]] |
| 4    | 1     | [3,3,3] | 9   | sum>7, prune | [[2,2,3]] |
| 1    | 0     | []      | 0   | try 7  | [[2,2,3]] |
| 2    | 3     | [7]     | 7   | sum==7, add | [[2,2,3],[7]] |

### Optimized Solution from Canonical Skeleton:
```csharp
IList<IList<int>> CombinationSum(int[] candidates, int target) 
{
    var result = new List<IList<int>>();
    
    void Backtrack(int start, List<int> current, int sum) 
    {
        if (sum == target) 
        {
            result.Add(new List<int>(current));
            return;
        }
        if (sum > target) return;
        
        for (int i = start; i < candidates.Length; i++) 
        {
            current.Add(candidates[i]);
            Backtrack(i, current, sum + candidates[i]);
            current.RemoveAt(current.Count - 1);
        }
    }
    
    Backtrack(0, new List<int>(), 0);
    return result;
}
```

### Explanation of Pruning:
- **Early termination:** Stop when `sum > target` (no point exploring further)
- **Reuse allowed:** Pass `i` (not `i+1`) to allow same element multiple times
- **Maintain order:** Start from `i` to avoid duplicates like [2,3] and [3,2]

### Optimized Solution Code Walkthrough / Variable Trace:
Same as brute force - the pruning condition `sum > target` is the optimization.

### Big-O Analysis:
- **Time Complexity:** O(n^(target/min)) → worst case when smallest candidate is 1
- **Space Complexity:** O(target/min) → maximum recursion depth
- **Output Space:** O(k × avg_length) → k combinations of varying lengths

</details>

<details>
<summary><b>Variant #4: Letter Combinations of Phone Number</b></summary>

## Variant #4: Letter Combinations of Phone Number

### Input/Output:
- Input: `digits = "23"`
- Output: `["ad","ae","af","bd","be","bf","cd","ce","cf"]`

### Full State Space:
Decision tree where each digit maps to multiple letters.
```
For "23": 2→"abc", 3→"def"
                    ""
        /a          |b          \c
       "a"         "b"          "c"
    /d |e \f    /d |e \f    /d |e \f
  "ad""ae""af" "bd""be""bf" "cd""ce""cf"

Total: 3×3 = 9 combinations
```
```csharp
void GenerateAllLetterCombinations(string digits)
{
    if (digits.Length == 0) return;
    
    var allCombinations = new List<string>();
    var digitToLetters = new Dictionary<char, string>
    {
        {'2', "abc"}, {'3', "def"}, {'4', "ghi"}, {'5', "jkl"},
        {'6', "mno"}, {'7', "pqrs"}, {'8', "tuv"}, {'9', "wxyz"}
    };
    
    void Generate(int index, string current)
    {
        if (index == digits.Length)
        {
            allCombinations.Add(current);
            return;
        }
        
        string letters = digitToLetters[digits[index]];
        for (int i = 0; i < letters.Length; i++)
        {
            Generate(index + 1, current + letters[i]);
        }
    }
    
    Generate(0, "");
}
```

### Expected/Pruned State Space:
No pruning needed - all combinations are valid.
```
For n digits with avg k letters per digit: k^n combinations
```

### State Space Leading to Output:
Every leaf node (depth = n) represents a valid letter combination → product of letter counts

### Brute Force Canonical Skeleton:
```csharp
IList<string> LetterCombinationsBruteForce(string digits) 
{
    if (digits.Length == 0) return new List<string>();
    
    var result = new List<string>();
    var digitToLetters = new Dictionary<char, string>
    {
        {'2', "abc"}, {'3', "def"}, {'4', "ghi"}, {'5', "jkl"},
        {'6', "mno"}, {'7', "pqrs"}, {'8', "tuv"}, {'9', "wxyz"}
    };
    
    void Backtrack(int index, string current) 
    {
        // Base case: processed all digits
        if (index == digits.Length) 
        {
            result.Add(current);
            return;
        }
        
        // Try each letter for current digit
        string letters = digitToLetters[digits[index]];
        for (int i = 0; i < letters.Length; i++) 
        {
            // Choose: append letter
            // Explore: recurse to next digit
            Backtrack(index + 1, current + letters[i]);
            // Unchoose: not needed (string is immutable)
        }
    }
    
    Backtrack(0, "");
    return result;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `digits = "23"`:

| Call | index | current | digit | letters | action | result so far |
| ---- | ----- | ------- | ----- | ------- | ------ | ------------- |
| 1    | 0     | ""      | '2'   | "abc"   | try 'a' | [] |
| 2    | 1     | "a"     | '3'   | "def"   | try 'd' | [] |
| 3    | 2     | "ad"    | -     | -       | complete, add "ad" | ["ad"] |
| 2    | 1     | "a"     | '3'   | "def"   | try 'e' | ["ad"] |
| 3    | 2     | "ae"    | -     | -       | complete, add "ae" | ["ad","ae"] |
| 2    | 1     | "a"     | '3'   | "def"   | try 'f' | ["ad","ae"] |
| 3    | 2     | "af"    | -     | -       | complete, add "af" | ["ad","ae","af"] |
| 1    | 0     | ""      | '2'   | "abc"   | try 'b' | ["ad","ae","af"] |
| 2    | 1     | "b"     | '3'   | "def"   | try 'd' | ["ad","ae","af"] |
| 3    | 2     | "bd"    | -     | -       | complete, add "bd" | ["ad","ae","af","bd"] |
| ...  | ...   | ...     | ...   | ...     | continues | all 9 combinations |

### Optimized Solution from Canonical Skeleton:
```csharp
IList<string> LetterCombinations(string digits) 
{
    if (digits.Length == 0) return new List<string>();
    
    var result = new List<string>();
    var digitToLetters = new Dictionary<char, string>
    {
        {'2', "abc"}, {'3', "def"}, {'4', "ghi"}, {'5', "jkl"},
        {'6', "mno"}, {'7', "pqrs"}, {'8', "tuv"}, {'9', "wxyz"}
    };
    
    void Backtrack(int index, string current) 
    {
        if (index == digits.Length) 
        {
            result.Add(current);
            return;
        }
        
        string letters = digitToLetters[digits[index]];
        for (int i = 0; i < letters.Length; i++) 
        {
            Backtrack(index + 1, current + letters[i]);
        }
    }
    
    Backtrack(0, "");
    return result;
}
```

### Explanation of Pruning:
- No pruning needed - all combinations are valid
- No backtracking cleanup needed since strings are immutable
- Each digit position is independent

### Optimized Solution Code Walkthrough / Variable Trace:
Same as brute force - this problem has no optimization opportunity as all combinations must be generated.

### Big-O Analysis:
- **Time Complexity:** O(4^n × n) → worst case 4 letters per digit (7,9), n to build string
- **Space Complexity:** O(n) → recursion depth
- **Output Space:** O(4^n × n) → storing all combinations

</details>

<details>
<summary><b>Variant #5: Palindrome Partitioning</b></summary>

## Variant #5: Palindrome Partitioning

### Input/Output:
- Input: `s = "aab"`
- Output: `[["a","a","b"],["aa","b"]]`

### Full State Space:
Decision tree where each level chooses where to partition the string.
```
For "aab": try all partition points
                    ""
          /a        /aa       \aab
       "a"         "aa"        "aab"
      /a  \ab      /b           (not palindrome)
    "aa" "ab"    "b"
    /b    (no)    ✓
   "b"
    ✓

Valid partitions where all parts are palindromes
```
```csharp
void GenerateAllPartitions(string s)
{
    var allPartitions = new List<List<string>>();
    
    bool IsPalindrome(int start, int end)
    {
        while (start < end)
        {
            if (s[start] != s[end]) return false;
            start++;
            end--;
        }
        return true;
    }
    
    void Generate(int start, List<string> current)
    {
        if (start == s.Length)
        {
            allPartitions.Add(new List<string>(current));
            return;
        }
        
        for (int end = start; end < s.Length; end++)
        {
            if (IsPalindrome(start, end))
            {
                current.Add(s.Substring(start, end - start + 1));
                Generate(end + 1, current);
                current.RemoveAt(current.Count - 1);
            }
        }
    }
    
    Generate(0, new List<string>());
}
```

### Expected/Pruned State Space:
Pruned when substring is not a palindrome.
```
For "aab": Only explore paths where current substring is palindrome
Valid: [["a","a","b"], ["aa","b"]]
```

### State Space Leading to Output:
Leaf nodes where all substrings are palindromes → 2^(n-1) worst case (all single chars)

### Brute Force Canonical Skeleton:
```csharp
IList<IList<string>> PartitionBruteForce(string s) 
{
    var result = new List<IList<string>>();
    
    bool IsPalindrome(int start, int end) 
    {
        while (start < end) 
        {
            if (s[start] != s[end]) return false;
            start++;
            end--;
        }
        return true;
    }
    
    void Backtrack(int start, List<string> current) 
    {
        // Base case: partitioned entire string
        if (start == s.Length) 
        {
            result.Add(new List<string>(current));
            return;
        }
        
        // Try all possible end positions
        for (int end = start; end < s.Length; end++) 
        {
            // Only proceed if substring is palindrome
            if (IsPalindrome(start, end)) 
            {
                // Choose: add palindrome substring
                current.Add(s.Substring(start, end - start + 1));
                
                // Explore: recurse from end+1
                Backtrack(end + 1, current);
                
                // Unchoose: backtrack
                current.RemoveAt(current.Count - 1);
            }
        }
    }
    
    Backtrack(0, new List<string>());
    return result;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `s = "aab"`:

| Call | start | end | substring | IsPalindrome | current | action | result so far |
| ---- | ----- | --- | --------- | ------------ | ------- | ------ | ------------- |
| 1    | 0     | 0   | "a"       | yes          | []      | choose "a" | [] |
| 2    | 1     | 1   | "a"       | yes          | ["a"]   | choose "a" | [] |
| 3    | 2     | 2   | "b"       | yes          | ["a","a"] | choose "b" | [] |
| 4    | 3     | -   | -         | -            | ["a","a","b"] | complete, add | [["a","a","b"]] |
| 2    | 1     | 2   | "ab"      | no           | ["a"]   | skip | [["a","a","b"]] |
| 1    | 0     | 1   | "aa"      | yes          | []      | choose "aa" | [["a","a","b"]] |
| 2    | 2     | 2   | "b"       | yes          | ["aa"]  | choose "b" | [["a","a","b"]] |
| 3    | 3     | -   | -         | -            | ["aa","b"] | complete, add | [["a","a","b"],["aa","b"]] |
| 1    | 0     | 2   | "aab"     | no           | []      | skip | [["a","a","b"],["aa","b"]] |

### Optimized Solution from Canonical Skeleton:
```csharp
IList<IList<string>> Partition(string s) 
{
    var result = new List<IList<string>>();
    
    bool IsPalindrome(int start, int end) 
    {
        while (start < end) 
        {
            if (s[start] != s[end]) return false;
            start++;
            end--;
        }
        return true;
    }
    
    void Backtrack(int start, List<string> current) 
    {
        if (start == s.Length) 
        {
            result.Add(new List<string>(current));
            return;
        }
        
        for (int end = start; end < s.Length; end++) 
        {
            if (IsPalindrome(start, end)) 
            {
                current.Add(s.Substring(start, end - start + 1));
                Backtrack(end + 1, current);
                current.RemoveAt(current.Count - 1);
            }
        }
    }
    
    Backtrack(0, new List<string>());
    return result;
}
```

### Explanation of Pruning:
- **Palindrome check:** Only explore substrings that are palindromes
- **Early rejection:** Skip non-palindrome partitions immediately
- **Could optimize further:** Memoize palindrome checks with DP table

### Optimized Solution Code Walkthrough / Variable Trace:
Same as brute force - the palindrome check is the key pruning mechanism.

### Big-O Analysis:
- **Time Complexity:** O(n × 2^n) → 2^n partitions, each taking O(n) to validate and copy
- **Space Complexity:** O(n) → recursion depth
- **Output Space:** O(n × 2^n) → worst case all single characters

</details>

<details>
<summary><b>Variant #6: Word Search (2D Grid)</b></summary>

## Variant #6: Word Search

### Input/Output:
- Input: `board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"`
- Output: `true`

### Full State Space:
DFS exploring all paths from each cell in the grid.
```
For each starting cell: explore 4 directions (up, down, left, right)
Maximum depth: length of word
Branching factor: up to 4 at each level
```
```csharp
bool ExistsInGrid(char[][] board, string word)
{
    int rows = board.Length;
    int cols = board[0].Length;
    bool[,] visited = new bool[rows, cols];
    
    bool DFS(int row, int col, int index)
    {
        // Found complete word
        if (index == word.Length) return true;
        
        // Out of bounds or visited or mismatch
        if (row < 0 || row >= rows || col < 0 || col >= cols ||
            visited[row, col] || board[row][col] != word[index])
            return false;
        
        visited[row, col] = true;
        
        // Try all 4 directions
        bool found = DFS(row + 1, col, index + 1) ||
                     DFS(row - 1, col, index + 1) ||
                     DFS(row, col + 1, index + 1) ||
                     DFS(row, col - 1, index + 1);
        
        visited[row, col] = false; // Backtrack
        return found;
    }
    
    // Try starting from each cell
    for (int row = 0; row < rows; row++)
    {
        for (int col = 0; col < cols; col++)
        {
            if (DFS(row, col, 0)) return true;
        }
    }
    
    return false;
}
```

### Expected/Pruned State Space:
Pruned when:
- Cell is out of bounds
- Cell is already visited
- Cell character doesn't match word[index]

### State Space Leading to Output:
One valid path from any starting cell matching the word → boolean result

### Brute Force Canonical Skeleton:
```csharp
bool ExistBruteForce(char[][] board, string word) 
{
    int rows = board.Length;
    int cols = board[0].Length;
    
    bool DFS(int row, int col, int index, bool[,] visited) 
    {
        // Base case: matched entire word
        if (index == word.Length) return true;
        
        // Pruning conditions
        if (row < 0 || row >= rows || col < 0 || col >= cols ||
            visited[row, col] || board[row][col] != word[index])
            return false;
        
        // Choose: mark visited
        visited[row, col] = true;
        
        // Explore: try all 4 directions
        bool found = DFS(row + 1, col, index + 1, visited) ||
                     DFS(row - 1, col, index + 1, visited) ||
                     DFS(row, col + 1, index + 1, visited) ||
                     DFS(row, col - 1, index + 1, visited);
        
        // Unchoose: backtrack
        visited[row, col] = false;
        
        return found;
    }
    
    // Try each cell as starting point
    for (int row = 0; row < rows; row++) 
    {
        for (int col = 0; col < cols; col++) 
        {
            if (DFS(row, col, 0, new bool[rows, cols])) 
                return true;
        }
    }
    
    return false;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `board = [["A","B"],["C","D"]], word = "ABCD"`:

| Call | row | col | index | word[i] | board[r][c] | visited | action | result |
| ---- | --- | --- | ----- | ------- | ----------- | ------- | ------ | ------ |
| 1    | 0   | 0   | 0     | 'A'     | 'A'         | [F,F][F,F] | match, mark visited | - |
| 2    | 1   | 0   | 1     | 'B'     | 'C'         | [T,F][F,F] | mismatch, return false | false |
| 2    | -1  | 0   | 1     | 'B'     | -           | [T,F][F,F] | out of bounds | false |
| 2    | 0   | 1   | 1     | 'B'     | 'B'         | [T,F][F,F] | match, mark visited | - |
| 3    | 1   | 1   | 2     | 'C'     | 'D'         | [T,T][F,F] | mismatch | false |
| 3    | 0   | 2   | 2     | 'C'     | -           | [T,T][F,F] | out of bounds | false |
| 3    | 1   | 0   | 2     | 'C'     | 'C'         | [T,T][F,F] | match, mark visited | - |
| 4    | 2   | 0   | 3     | 'D'     | -           | [T,T][T,F] | out of bounds | false |
| 4    | 1   | 1   | 3     | 'D'     | 'D'         | [T,T][T,F] | match, mark visited | - |
| 5    | -   | -   | 4     | -       | -           | [T,T][T,T] | index==4, return true | true |

### Optimized Solution from Canonical Skeleton:
```csharp
bool Exist(char[][] board, string word) 
{
    int rows = board.Length;
    int cols = board[0].Length;
    
    bool DFS(int row, int col, int index, bool[,] visited) 
    {
        if (index == word.Length) return true;
        
        if (row < 0 || row >= rows || col < 0 || col >= cols ||
            visited[row, col] || board[row][col] != word[index])
            return false;
        
        visited[row, col] = true;
        
        bool found = DFS(row + 1, col, index + 1, visited) ||
                     DFS(row - 1, col, index + 1, visited) ||
                     DFS(row, col + 1, index + 1, visited) ||
                     DFS(row, col - 1, index + 1, visited);
        
        visited[row, col] = false;
        return found;
    }
    
    for (int row = 0; row < rows; row++) 
    {
        for (int col = 0; col < cols; col++) 
        {
            if (DFS(row, col, 0, new bool[rows, cols])) 
                return true;
        }
    }
    
    return false;
}
```

### Explanation of Pruning:
- **Character mismatch:** Stop immediately if board[row][col] != word[index]
- **Visited cells:** Don't revisit cells in current path
- **Bounds checking:** Stop at grid boundaries
- **Early termination:** Return true immediately when word is found

### Optimized Solution Code Walkthrough / Variable Trace:
Same as brute force - all pruning conditions are already present.

### Big-O Analysis:
- **Time Complexity:** O(m × n × 4^L) → try each cell, branch 4 ways for word length L
- **Space Complexity:** O(L) → recursion depth = word length
- **Output Space:** O(1) → boolean result

</details>

<details>
<summary><b>Variant #7: N-Queens</b></summary>

## Variant #7: N-Queens

### Input/Output:
- Input: `n = 4`
- Output: `[[".Q..","...Q","Q...","..Q."],["..Q.","Q...","...Q",".Q.."]]`

### Full State Space:
Decision tree placing one queen per row, trying all columns.
```
For n=4: Place queen in each row
Row 0: try columns 0,1,2,3
Row 1: try columns 0,1,2,3 (if safe)
Row 2: try columns 0,1,2,3 (if safe)
Row 3: try columns 0,1,2,3 (if safe)

Unpruned: 4^4 = 256 states
```
```csharp
void GenerateAllQueenPlacements(int n)
{
    var allSolutions = new List<List<string>>();
    
    bool IsSafe(int row, int col, HashSet<int> cols, 
                 HashSet<int> diag1, HashSet<int> diag2)
    {
        return !cols.Contains(col) && 
               !diag1.Contains(row - col) && 
               !diag2.Contains(row + col);
    }
    
    void Generate(int row, List<string> board, 
                  HashSet<int> cols, HashSet<int> diag1, HashSet<int> diag2)
    {
        if (row == n)
        {
            allSolutions.Add(new List<string>(board));
            return;
        }
        
        for (int col = 0; col < n; col++)
        {
            if (IsSafe(row, col, cols, diag1, diag2))
            {
                var rowStr = new string('.', col) + 'Q' + new string('.', n - col - 1);
                board.Add(rowStr);
                cols.Add(col);
                diag1.Add(row - col);
                diag2.Add(row + col);
                
                Generate(row + 1, board, cols, diag1, diag2);
                
                diag2.Remove(row + col);
                diag1.Remove(row - col);
                cols.Remove(col);
                board.RemoveAt(board.Count - 1);
            }
        }
    }
    
    Generate(0, new List<string>(), new HashSet<int>(), 
             new HashSet<int>(), new HashSet<int>());
}
```

### Expected/Pruned State Space:
Pruned when placing queen conflicts with existing queens (same column or diagonal).
```
For n=4: Only 2 valid solutions after pruning
```

### State Space Leading to Output:
Leaf nodes at depth n where all queens are safely placed → varies by n

### Brute Force Canonical Skeleton:
```csharp
IList<IList<string>> SolveNQueensBruteForce(int n) 
{
    var result = new List<IList<string>>();
    
    bool IsSafe(int row, int col, HashSet<int> cols, 
                HashSet<int> diag1, HashSet<int> diag2) 
    {
        return !cols.Contains(col) && 
               !diag1.Contains(row - col) && 
               !diag2.Contains(row + col);
    }
    
    void Backtrack(int row, List<string> board, HashSet<int> cols, 
                   HashSet<int> diag1, HashSet<int> diag2) 
    {
        // Base case: placed all queens
        if (row == n) 
        {
            result.Add(new List<string>(board));
            return;
        }
        
        // Try each column in current row
        for (int col = 0; col < n; col++) 
        {
            if (!IsSafe(row, col, cols, diag1, diag2)) continue;
            
            // Choose: place queen
            var rowStr = new string('.', col) + 'Q' + new string('.', n - col - 1);
            board.Add(rowStr);
            cols.Add(col);
            diag1.Add(row - col);
            diag2.Add(row + col);
            
            // Explore: recurse to next row
            Backtrack(row + 1, board, cols, diag1, diag2);
            
            // Unchoose: remove queen
            diag2.Remove(row + col);
            diag1.Remove(row - col);
            cols.Remove(col);
            board.RemoveAt(board.Count - 1);
        }
    }
    
    Backtrack(0, new List<string>(), new HashSet<int>(), 
              new HashSet<int>(), new HashSet<int>());
    return result;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For `n = 4`:

| Call | row | col | cols | diag1 | diag2 | action | result so far |
| ---- | --- | --- | ---- | ----- | ----- | ------ | ------------- |
| 1    | 0   | 0   | {}   | {}    | {}    | place Q at (0,0) | [] |
| 2    | 1   | 0   | {0}  | {0}   | {0}   | not safe (col 0) | [] |
| 2    | 1   | 1   | {0}  | {0}   | {0}   | not safe (diag) | [] |
| 2    | 1   | 2   | {0}  | {0}   | {0}   | safe, place Q | [] |
| 3    | 2   | 0   | {0,2}| {0,-1}| {0,3} | not safe | [] |
| 3    | 2   | 1   | {0,2}| {0,-1}| {0,3} | not safe | [] |
| 3    | 2   | 2   | {0,2}| {0,-1}| {0,3} | not safe | [] |
| 3    | 2   | 3   | {0,2}| {0,-1}| {0,3} | not safe | [] |
| -    | -   | -   | -    | -     | -     | backtrack | [] |
| 1    | 0   | 1   | {}   | {}    | {}    | place Q at (0,1) | [] |
| ...  | ... | ... | ...  | ...   | ...   | continues | eventually 2 solutions |

### Optimized Solution from Canonical Skeleton:
```csharp
IList<IList<string>> SolveNQueens(int n) 
{
    var result = new List<IList<string>>();
    
    bool IsSafe(int row, int col, HashSet<int> cols, 
                HashSet<int> diag1, HashSet<int> diag2) 
    {
        return !cols.Contains(col) && 
               !diag1.Contains(row - col) && 
               !diag2.Contains(row + col);
    }
    
    void Backtrack(int row, List<string> board, HashSet<int> cols, 
                   HashSet<int> diag1, HashSet<int> diag2) 
    {
        if (row == n) 
        {
            result.Add(new List<string>(board));
            return;
        }
        
        for (int col = 0; col < n; col++) 
        {
            if (!IsSafe(row, col, cols, diag1, diag2)) continue;
            
            var rowStr = new string('.', col) + 'Q' + new string('.', n - col - 1);
            board.Add(rowStr);
            cols.Add(col);
            diag1.Add(row - col);
            diag2.Add(row + col);
            
            Backtrack(row + 1, board, cols, diag1, diag2);
            
            diag2.Remove(row + col);
            diag1.Remove(row - col);
            cols.Remove(col);
            board.RemoveAt(board.Count - 1);
        }
    }
    
    Backtrack(0, new List<string>(), new HashSet<int>(), 
              new HashSet<int>(), new HashSet<int>());
    return result;
}
```

### Explanation of Pruning:
- **Column conflict:** Skip if column already has a queen
- **Diagonal conflict:** Skip if either diagonal (row-col or row+col) has a queen
- **Massive pruning:** Reduces n^n to much smaller space
- **HashSet lookups:** O(1) safety checks

### Optimized Solution Code Walkthrough / Variable Trace:
Same as brute force - the conflict checking is the key pruning mechanism.

### Big-O Analysis:
- **Time Complexity:** O(n!) → roughly one queen per row, decreasing choices
- **Space Complexity:** O(n) → recursion depth + tracking sets
- **Output Space:** O(k × n^2) → k solutions, each n×n board

</details>

<details>
<summary><b>Variant #8: Sudoku Solver</b></summary>

## Variant #8: Sudoku Solver

### Input/Output:
- Input: `board = [["5","3",".",".","7",...],...]` (9x9 grid with empty cells marked as ".")
- Output: Solved board with all cells filled

### Full State Space:
Decision tree trying digits 1-9 for each empty cell.
```
For each empty cell: try 9 digits
Unpruned: 9^(empty_cells) states
```
```csharp
void SolveSudoku(char[][] board)
{
    bool IsValid(int row, int col, char num)
    {
        // Check row
        for (int c = 0; c < 9; c++)
            if (board[row][c] == num) return false;
        
        // Check column
        for (int r = 0; r < 9; r++)
            if (board[r][col] == num) return false;
        
        // Check 3x3 box
        int boxRow = (row / 3) * 3;
        int boxCol = (col / 3) * 3;
        for (int r = boxRow; r < boxRow + 3; r++)
            for (int c = boxCol; c < boxCol + 3; c++)
                if (board[r][c] == num) return false;
        
        return true;
    }
    
    bool Solve()
    {
        for (int row = 0; row < 9; row++)
        {
            for (int col = 0; col < 9; col++)
            {
                if (board[row][col] == '.')
                {
                    for (char num = '1'; num <= '9'; num++)
                    {
                        if (IsValid(row, col, num))
                        {
                            board[row][col] = num;
                            
                            if (Solve()) return true;
                            
                            board[row][col] = '.'; // Backtrack
                        }
                    }
                    return false; // No valid number found
                }
            }
        }
        return true; // All cells filled
    }
    
    Solve();
}
```

### Expected/Pruned State Space:
Pruned when digit violates Sudoku rules (row, column, or 3x3 box).
```
Massive pruning from constraint checking
```

### State Space Leading to Output:
One leaf node representing the unique valid solution → typically one solution per valid puzzle

### Brute Force Canonical Skeleton:
```csharp
void SolveSudokuBruteForce(char[][] board) 
{
    bool IsValid(int row, int col, char num) 
    {
        // Check row
        for (int c = 0; c < 9; c++)
            if (board[row][c] == num) return false;
        
        // Check column
        for (int r = 0; r < 9; r++)
            if (board[r][col] == num) return false;
        
        // Check 3x3 box
        int boxRow = (row / 3) * 3;
        int boxCol = (col / 3) * 3;
        for (int r = boxRow; r < boxRow + 3; r++)
            for (int c = boxCol; c < boxCol + 3; c++)
                if (board[r][c] == num) return false;
        
        return true;
    }
    
    bool Solve() 
    {
        // Find next empty cell
        for (int row = 0; row < 9; row++) 
        {
            for (int col = 0; col < 9; col++) 
            {
                if (board[row][col] == '.') 
                {
                    // Try each digit
                    for (char num = '1'; num <= '9'; num++) 
                    {
                        if (IsValid(row, col, num)) 
                        {
                            // Choose: place digit
                            board[row][col] = num;
                            
                            // Explore: recurse
                            if (Solve()) return true;
                            
                            // Unchoose: backtrack
                            board[row][col] = '.';
                        }
                    }
                    return false; // No valid digit for this cell
                }
            }
        }
        return true; // All cells filled successfully
    }
    
    Solve();
}
```

### Brute Force Code Walkthrough / Variable Trace:
For a partial Sudoku board (simplified 4x4 example):
```
[1, 2, ., .]
[., ., ., .]
[., ., ., .]
[., ., ., .]
```

| Call | row | col | num | IsValid | action | result |
| ---- | --- | --- | --- | ------- | ------ | ------ |
| 1    | 0   | 2   | '1' | no      | skip (row has 1) | - |
| 1    | 0   | 2   | '2' | no      | skip (row has 2) | - |
| 1    | 0   | 2   | '3' | yes     | place 3 | - |
| 2    | 0   | 3   | '1' | no      | skip (row has 1) | - |
| 2    | 0   | 3   | '4' | yes     | place 4 | - |
| 3    | 1   | 0   | '2' | no      | skip (col has 2) | - |
| 3    | 1   | 0   | '3' | yes     | place 3 | - |
| ...  | ... | ... | ... | ...     | continues | eventually solved |

### Optimized Solution from Canonical Skeleton:
```csharp
void SolveSudoku(char[][] board) 
{
    bool IsValid(int row, int col, char num) 
    {
        for (int c = 0; c < 9; c++)
            if (board[row][c] == num) return false;
        
        for (int r = 0; r < 9; r++)
            if (board[r][col] == num) return false;
        
        int boxRow = (row / 3) * 3;
        int boxCol = (col / 3) * 3;
        for (int r = boxRow; r < boxRow + 3; r++)
            for (int c = boxCol; c < boxCol + 3; c++)
                if (board[r][c] == num) return false;
        
        return true;
    }
    
    bool Solve() 
    {
        for (int row = 0; row < 9; row++) 
        {
            for (int col = 0; col < 9; col++) 
            {
                if (board[row][col] == '.') 
                {
                    for (char num = '1'; num <= '9'; num++) 
                    {
                        if (IsValid(row, col, num)) 
                        {
                            board[row][col] = num;
                            if (Solve()) return true;
                            board[row][col] = '.';
                        }
                    }
                    return false;
                }
            }
        }
        return true;
    }
    
    Solve();
}
```

### Explanation of Pruning:
- **Constraint checking:** Only place digit if it doesn't violate Sudoku rules
- **Row/Column/Box validation:** Prunes invalid branches immediately
- **Early termination:** Return true as soon as solution is found
- **Further optimization possible:** Track candidates per cell, use heuristics

### Optimized Solution Code Walkthrough / Variable Trace:
Same as brute force - constraint checking is the primary pruning mechanism.

### Big-O Analysis:
- **Time Complexity:** O(9^m) where m = empty cells → heavily pruned in practice
- **Space Complexity:** O(m) → recursion depth = number of empty cells
- **Output Space:** O(1) → modifies board in-place

</details>


---

## Key Takeaways

### Backtracking = DFS + Undo
Always define:
- **State:** What does the current partial solution look like?
- **Choices:** What options do we have at each step?
- **Constraints:** What makes a choice invalid?
- **Base case:** When have we found a complete solution?

### Canonical Template
```csharp
void Backtrack(state, choices) 
{
    if (BaseCase(state)) 
    {
        RecordSolution(state);
        return;
    }
    
    foreach (var choice in choices) 
    {
        if (!IsValid(choice, state)) continue; // Pruning
        
        MakeChoice(choice, state);     // Choose
        Backtrack(newState, newChoices); // Explore
        UndoChoice(choice, state);     // Unchoose (Backtrack)
    }
}
```

### Optimization = Pruning
- Prune invalid branches early with constraint checks
- Track state with HashSets or arrays for O(1) validation
- Many DP problems start as backtracking problems

### Problem Categories
1. **Generation problems:** Generate all valid combinations (Subsets, Permutations)
2. **Search problems:** Find if valid solution exists (Word Search, Sudoku)
3. **Optimization problems:** Find best solution among valid ones (often converted to DP)

---

_End of Backtracking Pattern_

