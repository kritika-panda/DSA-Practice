# Check if a Binary Tree is a Binary Search Tree (BST)

## Problem Statement

Given the root of a binary tree, determine whether it is a valid Binary Search Tree (BST).

A valid BST satisfies:

```text
Left Subtree < Root < Right Subtree
```

for every node in the tree.

Additionally:

- Every node in the left subtree must be smaller than the current node.
- Every node in the right subtree must be greater than the current node.
- Both left and right subtrees must themselves be valid BSTs.

---

## Example 1

### Valid BST

```text
        5
       / \
      3   7
     / \ / \
    2  4 6  8
```

For every node:

```text
Left < Root < Right
```

Answer:

```text
true
```

---

## Example 2

### Not a BST

```text
        5
       / \
      3   7
         /
        4
```

Why?

```text
4 < 5
```

but `4` is present inside the right subtree of `5`.

Therefore:

```text
Invalid BST
```

Answer:

```text
false
```

---

# Common Mistake

Many beginners check only:

```java
root.left.val < root.val
root.right.val > root.val
```

This is incorrect.

### Example

```text
        10
       /  \
      5    15
          /  \
         6   20
```

Local checks:

```text
6 < 15 ✅
20 > 15 ✅
```

Everything appears correct.

However:

```text
6 < 10
```

and it exists in the right subtree of `10`.

Therefore:

```text
Not a BST
```

---

# Correct Intuition

Each node must lie within a valid range.

For a node:

```text
min < node.val < max
```

Initially:

```text
(-∞, +∞)
```

As we move:

### Left Child

```text
(min, root.val)
```

### Right Child

```text
(root.val, max)
```

If any node violates the range:

```text
Not a BST
```

---

# Recursive Range Solution (Optimal)

## Algorithm

For every node:

1. If node is null, return true.
2. Check if node value lies within range.
3. Recursively validate:
   - Left subtree using `(min, node.val)`
   - Right subtree using `(node.val, max)`

---

## Java Code

```java
class Solution {

    public boolean isValidBST(TreeNode root) {
        return validate(root, Long.MIN_VALUE, Long.MAX_VALUE);
    }

    private boolean validate(TreeNode root, long min, long max) {

        if (root == null)
            return true;

        if (root.val <= min || root.val >= max)
            return false;

        return validate(root.left, min, root.val)
            && validate(root.right, root.val, max);
    }
}
```

---

## Why Use Long?

Consider:

```text
Node Value = Integer.MIN_VALUE
```

or

```text
Node Value = Integer.MAX_VALUE
```

Using `int` boundaries may cause errors.

Therefore:

```java
Long.MIN_VALUE
Long.MAX_VALUE
```

are safer.

---

# Dry Run

### BST

```text
        5
       / \
      3   7
     / \ / \
    2  4 6  8
```

---

### Node 5

Allowed Range:

```text
(-∞ , +∞)
```

Valid ✅

---

### Node 3

Allowed Range:

```text
(-∞ , 5)
```

Valid ✅

---

### Node 2

Allowed Range:

```text
(-∞ , 3)
```

Valid ✅

---

### Node 4

Allowed Range:

```text
(3 , 5)
```

Valid ✅

---

### Node 7

Allowed Range:

```text
(5 , +∞)
```

Valid ✅

---

### Node 6

Allowed Range:

```text
(5 , 7)
```

Valid ✅

---

### Node 8

Allowed Range:

```text
(7 , +∞)
```

Valid ✅

Final Answer:

```text
true
```

---

# Inorder Traversal Approach

## Key Observation

For a BST:

```text
Inorder Traversal
=
Sorted Order
```

Example:

```text
        5
       / \
      3   7
```

Inorder:

```text
3 5 7
```

Sorted ✅

---

## Idea

Perform inorder traversal and ensure:

```text
current > previous
```

at every step.

If not:

```text
Not a BST
```

---

## Java Code

```java
class Solution {

    TreeNode prev = null;

    public boolean isValidBST(TreeNode root) {

        if (root == null)
            return true;

        if (!isValidBST(root.left))
            return false;

        if (prev != null && root.val <= prev.val)
            return false;

        prev = root;

        return isValidBST(root.right);
    }
}
```

---

# Iterative Inorder Solution

## Java Code

```java
class Solution {

    public boolean isValidBST(TreeNode root) {

        Stack<TreeNode> stack = new Stack<>();
        TreeNode prev = null;

        while (root != null || !stack.isEmpty()) {

            while (root != null) {
                stack.push(root);
                root = root.left;
            }

            root = stack.pop();

            if (prev != null && root.val <= prev.val)
                return false;

            prev = root;

            root = root.right;
        }

        return true;
    }
}
```

---

# Complexity Analysis

## Range-Based Solution

| Complexity | Value |
|------------|--------|
| Time | O(n) |
| Space | O(h) |

where:

```text
n = number of nodes
h = height of tree
```

---

## Inorder Solution

| Complexity | Value |
|------------|--------|
| Time | O(n) |
| Space | O(h) |

---

## Balanced BST

```text
h = log n
```

Space:

```text
O(log n)
```

---

## Skewed BST

```text
h = n
```

Space:

```text
O(n)
```

---

# Visualization

## Valid BST

```text
         10
        /  \
       5    15
      / \   / \
     2  7 12 20
```

All nodes satisfy:

```text
Left < Root < Right
```

Answer:

```text
true
```

---

## Invalid BST

```text
         10
        /  \
       5    15
           / \
          6  20
```

Node:

```text
6
```

should be greater than:

```text
10
```

because it lies in the right subtree of `10`.

Answer:

```text
false
```

---

# Range Visualization

For:

```text
        10
       /  \
      5    15
```

Allowed ranges:

```text
10 -> (-∞ , +∞)

5  -> (-∞ , 10)

15 -> (10 , +∞)
```

Every node must remain within its assigned range.

---

# Key Takeaways

- Checking only immediate children is **not sufficient**.
- Every node must satisfy:

```text
min < node.val < max
```

- The **Range-Based DFS Solution** is the most common interview solution.
- Inorder traversal of a BST always produces a strictly increasing sequence.
- Time Complexity: **O(n)**
- Space Complexity: **O(h)**
- This is one of the most frequently asked BST interview questions and appears regularly in coding interviews.
