 # Lowest Common Ancestor of a Binary Tree - Java

 

## Problem Statement

 

Given a binary tree, find the lowest common ancestor (LCA) of two given nodes, `p` and `q`.

 

According to the definition of LCA on Wikipedia: “The lowest common ancestor is defined between two nodes `p` and `q` as the lowest node in `T` that has both `p` and `q` as descendants (where we allow **a node to be a descendant of itself**).”

 

---

 

## Example

 

### Input

 

```text

        3

       / \

      5   1

     / \ / \

    6   2 0  8

       / \

      7   4

```

`p = 5`, `q = 1`

 

### Output

 

```text

3

```

 

### Explanation

 

The lowest common ancestor of nodes `5` and `1` is `3`, as it is the lowest node that contains both `5` and `1` as descendants.

 

---

 

# Approach

 

## Idea

 

We can solve this problem using a bottom-up **Depth First Search (DFS)** recursion approach.

 

We search for nodes `p` and `q` in the left and right subtrees:

- If the current node matches either `p` or `q`, we return the current node up the call stack.

- We recursively analyze the left subtree and right subtree results.

- **LCA Decision Rule**:

  - If both the left and right recursive calls return a non-null node, it means `p` is found in one subtree and `q` is found in the other. Therefore, the current node is their lowest common ancestor.

  - If only one side returns a non-null node, it means both `p` and `q` exist along that specific branch, or only one was found and the other is nested below it. We pass that non-null result up.

  - If both sides return null, neither node exists below this branch.

 

---

 

## Algorithm

 

1. **Base Case**: If the current node is `null`, or matches `p`, or matches `q`, return the current node.

2. Recurse for the left subtree: `leftResult = lowestCommonAncestor(root.left, p, q)`.

3. Recurse for the right subtree: `rightResult = lowestCommonAncestor(root.right, p, q)`.

4. Evaluate the results:

   - If `leftResult != null && rightResult != null`, return `root` (Current node is the LCA).

   - If `leftResult != null`, return `leftResult`.

   - Otherwise, return `rightResult`.

 

---

 

## Java Solution

 

```java

public class LowestCommonAncestor {

    static class TreeNode {

        int val;

        TreeNode left;

        TreeNode right;

        TreeNode(int val) {

            this.val = val;

        }

    }

 

    public static TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {

        // Base case: if root is null or matches either target node

        if (root == null || root == p || root == q) {

            return root;

        }

 

        // Look for p and q in the left and right subtrees

        TreeNode leftResult = lowestCommonAncestor(root.left, p, q);

        TreeNode rightResult = lowestCommonAncestor(root.right, p, q);

 

        // If both subtrees returned a non-null node, current node is the LCA

        if (leftResult != null && rightResult != null) {

            return root;

        }

 

        // If only one subtree returned a node, pass it up

        return leftResult != null ? leftResult : rightResult;

    }

 

    public static void main(String[] args) {

        TreeNode root = new TreeNode(3);

        root.left = new TreeNode(5);

        root.right = new TreeNode(1);

        root.left.left = new TreeNode(6);

        root.left.right = new TreeNode(2);

        root.left.right.left = new TreeNode(7);

        root.left.right.right = new TreeNode(4);

        root.right.left = new TreeNode(0);

        root.right.right = new TreeNode(8);

 

        TreeNode p = root.left;       // Node 5

        TreeNode q = root.right;      // Node 1

 

        TreeNode lca = lowestCommonAncestor(root, p, q);

        System.out.println("LCA of " + p.val + " and " + q.val + " is: " + lca.val);

    }

}

```

 

---

 

## Dry Run

 

Using the tree from the example with `p = 5` and `q = 4`:

 

### Step 1: Root Node 3

- Calls `root.left` (Node 5).

 

### Step 2: Node 5

- Condition `root == p` triggers true because node is `5`.

- Immediately returns Node 5 back to Node 3 without looking at its children.

 

### Step 3: Node 3 processes its Right Subtree

- Calls `root.right` (Node 1).

 

### Step 4: Node 1

- Explores Node 0 (returns null) and Node 8 (returns null).

- Both subtrees of Node 1 are null, so Node 1 returns null back to Node 3.

 

### Step 5: Final Evaluation at Node 3

- `leftResult = Node 5`

- `rightResult = null`

- Result is `leftResult != null ? leftResult : rightResult` -> Returns Node 5.

- Output is correctly identified as Node 5 (since 4 is a descendant of 5, 5 is the LCA).

 

---

 

## Visual Representation

 

```text

          3  <-- leftResult = 5, rightResult = null -> Returns 5

         / \

      (p)5  1  --> returns null

        / \

       6   2

          / \

         7   4(q)

```

 

---

 

## Edge Cases

 

### Node is an Ancestor of Itself

```text

p = 5, q = 4 (4 is inside 5's subtree)

Output: 5

```

 

### Distant Leaf Nodes

```text

p = 6, q = 8

Output: 3

```

 

---

 

## Time Complexity

 

```text

O(N)

```

Where `N` is the number of nodes in the binary tree. In the worst case, we may need to visit all nodes to locate `p` and `q`.

 

---

 

## Space Complexity

 

```text

O(H)

```

Where `H` is the height of the tree, representing the maximum call stack frames. For a skewed tree, this becomes `O(N)`. For a balanced tree, it remains `O(\log N)`.

 

---

 

## Interview Follow-Up Questions

 

### Q1. What if one or both target nodes do not exist in the binary tree?

The standard code assumes both nodes exist. If one node is missing, the code incorrectly returns the single existing node as the LCA. To handle missing nodes, you must perform a preliminary pass to confirm both nodes exist, or use boolean flags inside the recursion to track when both nodes are found.

 

### Q2. How would you solve this if it were a Binary Search Tree (BST)?

In a BST, you can find the LCA iteratively without checking the entire tree. Starting from the root:

- If both `p` and `q` are smaller than the current node, move to the left child.

- If both `p` and `q` are larger than the current node, move to the right child.

- The first split point where `p` and `q` lie on different sides (or one equals the current node) is the LCA.

 

---

 

## Important Interview Takeaways

 

- ✅ Use bottom-up recursion to check subtrees for targets.

- ✅ Node matching condition `root == p || root == q` handles cases where a node is its own ancestor.

- ✅ Dual non-null returns (`leftResult != null && rightResult != null`) explicitly indicate the exact split-point LCA node.

- ✅ Time Complexity = O(N)

- ✅ Space Complexity = O(H)

- ✅ Frequently Asked In:

  - Meta (Facebook)

  - Amazon

  - Microsoft

  - Google

- ✅ Related Problems:

  - Lowest Common Ancestor of a Binary Search Tree

  - Lowest Common Ancestor of a Binary Tree II (with missing nodes)

  - Lowest Common Ancestor of a Binary Tree IV (multiple nodes)

 
