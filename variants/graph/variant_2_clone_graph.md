# Clone Graph

**Difficulty:** Medium  
**LeetCode Problem:** #133  
**Tags:** `Graph`, `DFS`, `BFS`, `Hash Map`, `Clone`, `Deep Copy`

---

## Problem Statement

Given a reference of a node in a **connected** undirected graph.

Return a **deep copy** (clone) of the graph.

Each node in the graph contains a value (`int`) and a list (`List[Node]`) of its neighbors.

```
class Node {
    public int val;
    public List<Node> neighbors;
}
```

**Test case format:**

For simplicity, each node's value is the same as the node's index (1-indexed). For example, the first node with `val == 1`, the second node with `val == 2`, and so on. The graph is represented in the test case using an adjacency list.

**Constraints:**
- The number of nodes in the graph is in the range `[0, 100]`.
- `1 <= Node.val <= 100`
- `Node.val` is unique for each node.
- There are no repeated edges and no self-loops in the graph.
- The Graph is connected and all nodes can be visited starting from the given node.

**Example 1:**
```
Input: adjList = [[2,4],[1,3],[2,4],[1,3]]
Output: [[2,4],[1,3],[2,4],[1,3]]
Explanation: 
Node 1: neighbors are [2, 4]
Node 2: neighbors are [1, 3]
Node 3: neighbors are [2, 4]
Node 4: neighbors are [1, 3]
```

**Example 2:**
```
Input: adjList = [[]]
Output: [[]]
Explanation: Single node with no neighbors.
```

**Example 3:**
```
Input: adjList = []
Output: []
Explanation: Empty graph.
```

---

## Step 1: State Space

### Problem Visualization

Original graph:
```
    1 ---- 2
    |      |
    |      |
    4 ---- 3

Node 1: neighbors [2, 4]
Node 2: neighbors [1, 3]
Node 3: neighbors [2, 4]
Node 4: neighbors [1, 3]
```

Cloned graph (deep copy):
```
    1' --- 2'
    |      |
    |      |
    4' --- 3'

All nodes are new objects with same structure
```

**Challenge: Handling cycles**
```
When cloning node 1:
  - Create clone of node 1
  - Clone its neighbors: [2, 4]
    - When cloning node 2:
      - Create clone of node 2
      - Clone its neighbors: [1, 3]
        - Node 1 already cloned! Use existing clone (not create new)
        
Need to track: original node → cloned node mapping
```

**DFS Approach:**
```
map = {} // original → clone mapping

CloneNode(node):
  if node in map:
    return map[node]  // already cloned
  
  clone = new Node(node.val)
  map[node] = clone  // save mapping BEFORE recursion
  
  for neighbor in node.neighbors:
    clonedNeighbor = CloneNode(neighbor)
    clone.neighbors.add(clonedNeighbor)
  
  return clone
```

### Core Question

**How do we create a complete deep copy of a graph with cycles?**

Use hash map to track cloned nodes and prevent infinite recursion.

### Deriving from First Principles

**Observation 1: Deep copy vs shallow copy**
```
Shallow: Copy node values, but share neighbor references
Deep: Copy nodes AND all connections with new objects

We need deep copy with new Node objects.
```

**Observation 2: Cycle handling**
```
Graph may have cycles: 1→2→3→1

Without tracking, infinite recursion:
  Clone(1) → Clone(2) → Clone(3) → Clone(1) → ...

Need visited tracking: original → clone map
```

**Observation 3: Clone before recursing**
```
CRITICAL: Add to map before recursing on neighbors

Why? If neighbor points back to current node:
  Clone(1) creates clone1, maps 1→clone1
    Clone(2) creates clone2, maps 2→clone2
      Clone(1) finds 1 in map, returns clone1 ✓

If we map after recursion:
  Clone(1) creates clone1, recurses first
    Clone(2) creates clone2, recurses first
      Clone(1) not in map yet, creates another clone1 ✗
```

**Observation 4: Two-pass isn't needed**
```
Could do:
  Pass 1: Clone all nodes (just values)
  Pass 2: Connect all neighbors

But one-pass with map is sufficient and elegant.
```

**Observation 5: BFS also works**
```
Queue-based approach:
1. Clone starting node
2. BFS: for each node, clone unvisited neighbors
3. Connect cloned nodes
```

**Formula (DFS):**
```
map = {}

CloneGraph(node):
  if node is null:
    return null
  return DFS(node)

DFS(node):
  if node in map:
    return map[node]
  
  clone = new Node(node.val)
  map[node] = clone  // BEFORE recursion!
  
  for neighbor in node.neighbors:
    clonedNeighbor = DFS(neighbor)
    clone.neighbors.add(clonedNeighbor)
  
  return clone
```

### State Space Structure

**Type:** Graph traversal with cloning.

**Structure:**
- Hash map: original node → cloned node
- DFS/BFS to traverse all nodes
- Create new nodes and connections

**Cardinality:**
- N nodes, E edges
- DFS visits each node once: O(N)
- Each edge traversed once: O(E)
- Total: O(N + E)
- Space: O(N) for map + recursion stack

**Key Property:** Map prevents revisiting and enables cycle handling.

### Example Computation

Graph: 1-2-3 (linear, no cycle for simplicity)

