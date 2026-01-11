# Binary Tree Level Order Traversal

**Difficulty:** Medium  
**LeetCode Problem:** #102  
**Tags:** `Binary Tree`, `BFS`, `Queue`

## Problem Statement

Given the root of a binary tree, return the **level order traversal** of its nodes' values (i.e., from left to right, level by level).

**Example:**
```
    3
   / \
  9  20
    /  \
   15   7

Output: [[3], [9,20], [15,7]]
```

## Optimal Solution

```csharp
public class Solution 
{
    public IList<IList<int>> LevelOrder(TreeNode root) 
    {
        var result = new List<IList<int>>();
        if (root == null) return result;
        
        var queue = new Queue<TreeNode>();
        queue.Enqueue(root);
        
        while (queue.Count > 0) 
        {
            int levelSize = queue.Count;
            var currentLevel = new List<int>();
            
            for (int i = 0; i < levelSize; i++) 
            {
                var node = queue.Dequeue();
                currentLevel.Add(node.val);
                
                if (node.left != null) queue.Enqueue(node.left);
                if (node.right != null) queue.Enqueue(node.right);
            }
            
            result.Add(currentLevel);
        }
        
        return result;
    }
}
```

**Complexity:** O(n) time, O(w) space (w = max width)

**Key Insight:** Process nodes level by level using queue. Track level size to group nodes.
