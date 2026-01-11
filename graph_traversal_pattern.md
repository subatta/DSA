# Pattern: Graph Traversal (BFS / DFS)

## Pattern Description
**Graph Traversal** is used to explore nodes and edges of a graph systematically.

**What is it?**
- **BFS (Breadth-First Search):** Level-by-level traversal using a queue
  - Explores all neighbors before moving to next level
  - Guarantees shortest path in unweighted graphs
  - Uses O(V) space for queue
- **DFS (Depth-First Search):** Path-by-path traversal using recursion or stack
  - Explores as deep as possible before backtracking
  - Uses O(h) space for recursion stack (h = height)
  - Natural choice for recursive problems

**What abstract problem does it solve?**
- Explore all reachable nodes from a starting point
- Find shortest paths (BFS) or any path (DFS)
- Detect cycles, connected components
- Topological ordering of DAGs
- Level-order processing

**Real-world / interview variants (simplest → harder):**
1. **Grid Traversal (Number of Islands)** - 2D matrix BFS/DFS
2. **Tree Traversals** - Preorder, inorder, postorder, level-order
3. **Shortest Path in Unweighted Graph** - BFS shortest path
4. **Connected Components** - Count separate components
5. **Cycle Detection** - Detect cycles in directed/undirected graphs
6. **Topological Sort** - DAG ordering with dependencies
7. **Word Ladder** - BFS transformation sequence
8. **Maze Solving** - BFS/DFS for minimum steps

---

## Canonical Skeleton (DFS)

```csharp
void DFS(Node node, HashSet<Node> visited)
{
    if (visited.Contains(node)) return;
    visited.Add(node);
    foreach (var neighbor in node.Neighbors)
    {
        DFS(neighbor, visited);
    }
}
```

## Canonical Skeleton (BFS)

```csharp
void BFS(Node start)
{
    var visited = new HashSet<Node>();
    var queue = new Queue<Node>();
    queue.Enqueue(start);
    visited.Add(start);
    while (queue.Count > 0)
    {
        var node = queue.Dequeue();
        foreach (var neighbor in node.Neighbors)
        {
            if (!visited.Contains(neighbor))
            {
                visited.Add(neighbor);
                queue.Enqueue(neighbor);
            }
        }
    }
}
```


<details>
<summary><b>Variant #1: Grid Traversal (Number of Islands)</b></summary>

## Variant #1: Grid Traversal (Number of Islands)

### Input/Output:
- Input: 2D grid of '1's (land) and '0's (water)
```
11000
11000
00100
00011
```
- Output: `3` (number of connected islands)

### Full State Space:
All possible ways to explore the grid.
```
For m×n grid: all cell visit orders
Exponential paths through cells
Without visited tracking: infinite loops possible
```

### Expected/Pruned State Space:
Explore only connected land cells, mark visited.
```
DFS/BFS from each unvisited '1'
Mark visited to avoid re-exploration
Each cell visited exactly once
```

### State Space Leading to Output:
Visited set prunes repeated exploration, count tracks separate islands.

### Brute Force Canonical Skeleton:
```csharp
int CountIslandsBruteForce(char[][] grid)
{
    int m = grid.Length, n = grid[0].Length;
    bool[,] visited = new bool[m, n];
    int count = 0;
    
    void DFS(int r, int c)
    {
        // Boundary checks
        if (r < 0 || r >= m || c < 0 || c >= n)
            return;
        
        // Water or already visited
        if (grid[r][c] == '0' || visited[r, c])
            return;
        
        // Mark visited
        visited[r, c] = true;
        
        // Explore 4 directions
        DFS(r + 1, c);
        DFS(r - 1, c);
        DFS(r, c + 1);
        DFS(r, c - 1);
    }
    
    // Try starting DFS from each cell
    for (int i = 0; i < m; i++)
    {
        for (int j = 0; j < n; j++)
        {
            if (grid[i][j] == '1' && !visited[i, j])
            {
                DFS(i, j);
                count++;
            }
        }
    }
    
    return count;
}
```

### Brute Force Code Walkthrough / Variable Trace:
For grid:
```
11000
11000
00100
00011
```

