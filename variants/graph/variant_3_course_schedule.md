# Course Schedule

**Difficulty:** Medium  
**LeetCode Problem:** #207  
**Tags:** `Graph`, `DFS`, `BFS`, `Topological Sort`, `Cycle Detection`, `DAG`

---

## Problem Statement

There are a total of `numCourses` courses you have to take, labeled from `0` to `numCourses - 1`. You are given an array `prerequisites` where `prerequisites[i] = [ai, bi]` indicates that you **must** take course `bi` first if you want to take course `ai`.

Return `true` if you can finish all courses. Otherwise, return `false`.

**Constraints:**
- `1 <= numCourses <= 2000`
- `0 <= prerequisites.length <= 5000`
- `prerequisites[i].length == 2`
- `0 <= ai, bi < numCourses`
- All the pairs `prerequisites[i]` are **unique**.

**Example 1:**
```
Input: numCourses = 2, prerequisites = [[1,0]]
Output: true
Explanation: Take course 0, then course 1.
```

**Example 2:**
```
Input: numCourses = 2, prerequisites = [[1,0],[0,1]]
Output: false
Explanation: Cycle: 0 → 1 → 0. Cannot finish.
```

**Example 3:**
```
Input: numCourses = 4, prerequisites = [[1,0],[2,0],[3,1],[3,2]]
Output: true
Explanation: Valid order: 0 → 1 → 2 → 3 or 0 → 2 → 1 → 3
```

---

## Step 1: State Space

### Problem Visualization

Example: numCourses = 4, prerequisites = [[1,0], [2,0], [3,1], [3,2]]

```
Dependency graph:
  0 → 1 → 3
  0 → 2 ↗

Edge [1,0] means: 0 must be taken before 1 (0 → 1)

Graph representation:
  Node 0: neighbors [1, 2] (courses that depend on 0)
  Node 1: neighbors [3]
  Node 2: neighbors [3]
  Node 3: neighbors []

Valid topological order:
  0 → 1 → 2 → 3  ✓
  0 → 2 → 1 → 3  ✓
```

**Cycle detection:**
```
Example with cycle: [[1,0], [0,1]]

Graph:
  0 → 1
  1 → 0

Trying to find topological order:
  Start from 0: 0 → 1 → 0 (back to 0) → CYCLE!
  
Cannot finish: return false
```

**Three states in DFS:**
```
White (0): Unvisited
Gray (1): Visiting (in current DFS path)
Black (2): Visited (completely processed)

If we reach a GRAY node during DFS:
  → Cycle detected (back edge to ancestor)
```

### Core Question

**Can we order courses such that all prerequisites are satisfied?**

This is equivalent to: Does the directed graph have a topological ordering? (Is it a DAG?)

### Deriving from First Principles

**Observation 1: Graph representation**
```
Course schedule → Directed graph
- Nodes: courses
- Edge a → b: course a is prerequisite for course b

prerequisites[i] = [a, b] means edge: b → a (b depends on a)
```

**Observation 2: Topological sort**
```
Valid course order = Topological ordering of DAG

Topological sort exists if and only if graph is acyclic (DAG)

Problem reduces to: Detect if graph has a cycle
```

**Observation 3: Cycle detection with DFS**
```
Use three-color DFS:
- White: not visited
- Gray: currently visiting (in DFS path)
- Black: completely visited

If DFS encounters a GRAY node:
  → Back edge → Cycle detected
```

**Observation 4: DFS algorithm**
```
for each course:
  if not visited:
    if DFS(course) returns false:  // cycle found
      return false
return true  // no cycles, can finish

DFS(course):
  mark course as GRAY (visiting)
  for each neighbor (dependent course):
    if neighbor is GRAY:  // back edge
      return false  // cycle!
    if neighbor is WHITE and DFS(neighbor) == false:
      return false
  mark course as BLACK (visited)
  return true
```

**Observation 5: Alternative - Kahn's algorithm (BFS)**
```
Topological sort using in-degrees:
1. Calculate in-degree for each node
2. Queue nodes with in-degree 0 (no prerequisites)
3. Process queue:
   - Remove node, decrease neighbors' in-degrees
   - Add newly zero-in-degree nodes to queue
4. If all nodes processed: no cycle
   If some remain: cycle exists
```

