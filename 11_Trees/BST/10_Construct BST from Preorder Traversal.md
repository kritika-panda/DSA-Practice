# Construct BST from Preorder Traversal

## Problem Statement

Given an array of integers representing the **preorder traversal** of a Binary Search Tree (BST), reconstruct the original tree and return its root.

### Definition

A **Binary Search Tree (BST)** is a binary tree where for each node, all values in its left subtree are less than the node's value, and all values in its right subtree are greater than the node's value. A **preorder traversal** visits the nodes in the order: `Root -> Left -> Right`.

---

## Example

### Input

```text
preorder =
```

### Output

The root node of the following BST:

```text
           8
         /   \
        5     10
       / \      \
      1   7      12
```

Because:

```text
Root is 8.
Values smaller than 8 () form the left subtree.
Values greater than 8 () form the right subtree.
```

---

# Key BST Property

```text
Left Subtree < Root < Right Subtree
```

In a preorder sequence, the first element is always the `Root`. The subsequent elements can be partitioned or bounded based on this property to build the left and right subtrees.

---

# Intuition

We can solve this efficiently by simulating the tree construction while enforcing a valid value range (upper bound) for each node.

At every step:

### Case 1

The next value in the preorder array exceeds the allowed upper bound for the current subtree.

```text
preorder[index] > upper_bound
```

This node cannot belong to the current subtree. 

Return `null`.

---

### Case 2

The next value is within the valid bound.

```text
preorder[index] < upper_bound
```

Create a new tree node with this value. 

Advance to the next element in the preorder array.

---

### Tree Splitting

For the newly created node:

1. Elements belonging to its left subtree must be smaller than the node's value.
2. Elements belonging to its right subtree must be smaller than the parent's upper bound.

---

# Visualization

Constructing from:

```text
preorder =
```

```text
1. Process 8: Root node. Left bound = 8, Right bound = infinity.
           8
          / \

2. Process 5: Valid for 8's left (< 8). Becomes 8's left child.
           8
          /
         5

3. Process 1: Valid for 5's left (< 5). Becomes 5's left child.
           8
          /
         5
        /
       1

4. Process 7: Invalid for 1's left (< 1) and 1's right (< 5). 
   Valid for 5's right (< 8). Becomes 5's right child.
           8
          /
         5
        / \
       1   7

5. Process 10: Invalid for 8's left side. Valid for 8's right (< infinity).
   Becomes 8's right child.
           8
         /   \
        5     10
       / \
      1   7

6. Process 12: Valid for 10's right (< infinity). Becomes 10's right child.
           8
         /   \
        5     10
       / \      \
      1   7      12
```

---

# Recursive Solution

## Algorithm

1. Maintain a global pointer/index to track the current element in the `preorder` array.
2. Pass an `upper_bound` parameter into the recursive function (initially set to infinity).
3. If the current element exceeds `upper_bound` or the index reaches the end of the array, return `null`.
4. Create a node with the current element and increment the index.
5. Recursively build the left subtree by setting the `upper_bound` to the current node's value.
6. Recursively build the right subtree by keeping the inherited `upper_bound`.

---

## Java Implementation

```java
// Definition for a binary tree node.
class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    
    TreeNode() {}
    
    TreeNode(int val) { 
        this.val = val; 
    }
    
    TreeNode(int val, TreeNode left, TreeNode right) {
        this.val = val;
        this.left = left;
        this.right = right;
    }
}

class Solution {
    // Global index to track current element in preorder traversal
    private int index = 0;

    public TreeNode bstFromPreorder(int[] preorder) {
        if (preorder == null || preorder.length == 0) {
            return null;
        }
        // Initialize the construction with an infinite upper bound
        return buildBST(preorder, Integer.MAX_VALUE);
    }

    private TreeNode buildBST(int[] preorder, int upperBound) {
        // Base case: if all elements are processed or the current element 
        // violates the upper bound constraint for this subtree
        if (index >= preorder.length || preorder[index] > upperBound) {
            return null;
        }

        // Create the root node for the current subtree
        TreeNode root = new TreeNode(preorder[index]);
        index++; // Move to the next element in the preorder sequence

        // Build the left subtree: elements must be strictly less than the current root value
        root.left = buildBST(preorder, root.val);

        // Build the right subtree: elements must be less than the current inherited upper bound
        root.right = buildBST(preorder, upperBound);

        return root;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N) where N is the number of nodes in the array. Every element is visited exactly once by the index pointer.
* **Space Complexity:** O(N) for the recursion stack in the worst-case scenario (a skewed tree), and O(H) in the average case where H is the height of the tree.
