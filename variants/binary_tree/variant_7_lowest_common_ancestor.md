# Lowest Common Ancestor

**Difficulty:** Medium  
**LeetCode Problem:** #236  
**Tags:** `Binary Tree`, `DFS`, `Recursion`

## Problem Statement

Given a binary tree, find the **lowest common ancestor (LCA)** of two given nodes `p` and `q`.

The LCA is the lowest node that has both `p` and `q` as descendants (a node can be a descendant of itself).

**Example:**
```
        3
       / \
      5   1
     / \ / \
    6  2 0  8
      / \
     7   4

LCA(5, 1) = 3
LCA(5, 4) = 5
```

## Optimal Solution

```csharp
public class Solution 
{
    public TreeNode LowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) 
    {
        // Base case: found target or reached null
        if (root == null || root == p || root == q) 
            return root;
        
        // Search in subtrees
        TreeNode left = LowestCommonAncestor(root.left, p, q);
        TreeNode right = LowestCommonAncestor(root.right, p, q);
        
        // If both found in different subtrees, current node is LCA
        if (left != null && right != null) 
            return root;
        
        // Otherwise return whichever is not null
        return left != null ? left : right;
    }
}
```

**Complexity:** O(n) time, O(h) space

**Key Insight:** If p and q are in different subtrees, current node is LCA. If both in same subtree, return that result.