| Step | Cell (i,j) | grid[i][j] | visited | action | count |
|------|------------|------------|---------|--------|-------|
| 1 | (0,0) | '1' | no | DFS start | 1 |
| 2 | (0,1) | '1' | no | DFS continue | 1 |
| 3 | (1,0) | '1' | no | DFS continue | 1 |
| 4 | (1,1) | '1' | no | DFS continue | 1 |
| ... | (0,2)-(1,4) | '0' | - | skip | 1 |
| 5 | (2,2) | '1' | no | DFS start | 2 |
| ... | (2,3)-(2,4) | '0' | - | skip | 2 |
| 6 | (3,3) | '1' | no | DFS start | 3 |
| 7 | (3,4) | '1' | no | DFS continue | 3 |

### Optimized Solution from Canonical Skeleton (BFS):
```csharp
int CountIslandsBFS(char[][] grid)
{
    int m = grid.Length, n = grid[0].Length;
    bool[,] visited = new bool[m, n];
    int count = 0;
    
    int[] dr = {1, -1, 0, 0};
    int[] dc = {0, 0, 1, -1};
    
    for (int i = 0; i < m; i++)
    {
        for (int j = 0; j < n; j++)
        {
            if (grid[i][j] == '1' && !visited[i, j])
            {
                // BFS from this cell
                Queue<(int, int)> queue = new();
                queue.Enqueue((i, j));
                visited[i, j] = true;
                
                while (queue.Count > 0)
                {
                    var (r, c) = queue.Dequeue();
                    
                    // Explore 4 neighbors
                    for (int k = 0; k < 4; k++)
                    {
                        int nr = r + dr[k];
                        int nc = c + dc[k];
                        
                        if (nr >= 0 && nr < m && nc >= 0 && nc < n &&
                            grid[nr][nc] == '1' && !visited[nr, nc])
                        {
                            visited[nr, nc] = true;
                            queue.Enqueue((nr, nc));
                        }
                    }
                }
                
                count++;
            }
        }
    }
    
    return count;
}
```

### Explanation of Pruning:
- **Visited tracking:** Prevents re-exploring cells (O(m×n) → each cell once)
- **DFS:** Explores depth-first, marks entire island before returning
- **BFS:** Explores level-by-level, marks entire island in queue
- **Key insight:** Each connected component is one island
- **Why it works:** Visited set ensures each cell processed exactly once

### Optimized Solution Code Walkthrough / Variable Trace (BFS):
For grid:
```
11000
11000
00100
00011
```

**Island 1 (starting at 0,0):**

| queue | dequeue | neighbors checked | enqueue | visited |
|-------|---------|-------------------|---------|----------|
| [(0,0)] | (0,0) | (1,0), (0,1) | both | {(0,0),(1,0),(0,1)} |
| [(1,0),(0,1)] | (1,0) | (1,1) | (1,1) | {(0,0),(1,0),(0,1),(1,1)} |
| [(0,1),(1,1)] | (0,1) | already visited | none | same |
| [(1,1)] | (1,1) | already visited | none | same |
| [] | done | - | - | island complete |

**count = 1**

**Island 2 (starting at 2,2):**
- queue: [(2,2)] → dequeue, no unvisited neighbors
- **count = 2**

**Island 3 (starting at 3,3):**
- queue: [(3,3)] → dequeue, enqueue (3,4)
- queue: [(3,4)] → dequeue, no more
- **count = 3**

### Big-O Analysis:
- **Brute Force (DFS):** O(m × n) → each cell visited once
- **Optimized (BFS):** O(m × n) → each cell visited once
- **Space Complexity:**
  - DFS: O(m × n) for visited + O(m × n) recursion stack worst case
  - BFS: O(m × n) for visited + O(min(m,n)) for queue
- **Note:** Both have same time complexity; BFS better for space in some cases

</details>

---

<details>
<summary><b>Variant #2: Tree Traversals (Preorder, Inorder, Postorder, Level-Order)</b></summary>

## Variant #2: Tree Traversals

### Input/Output:
- **Input:** Root of binary tree
- **Output:** List of node values in specific order
  - **Preorder:** Root → Left → Right
  - **Inorder:** Left → Root → Right (gives sorted order for BST)
  - **Postorder:** Left → Right → Root
  - **Level-Order (BFS):** Level-by-level from top to bottom

### Example:
Tree:
```
      1
     / \
    2   3
   / \
  4   5
```
- Preorder: [1,2,4,5,3]
- Inorder: [4,2,5,1,3]
- Postorder: [4,5,2,3,1]
- Level-Order: [[1],[2,3],[4,5]]

### Full State Space:
- **All possible visit orders:** n! permutations of n nodes
- **Example:** For 3 nodes → 3! = 6 possible orders
- **Reality:** DFS/BFS rules constrain to specific patterns

