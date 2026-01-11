# Serialize and Deserialize Binary Tree

**Difficulty:** Hard  
**LeetCode Problem:** #297  
**Tags:** `Binary Tree`, `DFS`, `String`, `Design`

## Problem Statement

Design an algorithm to **serialize and deserialize** a binary tree. Serialization is converting a tree to a string. Deserialization is reconstructing the tree from the string.

**Example:**
```
Input:    1
         / \
        2   3
           / \
          4   5

Serialized: "1,2,null,null,3,4,null,null,5,null,null"
```

## Optimal Solution

```csharp
public class Codec 
{
    // Encodes a tree to a single string (preorder)
    public string serialize(TreeNode root) 
    {
        if (root == null) return "null";
        
        return root.val + "," 
             + serialize(root.left) + "," 
             + serialize(root.right);
    }

    // Decodes your encoded data to tree
    public TreeNode deserialize(string data) 
    {
        var queue = new Queue<string>(data.Split(','));
        return DeserializeHelper(queue);
    }
    
    private TreeNode DeserializeHelper(Queue<string> queue) 
    {
        string val = queue.Dequeue();
        if (val == "null") return null;
        
        var node = new TreeNode(int.Parse(val));
        node.left = DeserializeHelper(queue);
        node.right = DeserializeHelper(queue);
        return node;
    }
}
```

**Complexity:** O(n) time, O(n) space for both operations

**Key Insight:** Preorder traversal with null markers allows reconstruction. Use queue for parsing during deserialization.

### Alternative: Level Order Serialization

```csharp
public string serializeBFS(TreeNode root) 
{
    if (root == null) return "";
    
    var sb = new StringBuilder();
    var queue = new Queue<TreeNode>();
    queue.Enqueue(root);
    
    while (queue.Count > 0) 
    {
        var node = queue.Dequeue();
        if (node == null) 
        {
            sb.Append("null,");
        }
        else 
        {
            sb.Append(node.val + ",");
            queue.Enqueue(node.left);
            queue.Enqueue(node.right);
        }
    }
    
    return sb.ToString();
}
```
