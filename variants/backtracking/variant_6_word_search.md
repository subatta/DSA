# Word Search

**Difficulty:** Medium  
**LeetCode Problem:** #79  
**Tags:** `Backtracking`, `DFS`, `Grid`, `Matrix`, `Path Finding`

---

## Problem Statement

Given an `m x n` grid of characters `board` and a string `word`, return `true` if `word` exists in the grid.

The word can be constructed from letters of sequentially adjacent cells, where adjacent cells are horizontally or vertically neighboring. The same letter cell **may not be used more than once**.

**Constraints:**
- `m == board.length`
- `n == board[i].length`
- `1 <= m, n <= 6`
- `1 <= word.length <= 15`
- `board` and `word` consists of only lowercase and uppercase English letters.

**Example 1:**
```
Input: board = [["A","B","C","E"],
                ["S","F","C","S"],
                ["A","D","E","E"]], 
       word = "ABCCED"
Output: true
```

```
Visualization:
A → B → C ↓ E
S   F   C   S
A   D   E ← E

Path: (0,0) → (0,1) → (0,2) → (1,2) → (2,2) → (2,3)
```

**Example 2:**
```
Input: board = [["A","B","C","E"],
                ["S","F","C","S"],
                ["A","D","E","E"]], 
       word = "SEE"
Output: true
```

```
Path: (1,3) → (2,3) → (2,2)
S   F   C   S
A   D   E → E
         ↑
```

**Example 3:**
```
Input: board = [["A","B","C","E"],
                ["S","F","C","S"],
                ["A","D","E","E"]], 
       word = "ABCB"
Output: false
```

---

## Step 1: State Space

### Problem Visualization

Input: `board = [["A","B"],["C","D"]]`, `word = "ABDC"`

```
Board:
  A  B
  C  D

Searching for "ABDC":

Start at 'A' (0,0):
  A matches word[0] ✓
  Mark (0,0) as visited
  
  Try neighbors:
    → Right (0,1) 'B':
        B matches word[1] ✓
        Mark (0,1) as visited
        
        Try neighbors:
          → Down (1,1) 'D':
              D matches word[2] ✓
              Mark (1,1) as visited
              
              Try neighbors:
                → Left (1,0) 'C':
                    C matches word[3] ✓
                    Mark (1,0) as visited
                    
                    Reached end of word!
                    Return TRUE ✓

Path found: (0,0) → (0,1) → (1,1) → (1,0)
            A → B → D → C
```

**Decision tree (backtracking):**

```
For word = "ABC" on a grid:

Start from each cell matching 'A':

From (0,0) 'A':
  Explore 4 directions:
    ↑ out of bounds
    → (0,1) 'B' matches word[1]
        Explore from (0,1):
          ↑ out of bounds
          → (0,2) 'C' matches word[2]
              Word complete! ✓
          ↓ (1,1) doesn't match
          ← (0,0) visited
    ↓ (1,0) doesn't match
    ← out of bounds

Found valid path!
```

### Core Question

**How do we find a path in a grid that spells a given word?**

Backtracking with:
1. **DFS from each starting cell**
2. **Visited tracking** (no cell reuse)
3. **4-directional exploration**
4. **Match character by character**

### Deriving from First Principles

**Observation 1: Multiple starting points**
```
Word can start from any cell matching word[0]
Must try all potential starting cells

Strategy:
  for each cell in grid:
    if cell == word[0]:
      try DFS from this cell
      if found: return true
  
  return false  // No starting point worked
```

**Observation 2: 4-directional DFS**
```
From current cell (r, c), can move:
  Up:    (r-1, c)
  Down:  (r+1, c)
  Left:  (r, c-1)
  Right: (r, c+1)

Constraints:
  - Must stay in bounds
  - Must not revisit cells
  - Must match next character in word
```

**Observation 3: Visited tracking**
```
"Same cell may not be used more than once"

Options:
  1. Use Set<(row, col)>: Track visited cells
  2. Mark in-place: board[r][c] = '#' (temporary marker)
  3. Boolean visited[m][n] array

Option 2 (in-place) is space efficient:
  Before recursion: save original, mark as visited
  After recursion: restore original (backtrack)
```

**Observation 4: Termination conditions**
```
Success: index == word.length
  Matched all characters

Failure conditions:
  - Out of bounds
  - Cell already visited
  - Character doesn't match word[index]
  - No valid neighbor found (implicit in loop)
```

**Observation 5: Complexity**
```
Time: O(m × n × 4^L) where L = word.length
  - Try all m×n starting cells
  - From each cell, up to 4 directions
  - Recursion depth L
  - But pruning reduces significantly

Space: O(L) - recursion depth
  - No extra visited structure if marking in-place
```

