# Invert Binary Tree

## Problem

Given the `root` of a binary tree, invert the tree and return its root.

### Example

**Input**

```text
      4
    /   \
   2     7
  / \   / \
 1   3 6   9
```

**Output**

```text
      4
    /   \
   7     2
  / \   / \
 9   6 3   1
```

---

## Approach

For every node:

1. Swap its left and right child.
2. Recursively invert the left subtree.
3. Recursively invert the right subtree.
4. Return the current node.

This works because every subtree is inverted independently.

---

## Java Solution

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {

    public TreeNode invertTree(TreeNode root) {
        if (root == null) {
            return null;
        }

        TreeNode temp = root.left;
        root.left = root.right;
        root.right = temp;

        invertTree(root.left);
        invertTree(root.right);

        return root;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** `O(n)`
  
  Every node is visited exactly once.

- **Space Complexity:** `O(h)`
  
  `h` is the height of the tree due to recursion.

  - Balanced Tree: `O(log n)`
  - Skewed Tree: `O(n)`

---

## Key Idea

Recursively swap the left and right child of every node in the tree.