```csharp
// Conceptual: Try all permutations (exponential)
void AllOrders(TreeNode[] nodes, List<int> current, bool[] used)
{
    if (current.Count == nodes.Length)
    {
        // One possible order
        return;
    }
    
    for (int i = 0; i < nodes.Length; i++)
    {
        if (!used[i])
        {
            used[i] = true;
            current.Add(nodes[i].val);
            AllOrders(nodes, current, used);
            current.RemoveAt(current.Count - 1);
            used[i] = false;
        }
    }
}
```

### Expected / Pruned State Space:
- **Preorder:** Visit root first, then recursively left, then right → O(n)
- **Inorder:** Visit left first, then root, then right → O(n)
- **Postorder:** Visit children first, then root → O(n)
- **Level-Order:** Visit all nodes at depth d before depth d+1 → O(n)
- **Pruning:** Tree structure + traversal rules → only 1 valid order per type

### Brute Force Canonical Skeleton (DFS Recursive):
```csharp
public class Solution
{
    // PREORDER: Root → Left → Right
    public void Preorder(TreeNode node, List<int> result)
    {
        if (node == null) return;
        
        result.Add(node.val);           // Visit root FIRST
        Preorder(node.left, result);    // Then left subtree
        Preorder(node.right, result);   // Then right subtree
    }
    
    // INORDER: Left → Root → Right
    public void Inorder(TreeNode node, List<int> result)
    {
        if (node == null) return;
        
        Inorder(node.left, result);     // Left subtree first
        result.Add(node.val);           // Visit root MIDDLE
        Inorder(node.right, result);    // Then right subtree
    }
    
    // POSTORDER: Left → Right → Root
    public void Postorder(TreeNode node, List<int> result)
    {
        if (node == null) return;
        
        Postorder(node.left, result);   // Left subtree first
        Postorder(node.right, result);  // Then right subtree
        result.Add(node.val);           // Visit root LAST
    }
}
```

### Brute Force Code Walkthrough / Variable Trace (Preorder):
For tree:
```
    1
   / \
  2   3
 /
4
```

| Call Stack | node.val | result | Action |
|------------|----------|--------|--------|
| Preorder(1) | 1 | [1] | Add 1, recurse left |
| Preorder(2) | 2 | [1,2] | Add 2, recurse left |
| Preorder(4) | 4 | [1,2,4] | Add 4, no children |
| back to (2) | - | [1,2,4] | Recurse right (null) |
| back to (1) | - | [1,2,4] | Recurse right |
| Preorder(3) | 3 | [1,2,4,3] | Add 3, no children |
| back to (1) | - | [1,2,4,3] | Done |

**Result:** [1,2,4,3]

### Optimized Solution (BFS Level-Order):
```csharp
public List<List<int>> LevelOrder(TreeNode root)
{
    List<List<int>> result = new();
    if (root == null) return result;
    
    Queue<TreeNode> queue = new();
    queue.Enqueue(root);
    
    while (queue.Count > 0)
    {
        int levelSize = queue.Count;      // Capture current level size
        List<int> currentLevel = new();
        
        for (int i = 0; i < levelSize; i++)
        {
            TreeNode node = queue.Dequeue();
            currentLevel.Add(node.val);
            
            // Enqueue children for next level
            if (node.left != null) queue.Enqueue(node.left);
            if (node.right != null) queue.Enqueue(node.right);
        }
        
        result.Add(currentLevel);
    }
    
    return result;
}
```

### Explanation of Pruning:
- **DFS (Preorder/Inorder/Postorder):**
  - Recursion naturally enforces tree structure
  - Each node visited exactly once → O(n)
  - Order determined by when we process node.val
  - No extra visited tracking needed (tree has no cycles)
  
- **BFS (Level-Order):**
  - Queue ensures level-by-level processing
  - Capture `levelSize` before inner loop → separates levels
  - Each node visited exactly once → O(n)
  - Children enqueued for next level automatically

### Optimized Solution Code Walkthrough / Variable Trace (Level-Order):
For tree:
```
    1
   / \
  2   3
 / \
4   5
```

| queue | levelSize | dequeue | currentLevel | enqueue | result |
|-------|-----------|---------|--------------|---------|--------|
| [1] | 1 | 1 | [1] | 2,3 | [[1]] |
| [2,3] | 2 | 2 | [2] | 4,5 | - |
| [3,4,5] | - | 3 | [2,3] | none | [[1],[2,3]] |
| [4,5] | 2 | 4 | [4] | none | - |
| [5] | - | 5 | [4,5] | none | [[1],[2,3],[4,5]] |
| [] | - | - | - | - | Done |

