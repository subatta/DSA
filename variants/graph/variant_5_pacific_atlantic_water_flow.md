# Pacific Atlantic Water Flow

**Difficulty:** Medium  
**LeetCode Problem:** #417  
**Tags:** `Graph`, `DFS`, `BFS`, `Multi-Source`, `Grid`, `Matrix`

---

## Problem Statement

There is an `m x n` rectangular island that borders both the **Pacific Ocean** and **Atlantic Ocean**. The **Pacific Ocean** touches the island's left and top edges, and the **Atlantic Ocean** touches the island's right and bottom edges.

The island is partitioned into a grid of square cells. You are given an `m x n` integer matrix `heights` where `heights[r][c]` represents the **height above sea level** of the cell at coordinate `(r, c)`.

The island receives a lot of rain, and the rain water can flow to neighboring cells directly north, south, east, and west if the neighboring cell's height is **less than or equal to** the current cell's height. Water can flow from any cell adjacent to an ocean into the ocean.

Return a **2D list** of grid coordinates `result` where `result[i] = [ri, ci]` denotes that rain water can flow from cell `(ri, ci)` to **both** the Pacific and Atlantic oceans.

**Constraints:**
- `m == heights.length`
- `n == heights[r].length`
- `1 <= m, n <= 200`
- `0 <= heights[r][c] <= 10^5`

**Example 1:**
```
Input: heights = [
  [1,2,2,3,5],
  [3,2,3,4,4],
  [2,4,5,3,1],
  [6,7,1,4,5],
  [5,1,1,2,4]
]
Output: [[0,4],[1,3],[1,4],[2,2],[3,0],[3,1],[4,0]]

Explanation:
Pacific Ocean:    Atlantic Ocean:
    ~  ~  ~  ~  ~      ~  ~  ~  ~  ~
  ~ 1  2  2  3  5  ~  ~ 1  2  2  3  5 ~
  ~ 3  2  3  4  4  ~  ~ 3  2  3  4  4 ~
  ~ 2  4  5  3  1  ~  ~ 2  4  5  3  1 ~
  ~ 6  7  1  4  5  ~  ~ 6  7  1  4  5 ~
  ~ 5  1  1  2  4  ~  ~ 5  1  1  2  4 ~
    ~  ~  ~  ~  ~      ~  ~  ~  ~  ~
```

**Example 2:**
```
Input: heights = [[1]]
Output: [[0,0]]
Explanation: Water can flow from the only cell to both oceans.
```

---

## Step 1: State Space

### Problem Visualization

Grid:
```
    P  P  P  P  P
  P 1  2  2  3  5 A
  P 3  2  3  4  4 A
  P 2  4  5  3  1 A
  P 6  7  1  4  5 A
  P 5  1  1  2  4 A
    A  A  A  A  A

P = Pacific (top and left borders)
A = Atlantic (bottom and right borders)
```

**Key insight: Reverse the flow!**

Instead of checking from each cell whether it can reach both oceans:
- Start from ocean borders
- Flow **upward** (to cells with height >= current)
- Mark which cells can reach each ocean

```
Pacific reachable (DFS from top/left borders):
  T  T  T  T  T
  T  T  T  T  T
  T  T  T  T  F
  T  T  F  F  F
  T  F  F  F  F

Atlantic reachable (DFS from bottom/right borders):
  F  F  F  F  T
  F  F  F  T  T
  F  T  T  T  T
  T  T  T  T  T
  T  T  T  T  T

Cells reachable by BOTH:
  [0,4], [1,3], [1,4], [2,2], [3,0], [3,1], [4,0]
```

### Core Question

**Which cells can flow water to both the Pacific and Atlantic oceans?**

Reverse approach: From oceans, find which cells are reachable flowing uphill.

### Deriving from First Principles

**Observation 1: Naive approach is expensive**
```
For each cell (m×n cells):
  DFS/BFS to check if can reach Pacific: O(m×n)
  DFS/BFS to check if can reach Atlantic: O(m×n)
  
Total: O(m²×n²) - too slow
```

**Observation 2: Reverse the flow**
```
Instead of: cell → ocean (downhill)
Think: ocean → cell (uphill)

Multi-source DFS/BFS:
  From all Pacific border cells
  From all Atlantic border cells
  
Mark which cells each ocean can reach
Intersection = cells reachable by both
```

**Observation 3: Flow rules reversed**
```
Original: water flows from high to low (>=)
  Can move to neighbor if neighbor_height <= current_height

Reversed: flow from ocean upward
  Can move to neighbor if neighbor_height >= current_height
```

**Observation 4: Two DFS/BFS passes**
```
Pass 1: DFS from all Pacific border cells
  Mark pacific_reachable set

Pass 2: DFS from all Atlantic border cells
  Mark atlantic_reachable set

Result: intersection of both sets
```

