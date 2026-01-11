# Surrounded Regions

**Difficulty:** Medium  
**LeetCode Problem:** #130  
**Tags:** `Graph`, `DFS`, `BFS`, `Grid`, `Boundary`, `Matrix`

---

## Problem Statement

Given an `m x n` matrix `board` containing `'X'` and `'O'`, capture all regions that are 4-directionally surrounded by `'X'`.

A region is **captured** by flipping all `'O'`s into `'X'`s in that surrounded region.

**Constraints:**
- `m == board.length`
- `n == board[i].length`
- `1 <= m, n <= 200`
- `board[i][j]` is `'X'` or `'O'`.

**Example 1:**
```
Input: board = [
  ["X","X","X","X"],
  ["X","O","O","X"],
  ["X","X","O","X"],
  ["X","O","X","X"]
]

Output: [
  ["X","X","X","X"],
  ["X","X","X","X"],
  ["X","X","X","X"],
  ["X","O","X","X"]
]

Explanation:
Surrounded regions should not be on the border.
The 'O' at (3,1) is on the border, so it and its connected 'O's are not captured.
The 'O' at (1,1) and (1,2) are surrounded by 'X', so they are captured.
```

**Example 2:**
```
Input: board = [["X"]]
Output: [["X"]]
```

---

## Step 1: State Space

### Problem Visualization

Original board:
```
X X X X
X O O X
X X O X
X O X X
```

**Key insight: Border 'O's cannot be captured**

```
Border cells:
  Row 0: X X X X  (no O)
  Row 3: X O X X  (O at (3,1))
  Col 0: X X X X  (no O)
  Col 3: X X X X  (no O)

Border O at (3,1) and its connected O's are safe:
  (3,1) connects to nothing (surrounded by X)
  Actually just (3,1) is safe

Interior O's at (1,1), (1,2), (2,2):
  Check if connected to any border O:
    (1,1) → (1,2) → (2,2) → all surrounded, no path to border
  These should be captured!
```

**Algorithm:**
1. Mark all border 'O's and their connected regions as "safe"
2. Flip all unmarked 'O's to 'X' (captured)
3. Restore safe 'O's

```
Step 1: DFS from border O's, mark as safe (use temporary marker like 'S'):
X X X X
X O O X
X X O X
X S X X  ← (3,1) marked safe

Step 2: Flip all remaining 'O' to 'X':
X X X X
X X X X  ← (1,1) and (1,2) captured
X X X X  ← (2,2) captured
X S X X

Step 3: Restore 'S' back to 'O':
X X X X
X X X X
X X X X
X O X X  ✓
```

### Core Question

**Which 'O' regions are completely surrounded by 'X' (not touching borders)?**

Reverse approach: Find regions touching borders, mark as safe, capture the rest.

### Deriving from First Principles

**Observation 1: Definition of "surrounded"**
```
A region is surrounded if:
  - It has no 'O' cells on the border
  - All paths from region to border go through 'X'

Equivalently:
  - Region is NOT connected to any border 'O'
```

**Observation 2: Reverse the problem**
```
Instead of finding surrounded regions:
  1. Find all 'O's connected to border
  2. These are NOT surrounded (safe)
  3. All other 'O's are surrounded (capture them)
```

**Observation 3: Border DFS/BFS**
```
Multi-source traversal:
  Start from all border 'O' cells
  DFS/BFS to mark all connected 'O's as safe
  
Remaining 'O's are surrounded → flip to 'X'
```

**Observation 4: Three-phase algorithm**
```
Phase 1: Mark safe regions
  - DFS/BFS from all border 'O' cells
  - Mark connected 'O's as 'S' (safe)

Phase 2: Capture surrounded regions
  - Iterate through board
  - Flip all 'O' to 'X' (these are surrounded)

Phase 3: Restore safe regions
  - Flip all 'S' back to 'O'
```

**Observation 5: In-place modification**
```
Use board itself for marking:
  'O' → 'S' (safe, connected to border)
  'O' → 'X' (captured, not connected to border)
  'S' → 'O' (restore safe cells)

No extra space needed beyond recursion stack!
```

