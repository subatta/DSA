# Invert Binary Tree

**Difficulty:** Easy  
**LeetCode Problem:** #226  
**Tags:** `Binary Tree`, `DFS`, `Recursion`, `Tree Transformation`

---

## Problem Statement

Given the root of a binary tree, **invert the tree**, and return its root.

Inverting means swapping the left and right children of every node.

**Constraints:**
- The number of nodes in the tree is in the range `[0, 100]`
- `-100 <= Node.val <= 100`

**Example 1:**
```
Input:          4
              /   \
             2     7
            / \   / \
           1   3 6   9

Output:         4
              /   \
             7     2
            / \   / \
           9   6 3   1
```

**Example 2:**
```
Input: root = [2,1,3]
Output: [2,3,1]
```

**Example 3:**
```
Input: root = []
Output: []
```

---

## Step 1: State Space

### Problem Analysis

**Input:** Binary tree with `n` nodes  
**Output:** Same tree with all children swapped

### State Space Definition

- **Type**: All nodes in binary tree
- **Structure**: Each node needs left/right children swapped
- **Cardinality**: n nodes = **O(n)** swap operations
- **Traversal**: DFS or BFS to visit every node

### Core Question

**How do we swap left and right subtrees for every node?**

Recursively invert left subtree, invert right subtree, then swap them.

### Deriving from First Principles

**Observation 1: Recursive structure**
```
invert(node):
  1. Invert left subtree
  2. Invert right subtree  
  3. Swap left and right
```

**Observation 2: Base case**
- Null node stays null (nothing to invert)

**Observation 3: Order matters**
- Can swap before or after recursing (both work)

---

## Step 2: Brute Force Solution

### Approach
Recursively swap children for each node

### Code
```csharp
TreeNode InvertTreeBruteForce(TreeNode root) 
{
    if (root == null) return null;
    
    // Recursively invert subtrees
    TreeNode leftInverted = InvertTreeBruteForce(root.left);
    TreeNode rightInverted = InvertTreeBruteForce(root.right);
    
    // Swap left and right
    root.left = rightInverted;
    root.right = leftInverted;
    
    return root;
}
```

### Complexity
- **Time:** O(n) - visit each node once
- **Space:** O(h) - recursion stack

---

## Step 3: Optimal Solution

The brute force is already optimal!

### Clean Recursive Solution

```csharp
public class Solution 
{
    public TreeNode InvertTree(TreeNode root) 
    {
        // Base case: empty tree
        if (root == null) return null;
        
        // Swap left and right children
        TreeNode temp = root.left;
        root.left = root.right;
        root.right = temp;
        
        // Recursively invert subtrees
        InvertTree(root.left);
        InvertTree(root.right);
        
        return root;
    }
}
```

### One-Liner Swap Version
```csharp
public TreeNode InvertTree(TreeNode root) 
{
    if (root == null) return null;
    
    (root.left, root.right) = (root.right, root.left); // C# tuple swap
    
    InvertTree(root.left);
    InvertTree(root.right);
    
    return root;
}
```

### Ultra-Compact Version
```csharp
public TreeNode InvertTree(TreeNode root) 
{
    if (root == null) return null;
    
    // Swap and recurse in one statement
    TreeNode left = InvertTree(root.left);
    TreeNode right = InvertTree(root.right);
    root.left = right;
    root.right = left;
    
    return root;
}
```

---

## Alternative Implementations

### Iterative BFS (Queue)
```csharp
public TreeNode InvertTreeBFS(TreeNode root) 
{
    if (root == null) return null;
    
    var queue = new Queue<TreeNode>();
    queue.Enqueue(root);
    
    while (queue.Count > 0) 
    {
        TreeNode node = queue.Dequeue();
        
        // Swap children
        TreeNode temp = node.left;
        node.left = node.right;
        node.right = temp;
        
        // Add children to queue
        if (node.left != null) queue.Enqueue(node.left);
        if (node.right != null) queue.Enqueue(node.right);
    }
    
    return root;
}
```

### Iterative DFS (Stack)
```csharp
public TreeNode InvertTreeDFS(TreeNode root) 
{
    if (root == null) return null;
    
    var stack = new Stack<TreeNode>();
    stack.Push(root);
    
    while (stack.Count > 0) 
    {
        TreeNode node = stack.Pop();
        
        // Swap children
        TreeNode temp = node.left;
        node.left = node.right;
        node.right = temp;
        
        // Push children to stack
        if (node.left != null) stack.Push(node.left);
        if (node.right != null) stack.Push(node.right);
    }
    
    return root;
}
```

---

## Visualization

### Step-by-Step Inversion

```
Original:       4
              /   \
             2     7
            / \   / \
           1   3 6   9

Step 1: Invert at node 4
            4
          /   \
         7     2      ← Swap 2 and 7
        / \   / \
       6   9 1   3

Step 2: Recursively invert left subtree (7)
            4
          /   \
         7     2
        / \   / \
       9   6 1   3    ← Swap 6 and 9

Step 3: Recursively invert right subtree (2)
            4
          /   \
         7     2
        / \   / \
       9   6 3   1    ← Swap 1 and 3

Done! All subtrees inverted.
```

### Call Stack
```
InvertTree(4)
  Swap(2, 7)
  → InvertTree(7)
    Swap(6, 9)
    → InvertTree(9) → return 9
    → InvertTree(6) → return 6
  → InvertTree(2)
    Swap(1, 3)
    → InvertTree(3) → return 3
    → InvertTree(1) → return 1
```

---

## Edge Cases

```csharp
// Empty tree
InvertTree(null) → null

// Single node
InvertTree(new TreeNode(1)) → [1] (unchanged)

// Two nodes
//   1          1
//  /    →       \
// 2              2
InvertTree(twoNodes) → right child

// Already inverted (palindrome)
//     1
//    / \
//   2   2
//  / \ / \
// 3  4 4  3
InvertTree(palindrome) → stays same (inverts to self)
```

---

## Related Problems

- **Symmetric Tree** (LeetCode #101) - Check if tree is mirror of itself
- **Flip Binary Tree To Match Preorder** (LeetCode #971) - Selective flipping
- **Mirror Reflection** (LeetCode #858) - Related mirror concept

---

## Pattern Recognition

**When you see:**
- "Swap left and right"
- "Mirror a tree"
- "Invert/flip"

**Think:**
- Recursive swap at each node
- DFS or BFS work equally well
- Modify in-place

---

## Key Insights

1. **Both recursion orders work:** Can swap before or after recursing
2. **In-place modification:** No need for extra tree storage
3. **All traversals work:** DFS (recursive/iterative) and BFS all O(n)
4. **Simple problem, profound insight:** Max Howell (Homebrew creator) famously failed this in a Google interview
5. **Building block:** Understanding tree manipulation for harder problems

---

## Famous Interview Story

This problem became famous when **Max Howell** (creator of Homebrew) tweeted:

> "Google: 90% of our engineers use the software you wrote (Homebrew), but you can't invert a binary tree on a whiteboard so fuck off."

It sparked debate about interview practices, but also highlighted that even experienced engineers can struggle with fundamental algorithms under pressure.

**Takeaway:** Practice these foundational problems until they're muscle memory!
