# Word Ladder

**Difficulty:** Hard  
**LeetCode Problem:** #127  
**Tags:** `Graph`, `BFS`, `Shortest Path`, `String`, `Transformation`

---

## Problem Statement

A **transformation sequence** from word `beginWord` to word `endWord` using a dictionary `wordList` is a sequence of words `beginWord -> s1 -> s2 -> ... -> sk` such that:
- Every adjacent pair of words differs by a single letter.
- Every `si` for `1 <= i <= k` is in `wordList`. Note that `beginWord` does not need to be in `wordList`.
- `sk == endWord`

Given two words, `beginWord` and `endWord`, and a dictionary `wordList`, return the **number of words** in the **shortest transformation sequence** from `beginWord` to `endWord`, or `0` if no such sequence exists.

**Constraints:**
- `1 <= beginWord.length <= 10`
- `endWord.length == beginWord.length`
- `1 <= wordList.length <= 5000`
- `wordList[i].length == beginWord.length`
- `beginWord`, `endWord`, and `wordList[i]` consist of lowercase English letters.
- `beginWord != endWord`
- All the words in `wordList` are **unique**.

**Example 1:**
```
Input: beginWord = "hit", endWord = "cog", 
       wordList = ["hot","dot","dog","lot","log","cog"]
Output: 5
Explanation: "hit" -> "hot" -> "dot" -> "dog" -> "cog"
```

**Example 2:**
```
Input: beginWord = "hit", endWord = "cog", 
       wordList = ["hot","dot","dog","lot","log"]
Output: 0
Explanation: endWord "cog" is not in wordList.
```

---

## Step 1: State Space

### Problem Visualization

beginWord = "hit", endWord = "cog"
wordList = ["hot","dot","dog","lot","log","cog"]

```
Graph of transformations (differ by 1 letter):

hit → hot (h→h, i→o, t→t: differ by 1)
hot → dot (h→d, differ by 1)
hot → lot (h→l, differ by 1)
dot → dog (t→g, differ by 1)
dot → lot (d→l, differ by 1)
lot → log (t→g, differ by 1)
dog → cog (d→c, differ by 1)
log → cog (l→c, differ by 1)

Graph structure:
       hit
        |
       hot
      /   \
    dot   lot
     |     |
    dog   log
      \   /
       cog

Shortest path: hit → hot → dot → dog → cog
Length: 5 words
```

**BFS for shortest path:**
```
Queue: [(word, level)]

Level 0: hit
  Neighbors: hot (only word differing by 1)
  
Level 1: hot
  Neighbors: dot, lot
  
Level 2: dot, lot
  From dot: dog
  From lot: log
  
Level 3: dog, log
  From dog: cog ← Found! Level 4
  
Return 5 (level + 1)
```

### Core Question

**What is the shortest transformation sequence from beginWord to endWord?**

Model as graph, find shortest path using BFS.

### Deriving from First Principles

**Observation 1: Graph modeling**
```
Nodes: All words (beginWord + wordList)
Edges: Connect words differing by exactly 1 character

Example:
  "hit" connects to "hot" (differ at position 1)
  "hot" connects to "dot", "lot" (differ at position 0)
```

**Observation 2: Shortest path → BFS**
```
BFS guarantees shortest path in unweighted graph
- Explores level by level
- First time reaching endWord = shortest path
```

**Observation 3: Checking neighbors**
```
Naive: For each word, check all wordList words
  - Check if differ by 1: O(L) where L = word length
  - Do for all words: O(N × L) per word
  - Total: O(N² × L)

Optimized: Generate all possible 1-letter changes
  - For each position, try all 26 letters: O(L × 26)
  - Check if result in wordList: O(1) with HashSet
  - Total: O(L × 26) = O(L) per word
```

**Observation 4: Generic pattern**
```
Pattern-based approach:
  "hot" → "*ot", "h*t", "ho*" (patterns)
  Any word matching pattern is 1 transformation away
  
Build pattern → words mapping
Connect words sharing patterns
```

