# Validate Binary Search Tree

**Difficulty:** Medium  
**LeetCode Problem:** #98  
**Tags:** `Binary Tree`, `BST`, `DFS`, `Recursion`, `Range Tracking`

---

## Problem Statement

Given the root of a binary tree, determine if it is a **valid binary search tree (BST)**.

A valid BST is defined as follows:
- The left subtree of a node contains only nodes with keys **less than** the node's key
- The right subtree of a node contains only nodes with keys **greater than** the node's key
- Both the left and right subtrees must also be binary search trees

**Constraints:**
- The number of nodes in the tree is in the range `[1, 10^4]`
- `-2^31 <= Node.val <= 2^31 - 1`

**Example 1:**
```
Input:    2
         / \
        1   3
Output: true
```

**Example 2:**
```
Input:    5
         / \
        1   4
           / \
          3   6
Output: false
Explanation: Root's value is 5 but right child 4 < 5
```

**Example 3:**
```
Input:      5
           / \
          4   6
             / \
            3   7
Output: false
Explanation: 6's left child is 3, but 3 < 5 (violates BST)
```

---

## Step 1: State Space

### Problem Analysis

**Input:** Binary tree with `n` nodes  
**Output:** Boolean - is valid BST?

### State Space Definition

- **Type**: All nodes in binary tree
- **Structure**: Each node must satisfy range constraint [min, max]
- **Cardinality**: n nodes to validate = **O(n)**

### Core Question

**How do we validate that EVERY node satisfies BST properties globally, not just locally?**

**Common mistake:** Just checking `left.val < node.val < right.val` is insufficient!

```
Example of local check failing:
       5
      / \
     4   6
        / \
       3   7

Local check: 3 < 6 ✓, 4 < 5 ✓, 5 < 6 ✓
BUT: 3 is in right subtree of 5, so must be > 5. INVALID!
```

### Deriving from First Principles

**Observation 1: Range constraints**
```
For any node:
  - All nodes in left subtree: value < node.val
  - All nodes in right subtree: value > node.val
```

**Observation 2: Pass down valid range**
```
validate(node, min, max):
  - Node must be in range (min, max)
  - Left subtree: range (min, node.val)
  - Right subtree: range (node.val, max)
```

**Observation 3: Initial range**
- Root can be any value: range (-∞, +∞)

---

## Step 2: Brute Force Solution

### Wrong Approach (Common Mistake)
```csharp
// ❌ WRONG - only checks local property
bool IsValidBSTWrong(TreeNode root) 
{
    if (root == null) return true;
    
    bool leftOk = root.left == null || root.left.val < root.val;
    bool rightOk = root.right == null || root.right.val > root.val;
    
    return leftOk && rightOk 
        && IsValidBSTWrong(root.left) 
        && IsValidBSTWrong(root.right);
}
```

**Why wrong?** Doesn't enforce global BST property.

### Correct Approach: Range Tracking
```csharp
bool IsValidBST(TreeNode root) 
{
    return Validate(root, long.MinValue, long.MaxValue);
}

bool Validate(TreeNode node, long min, long max) 
{
    // Base case: empty tree is valid
    if (node == null) return true;
    
    // Check if current node violates range
    if (node.val <= min || node.val >= max) return false;
    
    // Validate subtrees with updated ranges
    return Validate(node.left, min, node.val)       // Left: (min, node.val)
        && Validate(node.right, node.val, max);     // Right: (node.val, max)
}
```

### Complexity
- **Time:** O(n) - visit each node once
- **Space:** O(h) - recursion stack

---

## Step 3: Optimal Solution

### Clean Implementation

```csharp
public class Solution 
{
    public bool IsValidBST(TreeNode root) 
    {
        return Validate(root, null, null);
    }
    
    private bool Validate(TreeNode node, int? min, int? max) 
    {
        if (node == null) return true;
        
        // Check range constraints
        if ((min.HasValue && node.val <= min.Value) ||
            (max.HasValue && node.val >= max.Value))
        {
            return false;
        }
        
        // Recursively validate subtrees
        return Validate(node.left, min, node.val) 
            && Validate(node.right, node.val, max);
    }
}
```

**Using nullable int avoids long.MinValue/MaxValue handling**

---

## Alternative Implementations

### Approach 2: Inorder Traversal
**Key insight:** Inorder traversal of BST yields sorted sequence