**Formula (DFS Cycle Detection):**
```
color = [WHITE] * numCourses
graph = build adjacency list

for course in 0..numCourses-1:
  if color[course] == WHITE:
    if not DFS(course):
      return false
return true

DFS(course):
  color[course] = GRAY
  for neighbor in graph[course]:
    if color[neighbor] == GRAY:
      return false  // cycle
    if color[neighbor] == WHITE:
      if not DFS(neighbor):
        return false
  color[course] = BLACK
  return true
```

### State Space Structure

**Type:** Cycle detection in directed graph.

**Structure:**
- Adjacency list for graph
- DFS with three states
- Or BFS with in-degrees

**Cardinality:**
- V = numCourses vertices
- E = prerequisites edges
- DFS: O(V + E) to visit all nodes and edges
- Space: O(V + E) for graph + O(V) for state

**Key Property:** Cycle detection determines if topological order exists.

### Example Computation

Input: numCourses = 4, prerequisites = [[1,0],[2,0],[3,1],[3,2]]

```
Build adjacency list:
  [1,0] → edge 0→1: graph[0] = [1]
  [2,0] → edge 0→2: graph[0] = [1, 2]
  [3,1] → edge 1→3: graph[1] = [3]
  [3,2] → edge 2→3: graph[2] = [3]
  
Graph:
  0: [1, 2]
  1: [3]
  2: [3]
  3: []

DFS traversal:
  color = [W, W, W, W]
  
  DFS(0):
    color[0] = G (gray)
    Visit neighbor 1:
      DFS(1):
        color[1] = G
        Visit neighbor 3:
          DFS(3):
            color[3] = G
            No neighbors
            color[3] = B (black)
        color[1] = B
    Visit neighbor 2:
      DFS(2):
        color[2] = G
        Visit neighbor 3:
          color[3] = B (already processed, skip)
        color[2] = B
    color[0] = B
  
  All nodes processed without encountering GRAY → No cycle
  Return true ✓
```

Example with cycle: [[1,0],[0,1]]
```
Graph:
  0: [1]
  1: [0]

DFS(0):
  color[0] = G
  Visit neighbor 1:
    DFS(1):
      color[1] = G
      Visit neighbor 0:
        color[0] == G → CYCLE DETECTED!
        Return false

Result: false (cannot finish) ✓
```

### Generation Pattern

**Brute force:**
```csharp
// Try all possible orderings: O(V!)
// Check if each satisfies prerequisites
// Exponential time
```

---

## Step 2: Brute Force

Try all permutations of courses, check validity: O(V! × E) - too slow.

---

## Step 3: Optimization Ideas

### Key Insights
1. **Topological sort:** Problem is about ordering
2. **Cycle detection:** Key to determining possibility
3. **DFS with colors:** Efficient cycle detection
4. **Kahn's algorithm:** BFS-based alternative

---

## Step 4: Optimal Solution (DFS)

```csharp
public class Solution {
    private enum State { Unvisited, Visiting, Visited }
    
    public bool CanFinish(int numCourses, int[][] prerequisites) {
        // Build adjacency list
        var graph = new List<int>[numCourses];
        for (int i = 0; i < numCourses; i++) {
            graph[i] = new List<int>();
        }
        
        foreach (var prereq in prerequisites) {
            int course = prereq[0];
            int prerequisite = prereq[1];
            // Edge: prerequisite → course
            graph[prerequisite].Add(course);
        }
        
        // Track state of each course
        var state = new State[numCourses];
        
        // Check for cycles starting from each unvisited course
        for (int i = 0; i < numCourses; i++) {
            if (state[i] == State.Unvisited) {
                if (HasCycle(i, graph, state)) {
                    return false;
                }
            }
        }
        
        return true;
    }
    
    private bool HasCycle(int course, List<int>[] graph, State[] state) {
        // Mark as currently visiting
        state[course] = State.Visiting;
        
        // Check all neighbors (dependent courses)
        foreach (int neighbor in graph[course]) {
            if (state[neighbor] == State.Visiting) {
                // Back edge - cycle detected!
                return true;
            }
            
            if (state[neighbor] == State.Unvisited) {
                if (HasCycle(neighbor, graph, state)) {
                    return true;
                }
            }
        }
        
        // Mark as completely visited
        state[course] = State.Visited;
        return false;
    }
}
```