**Observation 5: Bidirectional BFS**
```
Advanced: BFS from both ends simultaneously
  - From beginWord
  - From endWord
  - Meet in middle
  
Reduces search space significantly
Time: O(N × L) instead of O(N² × L)
```

**Formula (BFS):**
```
if endWord not in wordList:
  return 0

wordSet = HashSet(wordList)
queue = [(beginWord, 1)]
visited = {beginWord}

while queue not empty:
  (word, level) = queue.dequeue()
  
  if word == endWord:
    return level
  
  for each possible 1-letter transformation:
    if transformed word in wordSet and not visited:
      queue.enqueue((transformed word, level + 1))
      mark as visited

return 0  // no path found
```

### State Space Structure

**Type:** Shortest path in unweighted graph (BFS).

**Structure:**
- Implicit graph of word transformations
- BFS queue with (word, level)
- HashSet for O(1) lookups

**Cardinality:**
- N = number of words
- L = word length
- Build graph implicitly during BFS
- Each word processed once: O(N)
- Each word generates O(L × 26) candidates
- Total: O(N × L × 26) = O(N × L)
- Space: O(N) for queue and visited set

**Key Property:** BFS finds shortest path in unweighted graph.

### Example Computation

beginWord = "hit", endWord = "cog"
wordList = ["hot","dot","dog","lot","log","cog"]

```
wordSet = {hot, dot, dog, lot, log, cog}
queue = [(hit, 1)]
visited = {hit}

Level 1:
  Process (hit, 1):
    Try transformations:
      ait, bit, cit, ..., xit, yit, zit - none in wordSet
      hat, hbt, hct, ..., hot ← in wordSet!
      hia, hib, ..., his, hit - hit already visited
    Add (hot, 2) to queue
    visited = {hit, hot}

Level 2:
  Process (hot, 2):
    Try transformations:
      aot, bot, ..., dot ← in wordSet
      lot ← in wordSet
      hat, hbt, ... - hot already visited
      hoa, hob, ..., hog - none in wordSet (dot, lot, cog have different pattern)
    Add (dot, 3), (lot, 3) to queue
    visited = {hit, hot, dot, lot}

Level 3:
  Process (dot, 3):
    Try: dog ← in wordSet
    Add (dog, 4)
    visited = {hit, hot, dot, lot, dog}
  
  Process (lot, 3):
    Try: log ← in wordSet
    Add (log, 4)
    visited = {hit, hot, dot, lot, dog, log}

Level 4:
  Process (dog, 4):
    Try: cog ← in wordSet AND equals endWord!
    Return 5

Result: 5 ✓
```

### Generation Pattern

**Brute force:**
```csharp
// DFS to explore all paths
// Track shortest path found
// Time: Exponential (many paths)
```

---

## Step 2: Brute Force

DFS with backtracking to find all paths: O(N^N) - exponential.

---

## Step 3: Optimization Ideas

### Key Insights
1. **BFS for shortest path:** Level-order guarantees shortest
2. **HashSet for O(1) lookup:** Check if word exists
3. **Generate transformations:** Try all 26 letters at each position
4. **Mark visited:** Avoid revisiting words
5. **Early termination:** Return when endWord found

---

## Step 4: Optimal Solution (BFS)

```csharp
public class Solution {
    public int LadderLength(string beginWord, string endWord, IList<string> wordList) {
        // Convert to HashSet for O(1) lookup
        var wordSet = new HashSet<string>(wordList);
        
        // If endWord not in list, no solution
        if (!wordSet.Contains(endWord)) {
            return 0;
        }
        
        // BFS
        var queue = new Queue<(string word, int level)>();
        queue.Enqueue((beginWord, 1));
        
        var visited = new HashSet<string> { beginWord };
        
        while (queue.Count > 0) {
            var (word, level) = queue.Dequeue();
            
            // Found the target
            if (word == endWord) {
                return level;
            }
            
            // Try all possible 1-letter transformations
            char[] chars = word.ToCharArray();
            for (int i = 0; i < chars.Length; i++) {
                char originalChar = chars[i];
                
                // Try all 26 letters
                for (char c = 'a'; c <= 'z'; c++) {
                    if (c == originalChar) continue;
                    
                    chars[i] = c;
                    string newWord = new string(chars);
                    
                    if (wordSet.Contains(newWord) && !visited.Contains(newWord)) {
                        queue.Enqueue((newWord, level + 1));
                        visited.Add(newWord);
                    }
                }
                
                // Restore original character
                chars[i] = originalChar;
            }
        }
        
        return 0;  // No path found
    }
}
```

