# Partition Labels

**Difficulty:** Medium  
**LeetCode Problem:** #763  
**Tags:** `Greedy`, `String`, `Hash Map`, `Two Pointers`

---

## Problem Statement

You are given a string `s`. We want to partition the string into as many parts as possible so that each letter appears in at most one part.

Note that the partition is done so that after concatenating all the parts in order, the resultant string should be `s`.

Return a list of integers representing the size of these parts.

**Constraints:**
- `1 <= s.length <= 500`
- `s` consists of lowercase English letters

**Example 1:**
```
Input: s = "ababcbacadefegdehijhklij"
Output: [9,7,8]
Explanation:
Partition: "ababcbaca", "defegde", "hijhklij"
Each letter appears in at most one partition.
```

**Example 2:**
```
Input: s = "eccbbbbdec"
Output: [10]
Explanation: All letters must stay together.
```

---

## Step 1: State Space

### Problem Visualization

For `s = "ababcbacadefegdehijhklij"`:

```
Last occurrence of each letter:
a: 8, b: 5, c: 7, d: 14, e: 15, f: 11, g: 13, 
h: 19, i: 22, j: 23, k: 20, l: 21

Index:  0  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 21 22 23
Char:   a  b  a  b  c  b  a  c  a  d  e  f  e  g  d  e  h  i  j  h  k  l  i  j
Last:   8  5  8  5  7  5  8  7  8 14 15 11 15 13 14 15 19 22 23 19 20 21 22 23

Partition 1: indices [0,8]
  Contains: a,b,c
  Last 'a' at 8 → must extend to 8
  
Partition 2: indices [9,15]
  Contains: d,e,f,g
  Last 'e' at 15 → must extend to 15
  
Partition 3: indices [16,23]
  Contains: h,i,j,k,l
  Last 'j' at 23 → must extend to 23
```

Greedy expansion:
```
Start partition at index 0
Track furthest last occurrence in current partition

As we scan:
  If we see char 'x', partition must extend to last_occurrence[x]
  When we reach end of partition, cut and start new partition
```

### Core Question

**How do we partition the string such that each character appears in exactly one partition, maximizing the number of partitions?**

Greedy approach: Make partitions as small as possible while respecting the constraint.

### Deriving from First Principles

**Observation 1: Last occurrence constraint**
If character 'x' appears in a partition, the partition must extend to the last occurrence of 'x'.

**Observation 2: Expanding partition boundary**
Start partition at index i.
As we scan from i, track the furthest last occurrence:
```
partitionEnd = last_occurrence[s[i]]
For each position j in [i, partitionEnd]:
  partitionEnd = max(partitionEnd, last_occurrence[s[j]])
```

**Observation 3: Cut when boundary reached**
When we reach position where index == partitionEnd:
- All characters in [partitionStart, partitionEnd] have last occurrence within this range
- Safe to cut here and start new partition

**Observation 4: Precompute last occurrences**
Build hash map: char → last index where it appears.
Use this to determine partition boundaries.

**Observation 5: Greedy algorithm**
```
Build last_occurrence map

partitionStart = 0
partitionEnd = 0

for i = 0 to n-1:
  partitionEnd = max(partitionEnd, last_occurrence[s[i]])
  
  if i == partitionEnd:
    Record partition size: partitionEnd - partitionStart + 1
    partitionStart = i + 1
```

**Formula:**
```
last = {} 
for i = 0 to n-1:
  last[s[i]] = i

sizes = []
start = 0, end = 0

for i = 0 to n-1:
  end = max(end, last[s[i]])
  if i == end:
    sizes.append(end - start + 1)
    start = i + 1
    
return sizes
```

### State Space Structure

**Type:** Greedy partition with expanding boundaries.

**Structure:**
- Hash map for last occurrences
- Two variables: start and end of current partition
- Scan string once, expand partition, cut when boundary reached

**Cardinality:**
- O(n) to build last occurrence map
- O(n) to scan and partition
- Total: O(n)

**Key Property:** Greedy expansion of partition boundary ensures each character in one partition.

### Example Computation

Input: `s = "ababcbacadefegdehijhklij"`

