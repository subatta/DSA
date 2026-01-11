# Pattern Title: Binary Tree Traversal & Properties

## Pattern Description:
- **What is it?**  
  Binary Tree pattern encompasses techniques for traversing, analyzing, and manipulating tree structures where each node has at most two children (left and right). Core techniques include DFS (preorder, inorder, postorder), BFS (level-order), and recursive property checking.

- **What abstract problem does it solve?**  
  Efficiently exploring hierarchical data structures, computing aggregate properties (height, diameter, balance), validating tree constraints (BST properties, symmetry), and transforming tree structures.

- **Core Optimization:**  
  Recursion naturally matches tree structure (divide into left/right subtrees), while BFS with queues handles level-by-level processing. Most operations achieve O(n) time by visiting each node once.

## State Space Context:
- **Primary State Space**: All nodes in tree - O(n) nodes to visit
- **Traversal Orders**: Different visiting sequences reveal different properties
- **Key Insight**: Recursive solutions exploit tree's recursive structure (node + left subtree + right subtree)

## Canonical Code Skeleton:

### **DFS Recursive (Most Common)**
```csharp
int DFS(TreeNode root) 
{
    // Base case: null node
    if (root == null) return 0; // or other base value
    
    // Recursive case: process left and right subtrees
    int left = DFS(root.left);
    int right = DFS(root.right);
    
    // Combine results with current node
    return CombineResults(left, right, root.val);
}
```

### **BFS Iterative (Level Order)**
```csharp
void BFS(TreeNode root) 
{
    if (root == null) return;
    
    var queue = new Queue<TreeNode>();
    queue.Enqueue(root);
    
    while (queue.Count > 0) 
    {
        int levelSize = queue.Count;
        
        // Process entire level
        for (int i = 0; i < levelSize; i++) 
        {
            var node = queue.Dequeue();
            // Process node
            
            if (node.left != null) queue.Enqueue(node.left);
            if (node.right != null) queue.Enqueue(node.right);
        }
    }
}
```

## Pattern Variants (8 Total)

### 🟢 Easy (4 variants)
**Master basic tree traversal and simple properties**

1. **[Maximum Depth of Binary Tree](variants/binary_tree/variant_1_max_depth.md)** - LeetCode #104 ⭐
   - **Concept:** Recursive DFS, height = 1 + max(left, right)
   - **Complexity:** O(n) time, O(h) space (recursion stack)
   - **Why Easy:** Simple recursive template, single property to track

2. **[Invert Binary Tree](variants/binary_tree/variant_2_invert_tree.md)** - LeetCode #226 ⭐
   - **Concept:** Swap left/right children recursively
   - **Complexity:** O(n) time, O(h) space
   - **Why Easy:** Straightforward recursion, in-place modification

3. **[Diameter of Binary Tree](variants/binary_tree/variant_3_diameter.md)** - LeetCode #543
   - **Concept:** Longest path through any node = left_height + right_height
   - **Complexity:** O(n) time, O(h) space
   - **Why Easy:** Extension of max depth, track global maximum

4. **[Path Sum](variants/binary_tree/variant_4_path_sum.md)** - LeetCode #112
   - **Concept:** DFS tracking running sum, check at leaves
   - **Complexity:** O(n) time, O(h) space
   - **Why Easy:** Simple DFS with accumulator

### 🟡 Medium (3 variants)
**Handle tree validation and level-order processing**

5. **[Validate Binary Search Tree](variants/binary_tree/variant_5_validate_bst.md)** - LeetCode #98 ⭐⭐
   - **Concept:** Track valid range [min, max] for each subtree
   - **Complexity:** O(n) time, O(h) space
   - **Why Medium:** Requires range tracking, not just local comparison

6. **[Binary Tree Level Order Traversal](variants/binary_tree/variant_6_level_order.md)** - LeetCode #102 ⭐
   - **Concept:** BFS with queue, track level boundaries
   - **Complexity:** O(n) time, O(w) space (w = max width)
   - **Why Medium:** Iterative queue management, level grouping

7. **[Lowest Common Ancestor](variants/binary_tree/variant_7_lowest_common_ancestor.md)** - LeetCode #236 ⭐⭐
   - **Concept:** Recurse until finding both nodes, return first common ancestor
   - **Complexity:** O(n) time, O(h) space
   - **Why Medium:** Non-obvious recursive logic, multiple base cases

### 🔴 Hard (1 variant)
**Master complex tree transformations**

8. **[Serialize and Deserialize Binary Tree](variants/binary_tree/variant_8_serialize_deserialize.md)** - LeetCode #297 ⭐⭐
   - **Concept:** Convert tree to string (preorder) and reconstruct
   - **Complexity:** O(n) time, O(n) space
   - **Why Hard:** Bidirectional transformation, parsing logic

### Practice Progression
1. Start with **Maximum Depth** (#1) - learn basic DFS recursion
2. Practice **Invert Tree** (#2) - modify tree structure
3. Master **Diameter** (#3) - track global state during recursion
4. Learn **Path Sum** (#4) - accumulator pattern
5. Tackle **Validate BST** (#5) - range constraints, tricky edge cases
6. Master **Level Order** (#6) - BFS template for all level problems
7. Study **Lowest Common Ancestor** (#7) - advanced recursion pattern
8. Complete **Serialize/Deserialize** (#8) - hardest, design problem

---

## Common Tree Traversal Orders

### **DFS Traversals:**
```
       1
      / \
     2   3
    / \
   4   5

Preorder (Root→Left→Right):  1, 2, 4, 5, 3
Inorder (Left→Root→Right):   4, 2, 5, 1, 3
Postorder (Left→Right→Root): 4, 5, 2, 3, 1
```

### **BFS Traversal (Level Order):**
```
Level 0: [1]
Level 1: [2, 3]
Level 2: [4, 5]
```

---

## Key Insights

1. **Recursion matches tree structure perfectly** - most problems are 3-5 lines recursive
2. **Height = O(log n) for balanced trees, O(n) for skewed trees**
3. **Space complexity = O(h)** for recursion stack (not O(n) unless storing results)
4. **Null checks first** - always handle `root == null` before accessing properties
5. **Inorder traversal of BST yields sorted order**
6. **Level order = BFS** - use queue and track level size
7. **Many problems need global state** - use class variables or pass by reference

---

## When to Use This Pattern

✅ **Use when:**
- Problem involves binary tree data structure
- Need to traverse all nodes or check properties
- Looking for paths, heights, depths, or structural properties
- Need to validate tree constraints (BST, balanced, etc.)
- Transform or serialize tree structure

❌ **Don't use when:**
- Problem is about arrays or sequences (use different pattern)
- Tree is N-ary (children > 2) - adapt techniques
- Graph with cycles (trees are acyclic by definition)

---

## Related Patterns

- **Fast & Slow Pointers** - Finding middle of linked list (similar to tree height)
- **Backtracking** - Tree path exploration shares recursive structure
- **BFS/Graph Traversal** - Level order traversal is tree-specific BFS
- **Dynamic Programming** - Some tree problems (max path sum) combine DP with DFS
