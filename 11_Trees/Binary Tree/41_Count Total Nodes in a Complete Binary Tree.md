# Count Total Nodes in a Complete Binary Tree - Java

 

## Problem Statement

 

Given the `root` of a **complete binary tree**, return the number of nodes in the tree.

 

According to Wikipedia, a complete binary tree is a binary tree in which every level, except possibly the last, is completely filled, and all nodes in the last level are as far left as possible. It can have between `1` and `2^h` nodes at the last level `h`.

 

---

 

## Example

 

### Input

 

```text

        1

       / \

      2   3

     / \  /

    4   5 6

```

 

### Output

 

```text

6

```

 

---

 

# Approach

 

## Idea

 

A naive recursive approach visits every single node, taking `O(N)` time. We can optimize this by using the properties of a **Complete Binary Tree**:

1. Find the height of the tree by traversing only the **leftmost** path (`leftHeight`).

2. Find the height of the tree by traversing only the **rightmost** path (`rightHeight`).

3. If `leftHeight == rightHeight`, the subtree is a **perfect binary tree**. The total number of nodes in a perfect binary tree of height `h` is exactly `2^h - 1` (which can be efficiently computed using bitwise shift: `(1 << h) - 1`). We can return this value immediately in `O(1)` time without visiting intermediate child nodes.

4. If `leftHeight != rightHeight`, the left and right subtrees are not both perfect at this root level, so we fallback to a standard recursive count: `1 + countNodes(root.left) + countNodes(root.right)`.

 

Because it is a complete binary tree, at least one of the subtrees at any division point is guaranteed to be a perfect binary tree, significantly cutting down execution paths.

 

---

 

## Algorithm

 

1. If the `root` is null, return `0`.

2. Compute `leftHeight` by traversing down left child pointers continuously.

3. Compute `rightHeight` by traversing down right child pointers continuously.

4. If `leftHeight == rightHeight`, return `(1 << leftHeight) - 1`.

5. Otherwise, return `1 + countNodes(root.left) + countNodes(root.right)`.

 

---

 

## Java Solution

 

```java

public class CountCompleteTreeNodes {

    static class TreeNode {

        int val;

        TreeNode left;

        TreeNode right;

        TreeNode(int val) {

            this.val = val;

        }

    }

 

    public static int countNodes(TreeNode root) {

        if (root == null) {

            return 0;

        }

 

        int leftHeight = getLeftHeight(root);

        int rightHeight = getRightHeight(root);

 

        // If left and right heights are equal, it's a perfect binary tree

        if (leftHeight == rightHeight) {

            return (1 << leftHeight) - 1; // Equivalent to 2^leftHeight - 1

        }

 

        // Otherwise, recursively count standard nodes

        return 1 + countNodes(root.left) + countNodes(root.right);

    }

 

    private static int getLeftHeight(TreeNode node) {

        int height = 0;

        while (node != null) {

            height++;

            node = node.left;

        }

        return height;

    }

 

    private static int getRightHeight(TreeNode node) {

        int height = 0;

        while (node != null) {

            height++;

            node = node.right;

        }

        return height;

    }

 

    public static void main(String[] args) {

        TreeNode root = new TreeNode(1);

        root.left = new TreeNode(2);

        root.right = new TreeNode(3);

        root.left.left = new TreeNode(4);

        root.left.right = new TreeNode(5);

        root.right.left = new TreeNode(6);

 

        System.out.println("Total nodes: " + countNodes(root));

    }

}

```

 

---

 

## Dry Run

 

Using the example tree:

 

### Step 1: Root Node 1

- `getLeftHeight(Node 1)` scans left path (`1 -> 2 -> 4`) -> `leftHeight = 3`

- `getRightHeight(Node 1)` scans right path (`1 -> 3 -> null`) -> `rightHeight = 2`

- `leftHeight != rightHeight` (`3 != 2`). Fallback to `1 + countNodes(Node 2) + countNodes(Node 3)`.

 

### Step 2: Node 2 (Left Subtree)

