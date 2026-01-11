# Maximum Depth of Binary Tree

**Difficulty:** Easy  
**LeetCode Problem:** #104  
**Tags:** `Binary Tree`, `DFS`, `Recursion`, `Tree Properties`

---

## Problem Statement

Given the root of a binary tree, return its **maximum depth**.

A binary tree's **maximum depth** is the number of nodes along the longest path from the root node down to the farthest leaf node.

**Constraints:**
- The number of nodes in the tree is in the range `[0, 10^4]`
- `-100 <= Node.val <= 100`

**Example 1:**
```
Input: root = [3,9,20,null,null,15,7]
       3
      / \
     9  20
        / \
       15  7

Output: 3
```

**Example 2:**
```
Input: root = [1,null,2]
       1
        \
         2

Output: 2
```

---

## Step 1: State Space

### Problem Analysis

**Input:** Binary tree with `n` nodes  
**Output:** Integer representing maximum depth (height)

### State Space Definition

- **Type**: All nodes in binary tree
- **Structure**: Recursive tree structure (node + left subtree + right subtree)
- **Cardinality**: n nodes = **O(n)**
- **Traversal**: DFS visits each node exactly once

### Full State Space Enumeration

For tree `[3,9,20,null,null,15,7]`:

```
All nodes: 3, 9, 20, 15, 7 (5 nodes)

Recursive breakdown:
  root(3): depth = 1 + max(left(9), right(20))
    left(9): depth = 1 + max(null, null) = 1
    right(20): depth = 1 + max(left(15), right(7))
      left(15): depth = 1 + max(null, null) = 1
      right(7): depth = 1 + max(null, null) = 1
    right(20): depth = 1 + max(1, 1) = 2
  root(3): depth = 1 + max(1, 2) = 3
```

### Core Question

**What is the height of the tree?**

Height = longest path from root to any leaf

### Deriving from First Principles

**Observation 1: Recursive definition**
```
depth(node) = 0                           if node == null
depth(node) = 1 + max(depth(left), depth(right))
```

**Observation 2: Base case**
- Null node has depth 0
- Leaf node has depth 1

**Observation 3: Combine subproblems**
- Height of tree = 1 (current node) + max height of subtrees

---

## Step 2: Brute Force Solution with Value Tracing

### Brute Force Approach
Visit every node recursively, compute depth bottom-up

### Brute Force Code
```csharp
int MaxDepthBruteForce(TreeNode root) 
{
    // Base case: empty tree
    if (root == null) return 0;
    
    // Recursive case: compute left and right depths
    int leftDepth = MaxDepthBruteForce(root.left);
    int rightDepth = MaxDepthBruteForce(root.right);
    
    // Current depth = 1 + max of subtrees
    return 1 + Math.Max(leftDepth, rightDepth);
}
```

### Value Tracing

For tree `[3,9,20,null,null,15,7]`:

```
Call Stack:
MaxDepth(3)
  → MaxDepth(9)
    → MaxDepth(null) = 0
    → MaxDepth(null) = 0
    → return 1 + max(0, 0) = 1
  → MaxDepth(20)
    → MaxDepth(15)
      → MaxDepth(null) = 0
      → MaxDepth(null) = 0
      → return 1 + max(0, 0) = 1
    → MaxDepth(7)
      → MaxDepth(null) = 0
      → MaxDepth(null) = 0
      → return 1 + max(0, 0) = 1
    → return 1 + max(1, 1) = 2
  → return 1 + max(1, 2) = 3

Result: 3
```

### Complexity Analysis
- **Time:** O(n) - visit each node once
- **Space:** O(h) - recursion stack depth (h = height)
  - Best case (balanced): O(log n)
  - Worst case (skewed): O(n)

---

## Step 3: Pruning Analysis

**No pruning possible** - must visit all nodes to find maximum depth.

A node's depth can only be determined after exploring both subtrees.

---

## Step 4: Optimal Solution

The brute force solution is already optimal!

### Optimal Code (Same as Brute Force)