**Formula (Backtracking with in-place marking):**
```
Exist(board, word):
  for r in 0 to m-1:
    for c in 0 to n-1:
      if DFS(board, word, r, c, 0):
        return true
  return false

DFS(board, word, r, c, index):
  // Success: matched all characters
  if index == word.length:
    return true
  
  // Out of bounds or mismatch
  if r < 0 or r >= m or c < 0 or c >= n:
    return false
  if board[r][c] != word[index]:
    return false
  
  // Mark as visited
  temp = board[r][c]
  board[r][c] = '#'
  
  // Try all 4 directions
  found = DFS(r-1, c, index+1) or
          DFS(r+1, c, index+1) or
          DFS(r, c-1, index+1) or
          DFS(r, c+1, index+1)
  
  // Backtrack: restore cell
  board[r][c] = temp
  
  return found
```

### State Space Structure

**Type:** Grid-based DFS backtracking.

**Structure:**
- Starting points: up to m×n cells
- Branching: 4 directions per cell
- Depth: word.length
- Heavy pruning by character mismatch

**Cardinality:**
- Worst case: O(m × n × 4^L)
- Typical case: Much less due to pruning
- Space: O(L) recursion depth

**Key Property:** In-place visited marking avoids extra O(m×n) space.

### Example Computation

Input: `board = [["A","B"],["C","D"]]`, `word = "AB"`

```
Exist():
  for r=0, c=0 ('A'):
    DFS(0, 0, 0):
      index=0, board[0][0]='A', word[0]='A' ✓
      Mark board[0][0] = '#'
      
      Try (r-1,c): r=-1, out of bounds → false
      Try (r+1,c): DFS(1, 0, 1):
        index=1, board[1][0]='C', word[1]='B' ✗
        Return false
      
      Try (r,c-1): c=-1, out of bounds → false
      Try (r,c+1): DFS(0, 1, 1):
        index=1, board[0][1]='B', word[1]='B' ✓
        Mark board[0][1] = '#'
        
        Try all 4 directions:
          All return false or out of bounds
        
        found = false... wait, but index == 2 == word.length!
        
Let me recorrect:

DFS(board, word, r, c, index):
  if index == word.length:
    return true  // Matched all!
  
  if out of bounds or visited or mismatch:
    return false
  
  mark visited
  found = explore 4 directions with index+1
  restore
  return found

So:

DFS(0, 0, 0):
  index=0, word.length=2, continue
  board[0][0]='A', word[0]='A' ✓
  Mark '#'
  
  Try right: DFS(0, 1, 1):
    index=1, word.length=2, continue
    board[0][1]='B', word[1]='B' ✓
    Mark '#'
    
    Try all 4: DFS(?, ?, 2):
      index=2 == word.length=2
      Return TRUE ✓
    
    found = true
    Restore board[0][1] = 'B'
    Return TRUE
  
  found = true
  Restore board[0][0] = 'A'
  Return TRUE

Exist() returns TRUE ✓
```

---

## Step 2: Approaches

### Approach 1: Backtracking with In-Place Marking
Mark visited cells directly in board

### Approach 2: Backtracking with Visited Set
Use Set<(row,col)> to track visited

### Approach 3: Optimized Starting Points
Only start from cells matching word[0]

---

## Step 3: Optimization Ideas

### Key Insights
1. **In-place marking:** Saves O(m×n) space
2. **Early mismatch:** Return false immediately
3. **Character frequency check:** If board lacks required chars, return false early
4. **Reverse word:** If word[0] frequency > word[-1] frequency, reverse word

---

## Step 4: Optimal Solution (Backtracking with In-Place Marking)

```csharp
public class Solution {
    public bool Exist(char[][] board, string word) {
        int m = board.Length;
        int n = board[0].Length;
        
        // Try starting from each cell
        for (int r = 0; r < m; r++) {
            for (int c = 0; c < n; c++) {
                if (DFS(board, word, r, c, 0)) {
                    return true;
                }
            }
        }
        
        return false;
    }
    
    private bool DFS(char[][] board, string word, int r, int c, int index) {
        // Success: matched entire word
        if (index == word.Length) {
            return true;
        }
        
        // Out of bounds
        if (r < 0 || r >= board.Length || c < 0 || c >= board[0].Length) {
            return false;
        }
        
        // Cell already visited or character mismatch
        if (board[r][c] != word[index]) {
            return false;
        }
        
        // Mark cell as visited
        char temp = board[r][c];
        board[r][c] = '#';
        
        // Explore all 4 directions
        bool found = DFS(board, word, r - 1, c, index + 1) ||  // Up
                     DFS(board, word, r + 1, c, index + 1) ||  // Down
                     DFS(board, word, r, c - 1, index + 1) ||  // Left
                     DFS(board, word, r, c + 1, index + 1);    // Right
        
        // Backtrack: restore cell
        board[r][c] = temp;
        
        return found;
    }
}
```

