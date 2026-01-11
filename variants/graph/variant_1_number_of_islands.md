# Number of Islands

**Difficulty:** Medium  
**LeetCode Problem:** #200  
**Tags:** `Graph`, `DFS`, `BFS`, `Grid`, `Connected Components`, `Union Find`

---

## Problem Statement

Given an `m x n` 2D binary grid `grid` which represents a map of `'1'`s (land) and `'0'`s (water), return the number of islands.

An **island** is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

**Constraints:**
- `m == grid.length`
- `n == grid[i].length`
- `1 <= m, n <= 300`
- `grid[i][j]` is `'0'` or `'1'`.

**Example 1:**
```
Input: grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
Output: 1
```

**Example 2:**
```
Input: grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
Output: 3
```

---

## Step 1: State Space

### Problem Visualization

Grid:
```
1 1 1 1 0
1 1 0 1 0
1 1 0 0 0
0 0 0 0 0
```

Connected components (islands):
```
Island 1: All connected 1's form one island

A A A A 0
A A 0 B 0
A A 0 0 0
0 0 0 0 0

Actually, let me retrace:
Starting at (0,0), DFS/BFS explores all connected 1's:
  (0,0) → (0,1) → (0,2) → (0,3)
        ↓
  (1,0) → (1,1)
        ↓
  (2,0) → (2,1)

Also (1,3) is land but not connected to main group
Wait, checking again:

Row 0: 1 1 1 1 0
Row 1: 1 1 0 1 0
Row 2: 1 1 0 0 0
Row 3: 0 0 0 0 0

From (0,0): Can reach (0,1), (1,0)
From (0,1): Can reach (0,0), (0,2), (1,1)
From (0,2): Can reach (0,1), (0,3)
From (0,3): Can reach (0,2), (1,3)? No, (1,3) is separated by (0,4)=0

Wait, (0,3) connects to (1,3)? Let me check:
  (0,3) is '1'
  Below it: (1,3) is '1'
  So they connect vertically!

So from (0,0), we can reach:
  (0,0)→(0,1)→(0,2)→(0,3)
                         ↓
                      (1,3)
  (0,0)→(1,0)→(1,1)→(2,0)→(2,1)

All connected as one island! Count = 1 ✓

Example 2:
1 1 0 0 0
1 1 0 0 0
0 0 1 0 0
0 0 0 1 1

Island 1: (0,0), (0,1), (1,0), (1,1) - top-left group
Island 2: (2,2) - single cell
Island 3: (3,3), (3,4) - bottom-right group
Count = 3 ✓
```

**DFS Approach:**
```
For each cell in grid:
  If cell is '1' (unvisited land):
    island_count++
    DFS from this cell to mark entire island as visited
    
DFS(row, col):
  If out of bounds or cell is '0' or visited:
    return
  Mark cell as visited (or change to '0')
  DFS(row-1, col)  # up
  DFS(row+1, col)  # down
  DFS(row, col-1)  # left
  DFS(row, col+1)  # right
```

### Core Question

**How many connected components of land cells exist in the grid?**

Each island is a connected component. DFS/BFS finds all cells in one component.

### Deriving from First Principles

**Observation 1: Graph representation**
```
Grid is an implicit graph:
- Nodes: cells with value '1'
- Edges: adjacent (horizontally/vertically) land cells

Finding islands = finding connected components
```

**Observation 2: DFS to explore component**
```
Starting from any unvisited land cell:
- DFS explores all reachable land cells (one island)
- Mark them as visited
- This is one connected component
```

**Observation 3: Count components**
```
for each cell:
  if cell is unvisited land:
    component_count++
    DFS/BFS to mark entire component
    
return component_count
```

**Observation 4: Marking visited**
```
Options:
1. Modify grid: change '1' to '0' (visited)
2. Separate visited set: track (row, col)
3. Separate boolean matrix

Modifying grid is space-efficient if allowed.
```

**Observation 5: Four directions**
```
From (r, c), explore:
  (r-1, c)  # up
  (r+1, c)  # down
  (r, c-1)  # left
  (r, c+1)  # right

Check bounds and whether cell is land.
```