```csharp
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     public int val;
 *     public TreeNode left;
 *     public TreeNode right;
 *     public TreeNode(int val=0, TreeNode left=null, TreeNode right=null) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */

public class Solution 
{
    public int MaxDepth(TreeNode root) 
    {
        // Base case: null node has depth 0
        if (root == null) return 0;
        
        // Recursive case: 1 + max depth of subtrees
        int leftDepth = MaxDepth(root.left);
        int rightDepth = MaxDepth(root.right);
        
        return 1 + Math.Max(leftDepth, rightDepth);
    }
}
```

### One-Liner Version
```csharp
public int MaxDepth(TreeNode root) 
{
    return root == null ? 0 : 1 + Math.Max(MaxDepth(root.left), MaxDepth(root.right));
}
```

---

## Alternative Implementations

### Iterative BFS (Level Order)
```csharp
public int MaxDepthBFS(TreeNode root) 
{
    if (root == null) return 0;
    
    var queue = new Queue<TreeNode>();
    queue.Enqueue(root);
    int depth = 0;
    
    while (queue.Count > 0) 
    {
        int levelSize = queue.Count;
        depth++; // Increment depth for each level
        
        for (int i = 0; i < levelSize; i++) 
        {
            var node = queue.Dequeue();
            
            if (node.left != null) queue.Enqueue(node.left);
            if (node.right != null) queue.Enqueue(node.right);
        }
    }
    
    return depth;
}
```

**Complexity:** O(n) time, O(w) space (w = max width at any level)

### Iterative DFS with Stack
```csharp
public int MaxDepthIterativeDFS(TreeNode root) 
{
    if (root == null) return 0;
    
    var stack = new Stack<(TreeNode node, int depth)>();
    stack.Push((root, 1));
    int maxDepth = 0;
    
    while (stack.Count > 0) 
    {
        var (node, depth) = stack.Pop();
        maxDepth = Math.Max(maxDepth, depth);
        
        if (node.left != null) stack.Push((node.left, depth + 1));
        if (node.right != null) stack.Push((node.right, depth + 1));
    }
    
    return maxDepth;
}
```

---

## Edge Cases

```csharp
// Empty tree
MaxDepth(null) → 0

// Single node
MaxDepth(new TreeNode(1)) → 1

// Skewed left
//   1
//  /
// 2
///
//3
MaxDepth(skewed) → 3

// Complete binary tree
//     1
//    / \
//   2   3
//  / \ / \
// 4  5 6  7
MaxDepth(complete) → 3
```

---

## Visualization

### Recursive Call Tree
```
            MaxDepth(3)
           /           \
    MaxDepth(9)      MaxDepth(20)
     /    \           /         \
   0      0      MaxDepth(15)  MaxDepth(7)
                   /    \        /    \
                  0      0       0     0
                  
Combine bottom-up:
  9: 1 + max(0,0) = 1
  15: 1 + max(0,0) = 1
  7: 1 + max(0,0) = 1
  20: 1 + max(1,1) = 2
  3: 1 + max(1,2) = 3
```

---

## Related Problems

- **Minimum Depth of Binary Tree** (LeetCode #111) - Find shortest path to leaf
- **Balanced Binary Tree** (LeetCode #110) - Check if height-balanced
- **Diameter of Binary Tree** (LeetCode #543) - Longest path between any two nodes
- **Binary Tree Maximum Path Sum** (LeetCode #124) - Hard variant with values

---

## Pattern Recognition

**When you see:**
- "Maximum/minimum depth/height"
- "Longest/shortest path to leaf"
- Tree property calculation

**Think:**
- Recursive DFS
- Base case: null = 0 (or 1 for specific problems)
- Combine: 1 + operation on subtrees

---

## Key Insights

1. **Tree problems are naturally recursive** - structure matches recursion
2. **Null check first** - always handle base case before accessing children
3. **Bottom-up computation** - combine results from subtrees
4. **Space = O(h) not O(n)** - only recursion stack, not storing all nodes
5. **Three approaches work:** Recursive DFS (simplest), BFS (more code), Iterative DFS (explicit stack)