- `getLeftHeight(Node 2)` scans left path (`2 -> 4`) -> `leftHeight = 2`

- `getRightHeight(Node 2)` scans right path (`2 -> 5`) -> `rightHeight = 2`

- `leftHeight == rightHeight` (`2 == 2`). Perfect subtree shortcut triggers!

- Return `(1 << 2) - 1 = 3`. (Nodes: 2, 4, 5 are successfully counted without deeper recursive visits).

 

### Step 3: Node 3 (Right Subtree)

- `getLeftHeight(Node 3)` scans left path (`3 -> 6`) -> `leftHeight = 2`

- `getRightHeight(Node 3)` scans right path (`3 -> null`) -> `rightHeight = 1`

- `leftHeight != rightHeight` (`2 != 1`). Fallback to `1 + countNodes(Node 6) + countNodes(null)`.

 

### Step 4: Node 6

- `getLeftHeight(Node 6)` -> `1`

- `getRightHeight(Node 6)` -> `1`

- Matches! Return `(1 << 1) - 1 = 1`.

 

### Step 5: Final Evaluation

- Node 3 returns: `1 + 1 (from Node 6) + 0 (from null) = 2`.

- Root Node 1 aggregates: `1 + 3 (from Node 2) + 2 (from Node 3) = 6`.

 

---

 

## Visual Representation

 

```text

            1                  -> leftHeight = 3, rightHeight = 2 (No match)

           / \

          2   3                -> Node 2: leftHeight = 2, rightHeight = 2 (Match! Shortcut = 3)

         / \  /                -> Node 3: leftHeight = 2, rightHeight = 1 (No match)

        4   5 6                -> Node 6: leftHeight = 1, rightHeight = 1 (Match! Shortcut = 1)

```

 

---

 

## Edge Cases

 

### Empty Tree

```text

Input: null

Output: 0

```

 

### Single Node

```text

Input: 1

Output: 1

```

 

### Full Perfect Binary Tree

```text

      1

     / \

    2   3

```

Output:

```text

3

```

*(Triggers the perfect binary shortcut on the first step at the root level)*

 

---

 

## Time Complexity

 

```text

O(log^2 N)

```

Finding the height of a subtree takes `O(log N)` steps. At each level of the recursion, we perform height calculations. Since the input is guaranteed to be a complete tree, one of the split subtrees is always perfect and terminates in `O(1)` time. This leaves only one branch to recurse down, leading to a recurrence relation of `T(N) = T(N/2) + O(log N)`, which solves mathematically to `O(log^2 N)`.

 

---

 

## Space Complexity

 

```text

O(log N)

```

The space is consumed strictly by the recursive call stack frames, which scale linearly with the maximum height of the complete binary tree (`log N`).

 

---

 

## Interview Follow-Up Questions

 

### Q1. Can we count nodes using binary search?

Yes. Because the nodes in the last level are filled from left to right, we can think of the leaf nodes as a sorted array of existence. We can perform a binary search on the leaf indices ranging from `0` to `2^h - 1` to find the last valid node path. Each path validation takes `O(log N)` time, making the overall binary search runtime `O(log^2 N)`, matching the time complexity of our recursive height approach.

 

### Q2. What happens if the input tree is not a complete binary tree?

If the tree is completely unbalanced or arbitrary, the height matching check `leftHeight == rightHeight` will rarely match except at leaf positions. The code will default to standard recursion across all branches, causing the execution timeline to degrade to a standard fallback of `O(N)`.

 

---

 

## Important Interview Takeaways

 

- ✅ Leverage subtree height checks to identify structural optimization shortcuts.

- ✅ Use bit shifting `(1 << height) - 1` to compute perfect tree contents in `O(1)` time.

- ✅ Capitalize on complete binary tree properties to drop runtime down from `O(N)` to `O(log^2 N)`.

- ✅ Frequently Asked In:

  - Google

  - Amazon

  - Meta (Facebook)

  - Microsoft

- ✅ Related Problems:

  - Validate Binary Search Tree

  - Maximum Depth of Binary Tree

  - Balanced Binary Tree