**Formula (DFS):**
```
count = 0
for r in 0..rows:
  for c in 0..cols:
    if grid[r][c] == '1':
      count++
      DFS(r, c)

DFS(r, c):
  if out_of_bounds(r, c) or grid[r][c] == '0':
    return
  grid[r][c] = '0'  # mark visited
  DFS(r-1, c)
  DFS(r+1, c)
  DFS(r, c-1)
  DFS(r, c+1)

return count
```

### State Space Structure

**Type:** Connected components in implicit grid graph.

**Structure:**
- Grid as adjacency representation
- DFS/BFS for traversal
- Count number of DFS/BFS calls

**Cardinality:**
- Each cell visited once: O(m × n)
- Each cell has at most 4 neighbors
- DFS depth: O(m × n) worst case (snake-like island)
- Space: O(m × n) for recursion stack or queue

**Key Property:** DFS/BFS explores entire connected component in one traversal.

### Example Computation

Grid:
```
1 1 0 0 0
1 1 0 0 0
0 0 1 0 0
0 0 0 1 1
```

```
Iteration through grid:

(0,0): '1' → island_count = 1, DFS from (0,0)
  DFS(0,0): mark '0', explore:
    DFS(0,1): mark '0', explore:
      DFS(1,1): mark '0', explore:
        DFS(1,0): mark '0', all neighbors visited
        
After first DFS:
Grid becomes:
0 0 0 0 0
0 0 0 0 0
0 0 1 0 0
0 0 0 1 1

(0,1) to (1,1): Already '0', skip

(2,2): '1' → island_count = 2, DFS from (2,2)
  DFS(2,2): mark '0', no unvisited neighbors

Grid becomes:
0 0 0 0 0
0 0 0 0 0
0 0 0 0 0
0 0 0 1 1

(3,3): '1' → island_count = 3, DFS from (3,3)
  DFS(3,3): mark '0', explore:
    DFS(3,4): mark '0', no unvisited neighbors

Final: island_count = 3 ✓
```

### Generation Pattern

**Brute force:**
```csharp
// For each cell, explore if it's part of an island
// Already optimal with DFS/BFS
// Time: O(m × n)
```

---

## Step 2: Brute Force (Naive)

Actually, DFS/BFS is already the optimal approach. A "more brute" approach would be:
- For each pair of cells, check if connected
- Build explicit graph
- Find components

But this is worse: O((m×n)²)

The DFS approach is the standard solution.

---

## Step 3: Optimization Ideas

### Key Insights
1. **DFS/BFS:** Explore entire component in one pass
2. **Mark visited:** Avoid revisiting cells
3. **Implicit graph:** No need to build explicit adjacency list
4. **Four directions:** Standard grid traversal

---

## Step 4: Optimal Solution (DFS)

```csharp
public class Solution {
    public int NumIslands(char[][] grid) {
        if (grid == null || grid.Length == 0) {
            return 0;
        }
        
        int rows = grid.Length;
        int cols = grid[0].Length;
        int count = 0;
        
        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) {
                if (grid[r][c] == '1') {
                    count++;
                    DFS(grid, r, c);
                }
            }
        }
        
        return count;
    }
    
    private void DFS(char[][] grid, int r, int c) {
        int rows = grid.Length;
        int cols = grid[0].Length;
        
        // Base case: out of bounds or water/visited
        if (r < 0 || r >= rows || c < 0 || c >= cols || grid[r][c] == '0') {
            return;
        }
        
        // Mark as visited
        grid[r][c] = '0';
        
        // Explore four directions
        DFS(grid, r - 1, c);  // up
        DFS(grid, r + 1, c);  // down
        DFS(grid, r, c - 1);  // left
        DFS(grid, r, c + 1);  // right
    }
}
```

**Complexity:**
- Time: O(m × n) - visit each cell once
- Space: O(m × n) - recursion stack in worst case

---

## Alternative: BFS