**Observation 5: Border initialization**
```
Pacific borders:
  - Top row: (0, c) for all c
  - Left column: (r, 0) for all r

Atlantic borders:
  - Bottom row: (m-1, c) for all c
  - Right column: (r, n-1) for all r
```

**Formula:**
```
pacific = set()
atlantic = set()

# DFS from Pacific borders
for c in 0..n-1:
  DFS(0, c, pacific)  // top row
for r in 1..m-1:
  DFS(r, 0, pacific)  // left column (skip [0,0])

# DFS from Atlantic borders
for c in 0..n-1:
  DFS(m-1, c, atlantic)  // bottom row
for r in 0..m-2:
  DFS(r, n-1, atlantic)  // right column (skip [m-1,n-1])

# Find intersection
result = pacific ∩ atlantic

DFS(r, c, reachable):
  if (r,c) in reachable:
    return
  reachable.add((r,c))
  
  for each neighbor (nr, nc):
    if in_bounds and heights[nr][nc] >= heights[r][c]:
      DFS(nr, nc, reachable)
```

### State Space Structure

**Type:** Multi-source DFS/BFS on grid.

**Structure:**
- Two DFS/BFS passes (one per ocean)
- Sets to track reachability
- Reverse flow (uphill instead of downhill)

**Cardinality:**
- m × n cells
- Each cell visited at most twice (once per ocean)
- Time: O(m × n)
- Space: O(m × n) for reachability sets

**Key Property:** Multi-source traversal more efficient than per-cell traversal.

### Example Computation

Grid (simplified 3×3):
```
1  2  3
4  5  6
7  8  9
```

```
Pacific borders: (0,0), (0,1), (0,2), (1,0), (2,0)
Atlantic borders: (2,0), (2,1), (2,2), (0,2), (1,2)

Pacific DFS:
  Start (0,0) height=1:
    Can reach (0,1)? height=2 >= 1 ✓
    Can reach (1,0)? height=4 >= 1 ✓
  Continue from (0,1) height=2:
    Can reach (0,2)? height=3 >= 2 ✓
    Can reach (1,1)? height=5 >= 2 ✓
  Continue...
  
  Pacific reachable: All cells (simplified case)

Atlantic DFS:
  Start (2,2) height=9:
    Neighbors all lower, can't flow uphill
  Start (2,1) height=8:
    Can reach (2,0)? height=7 < 8 ✗
    Can reach (1,1)? height=5 < 8 ✗
  Start (1,2) height=6:
    Can reach (1,1)? height=5 < 6 ✗
    Can reach (0,2)? height=3 < 6 ✗
  
  Atlantic reachable: Border cells only
  
  Intersection: Border cells
```

### Generation Pattern

**Brute force:**
```csharp
// For each cell, DFS to check Pacific and Atlantic
// Time: O(m²×n²)
```

---

## Step 2: Brute Force

For each cell, run DFS to both oceans: O(m²×n²) - too slow.

---

## Step 3: Optimization Ideas

### Key Insights
1. **Reverse flow:** Ocean → cells instead of cells → ocean
2. **Multi-source:** Start from all border cells
3. **Two passes:** One per ocean
4. **Intersection:** Cells reachable by both
5. **O(m×n):** Each cell visited once per ocean

---

## Step 4: Optimal Solution (DFS)

```csharp
public class Solution {
    private int[][] heights;
    private int rows, cols;
    
    public IList<IList<int>> PacificAtlantic(int[][] heights) {
        this.heights = heights;
        this.rows = heights.Length;
        this.cols = heights[0].Length;
        
        var pacific = new HashSet<(int, int)>();
        var atlantic = new HashSet<(int, int)>();
        
        // DFS from Pacific borders (top and left)
        for (int c = 0; c < cols; c++) {
            DFS(0, c, pacific);  // Top row
        }
        for (int r = 1; r < rows; r++) {
            DFS(r, 0, pacific);  // Left column
        }
        
        // DFS from Atlantic borders (bottom and right)
        for (int c = 0; c < cols; c++) {
            DFS(rows - 1, c, atlantic);  // Bottom row
        }
        for (int r = 0; r < rows - 1; r++) {
            DFS(r, cols - 1, atlantic);  // Right column
        }
        
        // Find intersection
        var result = new List<IList<int>>();
        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) {
                if (pacific.Contains((r, c)) && atlantic.Contains((r, c))) {
                    result.Add(new List<int> { r, c });
                }
            }
        }
        
        return result;
    }
    
    private void DFS(int r, int c, HashSet<(int, int)> reachable) {
        // Already visited
        if (reachable.Contains((r, c))) {
            return;
        }
        
        reachable.Add((r, c));
        
        // Explore neighbors (flow uphill)
        int[][] directions = { 
            new[] {-1, 0}, new[] {1, 0}, 
            new[] {0, -1}, new[] {0, 1} 
        };
        
        foreach (var dir in directions) {
            int nr = r + dir[0];
            int nc = c + dir[1];
            
            // Check bounds and if we can flow uphill
            if (nr >= 0 && nr < rows && nc >= 0 && nc < cols &&
                heights[nr][nc] >= heights[r][c]) {
                DFS(nr, nc, reachable);
            }
        }
    }
}
```