```csharp
public bool IsValidBSTInorder(TreeNode root) 
{
    int? prev = null;
    return InorderCheck(root, ref prev);
}

private bool InorderCheck(TreeNode node, ref int? prev) 
{
    if (node == null) return true;
    
    // Check left subtree
    if (!InorderCheck(node.left, ref prev)) return false;
    
    // Check current node (must be greater than previous)
    if (prev.HasValue && node.val <= prev.Value) return false;
    prev = node.val;
    
    // Check right subtree
    return InorderCheck(node.right, ref prev);
}
```

### Approach 3: Iterative Inorder
```csharp
public bool IsValidBSTIterative(TreeNode root) 
{
    var stack = new Stack<TreeNode>();
    int? prev = null;
    TreeNode curr = root;
    
    while (curr != null || stack.Count > 0) 
    {
        // Go to leftmost node
        while (curr != null) 
        {
            stack.Push(curr);
            curr = curr.left;
        }
        
        // Process current node
        curr = stack.Pop();
        
        // Check sorted property
        if (prev.HasValue && curr.val <= prev.Value) 
            return false;
        
        prev = curr.val;
        curr = curr.right;
    }
    
    return true;
}
```

---

## Visualization

### Range Tracking Example

```
Tree:       5
           / \
          4   6
             / \
            3   7

Validation:
validate(5, -∞, +∞)
  5 in (-∞, +∞)? ✓
  → validate(4, -∞, 5)
      4 in (-∞, 5)? ✓
      → validate(null, -∞, 4) ✓
      → validate(null, 4, 5) ✓
  → validate(6, 5, +∞)
      6 in (5, +∞)? ✓
      → validate(3, 5, 6)
          3 in (5, 6)? ❌ FALSE
          
Result: Invalid BST (3 < 5 but in right subtree of 5)
```

### Inorder Traversal Example

```
Tree:    2
        / \
       1   3

Inorder: 1, 2, 3 (sorted → valid)

Tree:    5
        / \
       1   4
          / \
         3   6

Inorder: 1, 5, 3, 6 (NOT sorted: 5 > 3 → invalid)
```

---

## Edge Cases

```csharp
// Single node
IsValidBST(new TreeNode(1)) → true

// Duplicate values (NOT allowed)
//   2
//  / \
// 2   2
IsValidBST(duplicates) → false (BST requires strictly less/greater)

// Integer boundaries
//   INT_MIN
//        \
//         INT_MAX
IsValidBST(boundaries) → true (use long or nullable to handle)

// Large tree but one invalid node
//        10
//       /  \
//      5    15
//          /  \
//         6    20
IsValidBST(largeInvalid) → false (6 < 10, invalid in right subtree)
```

---

## Common Pitfalls

### ❌ Pitfall 1: Only Checking Children
```csharp
// WRONG
if (node.left.val < node.val && node.right.val > node.val)
```
**Problem:** Doesn't check grandchildren and beyond

### ❌ Pitfall 2: Not Handling Duplicates
```csharp
// WRONG
if (node.val < min || node.val > max) // Should use <= and >=
```
**Problem:** BST doesn't allow equal values

### ❌ Pitfall 3: Integer Overflow
```csharp
// WRONG
int min = int.MinValue;
```
**Problem:** If tree contains int.MinValue, range check fails
**Solution:** Use `long` or `nullable int`

---

## Related Problems

- **Validate Binary Tree Nodes** (LeetCode #1361) - Check if valid tree structure
- **Convert Sorted Array to BST** (LeetCode #108) - Construct balanced BST
- **Kth Smallest in BST** (LeetCode #230) - Inorder traversal application
- **Recover BST** (LeetCode #99) - Fix swapped nodes in BST

---

## Pattern Recognition

**When you see:**
- "Validate BST"
- "Check if binary search tree"
- "Verify tree properties globally"

**Think:**
- Range tracking (min/max constraints)
- OR Inorder traversal (should be sorted)
- NOT just local parent-child comparison

---

## Key Insights

1. **Local check insufficient** - must validate global property
2. **Two valid approaches:** Range tracking OR inorder traversal
3. **Range method more intuitive** - directly enforces BST definition
4. **Inorder method elegant** - leverages BST sorted property
5. **Handle edge values carefully** - use long or nullable
6. **Duplicates invalid** - BST requires strict inequalities