**Complexity:**
- Time: O(N × L² × 26) = O(N × L²)
  - N words to process
  - For each word: L positions × 26 letters = O(L × 26)
  - String creation: O(L)
  - Total per word: O(L²)
- Space: O(N) - queue and visited set

---

## Alternative: Bidirectional BFS

More efficient for large graphs:

```csharp
public int LadderLength(string beginWord, string endWord, IList<string> wordList) {
    var wordSet = new HashSet<string>(wordList);
    if (!wordSet.Contains(endWord)) return 0;
    
    // Two sets for bidirectional search
    var beginSet = new HashSet<string> { beginWord };
    var endSet = new HashSet<string> { endWord };
    var visited = new HashSet<string>();
    
    int level = 1;
    
    while (beginSet.Count > 0 && endSet.Count > 0) {
        // Always expand smaller set
        if (beginSet.Count > endSet.Count) {
            var temp = beginSet;
            beginSet = endSet;
            endSet = temp;
        }
        
        var nextSet = new HashSet<string>();
        
        foreach (string word in beginSet) {
            char[] chars = word.ToCharArray();
            
            for (int i = 0; i < chars.Length; i++) {
                char originalChar = chars[i];
                
                for (char c = 'a'; c <= 'z'; c++) {
                    chars[i] = c;
                    string newWord = new string(chars);
                    
                    // If found in other set, connected!
                    if (endSet.Contains(newWord)) {
                        return level + 1;
                    }
                    
                    if (wordSet.Contains(newWord) && !visited.Contains(newWord)) {
                        nextSet.Add(newWord);
                        visited.Add(newWord);
                    }
                }
                
                chars[i] = originalChar;
            }
        }
        
        beginSet = nextSet;
        level++;
    }
    
    return 0;
}
```

**Complexity:** O(N × L²) but faster in practice (searches half the depth).

---

## Edge Cases

1. **endWord not in list:** Return `0`
2. **beginWord == endWord:** Return `1` (though problem says they differ)
3. **No path exists:** Return `0`
4. **Single transformation:** beginWord → endWord directly
5. **Multiple paths:** BFS finds shortest

---

## Visualization

```
Graph:
    hit
     |
    hot
   /   \
 dot   lot
  |     |
 dog   log
   \   /
    cog

BFS levels:
Level 0: [hit]
Level 1: [hot]
Level 2: [dot, lot]
Level 3: [dog, log]
Level 4: [cog] ← Found!

Return 5
```

---

## Related Problems

1. **Word Ladder II (LeetCode #126)** - Return all shortest paths
2. **Minimum Genetic Mutation (LeetCode #433)** - Similar with 8-char strings
3. **Open the Lock (LeetCode #752)** - BFS with digit transformations
4. **Shortest Path in Binary Matrix (LeetCode #1091)** - Grid BFS

---

## Pattern Recognition

**Problem Asks For:**
- Shortest transformation sequence
- Single-letter changes
- Minimum number of steps

**This Suggests:**
- Model as graph problem
- BFS for shortest path
- String transformation as edges
- → **BFS Shortest Path**

**Key Indicators:**
- "Shortest" path/sequence
- Transformations between states
- Unweighted (each step = 1)

---

## Tags

`#graph` `#bfs` `#shortest-path` `#string` `#transformation` `#unweighted-graph`
