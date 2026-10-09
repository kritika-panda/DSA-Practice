# Boundary Traversal of Binary Tree - Java

 

## Problem Statement

 

Given a binary tree, return the values of its boundary nodes in anti-clockwise direction starting from the root.

 

The boundary traversal consists of:

1. **Root node** (added first, unless it's a leaf and handled by leaf collection).

2. **Left boundary**: Leftmost nodes from top to bottom, excluding leaf nodes. If a node doesn't have a left child, use its right child.

3. **Leaf nodes**: All leaf nodes of the binary tree from left to right.

4. **Right boundary**: Rightmost nodes from bottom to top, excluding leaf nodes. If a node doesn't have a right child, use its left child.

 

---

 

## Example

 

### Input

 

```text

        1

       / \

      2   3

     / \   \

    4   5   6

       / \

      7   8

```

 

### Output

 

```text

[1, 2, 4, 7, 8, 6, 3]

```

 

### Boundary Breakdown

 

```text

Root           : [1]

Left Boundary  : [2, 4]

Leaf Nodes     : [7, 8, 6]

Right Boundary : [3] (in reverse bottom-up order: 3) -> Wait, 3 is right boundary, let's verify exact components.

```

 

---

 

# Approach

 

## Idea

 

Break the problem into three distinct, manageable parts executed sequentially:

1. **Traverse and add the Left Boundary**: Walk down the left side, adding non-leaf nodes.

2. **Collect all Leaf Nodes**: Perform a standard traversal (preorder/inorder/postorder) to collect all leaves from left to right.

3. **Traverse and add the Right Boundary**: Walk down the right side, storing non-leaf nodes in a temporary structure (like a stack or list) so they can be added in reverse order (bottom-up).

 

---

 

## Algorithm

 

1. If root is null, return an empty result list.

2. If root is not a leaf, add `root.val` to the result.

3. Call helper `addLeftBoundary(root.left, result)`:

   - While node is not null and not a leaf, add `node.val`, then move to `node.left` (if null, move to `node.right`).

4. Call helper `addLeaves(root, result)`:

   - Recursively traverse the tree; if a node is a leaf (`left == null && right == null`), add its value to result.

5. Call helper `addRightBoundary(root.right, result)`:

   - Walk down the right path, pushing non-leaf nodes into a temporary list or stack, then append them to result in reverse order.

6. Return result.

 

---

 

## Java Solution

 

```java

import java.util.*;

 

public class BoundaryTraversal {

    static class TreeNode {

        int val;

        TreeNode left;

        TreeNode right;

        TreeNode(int val) {

            this.val = val;

        }

    }

 

    public static boolean isLeaf(TreeNode node) {

        return node.left == null && node.right == null;

    }

 

    public static void addLeftBoundary(TreeNode node, List<Integer> res) {

        TreeNode curr = node;

        while (curr != null) {

            if (!isLeaf(curr)) {

                res.add(curr.val);

            }

            if (curr.left != null) {

                curr = curr.left;

            } else {

                curr = curr.right;

            }

        }

    }

 

    public static void addRightBoundary(TreeNode node, List<Integer> res) {

        TreeNode curr = node;

        List<Integer> temp = new ArrayList<>();

        while (curr != null) {

            if (!isLeaf(curr)) {

                temp.add(curr.val);

            }

            if (curr.right != null) {

                curr = curr.right;

            } else {

                curr = curr.left;

            }

        }

        // Reverse to get bottom-up order

        for (int i = temp.size() - 1; i >= 0; i--) {

            res.add(temp.get(i));

        }

    }

 

    public static void addLeaves(TreeNode node, List<Integer> res) {

        if (node == null) return;

        if (isLeaf(node)) {

            res.add(node.val);

            return;

        }

        addLeaves(node.left, res);

        addLeaves(node.right, res);

    }

 

    public static List<Integer> boundaryOfBinaryTree(TreeNode root) {

        List<Integer> result = new ArrayList<>();

        if (root == null) return result;

 

        if (!isLeaf(root)) {

            result.add(root.val);

        }

 

        addLeftBoundary(root.left, result);

        addLeaves(root, result);

        addRightBoundary(root.right, result);

 

        return result;

    }

 

    public static void main(String[] args) {

        TreeNode root = new TreeNode(1);

        root.left = new TreeNode(2);

        root.right = new TreeNode(3);

        root.left.left = new TreeNode(4);

        root.left.right = new TreeNode(5);

        root.right.right = new TreeNode(6);

        root.left.right.left = new TreeNode(7);

        root.left.right.right = new TreeNode(8);

 

        System.out.println(boundaryOfBinaryTree(root));

    }

}

```

 

---

 

## Visual Representation

 

```text

          1 (Root)

         / \

        2   3

       / \   \

      4   5   6

         / \

        7   8

```

- **Left Boundary**: `2`, `4`

- **Leaves**: `7`, `8`, `6`

- **Right Boundary (reversed)**: `3`

- **Combined Anti-clockwise**: `[1, 2, 4, 7, 8, 6, 3]`

 

---

 

## Edge Cases

 

### Empty Tree

```text

Input: null

Output: []

```

 

### Single Node

```text

    1

```

Output:

```text

[1]

```

 

---

 

## Time Complexity

 

```text

O(N)

```

Visiting the left boundary takes `O(H)`, right boundary takes `O(H)`, and collecting all leaves takes `O(N)`, where `N` is the total number of nodes and `H` is the height of the tree. Overall runtime is `O(N)`.

 

---

 

## Space Complexity

 

```text

O(H)

```

Space is consumed by the recursion stack for finding leaf nodes and the temporary list for the right boundary, which scales with the height of the tree `H` (or `O(N)` in the worst skewed case).

 

---

 

## Interview Follow-Up Questions

 

### Q1. Why do we store the right boundary in a temporary list before adding it?

The right boundary must be added in bottom-up (reverse) order. Using a temporary list and iterating backwards achieves this easily without complex pointer manipulations.

 

### Q2. How do you handle duplicate node additions if a tree has only a single left/right child?

We explicitly check `!isLeaf(curr)` during boundary traversals so that leaf nodes are not added twice (once by the boundary routine and once by the leaf collection routine).

 

---

 

## Important Interview Takeaways

 

- ✅ Divide boundary traversal into Root -> Left Boundary -> Leaves -> Right Boundary

- ✅ Exclude leaf nodes during left and right boundary scans

- ✅ Reverse right boundary elements to ensure anti-clockwise orientation

- ✅ Time Complexity = O(N)

- ✅ Space Complexity = O(H)

- ✅ Frequently Asked In:

  - Amazon

  - Microsoft

  - Accolite

  - Goldman Sachs

- ✅ Related Problems:

  - Binary Tree Zigzag Level Order Traversal

  - Vertical Order Traversal of Binary Tree

  - Diagonal Traversal of Binary Tree
