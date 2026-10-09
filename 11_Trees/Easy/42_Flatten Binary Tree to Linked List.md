  # Flatten Binary Tree to Linked List - Java

 

## Problem Statement

 

Given the `root` of a binary tree, flatten the tree into a "linked list":

 

- The "linked list" should use the same `TreeNode` class where the `right` child pointer points to the next node in the list and the `left` child pointer is always `null`.

- The "linked list" should be in the same order as a **pre-order traversal** of the binary tree.

 

The flattening must be done **in-place** by modifying the node pointers directly.

 

---

 

## Example

 

### Input

 

```text

        1

       / \

      2   5

     / \   \

    3   4   6

```

 

### Output

 

```text

1 -> null

\

  2 -> null

   \

    3 -> null

     \

      4 -> null

       \

        5 -> null

         \

          6 -> null

```

 

---

 

# Approach

 

## Idea

 

A standard recursive pre-order traversal follows the sequence **Root → Left → Right**. If we flatten the subtrees recursively using the reverse of pre-order (**Right → Left → Root**), we can easily stitch the nodes together in-place.

 

By maintaining a global or tracking variable `prev` that holds the node processed just before the current one:

1. Flatten the right subtree first.

2. Flatten the left subtree second.

3. For the current root node, set its `right` pointer to `prev` and its `left` pointer to `null`.

4. Update `prev` to point to the current node.

 

As the recursion unwinds back to the top, each node gets cleanly attached to the front of the growing right-linked chain.

 

---

 

## Algorithm

 

1. Initialize a pointer variable `prev` to `null`.

2. Create a recursive function `flatten(TreeNode root)`:

   - If `root` is null, return.

   - Recurse down the right child: `flatten(root.right)`.

   - Recurse down the left child: `flatten(root.left)`.

   - Set the current node's `right` pointer to `prev`.

   - Set the current node's `left` pointer to `null`.

   - Update `prev = root`.

 

---

 

## Java Solution

 

```java

public class FlattenBinaryTree {

    static class TreeNode {

        int val;

        TreeNode left;

        TreeNode right;

        TreeNode(int val) {

            this.val = val;

        }

    }

 

    // Global tracking variable for the previously processed node

    private static TreeNode prev = null;

 

    public static void flatten(TreeNode root) {

        // Reset prev tracker for every fresh invocation loop

        prev = null;

        flattenHelper(root);

    }

 

    private static void flattenHelper(TreeNode root) {

        if (root == null) {

            return;

        }

 

        // Step 1: Traverse the right subtree first

        flattenHelper(root.right);

 

        // Step 2: Traverse the left subtree second

        flattenHelper(root.left);

 

        // Step 3: Rearrange current node pointers in place

        root.right = prev;

        root.left = null;

 

        // Step 4: Move the prev marker up to the current node

        prev = root;

    }

 

    public static void printFlattenedTree(TreeNode root) {

        TreeNode curr = root;

        while (curr != null) {

            System.out.print(curr.val + " -> ");

            if (curr.left != null) {

                System.out.print("(Error: Left not null) ");

            }

            curr = curr.right;

        }

        System.out.println("null");

    }

 

    public static void main(String[] args) {

        TreeNode root = new TreeNode(1);

        root.left = new TreeNode(2);

        root.right = new TreeNode(5);

        root.left.left = new TreeNode(3);

        root.left.right = new TreeNode(4);

        root.right.right = new TreeNode(6);

 

        flatten(root);

        printFlattenedTree(root);

    }

}

```

 

---

 

## Dry Run

 

Using the example tree:

 

### Initial State

`prev = null`

 

### Step 1: Deepest Right Path

The recursion skips down to the rightmost leaf, Node 6.

- `flattenHelper(6.right)` -> Returns null.

- `flattenHelper(6.left)` -> Returns null.

- `6.right = prev` (`null`)

- `6.left = null`

- Update `prev = Node 6`.

 

### Step 2: Move to Node 5

- `flattenHelper(5.right)` processed Node 6.

- `flattenHelper(5.left)` -> Returns null.

- `5.right = prev` (`Node 6`)

- `5.left = null`

- Update `prev = Node 5`.

 

### Step 3: Move to Left Subtree Branch (Node 4)

- Node 4 has no children.

- `4.right = prev` (`Node 5`)

- `4.left = null`

- Update `prev = Node 4`.

 

### Step 4: Move to Node 3

- Node 3 has no children.

- `3.right = prev` (`Node 4`)

- `3.left = null`

- Update `prev = Node 3`.

 

### Step 5: Move to Node 2

- `2.right` points to Node 4 chain, `2.left` points to Node 3 chain.

- `2.right = prev` (`Node 3`)

- `2.left = null`

- Update `prev = Node 2`.

 

### Step 6: Return to Root Node 1

- `1.right = prev` (`Node 2`)

- `1.left = null`

- Final link sequence: `1 -> 2 -> 3 -> 4 -> 5 -> 6 -> null`.

 

---

 

## Visual Representation

 

```text

       1                    1                      1

      / \                    \                      \

     2   5    ======>         2        ======>       2

    / \   \                    \                      \

   3   4   6                    3                      3

                                 \                      \

                                  4                      4

                                   \                      \

                                    5                      5

                                     \                      \

                                      6                      6

```

 

---

 

## Edge Cases

 

### Empty Tree

```text

Input: null

Output: No modifications occur.

```

 

### Single Node

```text

Input: 1

Output: 1 -> null

```

 

### Right-Skewed Tree

```text

1 -> 2 -> 3

```

Output: Stays structurally identical; left pointers remain null.

 

---

 

## Time Complexity

 

```text

O(N)

```

Where `N` is the total number of nodes in the binary tree. Every single node is visited exactly once during the reverse pre-order recursion.

 

---

 

## Space Complexity

 

```text

O(H)

```

Where `H` is the height of the tree, representing the recursion stack overhead. In the worst case (a completely skewed tree), this scales to `O(N)`. For a balanced tree, it remains `O(log N)`.

 

---

 

## Interview Follow-Up Questions

 

### Q1. Can this problem be solved in O(1) constant auxiliary space?

Yes. You can achieve `O(1)` space using **Morris Traversal** logic. Starting at the root, if the current node has a left child, find the rightmost node in that left child's subtree (the pre-order predecessor). Link that predecessor's `right` pointer directly to the current node's original `right` child. Then, move the entire left subtree to the right side (`root.right = root.left`, `root.left = null`) and step forward to the next node on the right.

 

### Q2. Why does the standard Pre-order (Root -> Left -> Right) recursion fail without an external stack?

If you try to process nodes top-down in normal pre-order sequence (`root.right = root.left`), you overwrite the reference to the original right child before you have a chance to visit it. This breaks the tree connectivity unless you copy or store the right branches beforehand.

 

---

 

## Important Interview Takeaways

 

- ✅ Reverse pre-order traversal (**Right → Left → Root**) prevents premature pointer overwriting.

- ✅ Maintain a trailing `prev` pointer reference to build the link chain from bottom to top.

- ✅ Explicitly clear out the `left` pointers to satisfy the right-skewed linked list structure requirement.

- ✅ Time Complexity = O(N)

- ✅ Space Complexity = O(H)

- ✅ Frequently Asked In:

  - Microsoft

  - Amazon

  - Facebook (Meta)

  - Google

- ✅ Related Problems:

  - Binary Tree Preorder Traversal

  - Convert Binary Search Tree to Sorted Doubly Linked List

  - Populating Next Right Pointers in Each Node
