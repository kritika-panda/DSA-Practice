# Inorder Predecessor and Successor in a Binary Search Tree (BST)

## Problem Statement

Given a Binary Search Tree (BST) and a key value, find the **Inorder Predecessor** and **Inorder Successor** of the given key in the BST. If either does not exist, return `null`.

### Definition

* **Inorder Predecessor:** The node with the largest value smaller than the given key (the node visited immediately before the key in an inorder traversal).
* **Inorder Successor:** The node with the smallest value larger than the given key (the node visited immediately after the key in an inorder traversal).

---

## Example

### BST

```text
           8
         /   \
        5     10
       / \      \
      1   7      12
```

### Input

```text
key = 5
```

### Output

```text
Predecessor = 1
Successor = 7
```

Because:

```text
The inorder traversal of this BST is: 1, 5, 7, 8, 10, 12
The element before 5 is 1.
The element after 5 is 7.
```

---

## Another Example

### Input

```text
key = 8
```

### Output

```text
Predecessor = 7
Successor = 10
```

---

# Key BST Property

```text
Left Subtree < Root < Right Subtree
```

An inorder traversal of a BST yields a strictly increasing sorted sequence. We can use this property to narrow down candidates without checking every node.

---

# Intuition

Instead of performing a full tree traversal, we can determine the predecessor and successor dynamically while searching for the key.

At every node:

### Case 1

The current node's value matches the target key.

```text
root.val == key
```

* **Predecessor:** If a left subtree exists, the predecessor is the rightmost (maximum) node of that left subtree.
* **Successor:** If a right subtree exists, the successor is the leftmost (minimum) node of that right subtree.

---

### Case 2

The target key is smaller than the current node's value.

```text
key < root.val
```

The current node is a potential successor because its value is larger than the key. We record it as a candidate and move to the left subtree to check for smaller, valid successors.

```text
successor = root
move left
```

---

### Case 3

The target key is larger than the current node's value.

```text
key > root.val
```

The current node is a potential predecessor because its value is smaller than the key. We record it as a candidate and move to the right subtree to check for larger, valid predecessors.

```text
predecessor = root
move right
```

---

# Visualization

Finding Predecessor and Successor for:

```text
key = 5
```

```text
           8
         /   \
        5     10
       / \      \
      1   7      12
```

```text
1. At Node 8:
   5 < 8 -> Node 8 is a potential successor.
   Record Successor = 8. Move left.

2. At Node 5:
   5 == 5 -> Target key found!
   - To find Predecessor: Go to left child (1). It has no right child. 
     Predecessor = 1.
   - To find Successor: Go to right child (7). It has no left child. 
     Successor updates from 8 to 7.

Answer:
Predecessor = 1
Successor = 7
```

---

# Iterative Solution

## Algorithm

1. Initialize `predecessor = null` and `successor = null`.
2. Loop through the tree starting from the `root`.
3. If `key < root.val`, update `successor = root` and move `root = root.left`.
4. If `key > root.val`, update `predecessor = root` and move `root = root.right`.
5. If `key == root.val`:
   - If left child exists, find the maximum node in the left subtree and assign to `predecessor`.
   - If right child exists, find the minimum node in the right subtree and assign to `successor`.
   - Break the loop.

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
}

class Solution {
    // Container class to return both predecessor and successor nodes
    public static class Result {
        TreeNode predecessor = null;
        TreeNode successor = null;
    }

    public Result findPredecessorSuccessor(TreeNode root, int key) {
        Result result = new Result();
        TreeNode current = root;

        while (current != null) {
            if (key < current.val) {
                // Current node is a potential successor
                result.successor = current;
                current = current.left;
            } else if (key > current.val) {
                // Current node is a potential predecessor
                result.predecessor = current;
                current = current.right;
            } else {
                // Key node found
                // 1. Predecessor is the maximum value in the left subtree
                if (current.left != null) {
                    TreeNode temp = current.left;
                    while (temp.right != null) {
                        temp = temp.right;
                    }
                    result.predecessor = temp;
                }

                // 2. Successor is the minimum value in the right subtree
                if (current.right != null) {
                    TreeNode temp = current.right;
                    while (temp.left != null) {
                        temp = temp.left;
                    }
                    result.successor = temp;
                }
                break;
            }
        }
        return result;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(H) where H is the height of the BST. In the worst-case scenario (a skewed tree), this takes O(N) time. For a balanced tree, it takes O(log N) time.
* **Space Complexity:** O(1) as we are using an iterative traversal requiring no additional system stack frames or extra data structures.
