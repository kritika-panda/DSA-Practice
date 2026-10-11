# Delete Node in a BST

## Problem Statement

Given a root node of a Binary Search Tree (BST) and a `key`, delete the node with the given `key` in the BST. Return the root node reference (possibly updated) of the BST.

Basically, the deletion can be divided into two stages:
1. Search for a node to remove.
2. If the node is found, delete the node.

The overall run time complexity should be:

```text
O(h)
```

*(where h is the height of the BST)*

---

## Examples

### Example 1

**Input**

```java
root = [5, 3, 6, 2, 4, null, 7]
key = 3
```

**Output**

```java
[5, 4, 6, 2, null, null, 7]
```

**Explanation**

Given the tree:
```text
        5
       / \
      3   6
     / \   \
    2   4   7
```
One valid answer is to delete node `3` and replace it with its inorder successor `4`:
```text
        5
       / \
      4   6
     /     \
    2       7
```

---

### Example 2

**Input**

```java
root = [5, 3, 6, 2, 4, null, 7]
key = 0
```

**Output**

```java
[5, 3, 6, 2, 4, null, 7]
```

**Explanation**

The key `0` does not exist in the BST, so no modifications are made.

---

## Brute Force Approach

Flatten the entire tree into an ordered list via an inorder traversal, remove the target value from the collection, and rebuild a completely new balanced BST from scratch.

### Steps

1. Traverse the tree using inorder traversal and copy all node values into an array list except the node matching `key`.
2. Reconstruct a brand-new balanced BST from the filtered sorted array recursively by selecting midpoints as parent nodes.
3. Return the newly created root.

### Complexity

```text
Time Complexity: O(n)
Space Complexity: O(n) to retain tree nodes
```

This strategy reconstructs parts of the tree unnecessarily and fails to optimize mutations using BST properties. The problem requires a structural in-place solution.

---

# Optimal Approach: Recursive Structural Deletion

## Key Idea

We can delete the node in-place using a top-down recursive traversal. First, we use the standard BST property to locate the target node:
- If `key < root.val`, move left.
- If `key > root.val`, move right.

Once the target node is found (`key == root.val`), we handle three distinct structural scenarios based on its children count:

```text
Case 1: No children (Leaf node)
Simply return null to disconnect it from its parent.

Case 2: One child
Return the non-null child link upward to bypass the deleted node.

Case 3: Two children
Find the node's Inorder Successor (the absolute smallest element in its right subtree).
Overwrite the target node's value with the successor's value.
Recursively delete the successor node from the right subtree.
```

---

## Visual Understanding

Suppose we want to delete node `3` from the following tree structure:

```text
        5
       / \
      3   6
     / \
    2   4
```

- Node `3` has two active children (`2` and `4`).
- We locate its inorder successor by moving to its right child (`4`) and scanning left as far as possible. Here, node `4` has no left children, so `4` is the successor.
- Overwrite node `3`'s value with `4`.
- Delete the duplicate leaf node `4` from the right subtree.

The tree updates in-place seamlessly:
```text
        5
       / \
      4   6
     /
    2
```

---

## Partition Variables

Let:

```java
TreeNode current = root;
```

---

### Border Elements

If the search path encounters a `null` node reference boundary, it means the key is missing from the tree. We return `null` immediately:

```java
if (root == null) return null;
```

---

## Correct Partition Condition

When finding the inorder successor inside the right subtree to handle the two-children scenario:

```java
TreeNode successor = findMin(root.right);
root.val = successor.val;
root.right = deleteNode(root.right, successor.val);
```

---

## How to Move Binary Search

### Case 1

```java
key < root.val
```

The target node resides in the left branch.

Move left:

```java
root.left = deleteNode(root.left, key);
```

---

### Case 2

```java
key > root.val
```

The target node resides in the right branch.

Move right:

```java
root.right = deleteNode(root.right, key);
```

---

## Java Solution

```java
class Solution {

    public class TreeNode {
        int val;
        TreeNode left;
        TreeNode right;
        TreeNode(int val) { this.val = val; }
    }

    public TreeNode deleteNode(TreeNode root, int key) {
        // Base case: Key does not exist in the tree
        if (root == null) {
            return null;
        }

        // Step 1: Navigate the tree to locate the target node
        if (key < root.val) {
            root.left = deleteNode(root.left, key);
        } 
        else if (key > root.val) {
            root.right = deleteNode(root.right, key);
        } 
        // Step 2: Target node located (key == root.val)
        else {
            // Scenario A: Node has 0 or 1 child (Left child is missing)
            if (root.left == null) {
                return root.right;
            }
            // Scenario B: Node has 1 child (Right child is missing)
            if (root.right == null) {
                return root.left;
            }

            // Scenario C: Node has 2 children
            // Find the inorder successor (smallest value in the right subtree)
            TreeNode successor = findMin(root.right);
            
            // Overwrite value with successor's value
            root.val = successor.val;
            
            // Delete the duplicate successor node from the right subtree
            root.right = deleteNode(root.right, successor.val);
        }

        return root;
    }

    private TreeNode findMin(TreeNode node) {
        while (node.left != null) {
            node.left = node.left;
        }
        return node;
    }
}
```

---

## Dry Run

### Input

```java
root = [5, 3, 6, 2, 4] // key = 3
```

---

### Step Execution Traversal

- **Call 1 (`root = 5`):** `3 < 5` evaluates to true. Triggers `root.left = deleteNode(root.left, 3)`.
- **Call 2 (`root = 3`):** Match found (`3 == 3`). 
  - Both `root.left` (2) and `root.right` (4) are non-null.
  - Calls `findMin(node_4)`. Node `4` has no left children, so it returns `node_4`.
  - Overwrites current value: `root.val = 4`.
  - Triggers deletion of duplicate successor: `root.right = deleteNode(node_4, 4)`.
- **Call 3 (`root = 4`):** Match found (`4 == 4`). `root.left` and `root.right` are both null. Matches the single child checkpoint (`root.left == null`) and returns `root.right` (null).
- Unpacking frames updates links correctly. `node_4.left` points to `2`, and `node_5.left` points to `4`.

---

### Answer

```java
[5, 4, 6, 2]
```

---

## Why Is the Runtime Complexity Dependent on Height?

The algorithm paths downward along a single branch axis path to locate the target node, and performs a similar single-branch descent to isolate the inorder successor if a two-children deletion scenario triggers. No breadth-first scans or duplicate element lookups are performed.

This deterministic path walk yields:

```text
O(h)
```

*(where h is the height of the tree, scaling to O(log n) for balanced trees and O(n) for skewed trees)*

---

## Complexity Analysis

### Time Complexity

```text
O(h)
```

Locating the node takes at most O(h) operations. Finding the inorder successor and deleting it also takes at most O(h) time, keeping the overall execution within tree height boundaries.

---

### Space Complexity

```text
O(h)
```

The systemic call stack allocates memory frames proportional to the maximum height depth `h` of the active path tree branch.

---

## Key Insight

Overwriting double-child target values with their minimum right-subtree successor transforms a complex internal structure decoupling problem into a simple leaf or single-child edge pointer modification.

```text
Time  : O(h) execution runtime
Space : O(h) recursion frame overhead
```

---

## Similar Problems

1. Insert into a Binary Search Tree (701)
2. Search in a Binary Search Tree (700)
3. Validate Binary Search Tree (98)
4. Kth Smallest Element in a BST (230)
5. Trim a Binary Search Tree (669)