**Result:** [[1],[2,3],[4,5]]

### Big-O Analysis:
- **Time Complexity:**
  - All traversals: O(n) → visit each node exactly once
  - DFS: O(n) for recursion
  - BFS: O(n) for queue operations
  
- **Space Complexity:**
  - **Preorder/Inorder/Postorder:** O(h) recursion stack (h = height)
    - Balanced tree: O(log n)
    - Skewed tree: O(n)
  - **Level-Order:** O(w) for queue (w = max width)
    - Balanced tree: O(n/2) = O(n) at last level
    - Skewed tree: O(1)
    
- **Comparison:**
  - DFS better for deep trees (less space)
  - BFS better for wide trees or when need level info

</details>

---


<details>
<summary><b>Variant #3: Shortest Path in Unweighted Graph</b></summary>

## Variant #3: Shortest Path in Unweighted Graph

### Problem:
Given unweighted graph and source/target nodes, find shortest path length.

### Why BFS?
- BFS explores nodes level-by-level  distance from source
- First time we reach target = shortest path
- DFS doesn't guarantee shortest path (explores depth-first)

### Optimized Solution (BFS):
```csharp
public int ShortestPath(Dictionary<int, List<int>> graph, int source, int target)
{
    var visited = new HashSet<int>();
    var queue = new Queue<(int node, int dist)>();
    
    queue.Enqueue((source, 0));
    visited.Add(source);
    
    while (queue.Count > 0)
    {
        var (node, dist) = queue.Dequeue();
        
        if (node == target) return dist;  // Shortest path found!
        
        foreach (int neighbor in graph[node])
        {
            if (!visited.Contains(neighbor))
            {
                visited.Add(neighbor);
                queue.Enqueue((neighbor, dist + 1));
            }
        }
    }
    
    return -1;  // No path exists
}
```

### Big-O: O(V + E) time, O(V) space

</details>

---

<details>
<summary><b>Variant #4: Connected Components (Generic Graph)</b></summary>

## Variant #4: Connected Components

### Problem:
Count number of connected components in undirected graph.

### Approach:
- Start DFS/BFS from unvisited node  marks entire component
- Count how many times we start a new DFS/BFS

### Optimized Solution (DFS):
```csharp
public int CountComponents(int n, Dictionary<int, List<int>> graph)
{
    var visited = new HashSet<int>();
    int count = 0;
    
    for (int node = 0; node < n; node++)
    {
        if (!visited.Contains(node))
        {
            DFS(node, graph, visited);  // Mark entire component
            count++;  // Found a new component
        }
    }
    
    return count;
}

void DFS(int node, Dictionary<int, List<int>> graph, HashSet<int> visited)
{
    visited.Add(node);
    
    foreach (int neighbor in graph[node])
    {
        if (!visited.Contains(neighbor))
            DFS(neighbor, graph, visited);
    }
}
```

### Big-O: O(V + E) time, O(V) space

</details>

---

<details>
<summary><b>Variant #5: Cycle Detection</b></summary>

## Variant #5: Cycle Detection

### Directed Graph (DFS with Recursion Stack):
```csharp
public bool HasCycle(Dictionary<int, List<int>> graph, int n)
{
    var visited = new HashSet<int>();
    var recStack = new HashSet<int>();  // Track current path
    
    for (int node = 0; node < n; node++)
    {
        if (!visited.Contains(node))
        {
            if (DFS(node, graph, visited, recStack))
                return true;
        }
    }
    
    return false;
}

bool DFS(int node, Dictionary<int, List<int>> graph, 
         HashSet<int> visited, HashSet<int> recStack)
{
    visited.Add(node);
    recStack.Add(node);  // Add to current path
    
    foreach (int neighbor in graph[node])
    {
        if (!visited.Contains(neighbor))
        {
            if (DFS(neighbor, graph, visited, recStack))
                return true;
        }
        else if (recStack.Contains(neighbor))  // Back edge!
        {
            return true;  // Cycle detected
        }
    }
    
    recStack.Remove(node);  // Remove from current path
    return false;
}
```

