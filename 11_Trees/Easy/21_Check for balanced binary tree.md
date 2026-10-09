# Check for Balanced Binary Tree

## Problem Statement

Given the root of a binary tree, determine whether it is **height-balanced**.

A binary tree is said to be balanced if:

```text
| Height of Left Subtree - Height of Right Subtree | <= 1
```

for **every node** in the tree.

---

## Example 1

### Input

```text
        3
       / \
      9  20
         / \
        15  7
```

### Output

```text
true
```

### Explanation

```text
Node 3

Left Height  = 1
Right Height = 2

|1 - 2| = 1
```

Every node satisfies the balanced condition.

---

## Example 2

### Input

```text
            1
           /
          2
         /
        3
       /
      4
```

### Output

```text
false
```

### Explanation

For node:

```text
2
```

```text
Left Height  = 2
Right Height = 0

|2 - 0| = 2 > 1
```

Tree is not balanced.

---

# What is Height?

Height of a node:

```text
Number of nodes on the longest path
from that node to a leaf.
```

Example:

```text
        1
       / \
      2   3
     /
    4
```

Heights:

```text
4 → 1
2 → 2
3 → 1
1 → 3
```

---

# Brute Force Approach

For every node:

1. Calculate left subtree height.
2. Calculate right subtree height.
3. Check if difference is greater than 1.
4. Recursively check left and right subtrees.

---

## Algorithm

```text
isBalanced(root)

leftHeight = height(root.left)
rightHeight = height(root.right)

if abs(leftHeight - rightHeight) > 1
    return false

return
    isBalanced(root.left)
    &&
    isBalanced(root.right)
```

---

## Java Solution (Brute Force)

```java
class Solution {

    public boolean isBalanced(TreeNode root) {

        if (root == null) {
            return true;
        }

        int leftHeight = height(root.left);
        int rightHeight = height(root.right);

        if (Math.abs(leftHeight - rightHeight) > 1) {
            return false;
        }

        return isBalanced(root.left)
            && isBalanced(root.right);
    }

    private int height(TreeNode node) {

        if (node == null) {
            return 0;
        }

        return 1 + Math.max(
                height(node.left),
                height(node.right));
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
O(N²)
```

### Why?

For every node:

```text
Height calculation traverses subtree again.
```

Example:

```text
Node 1 -> computes full tree height
Node 2 -> computes subtree height again
Node 3 -> computes subtree height again
```

Many repeated computations occur.

---

### Space Complexity

```text
O(H)
```

Recursive stack.

---

# Optimal Approach (Bottom-Up DFS)

## Key Observation

While calculating height, we can simultaneously determine whether the subtree is balanced.

Instead of returning only:

```text
height
```

we return:

```text
height OR unbalanced signal
```

---

# Trick

Use:

```text
-1
```

to indicate:

```text
Subtree is not balanced.
```

---

## Logic

For each node:

### Step 1

Get left subtree height.

```java
int left = dfs(root.left);
```

If:

```java
left == -1
```

immediately return:

```java
-1
```

---

### Step 2

Get right subtree height.

```java
int right = dfs(root.right);
```

If:

```java
right == -1
```

return:

```java
-1
```

---

### Step 3

Check balance condition.

```java
Math.abs(left - right) > 1
```

If true:

```java
return -1;
```

---

### Step 4

Return current subtree height.

```java
1 + Math.max(left, right)
```

---

# Dry Run

## Input

```text
        3
       / \
      9  20
         / \
        15  7
```

---

### Node 9

```text
Left Height  = 0
Right Height = 0

Height = 1
```

---

### Node 15

```text
Height = 1
```

---

### Node 7

```text
Height = 1
```

---

### Node 20

```text
Left Height  = 1
Right Height = 1

Height = 2
```

---

### Node 3

```text
Left Height  = 1
Right Height = 2

|1 - 2| = 1
```

Balanced.

Height:

```text
3
```

---

Return:

```text
true
```

---

# Visualization

```text
        3
       / \
      9  20
         / \
        15  7
```

Bottom-up heights:

```text
9  -> 1
15 -> 1
7  -> 1

20 -> 2

3  -> 3
```

Every node:

```text
|left - right| <= 1
```

Therefore:

```text
Balanced ✅
```

---

# Optimal Java Solution

```java
class Solution {

    public boolean isBalanced(TreeNode root) {
        return height(root) != -1;
    }

    private int height(TreeNode root) {

        if (root == null) {
            return 0;
        }

        int left = height(root.left);

        if (left == -1) {
            return -1;
        }

        int right = height(root.right);

        if (right == -1) {
            return -1;
        }

        if (Math.abs(left - right) > 1) {
            return -1;
        }

        return 1 + Math.max(left, right);
    }
}
```

---

# Why Does This Work?

The recursion works **bottom-up**.

For every subtree:

1. Compute left height.
2. Compute right height.
3. Verify balance condition.
4. Return height.

As soon as an unbalanced subtree is found:

```text
Return -1
```

The signal propagates all the way up to the root.

Thus:

```text
No repeated height calculations.
```

---

# Complexity Analysis

## Time Complexity

```text
O(N)
```

Each node is visited exactly once.

---

## Space Complexity

```text
O(H)
```

Where:

```text
H = Height of Tree
```

---

### Balanced Tree

```text
O(log N)
```

---

### Skewed Tree

```text
O(N)
```

---

# Comparison

| Approach | Time | Space |
|-----------|--------|--------|
| Brute Force | O(N²) | O(H) |
| Optimal DFS | O(N) | O(H) |

---

# Interview Questions

## Q1. What is a Balanced Binary Tree?

A tree where:

```text
Height difference between left and right
subtrees is at most 1
```

for every node.

---

## Q2. Why is the brute-force solution O(N²)?

Because height is recalculated repeatedly for every node.

---

## Q3. Why use -1?

```text
-1 represents "unbalanced subtree"
```

This allows us to return both:

```text
Height information
+
Balance information
```

using a single integer.

---

## Q4. Is this a Top-Down or Bottom-Up approach?

✅ Bottom-Up

We first compute heights of children and then determine the parent's height.

---

## Related Problems

- Maximum Depth of Binary Tree
- Diameter of Binary Tree
- Symmetric Tree
- Same Tree
- Subtree of Another Tree
- Height of Binary Tree
- Binary Tree Maximum Path Sum

---

# Key Takeaway

The optimal solution uses a **Bottom-Up DFS** approach where each recursive call returns either:

```text
Height of subtree
```

or

```text
-1 (subtree is unbalanced)
```

This avoids repeated height calculations and achieves:

```text
Time Complexity  : O(N)
Space Complexity : O(H)
```

making it the preferred interview solution for checking whether a binary tree is balanced.
