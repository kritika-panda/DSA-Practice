# Merge Two Binary Trees

## Problem

You are given two binary trees `root1` and `root2`.

Merge them according to the following rules:

- If two nodes overlap, sum their values.
- If only one node exists, use that node.

Return the merged tree.

### Example

**Input**

```text
Tree 1: Tree 2:

     1 2
    / \ / \
   3 2 1 3
  / \ \
 5 4 7
```

**Output**

```text
       3
      / \
     4 5
    / \ \
   5 4 7
```

---

## Approach

Use recursion.

For each pair of nodes:

1. If `root1` is null, return `root2`.
2. If `root2` is null, return `root1`.
3. Add both node values.
4. Merge left children recursively.
5. Merge right children recursively.

---

## Java Solution

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 * int val;
 * TreeNode left;
 * TreeNode right;
 *
 * TreeNode() {}
 *
 * TreeNode(int val) {
 * this.val = val;
 * }
 *
 * TreeNode(int val, TreeNode left, TreeNode right) {
 * this.val = val;
 * this.left = left;
 * this.right = right;
 * }
 * }
 */
class Solution {

    public TreeNode mergeTrees(TreeNode root1, TreeNode root2) {

        if (root1 == null) {
            return root2;
        }

        if (root2 == null) {
            return root1;
        }

        root1.val += root2.val;

        root1.left = mergeTrees(root1.left, root2.left);
        root1.right = mergeTrees(root1.right, root2.right);

        return root1;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** `O(n)`
  
  Where `n` is the total number of nodes visited.

- **Space Complexity:** `O(h)`
  
  Due to recursion stack.

  - Balanced Tree: `O(log n)`
  - Skewed Tree: `O(n)`

---

## Key Idea

Traverse both trees simultaneously. When nodes overlap, sum their values. When only one node exists, reuse it directly.