```
Call CloneGraph(node1):
  DFS(node1):
    node1 not in map
    Create clone1 (val=1)
    map = {node1: clone1}
    For neighbor node2:
      DFS(node2):
        node2 not in map
        Create clone2 (val=2)
        map = {node1: clone1, node2: clone2}
        For neighbor node1:
          DFS(node1):
            node1 in map! Return clone1 ✓
        For neighbor node3:
          DFS(node3):
            node3 not in map
            Create clone3 (val=3)
            map = {node1: clone1, node2: clone2, node3: clone3}
            For neighbor node2:
              DFS(node2):
                node2 in map! Return clone2 ✓
            clone3.neighbors = [clone2]
            Return clone3
        clone2.neighbors = [clone1, clone3]
        Return clone2
    clone1.neighbors = [clone2]
    Return clone1

Final: clone1 with proper connections to clone2 and clone3
```

### Generation Pattern

**Brute force:**
```csharp
// Traverse graph to collect all nodes
// Create new node for each
// Build adjacency based on original
// Time: O(N + E), same as optimal
```

---

## Step 2: Approach Comparison

**DFS:** Natural recursion, simple code
**BFS:** Iterative with queue, level-by-level
Both are O(N + E) time and space.

---

## Step 3: Optimization Ideas

### Key Insights
1. **Hash map for mapping:** Original → Clone
2. **Clone before recursion:** Prevents infinite loops
3. **DFS/BFS traversal:** Visit all reachable nodes
4. **Single pass:** Clone nodes and edges together

---

## Step 4: Optimal Solution (DFS)

```csharp
public class Solution {
    private Dictionary<Node, Node> visited = new Dictionary<Node, Node>();
    
    public Node CloneGraph(Node node) {
        if (node == null) {
            return null;
        }
        
        return DFS(node);
    }
    
    private Node DFS(Node node) {
        // If already cloned, return the clone
        if (visited.ContainsKey(node)) {
            return visited[node];
        }
        
        // Create clone of current node
        Node clone = new Node(node.val);
        
        // CRITICAL: Add to map BEFORE recursing
        visited[node] = clone;
        
        // Clone all neighbors
        foreach (Node neighbor in node.neighbors) {
            Node clonedNeighbor = DFS(neighbor);
            clone.neighbors.Add(clonedNeighbor);
        }
        
        return clone;
    }
}
```

**Complexity:**
- Time: O(N + E)
  - Visit each node once: O(N)
  - Traverse each edge once: O(E)
- Space: O(N)
  - HashMap: O(N)
  - Recursion stack: O(N) worst case

---

## Alternative: BFS

```csharp
public Node CloneGraph(Node node) {
    if (node == null) {
        return null;
    }
    
    var visited = new Dictionary<Node, Node>();
    var queue = new Queue<Node>();
    
    // Clone starting node
    Node clone = new Node(node.val);
    visited[node] = clone;
    queue.Enqueue(node);
    
    while (queue.Count > 0) {
        Node current = queue.Dequeue();
        
        foreach (Node neighbor in current.neighbors) {
            if (!visited.ContainsKey(neighbor)) {
                // Clone the neighbor
                Node clonedNeighbor = new Node(neighbor.val);
                visited[neighbor] = clonedNeighbor;
                queue.Enqueue(neighbor);
            }
            
            // Add cloned neighbor to current clone's neighbors
            visited[current].neighbors.Add(visited[neighbor]);
        }
    }
    
    return clone;
}
```

**Complexity:** Same O(N + E) time and space.

**BFS vs DFS:**
- **DFS:** Cleaner code, natural recursion
- **BFS:** Iterative, no stack overflow risk, processes level-by-level

---

## Edge Cases

1. **Null input:** `node = null` → `null`
2. **Single node:** No neighbors → Clone with empty list
3. **Two nodes:** `1-2` → Clone both with connection
4. **Cycle:** `1-2-1` → Handle with map
5. **Complete graph:** All nodes connected to all others

---

## Visualization

```
Original:
    1 ---- 2
    
DFS(1):
  Create clone1
  Map: {1: clone1}
  Process neighbor 2:
    DFS(2):
      Create clone2
      Map: {1: clone1, 2: clone2}
      Process neighbor 1:
        DFS(1):
          1 in map, return clone1
      clone2.neighbors = [clone1]
      Return clone2
  clone1.neighbors = [clone2]
  Return clone1

Result:
    clone1 ---- clone2
```

---

## Common Mistakes

1. **Cloning after recursion:**
   ```csharp
   // WRONG: map after recursion
   Node clone = new Node(node.val);
   foreach (var neighbor in node.neighbors) {
       clone.neighbors.Add(DFS(neighbor));
   }
   visited[node] = clone;  // Too late! Causes duplicates
   ```

2. **Not checking map first:**
   ```csharp
   // WRONG: creates multiple clones
   Node clone = new Node(node.val);
   if (!visited.ContainsKey(node)) {
       visited[node] = clone;
   }
   ```

3. **Shallow copy:**
   ```csharp
   // WRONG: shares neighbor references
   Node clone = node;  // Not a deep copy!
   ```

---

## Related Problems

1. **Copy List with Random Pointer (LeetCode #138)** - Similar cloning with special pointer
2. **Clone Binary Tree With Random Pointer (LeetCode #1485)** - Tree variant
3. **Clone N-ary Tree (LeetCode #1490)** - N-ary tree cloning
4. **Serialize and Deserialize Binary Tree (LeetCode #297)** - Tree reconstruction

---

## Pattern Recognition

**Problem Asks For:**
- Deep copy of graph
- Preserve structure and connections
- Handle cycles

**This Suggests:**
- Graph traversal (DFS/BFS)
- Hash map for tracking
- Clone during traversal
- → **Graph Clone with Cycle Handling**

**Key Indicators:**
- "Clone" or "deep copy"
- Graph or tree structure
- Cycles or back references

---

## Tags

`#graph` `#dfs` `#bfs` `#hash-map` `#clone` `#deep-copy` `#cycle-detection`