**Complexity:**
- Time: O(V + E)
  - Build graph: O(E)
  - DFS visits each vertex once: O(V)
  - Each edge examined once: O(E)
- Space: O(V + E)
  - Graph: O(V + E)
  - State array: O(V)
  - Recursion stack: O(V)

---

## Alternative: Kahn's Algorithm (BFS Topological Sort)

```csharp
public bool CanFinish(int numCourses, int[][] prerequisites) {
    // Build adjacency list and calculate in-degrees
    var graph = new List<int>[numCourses];
    var inDegree = new int[numCourses];
    
    for (int i = 0; i < numCourses; i++) {
        graph[i] = new List<int>();
    }
    
    foreach (var prereq in prerequisites) {
        int course = prereq[0];
        int prerequisite = prereq[1];
        graph[prerequisite].Add(course);
        inDegree[course]++;
    }
    
    // Queue courses with no prerequisites
    var queue = new Queue<int>();
    for (int i = 0; i < numCourses; i++) {
        if (inDegree[i] == 0) {
            queue.Enqueue(i);
        }
    }
    
    // Process courses in topological order
    int processedCourses = 0;
    
    while (queue.Count > 0) {
        int course = queue.Dequeue();
        processedCourses++;
        
        // Reduce in-degree for dependent courses
        foreach (int neighbor in graph[course]) {
            inDegree[neighbor]--;
            if (inDegree[neighbor] == 0) {
                queue.Enqueue(neighbor);
            }
        }
    }
    
    // If all courses processed, no cycle
    return processedCourses == numCourses;
}
```

**Complexity:** Same O(V + E) time and space.

**DFS vs BFS:**
- **DFS:** Natural cycle detection, simpler concept
- **BFS (Kahn's):** Produces topological order directly, intuitive with in-degrees

---

## Edge Cases

1. **No prerequisites:** `prerequisites = []` → `true`
2. **Self-loop:** `[[0,0]]` → `false` (though problem says no self-loops)
3. **Disconnected components:** Multiple separate course chains
4. **Single course:** `numCourses = 1, prerequisites = []` → `true`
5. **Long chain:** `0→1→2→...→n` → `true`

---

## Visualization

```
Example: [[1,0],[2,1],[0,2]]

Graph:
    0 → 1 → 2
    ↑_______|

Cycle: 0 → 1 → 2 → 0

DFS from 0:
  state[0] = GRAY
  Visit 1:
    state[1] = GRAY
    Visit 2:
      state[2] = GRAY
      Visit 0:
        state[0] == GRAY → CYCLE!
        
Return false
```

---

## Follow-Up: Course Schedule II (LeetCode #210)

Return the actual course order:

```csharp
public int[] FindOrder(int numCourses, int[][] prerequisites) {
    // Same cycle detection
    // During DFS, add to result when marking BLACK (post-order)
    // Reverse result at end (or use stack)
}
```

---

## Related Problems

1. **Course Schedule II (LeetCode #210)** - Return actual order
2. **Course Schedule III (LeetCode #630)** - Maximum courses with deadlines
3. **Alien Dictionary (LeetCode #269)** - Topological sort for character order
4. **Sequence Reconstruction (LeetCode #444)** - Verify unique topological order
5. **Minimum Height Trees (LeetCode #310)** - Tree center finding

---

## Pattern Recognition

**Problem Asks For:**
- Ordering with dependencies
- Check if ordering is possible
- Detect circular dependencies

**This Suggests:**
- Directed graph from dependencies
- Topological sort
- Cycle detection (DFS or BFS)
- → **Topological Sort / Cycle Detection**

**Key Indicators:**
- "Prerequisites" or "dependencies"
- "Must do X before Y"
- Check if possible to complete all

---

## Tags

`#graph` `#dfs` `#bfs` `#topological-sort` `#cycle-detection` `#dag` `#directed-graph`