### Undirected Graph (Track Parent):
```csharp
bool DFS(int node, int parent, Dictionary<int, List<int>> graph, 
         HashSet<int> visited)
{
    visited.Add(node);
    
    foreach (int neighbor in graph[node])
    {
        if (!visited.Contains(neighbor))
        {
            if (DFS(neighbor, node, graph, visited))
                return true;
        }
        else if (neighbor != parent)  // Visited non-parent = cycle
        {
            return true;
        }
    }
    
    return false;
}
```

### Big-O: O(V + E) time, O(V) space

</details>

---

<details>
<summary><b>Variant #6: Topological Sort</b></summary>

## Variant #6: Topological Sort

### Problem:
Order nodes in DAG such that for every edge uv, u comes before v.

### DFS Approach (Post-Order):
```csharp
public List<int> TopologicalSort(Dictionary<int, List<int>> graph, int n)
{
    var visited = new HashSet<int>();
    var stack = new Stack<int>();
    
    for (int node = 0; node < n; node++)
    {
        if (!visited.Contains(node))
            DFS(node, graph, visited, stack);
    }
    
    return stack.ToList();  // Already in reverse post-order
}

void DFS(int node, Dictionary<int, List<int>> graph, 
         HashSet<int> visited, Stack<int> stack)
{
    visited.Add(node);
    
    foreach (int neighbor in graph[node])
    {
        if (!visited.Contains(neighbor))
            DFS(neighbor, graph, visited, stack);
    }
    
    stack.Push(node);  // Add AFTER visiting all descendants
}
```

### BFS Approach (Kahn's Algorithm):
```csharp
public List<int> TopologicalSort(Dictionary<int, List<int>> graph, int n)
{
    int[] inDegree = new int[n];
    
    // Calculate in-degrees
    foreach (var (node, neighbors) in graph)
    {
        foreach (int neighbor in neighbors)
            inDegree[neighbor]++;
    }
    
    var queue = new Queue<int>();
    for (int i = 0; i < n; i++)
    {
        if (inDegree[i] == 0)
            queue.Enqueue(i);  // Start with nodes having no dependencies
    }
    
    var result = new List<int>();
    
    while (queue.Count > 0)
    {
        int node = queue.Dequeue();
        result.Add(node);
        
        foreach (int neighbor in graph[node])
        {
            inDegree[neighbor]--;
            if (inDegree[neighbor] == 0)
                queue.Enqueue(neighbor);
        }
    }
    
    return result.Count == n ? result : new List<int>();  // Empty if cycle
}
```

### Big-O: O(V + E) time, O(V) space

</details>

---

<details>
<summary><b>Variant #7: Word Ladder</b></summary>

## Variant #7: Word Ladder

### Problem:
Transform `beginWord` to `endWord` changing one letter at a time. Each intermediate word must be in dictionary. Return minimum transformations.

### Why BFS?
Shortest transformation sequence  BFS guarantees shortest path.

### Optimized Solution:
```csharp
public int LadderLength(string beginWord, string endWord, IList<string> wordList)
{
    var wordSet = new HashSet<string>(wordList);
    if (!wordSet.Contains(endWord)) return 0;
    
    var queue = new Queue<(string word, int steps)>();
    queue.Enqueue((beginWord, 1));
    
    while (queue.Count > 0)
    {
        var (word, steps) = queue.Dequeue();
        
        if (word == endWord) return steps;
        
        // Try changing each character
        char[] chars = word.ToCharArray();
        for (int i = 0; i < chars.Length; i++)
        {
            char original = chars[i];
            
            for (char c = 'a'; c <= 'z'; c++)
            {
                if (c == original) continue;
                
                chars[i] = c;
                string newWord = new string(chars);
                
                if (wordSet.Contains(newWord))
                {
                    queue.Enqueue((newWord, steps + 1));
                    wordSet.Remove(newWord);  // Mark visited
                }
            }
            
            chars[i] = original;  // Restore
        }
    }
    
    return 0;  // No transformation exists
}
```

### Big-O: O(M  N) where M = word length, N = word count
- For each word: try M positions  26 letters = O(M  26)
- Create new string: O(M)
- Total: O(M  N)

</details>

---

<details>
<summary><b>Variant #8: Maze Solving (Minimum Steps)</b></summary>

## Variant #8: Maze Solving

### Problem:
Given grid with start, end, and obstacles, find minimum steps to reach end.

### Why BFS?
Minimum steps  shortest path  BFS.

