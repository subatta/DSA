# Variant #2: Group Anagrams

**LeetCode #49 - Medium**

## Problem Statement
Given an array of strings `strs`, group the anagrams together. You can return the answer in any order.

An **anagram** is a word formed by rearranging the letters of another word, using all original letters exactly once.

**Example:** 
- Input: `strs = ["eat","tea","tan","ate","nat","bat"]`
- Output: `[["bat"],["nat","tan"],["ate","eat","tea"]]`

## 4-Step Analysis Framework

### Step 1: State Space Derivation

### **Problem Visualization:**
```
Input: strs = ["eat", "tea", "tan", "ate", "nat", "bat"]

Goal: Group words that are anagrams

Anagram definition: Same letters, different order
  "eat" = {e:1, a:1, t:1}
  "tea" = {e:1, a:1, t:1} ← Same! They're anagrams
  "ate" = {e:1, a:1, t:1} ← Also same!

Key insight: Anagrams have IDENTICAL character frequency!

Two signature approaches:

1. Sorted string as key:
   "eat" → sort → "aet"
   "tea" → sort → "aet" ← Same signature!
   "ate" → sort → "aet"
   "tan" → sort → "ant"
   "nat" → sort → "ant"
   "bat" → sort → "abt"

2. Frequency count as key:
   "eat" → "#1#0#0...#1#0...#1#0..."  (a:1, e:1, t:1)
   "tea" → "#1#0#0...#1#0...#1#0..."  ← Same!
   "ate" → "#1#0#0...#1#0...#1#0..."

Hash map structure:
  {
    "aet": ["eat", "tea", "ate"],
    "ant": ["tan", "nat"],
    "abt": ["bat"]
  }

Single pass: O(n × k log k) vs brute force O(n² × k)
```

### **Core Question:**
How to group strings without comparing all pairs?

### **Deriving from First Principles:**

**Observation 1:** Brute force comparison
- For each string, compare with all others to find anagrams
- Comparison requires checking character frequencies → O(k) per pair
- Total: O(n² × k) comparisons

**Observation 2:** Anagrams share a property
- Anagrams have **identical character counts**
- This property is **consistent** — same for all anagrams
- Can use this as a "signature" or "key"

**Observation 3:** Hash map grouping
- Compute signature for each string → O(k) per string
- Use signature as hash map key
- Group strings with same signature together
- Single pass: O(n × k) for signature computation

**Observation 4:** Two signature methods
- **Sorted string:** Sort characters alphabetically
  - Time: O(k log k) per string
  - Space: O(k) for sorted string
  - Simpler to implement
- **Frequency array:** Count each character
  - Time: O(k) per string (optimal!)
  - Space: O(26) = O(1) for lowercase English
  - Slightly more complex

### **State Space Structure:**

**Type:** Partition strings into groups by anagram equivalence  
**Structure:** Each string belongs to exactly one group based on character frequency  
**Cardinality:** n strings, up to n groups (worst case: all unique)

**State Space Example:**
For `strs = ["eat", "tea", "tan", "ate", "nat", "bat"]`:

```
Character frequencies:
"eat" → {e:1, a:1, t:1}
"tea" → {e:1, a:1, t:1}  ← same as "eat"
"tan" → {t:1, a:1, n:1}
"ate" → {e:1, a:1, t:1}  ← same as "eat"
"nat" → {n:1, a:1, t:1}  ← same as "tan"
"bat" → {b:1, a:1, t:1}

Groups:
Group 1: "eat", "tea", "ate" (all have same chars)
Group 2: "tan", "nat"
Group 3: "bat"
```

**Key insight:** Anagrams have identical character frequency maps!

---

### Step 2: Brute Force with Value Tracing

**Approach:** Compare each string with all others to find anagrams