```
Build last occurrence map:
{a:8, b:5, c:7, d:14, e:15, f:11, g:13, h:19, i:22, j:23, k:20, l:21}

Scan and partition:

i=0, s[0]='a':
  end = max(0, last['a']=8) = 8
  0 != 8, continue

i=1, s[1]='b':
  end = max(8, last['b']=5) = 8
  1 != 8, continue

i=2, s[2]='a':
  end = max(8, last['a']=8) = 8

i=3, s[3]='b':
  end = max(8, last['b']=5) = 8

i=4, s[4]='c':
  end = max(8, last['c']=7) = 8

i=5, s[5]='b':
  end = max(8, last['b']=5) = 8

i=6, s[6]='a':
  end = max(8, last['a']=8) = 8

i=7, s[7]='c':
  end = max(8, last['c']=7) = 8

i=8, s[8]='a':
  end = max(8, last['a']=8) = 8
  i == end: Partition complete!
    Size = 8 - 0 + 1 = 9
    start = 9

i=9, s[9]='d':
  end = max(8, last['d']=14) = 14

i=10-14: (expand to 15 due to 'e')
  end reaches 15

i=15, s[15]='e':
  end = 15
  i == end: Partition complete!
    Size = 15 - 9 + 1 = 7
    start = 16

i=16-23: (expand to 23 due to 'j')
  end reaches 23

i=23:
  i == end: Partition complete!
    Size = 23 - 16 + 1 = 8

Result: [9, 7, 8]
```

### Generation Pattern

**Brute force:**
```csharp
// Try all possible partitions
// Check each partition for unique characters
// Return valid partition with max count
// Time: O(2^n) - exponential
```

---

## Step 2: Brute Force

Try all possible partitions, validate each: Exponential time.

---

## Step 3: Optimization Ideas

### Key Insights
1. **Precompute last occurrences:** O(n) preprocessing
2. **Greedy boundary expansion:** Partition must extend to furthest last occurrence
3. **Cut when boundary reached:** Safe to partition when all chars contained
4. **Single pass:** O(n) after preprocessing

### Why Greedy Works
At each position, we know the minimum extent required for current partition (furthest last occurrence of any character seen so far). Cutting as soon as possible maximizes partition count.

---

## Step 4: Optimal Solution

```csharp
public IList<int> PartitionLabels(string s) {
    // Build last occurrence map
    var last = new Dictionary<char, int>();
    for (int i = 0; i < s.Length; i++) {
        last[s[i]] = i;
    }
    
    var result = new List<int>();
    int start = 0;
    int end = 0;
    
    for (int i = 0; i < s.Length; i++) {
        // Expand partition boundary
        end = Math.Max(end, last[s[i]]);
        
        // If reached boundary, cut partition
        if (i == end) {
            result.Add(end - start + 1);
            start = i + 1;
        }
    }
    
    return result;
}
```

**Complexity:**
- Time: O(n) - two passes (one for last[], one for partition)
- Space: O(1) - hash map has at most 26 entries (lowercase letters)

**Alternative (using array for last):**
```csharp
public IList<int> PartitionLabels(string s) {
    int[] last = new int[26];
    
    // Record last occurrence of each letter
    for (int i = 0; i < s.Length; i++) {
        last[s[i] - 'a'] = i;
    }
    
    var result = new List<int>();
    int start = 0, end = 0;
    
    for (int i = 0; i < s.Length; i++) {
        end = Math.Max(end, last[s[i] - 'a']);
        
        if (i == end) {
            result.Add(end - start + 1);
            start = i + 1;
        }
    }
    
    return result;
}
```

---

## Edge Cases

1. **All same letter:** `"aaaa"` → `[4]`
2. **All different:** `"abcdef"` → `[1,1,1,1,1,1]`
3. **Single char:** `"a"` → `[1]`
4. **Interleaved:** `"ababab"` → `[6]` (all must be together)
5. **Pattern:** `"abcabc"` → `[6]`

---

## Related Problems

1. **Merge Intervals (LeetCode #56)** - Similar boundary expansion
2. **Non-overlapping Intervals (LeetCode #435)** - Greedy intervals
3. **Group Anagrams (LeetCode #49)** - Character grouping

---

## Pattern Recognition

**Problem Asks For:**
- Partition string/array
- Each element in at most one partition
- Maximize number of partitions

**This Suggests:**
- Track last occurrence
- Greedy boundary expansion
- Cut when all constraints satisfied
- → **Greedy pattern**

**Key Indicators:**
- "Partition into as many parts as possible"
- Characters/elements must be contained
- Last occurrence matters

---

## Tags

`#greedy` `#string` `#hash-map` `#partition` `#last-occurrence` `#boundary-expansion`