### Optimized Solution:
```csharp
public int ShortestPath(int[][] maze, (int r, int c) start, (int r, int c) end)
{
    int m = maze.Length, n = maze[0].Length;
    bool[,] visited = new bool[m, n];
    
    var queue = new Queue<(int r, int c, int steps)>();
    queue.Enqueue((start.r, start.c, 0));
    visited[start.r, start.c] = true;
    
    int[] dr = {-1, 1, 0, 0};
    int[] dc = {0, 0, -1, 1};
    
    while (queue.Count > 0)
    {
        var (r, c, steps) = queue.Dequeue();
        
        if (r == end.r && c == end.c) return steps;
        
        for (int k = 0; k < 4; k++)
        {
            int nr = r + dr[k];
            int nc = c + dc[k];
            
            if (nr >= 0 && nr < m && nc >= 0 && nc < n &&
                maze[nr][nc] == 0 && !visited[nr, nc])  // 0 = walkable
            {
                visited[nr, nc] = true;
                queue.Enqueue((nr, nc, steps + 1));
            }
        }
    }
    
    return -1;  // No path
}
```

### Big-O: O(m  n) time, O(m  n) space

</details>

---

# Key Takeaways

## BFS vs DFS

| Aspect | BFS | DFS |
|--------|-----|-----|
| **Data Structure** | Queue | Stack/Recursion |
| **Space** | O(width) | O(height) |
| **Shortest Path** |  Yes (unweighted) |  No |
| **All Paths** |  No |  Yes |
| **Use When** | Level-order, shortest path | Backtracking, topological sort |

## Common Patterns

### 1. Grid Traversal (Islands, Maze)
- Track visited with 2D array
- Explore 4/8 directions: `dr = {-1,1,0,0}`, `dc = {0,0,-1,1}`
- DFS for counting components, BFS for shortest path

### 2. Tree Traversal
- **Preorder:** Process root first (copy tree, prefix notation)
- **Inorder:** Process root middle (BST  sorted order)
- **Postorder:** Process root last (delete tree, postfix notation)
- **Level-Order:** BFS for level-by-level

### 3. Shortest Path (Unweighted)
- **Always use BFS**  guarantees shortest
- Track distance with each node
- Stop when target found

### 4. Connected Components
- Count DFS/BFS starts from unvisited nodes
- Each start = new component
- Works on grids and graphs

### 5. Cycle Detection
- **Directed:** DFS + recursion stack (back edge = cycle)
- **Undirected:** DFS + parent tracking (visited non-parent = cycle)

### 6. Topological Sort
- **DFS:** Post-order traversal  reverse result
- **BFS (Kahn's):** Process nodes with in-degree=0
- Only works on DAGs (cycle = impossible)

## Core Templates

### DFS Template (Recursive):
```csharp
void DFS(Node node, HashSet<Node> visited)
{
    if (visited.Contains(node)) return;
    visited.Add(node);
    
    // Process node
    
    foreach (var neighbor in node.Neighbors)
        DFS(neighbor, visited);
}
```

### BFS Template:
```csharp
void BFS(Node start)
{
    var visited = new HashSet<Node>();
    var queue = new Queue<Node>();
    
    queue.Enqueue(start);
    visited.Add(start);
    
    while (queue.Count > 0)
    {
        var node = queue.Dequeue();
        
        // Process node
        
        foreach (var neighbor in node.Neighbors)
        {
            if (!visited.Contains(neighbor))
            {
                visited.Add(neighbor);
                queue.Enqueue(neighbor);
            }
        }
    }
}
```

## Decision Tree

```
Need shortest path?
 Yes  BFS
 No
    Need all paths/backtracking?  DFS
    Level-by-level processing?  BFS
    Cycle detection?  DFS (recursion stack)
    Topological sort?  DFS (post-order) or BFS (Kahn's)
    Connected components?  DFS or BFS (count starts)
```

---

# Mental Checklist for Graph Traversal

- [ ] **Shortest path needed?**  BFS
- [ ] **All paths or backtracking?**  DFS
- [ ] **Level-by-level?**  BFS
- [ ] **Tree or DAG?**  No cycle concerns
- [ ] **Detect cycles?**  DFS with recursion stack (directed) or parent (undirected)
- [ ] **Connected components?**  Count DFS/BFS starts
- [ ] **Topological order?**  DFS post-order or BFS in-degree
- [ ] **Grid problem?**  Track visited 2D array, explore 4/8 directions
- [ ] **Graph representation?**  Adjacency list (Dict<int, List<int>>)

**If exploring systematically  Graph Traversal applies.**