```csharp
public IList<IList<string>> GroupAnagrams_BruteForce(string[] strs)
{
    var result = new List<IList<string>>();
    var used = new bool[strs.Length];
    
    for (int i = 0; i < strs.Length; i++)
    {
        if (used[i]) continue;
        
        var group = new List<string>();
        group.Add(strs[i]);
        used[i] = true;
        
        // Find all anagrams of strs[i]
        for (int j = i + 1; j < strs.Length; j++)
        {
            if (!used[j] && AreAnagrams(strs[i], strs[j]))
            {
                group.Add(strs[j]);
                used[j] = true;
            }
        }
        
        result.Add(group);
    }
    
    return result;
}

private bool AreAnagrams(string s1, string s2)
{
    if (s1.Length != s2.Length) return false;
    
    var freq = new int[26];
    foreach (char c in s1) freq[c - 'a']++;
    foreach (char c in s2) freq[c - 'a']--;
    
    return freq.All(count => count == 0);
}
```

**Value Trace for:** `["eat", "tea", "tan", "ate"]`

| i | strs[i] | Group Start | j | strs[j] | Anagram? | Group After |
|---|---------|-------------|---|---------|----------|-------------|
| 0 | "eat" | ["eat"] | 1 | "tea" | ✅ | ["eat","tea"] |
| 0 | "eat" | ["eat","tea"] | 2 | "tan" | ❌ | ["eat","tea"] |
| 0 | "eat" | ["eat","tea"] | 3 | "ate" | ✅ | ["eat","tea","ate"] |
| 2 | "tan" | ["tan"] | 3 | used | - | ["tan"] |
| Result: [["eat","tea","ate"], ["tan"]] |

**Complexity:**
- **Time:** O(n² × k) where k = average string length (n comparisons, each O(k))
- **Space:** O(n) for used array

---

### Step 3: Pruning Analysis - Can We Do Better?

**Key Observations:**

1. **Anagram property:** Same character frequencies → same "signature"
2. **Signature options:**
   - Sort characters: "eat" → "aet", "tea" → "aet" (same!)
   - Count array: both → [1,0,0,0,1,...,1] (26 positions)
3. **Hash Map benefit:** Group by signature in O(1) per string
4. **No need to compare:** Use signature as key!

**Two Signature Approaches:**

**Approach A: Sorted String**
```
"eat" → sort → "aet"
"tea" → sort → "aet"  (same key!)
"tan" → sort → "ant"
```

**Approach B: Frequency String**
```
"eat" → "#1#0#0#0#1#0...#1#0" (26 counts)
"tea" → "#1#0#0#0#1#0...#1#0" (same key!)
```

**Decision:** ✅ Use Hash Map with sorted string as key (simpler implementation)!

---

### Step 4: Optimal Solution with Skeleton Transformation

**Algorithm: Hash Map Grouping**

```csharp
public IList<IList<string>> GroupAnagrams_Optimal(string[] strs)
{
    // Map: sorted_string -> list of anagrams
    var groups = new Dictionary<string, List<string>>();
    
    foreach (string str in strs)
    {
        // Create signature by sorting characters
        char[] chars = str.ToCharArray();
        Array.Sort(chars);
        string key = new string(chars);
        
        // Add to corresponding group
        if (!groups.ContainsKey(key))
        {
            groups[key] = new List<string>();
        }
        groups[key].Add(str);
    }
    
    // Convert to list format
    return new List<IList<string>>(groups.Values);
}
```

**Alternative: Frequency Count Key**

```csharp
public IList<IList<string>> GroupAnagrams_FrequencyKey(string[] strs)
{
    var groups = new Dictionary<string, List<string>>();
    
    foreach (string str in strs)
    {
        // Create signature from character counts
        int[] count = new int[26];
        foreach (char c in str)
        {
            count[c - 'a']++;
        }
        
        // Build key like "#1#0#1#..." (a count, b count, c count...)
        var keyBuilder = new System.Text.StringBuilder();
        for (int i = 0; i < 26; i++)
        {
            keyBuilder.Append('#');
            keyBuilder.Append(count[i]);
        }
        string key = keyBuilder.ToString();
        
        if (!groups.ContainsKey(key))
        {
            groups[key] = new List<string>();
        }
        groups[key].Add(str);
    }
    
    return new List<IList<string>>(groups.Values);
}
```