```csharp
public int NumIslands(char[][] grid) {
    if (grid == null || grid.Length == 0) {
        return 0;
    }
    
    int rows = grid.Length;
    int cols = grid[0].Length;
    int count = 0;
    
    for (int r = 0; r < rows; r++) {
        for (int c = 0; c < cols; c++) {
            if (grid[r][c] == '1') {
                count++;
                BFS(grid, r, c);
            }
        }
    }
    
    return count;
}

private void BFS(char[][] grid, int startR, int startC) {
    int rows = grid.Length;
    int cols = grid[0].Length;
    var queue = new Queue<(int r, int c)>();
    
    queue.Enqueue((startR, startC));
    grid[startR][startC] = '0';  // mark visited
    
    int[][] directions = { new[] {-1, 0}, new[] {1, 0}, new[] {0, -1}, new[] {0, 1} };
    
    while (queue.Count > 0) {
        var (r, c) = queue.Dequeue();
        
        foreach (var dir in directions) {
            int nr = r + dir[0];
            int nc = c + dir[1];
            
            if (nr >= 0 && nr < rows && nc >= 0 && nc < cols && grid[nr][nc] == '1') {
                queue.Enqueue((nr, nc));
                grid[nr][nc] = '0';  // mark visited
            }
        }
    }
}
```

**Complexity:**
- Time: O(m × n)
- Space: O(min(m, n)) - queue size for BFS

**BFS vs DFS:**
- BFS: Queue, iterative, better space in some cases
- DFS: Stack (recursion), simpler code, natural for this problem

---

## Alternative: Union Find

```csharp
public int NumIslands(char[][] grid) {
    if (grid == null || grid.Length == 0) return 0;
    
    int rows = grid.Length;
    int cols = grid[0].Length;
    var uf = new UnionFind(rows * cols);
    int waterCells = 0;
    
    for (int r = 0; r < rows; r++) {
        for (int c = 0; c < cols; c++) {
            if (grid[r][c] == '0') {
                waterCells++;
            } else {
                // Connect to right neighbor
                if (c + 1 < cols && grid[r][c + 1] == '1') {
                    uf.Union(r * cols + c, r * cols + c + 1);
                }
                // Connect to down neighbor
                if (r + 1 < rows && grid[r + 1][c] == '1') {
                    uf.Union(r * cols + c, (r + 1) * cols + c);
                }
            }
        }
    }
    
    return uf.Count - waterCells;
}

class UnionFind {
    private int[] parent;
    public int Count { get; private set; }
    
    public UnionFind(int n) {
        parent = new int[n];
        Count = n;
        for (int i = 0; i < n; i++) {
            parent[i] = i;
        }
    }
    
    public int Find(int x) {
        if (parent[x] != x) {
            parent[x] = Find(parent[x]);
        }
        return parent[x];
    }
    
    public void Union(int x, int y) {
        int rootX = Find(x);
        int rootY = Find(y);
        if (rootX != rootY) {
            parent[rootX] = rootY;
            Count--;
        }
    }
}
```

**Complexity:**
- Time: O(m × n × α(m×n)) ≈ O(m × n)
- Space: O(m × n) for union-find structure

---

## Edge Cases

1. **Empty grid:** `grid = []` → `0`
2. **All water:** `grid = [["0","0"],["0","0"]]` → `0`
3. **All land:** `grid = [["1","1"],["1","1"]]` → `1`
4. **Single cell:** `grid = [["1"]]` → `1`
5. **Snake pattern:** Long connected island

---

## Visualization

```
Grid:
1 1 0
0 1 0
0 0 1

Process:
(0,0): Start island 1
  DFS → (0,1) → (1,1) → all marked
  
After first island:
0 0 0
0 0 0
0 0 1

(2,2): Start island 2
  DFS → marks (2,2)
  
Final count: 2
```

---

## Related Problems

1. **Max Area of Island (LeetCode #695)** - Find largest island
2. **Surrounded Regions (LeetCode #130)** - Boundary-based DFS
3. **Number of Closed Islands (LeetCode #1254)** - Exclude boundary islands
4. **Number of Distinct Islands (LeetCode #694)** - Shape-based counting
5. **Pacific Atlantic Water Flow (LeetCode #417)** - Multi-source traversal

---

## Pattern Recognition

**Problem Asks For:**
- Count connected components
- Grid-based connectivity
- Explore all reachable cells

**This Suggests:**
- DFS/BFS traversal
- Mark visited to avoid cycles
- Count number of traversals
- → **Connected Components in Grid**

**Key Indicators:**
- "Connected" or "adjacent"
- Grid with binary values
- Count groups/regions

---

## Tags

`#graph` `#dfs` `#bfs` `#grid` `#connected-components` `#union-find` `#traversal`
