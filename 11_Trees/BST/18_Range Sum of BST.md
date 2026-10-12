# Range Sum of BST

## Problem Statement

Given the `root` node of a binary search tree (BST) and two integers `low` and `high`, return *the sum of values of all nodes with a value in the **inclusive** range `[low, high]`*.

The overall runtime complexity must be:

```text
O(n)
```

*(where n is the total number of nodes inside the tree structure)*

---

## Examples

### Example 1

**Input**

```text
root = [10, 5, 15, 3, 7, null, 18]
low = 7, high = 15
```

```text
       10
      /  \
     5    15
    / \     \
   3   7     18
```

**Output**

```java
32
```

**Explanation**

Nodes 7, 10, and 15 are in the inclusive range `[7, 15]`. `7 + 10 + 15 = 32`.

---

### Example 2

**Input**

```text
root = [10, 5, 15, 3, 7, 13, 18, 1, null, 6]
low = 6, high = 10
```

```text
         10
        /  \
       5    15
      / \   / \
     3   7 13  18
    /   /
   1   6
```

**Output**

```java
23
```

**Explanation**

Nodes 6, 7, and 10 are in the inclusive range `[6, 10]`. `6 + 7 + 10 = 23`.

---

## Brute Force Approach

Traverse every single node inside the tree using a standard Depth-First Search (DFS) or Breadth-First Search (BFS) routine, check if each node's value falls inside the range constraints, and accumulate the matches.

### Steps

1. Launch a basic recursive traversal helper function starting at the `root`.
2. If the current node is `null`, return `0`.
3. Check the condition: if `node.val >= low && node.val <= high`, add `node.val` to a running total.
4. Recursively collect sums from both the left child and the right child independently.
5. Return the full accumulated sum.

### Complexity

```text
Time Complexity: O(n) // visits all n nodes in the tree
Space Complexity: O(h) // where h is the tree height, matching recursion call stack allocations
```

While linear, this approach completely ignores the **Binary Search Tree (BST) property**. A BST guarantees that all keys in a node's left subtree are smaller than the node's key, and all keys in its right subtree are larger. We can optimize this process by **pruning out branches** that cannot contain values in our range.

---

# Optimal Approach: BST Range Pruning via DFS

## Key Idea

We can optimize our traversal by leveraging the structural properties of a BST. At any given node, we can conditionally prune entire subtrees based on the node's value relative to our `low` and `high` boundaries:

1. **Prune Right Subtree:** If `root.val > high`, then all nodes in the right subtree are guaranteed to be strictly greater than `high`. This means they cannot possibly fall within the range `[low, high]`. We can completely skip exploring the right child and only recurse on the **left child**.
2. **Prune Left Subtree:** If `root.val < low`, then all nodes in the left subtree are guaranteed to be strictly smaller than `low`. They cannot fall within our range. We can completely skip exploring the left child and only recurse on the **right child**.
3. **In-Range Exploitation:** If `low <= root.val <= high`, the current node's value is valid. We add `root.val` to our sum and recursively explore **both** the left and right children since both could still contain elements in the valid range.

---

## Visual Understanding

Suppose `root = [10, 5, 15, 3, 7, null, 18]`, `low = 7`, `high = 15`:

- **At Node 10:** `7 <= 10 <= 15`. In-range! Total starts at `10`. Recurse on *both* children.
- **Left Branch - At Node 5:** `5 < 7`. Left subtree prune triggered! All nodes under 5's left child (like 3) are too small. Skip the left child entirely and recurse only on its **right child** (Node 7).
- **At Node 7:** `7 <= 7 <= 15`. In-range! Total updates to `10 + 7 = 17`. Both children are null. Return.
- **Right Branch - At Node 15:** `7 <= 15 <= 15`. In-range! Total updates to `17 + 15 = 32`. Recurse on both children.
- **At Node 18:** `18 > 15`. Right subtree prune triggered! Skip right child. Left child is null. Return.

The algorithm calculates the final sum `32` without ever looking at Node 3.

---

## Partition Variables

Let:

```java
int rangeSum = 0;
```

---

### Border Elements

The recursive path returns 0 instantly upon encountering a terminal `null` leaf pointer:

```java
if (root == null) {
    return 0;
}
```

---

## Correct Partition Condition

The internal routing gates apply BST pruning values precisely before invoking child recursions:

```java
if (root.val < low) {
    return rangeSumBST(root.right, low, high);
}
if (root.val > high) {
    return rangeSumBST(root.left, low, high);
}
```

---

## Java Solution

```java
class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode(int val) { this.val = val; }
}

class Solution {

    public int rangeSumBST(TreeNode root, int low, int high) {
        // Base Case: Terminal node boundary hit
        if (root == null) {
            return 0;
        }

        // Condition A: Node value is strictly smaller than the lower bound
        // Prune the left subtree completely and explore only the right child branch
        if (root.val < low) {
            return rangeSumBST(root.right, low, high);
        }

        // Condition B: Node value is strictly larger than the upper bound
        // Prune the right subtree completely and explore only the left child branch
        if (root.val > high) {
            return rangeSumBST(root.left, low, high);
        }

        // Condition C: Node value is securely within the inclusive range bounds
        // Collect current node value and recursively aggregate matching branches
        return root.val + rangeSumBST(root.left, low, high) + rangeSumBST(root.right, low, high);
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Pruned BST Search** | **O(n)** | **O(h)** | In the worst case (e.g., highly skewed tree or extremely wide range), it may visit all `n` nodes. The space complexity depends on the tree height `h` to store execution frames on the recursion stack. |

---

## Key Insight

Using conditional boundaries to prune subtrees that fall outside our range boundaries avoids unnecessary traversals, optimizing BST range sum collection paths significantly in practice.

```text
Time  : O(n) bounded linear execution
Space : O(h) dynamic stack frame memory allocation
```

---

## Similar Problems

1. Validate Binary Search Tree (98)
2. Search in a Binary Search Tree (700)
3. Insert into a Binary Search Tree (701)
4. Trim a Binary Search Tree (669)
5. Binary Search Tree Iterator (173)