**Skeleton Transformation:**

```
BRUTE FORCE SKELETON:
├── result = []
├── used = [false] * n
├── for i in range(n):
│   ├── if used[i]: skip
│   ├── group = [strs[i]]
│   └── for j in range(i+1, n):
│       └── if AreAnagrams(strs[i], strs[j]):
│           └── group.add(strs[j])
└── return result

                    ↓ TRANSFORM ↓

OPTIMAL SKELETON (Hash Map):
├── groups = {}
├── for str in strs:
│   ├── key = sorted(str)  // O(k log k)
│   ├── if key not in groups:
│   │   └── groups[key] = []
│   └── groups[key].add(str)
└── return groups.values()

KEY CHANGES:
- Nested comparison → Single pass with grouping
- O(n²) comparisons → O(n) hash map operations
- AreAnagrams check → Signature generation
- Track used → Implicit grouping via hash map
```

**Value Trace for:** `["eat", "tea", "tan", "ate", "nat", "bat"]`

| Step | str | Sorted Key | groups |
|------|-----|------------|--------|
| 1 | "eat" | "aet" | {"aet": ["eat"]} |
| 2 | "tea" | "aet" | {"aet": ["eat","tea"]} |
| 3 | "tan" | "ant" | {"aet": ["eat","tea"], "ant": ["tan"]} |
| 4 | "ate" | "aet" | {"aet": ["eat","tea","ate"], "ant": ["tan"]} |
| 5 | "nat" | "ant" | {"aet": ["eat","tea","ate"], "ant": ["tan","nat"]} |
| 6 | "bat" | "abt" | {"aet": ["eat","tea","ate"], "ant": ["tan","nat"], "abt": ["bat"]} |

**Result:** `[["eat","tea","ate"], ["tan","nat"], ["bat"]]` ✅

---

## Big-O Analysis

**Brute Force:**
- **Time:** O(n² × k) where k = avg string length
- **Space:** O(n)

**Optimal (Sorted Key):**
- **Time:** O(n × k log k) - n strings, sort each of length k
- **Space:** O(n × k) - store all strings in hash map

**Optimal (Frequency Key):**
- **Time:** O(n × k) - n strings, count each of length k
- **Space:** O(n × k)

**Key Advantage:** Frequency key achieves O(n × k) vs O(n² × k), and avoids sorting!

---

## Pattern Recognition

**When to use Hash Map for Grouping:**
- ✅ Need to partition items by some property
- ✅ Can compute consistent "key" for each item
- ✅ Items with same key belong to same group
- ✅ O(1) lookup/insertion per item

**Signature Design:**
- Must be **consistent**: Same anagrams → same key
- Must be **unique**: Different anagrams → different keys
- Should be **efficient**: Fast to compute

**Common Grouping Patterns:**
- Sort elements → use sorted result as key
- Count frequencies → use count array/string as key
- Extract feature → use feature as key

---

## Edge Cases

1. **Empty strings:** `[""]` → `[[""]]`
2. **Single character:** `["a", "a", "b"]` → `[["a","a"], ["b"]]`
3. **All anagrams:** All go to one group
4. **No anagrams:** Each string in its own group
5. **Different lengths:** Automatically separate (different sorted keys)

---

## Signature Comparison

| Aspect | Sorted String Key | Frequency Count Key |
|--------|------------------|---------------------|
| **Time per string** | O(k log k) | O(k) |
| **Space per key** | O(k) | O(26) = O(1) |
| **Implementation** | Simpler | More complex |
| **Overall** | O(n × k log k) | O(n × k) ← Better! |

For interviews, sorted string is often acceptable and easier to implement correctly.

---

## Related Problems

- **Valid Anagram** (LeetCode #242) - Check if two strings are anagrams
- **Find All Anagrams in String** (LeetCode #438) - Sliding window + frequency
- **Isomorphic Strings** (LeetCode #205) - Similar grouping idea
- **Group Shifted Strings** (LeetCode #249) - Different grouping key

---

## Tags
`hash-map` `string` `grouping` `anagram` `frequency-counting` `medium`
