# Diameter of Binary Tree

**Difficulty:** Easy  
**LeetCode Problem:** #543  
**Tags:** `Binary Tree`, `DFS`, `Global State`

## Problem Statement

Given the root of a binary tree, return the **length of the diameter** of the tree.

The diameter is the **length of the longest path** between any two nodes. The path may or may not pass through the root.

**Example:**
```
      1
     / \
    2   3
   / \
  4   5

Diameter = 3 (path: 4→2→1→3 or 5→2→1→3)
```

## Optimal Solution

```csharp
public class Solution 
{
    private int maxDiameter = 0;
    
    public int DiameterOfBinaryTree(TreeNode root) 
    {
        Height(root);
        return maxDiameter;
    }
    
    private int Height(TreeNode node) 
    {
        if (node == null) return 0;
        
        int leftHeight = Height(node.left);
        int rightHeight = Height(node.right);
        
        // Update diameter: path through this node
        maxDiameter = Math.Max(maxDiameter, leftHeight + rightHeight);
        
        // Return height for parent
        return 1 + Math.Max(leftHeight, rightHeight);
    }
}
```

**Complexity:** O(n) time, O(h) space

**Key Insight:** Diameter through any node = left_height + right_height. Track maximum globally while computing heights.