**Complexity:**
- Time: O(m × n × 4^L) where L = word.length
  - Try all m×n starting cells
  - Each cell: up to 4^L paths (heavily pruned)
- Space: O(L) - recursion stack depth
  - No extra visited structure

---

## Alternative: With Visited Set

```csharp
public bool Exist(char[][] board, string word) {
    int m = board.Length;
    int n = board[0].Length;
    
    for (int r = 0; r < m; r++) {
        for (int c = 0; c < n; c++) {
            var visited = new HashSet<(int, int)>();
            if (DFS(board, word, r, c, 0, visited)) {
                return true;
            }
        }
    }
    
    return false;
}

private bool DFS(char[][] board, string word, int r, int c, int index, HashSet<(int, int)> visited) {
    if (index == word.Length) {
        return true;
    }
    
    if (r < 0 || r >= board.Length || c < 0 || c >= board[0].Length) {
        return false;
    }
    
    if (visited.Contains((r, c)) || board[r][c] != word[index]) {
        return false;
    }
    
    visited.Add((r, c));
    
    bool found = DFS(board, word, r - 1, c, index + 1, visited) ||
                 DFS(board, word, r + 1, c, index + 1, visited) ||
                 DFS(board, word, r, c - 1, index + 1, visited) ||
                 DFS(board, word, r, c + 1, index + 1, visited);
    
    visited.Remove((r, c));  // Backtrack
    
    return found;
}
```

**Trade-off:** Cleaner (doesn't modify board), but O(L) extra space for visited set.

---

## Optimization: Character Frequency Check

```csharp
public bool Exist(char[][] board, string word) {
    // Quick reject: check if board has enough characters
    var boardFreq = new Dictionary<char, int>();
    foreach (var row in board) {
        foreach (var ch in row) {
            boardFreq[ch] = boardFreq.GetValueOrDefault(ch, 0) + 1;
        }
    }
    
    var wordFreq = new Dictionary<char, int>();
    foreach (var ch in word) {
        wordFreq[ch] = wordFreq.GetValueOrDefault(ch, 0) + 1;
    }
    
    foreach (var (ch, count) in wordFreq) {
        if (!boardFreq.ContainsKey(ch) || boardFreq[ch] < count) {
            return false;  // Not enough of this character
        }
    }
    
    // Continue with normal backtracking...
    // (same as before)
}
```

**Benefit:** Early rejection when board lacks required characters.

---

## Edge Cases

1. **Word length 1:** `word = "A"`, just check if 'A' exists
2. **Word longer than grid:** `m × n < word.length` → impossible
3. **Full grid path:** Word uses all cells
4. **No starting point:** No cell matches word[0]
5. **Single cell grid:** `m = n = 1`

---

## Visualization

```
Board:    Word: "SEE"
A B C E
S F C S
A D E E

Starting from (1,3) 'S':
  S matches word[0] ✓
  Mark (1,3) visited
  
  Try down (2,3) 'E':
    E matches word[1] ✓
    Mark (2,3) visited
    
    Try left (2,2) 'E':
      E matches word[2] ✓
      index == 3 == word.length
      SUCCESS! ✓

Path: (1,3) → (2,3) → (2,2)
```

---

## Related Problems

1. **Word Search II (LeetCode #212)** - Multiple words (Trie + DFS)
2. **Number of Islands (LeetCode #200)** - Connected components DFS
3. **Surrounded Regions (LeetCode #130)** - Grid DFS/BFS
4. **Path with Maximum Gold (LeetCode #1219)** - Similar backtracking
5. **Robot Room Cleaner (LeetCode #489)** - Grid exploration

---

## Pattern Recognition

**Problem Asks For:**
- Find path in grid
- Sequential matching
- No cell reuse
- 4-directional movement

**This Suggests:**
- DFS/Backtracking on grid
- Visited tracking (in-place or set)
- Try all starting points
- → **Grid Path Backtracking Pattern**

**Key Indicators:**
- "Find word/path in grid"
- "Adjacent cells" (4 or 8 directions)
- "Cannot reuse cells"
- Matrix traversal with constraints

---

## Tags

`#backtracking` `#dfs` `#grid` `#matrix` `#path-finding` `#word-search`
