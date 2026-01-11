# Path Sum

**Difficulty:** Easy  
**LeetCode Problem:** #112  
**Tags:** `Binary Tree`, `DFS`, `Accumulator`

## Problem Statement

Given the root of a binary tree and an integer `targetSum`, return `true` if there exists a root-to-leaf path such that adding up all the values equals `targetSum`.

A **leaf** is a node with no children.

**Example:**
```
        5
       / \
      4   8
     /   / \
    11  13  4
   /  \      \
  7    2      1

targetSum = 22
Output: true (path: 5→4→11→2 = 22)
```

## Optimal Solution

```csharp
public class Solution 
{
    public bool HasPathSum(TreeNode root, int targetSum) 
    {
        if (root == null) return false;
        
        // Check if leaf and sum matches
        if (root.left == null && root.right == null) 
        {
            return targetSum == root.val;
        }
        
        // Recurse with reduced target
        int remaining = targetSum - root.val;
        return HasPathSum(root.left, remaining) 
            || HasPathSum(root.right, remaining);
    }
}
```

**Complexity:** O(n) time, O(h) space

**Key Insight:** Subtract current value from target, check at leaves only.