**Complexity:**
- Time: O(m × n)
  - Each cell visited at most once per ocean
  - Two passes: 2 × O(m × n)
- Space: O(m × n)
  - Two sets: 2 × O(m × n)
  - Recursion stack: O(m × n)

---

## Alternative: BFS

```csharp
public IList<IList<int>> PacificAtlantic(int[][] heights) {
    int rows = heights.Length;
    int cols = heights[0].Length;
    
    var pacific = new HashSet<(int, int)>();
    var atlantic = new HashSet<(int, int)>();
    
    var pacificQueue = new Queue<(int r, int c)>();
    var atlanticQueue = new Queue<(int r, int c)>();
    
    // Initialize queues with border cells
    for (int c = 0; c < cols; c++) {
        pacificQueue.Enqueue((0, c));
        pacific.Add((0, c));
        atlanticQueue.Enqueue((rows - 1, c));
        atlantic.Add((rows - 1, c));
    }
    for (int r = 1; r < rows; r++) {
        pacificQueue.Enqueue((r, 0));
        pacific.Add((r, 0));
    }
    for (int r = 0; r < rows - 1; r++) {
        atlanticQueue.Enqueue((r, cols - 1));
        atlantic.Add((r, cols - 1));
    }
    
    // BFS for Pacific
    BFS(heights, pacificQueue, pacific);
    
    // BFS for Atlantic
    BFS(heights, atlanticQueue, atlantic);
    
    // Find intersection
    var result = new List<IList<int>>();
    for (int r = 0; r < rows; r++) {
        for (int c = 0; c < cols; c++) {
            if (pacific.Contains((r, c)) && atlantic.Contains((r, c))) {
                result.Add(new List<int> { r, c });
            }
        }
    }
    
    return result;
}

private void BFS(int[][] heights, Queue<(int r, int c)> queue, 
                 HashSet<(int, int)> reachable) {
    int rows = heights.Length;
    int cols = heights[0].Length;
    int[][] directions = { 
        new[] {-1, 0}, new[] {1, 0}, 
        new[] {0, -1}, new[] {0, 1} 
    };
    
    while (queue.Count > 0) {
        var (r, c) = queue.Dequeue();
        
        foreach (var dir in directions) {
            int nr = r + dir[0];
            int nc = c + dir[1];
            
            if (nr >= 0 && nr < rows && nc >= 0 && nc < cols &&
                !reachable.Contains((nr, nc)) &&
                heights[nr][nc] >= heights[r][c]) {
                reachable.Add((nr, nc));
                queue.Enqueue((nr, nc));
            }
        }
    }
}
```

**Complexity:** Same O(m × n) time and space.

---

## Edge Cases

1. **Single cell:** `[[1]]` → `[[0,0]]` (touches both oceans)
2. **All same height:** All cells can reach both
3. **Descending from corner:** Only border cells
4. **Large values:** Heights up to 10^5

---

## Visualization

```
Grid:
  P  P  P
P 1  2  3 A
P 4  5  6 A
P 7  8  9 A
  A  A  A

Pacific DFS (uphill flow):
  Start from top/left borders
  T  T  T
  T  T  T
  T  T  T
  (All reachable in this case)

Atlantic DFS (uphill flow):
  Start from bottom/right borders
  F  F  T
  F  T  T
  T  T  T
  (Lower-left has lower heights)

Intersection:
  F  F  T
  F  T  T
  T  T  T
  Result: [[0,2],[1,1],[1,2],[2,0],[2,1],[2,2]]
```

---

## Related Problems

1. **Number of Islands (LeetCode #200)** - Basic DFS/BFS
2. **Surrounded Regions (LeetCode #130)** - Border-based DFS
3. **Max Area of Island (LeetCode #695)** - DFS with area calculation
4. **01 Matrix (LeetCode #542)** - Multi-source BFS

---

## Pattern Recognition

**Problem Asks For:**
- Cells satisfying condition relative to multiple sources
- Flow or reachability from borders
- Intersection of multiple reachability sets

**This Suggests:**
- Multi-source DFS/BFS
- Reverse the problem direction
- Start from boundaries
- → **Multi-Source Border Traversal**

**Key Indicators:**
- "Flow to multiple destinations"
- Border-based conditions
- Intersection of reachability

---

## Tags

`#graph` `#dfs` `#bfs` `#multi-source` `#grid` `#matrix` `#reverse-thinking`