**Formula:**
```
# Phase 1: Mark safe O's from borders
for c in 0..n-1:
  if board[0][c] == 'O':
    DFS(0, c)  # top border
  if board[m-1][c] == 'O':
    DFS(m-1, c)  # bottom border

for r in 1..m-2:
  if board[r][0] == 'O':
    DFS(r, 0)  # left border
  if board[r][n-1] == 'O':
    DFS(r, n-1)  # right border

DFS(r, c):
  if out_of_bounds or board[r][c] != 'O':
    return
  board[r][c] = 'S'  # mark safe
  DFS(r-1, c); DFS(r+1, c)
  DFS(r, c-1); DFS(r, c+1)

# Phase 2: Capture surrounded O's
for r in 0..m-1:
  for c in 0..n-1:
    if board[r][c] == 'O':
      board[r][c] = 'X'

# Phase 3: Restore safe O's
for r in 0..m-1:
  for c in 0..n-1:
    if board[r][c] == 'S':
      board[r][c] = 'O'
```

### State Space Structure

**Type:** Boundary-based DFS/BFS on grid.

**Structure:**
- Multi-source DFS from border 'O' cells
- Three-phase in-place modification
- No extra space except recursion stack

**Cardinality:**
- m × n cells
- Each cell visited at most once
- Time: O(m × n)
- Space: O(m × n) for recursion stack in worst case (all O's)

**Key Property:** Border-connected regions cannot be captured.

### Example Computation

Board:
```
X X X X
X O O X
X X O X
X O X X
```

```
Phase 1: Mark safe O's from borders
  Borders:
    Top row (r=0): all X
    Bottom row (r=3): O at (3,1)
    Left col (c=0): all X
    Right col (c=3): all X
  
  DFS from (3,1):
    (3,1) = 'O' → mark 'S'
    Check neighbors:
      (2,1): 'X', skip
      (4,1): out of bounds
      (3,0): 'X', skip
      (3,2): 'X', skip
    
  Board after Phase 1:
  X X X X
  X O O X
  X X O X
  X S X X

Phase 2: Capture all remaining 'O'
  (1,1) = 'O' → 'X'
  (1,2) = 'O' → 'X'
  (2,2) = 'O' → 'X'
  
  Board after Phase 2:
  X X X X
  X X X X
  X X X X
  X S X X

Phase 3: Restore safe 'S' to 'O'
  (3,1) = 'S' → 'O'
  
  Final board:
  X X X X
  X X X X
  X X X X
  X O X X  ✓
```

### Generation Pattern

**Brute force:**
```csharp
// For each 'O', DFS to check if path to border exists
// Time: O(m² × n²)
```

---

## Step 2: Brute Force

For each 'O', check if connected to border: O(m² × n²) - too slow.

---

## Step 3: Optimization Ideas

### Key Insights
1. **Reverse approach:** Find safe regions first
2. **Border traversal:** Start from edges
3. **Three phases:** Mark, capture, restore
4. **In-place:** Use board for marking
5. **O(m×n):** Single pass per phase

---

## Step 4: Optimal Solution (DFS)

```csharp
public class Solution {
    private char[][] board;
    private int rows, cols;
    
    public void Solve(char[][] board) {
        if (board == null || board.Length == 0) {
            return;
        }
        
        this.board = board;
        this.rows = board.Length;
        this.cols = board[0].Length;
        
        // Phase 1: Mark safe O's from borders
        // Top and bottom borders
        for (int c = 0; c < cols; c++) {
            if (board[0][c] == 'O') {
                DFS(0, c);
            }
            if (board[rows - 1][c] == 'O') {
                DFS(rows - 1, c);
            }
        }
        
        // Left and right borders
        for (int r = 1; r < rows - 1; r++) {
            if (board[r][0] == 'O') {
                DFS(r, 0);
            }
            if (board[r][cols - 1] == 'O') {
                DFS(r, cols - 1);
            }
        }
        
        // Phase 2: Capture surrounded O's and restore safe O's
        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) {
                if (board[r][c] == 'O') {
                    board[r][c] = 'X';  // Capture surrounded
                } else if (board[r][c] == 'S') {
                    board[r][c] = 'O';  // Restore safe
                }
            }
        }
    }
    
    private void DFS(int r, int c) {
        // Check bounds and if cell is 'O'
        if (r < 0 || r >= rows || c < 0 || c >= cols || board[r][c] != 'O') {
            return;
        }
        
        // Mark as safe
        board[r][c] = 'S';
        
        // Explore all four directions
        DFS(r - 1, c);  // up
        DFS(r + 1, c);  // down
        DFS(r, c - 1);  // left
        DFS(r, c + 1);  // right
    }
}
```

**Complexity:**
- Time: O(m × n)
  - Phase 1: DFS visits each cell at most once
  - Phase 2: Single iteration through board
- Space: O(m × n) - recursion stack in worst case

---

## Alternative: BFS

```csharp
public void Solve(char[][] board) {
    if (board == null || board.Length == 0) return;
    
    int rows = board.Length;
    int cols = board[0].Length;
    var queue = new Queue<(int r, int c)>();
    
    // Collect all border O's
    for (int c = 0; c < cols; c++) {
        if (board[0][c] == 'O') queue.Enqueue((0, c));
        if (board[rows - 1][c] == 'O') queue.Enqueue((rows - 1, c));
    }
    for (int r = 1; r < rows - 1; r++) {
        if (board[r][0] == 'O') queue.Enqueue((r, 0));
        if (board[r][cols - 1] == 'O') queue.Enqueue((r, cols - 1));
    }
    
    // BFS to mark safe O's
    int[][] directions = { 
        new[] {-1, 0}, new[] {1, 0}, 
        new[] {0, -1}, new[] {0, 1} 
    };
    
    while (queue.Count > 0) {
        var (r, c) = queue.Dequeue();
        
        if (board[r][c] != 'O') continue;
        
        board[r][c] = 'S';
        
        foreach (var dir in directions) {
            int nr = r + dir[0];
            int nc = c + dir[1];
            
            if (nr >= 0 && nr < rows && nc >= 0 && nc < cols && 
                board[nr][nc] == 'O') {
                queue.Enqueue((nr, nc));
            }
        }
    }
    
    // Capture and restore
    for (int r = 0; r < rows; r++) {
        for (int c = 0; c < cols; c++) {
            if (board[r][c] == 'O') {
                board[r][c] = 'X';
            } else if (board[r][c] == 'S') {
                board[r][c] = 'O';
            }
        }
    }
}
```

**Complexity:** Same O(m × n) time and space.

---

## Edge Cases

1. **All X:** No changes needed
2. **All O:** All safe (all touch border)
3. **Single cell:** Always safe
4. **Border has no O:** All interior O's captured
5. **Completely surrounded region:** Gets captured

---

## Visualization

```
Original:
X X X X
X O O X
X X O X
X O X X

After marking safe from border:
X X X X
X O O X  ← Not marked (interior)
X X O X  ← Not marked (interior)
X S X X  ← Marked safe (border)

After capture:
X X X X
X X X X  ← Captured
X X X X  ← Captured
X S X X

After restore:
X X X X
X X X X
X X X X
X O X X  ← Restored
```

---

## Related Problems

1. **Number of Islands (LeetCode #200)** - Basic connected components
2. **Pacific Atlantic Water Flow (LeetCode #417)** - Multi-source border DFS
3. **Number of Enclaves (LeetCode #1020)** - Count land not touching border
4. **Number of Closed Islands (LeetCode #1254)** - Islands not touching border

---

## Pattern Recognition

**Problem Asks For:**
- Regions not connected to boundary
- Capture interior groups
- Modify based on border connectivity

**This Suggests:**
- Reverse the problem (find safe first)
- Multi-source DFS/BFS from borders
- Three-phase approach
- → **Boundary-Based Region Marking**

**Key Indicators:**
- "Surrounded" or "enclosed"
- Border vs interior distinction
- Capture or modify regions

---

## Tags

`#graph` `#dfs` `#bfs` `#grid` `#boundary` `#matrix` `#in-place` `#reverse-thinking`
