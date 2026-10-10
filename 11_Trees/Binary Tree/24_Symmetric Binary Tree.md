# Symmetric Binary Tree

## Problem Statement

Given the root of a binary tree, determine whether it is **symmetric around its center**.

A tree is symmetric if the left subtree is a mirror reflection of the right subtree.

---

## Example 1

### Input

```text
        1
      /   \
     2     2
    / \   / \
   3  4  4   3
```

### Output

```text
true
```

### Explanation

Left subtree is the exact mirror of the right subtree.

---

## Example 2

### Input

```text
        1
      /   \
     2     2
      \     \
      3      3
```

### Output

```text
false
```

### Explanation

The structure is not mirrored.

---

# What Does Symmetric Mean?

A tree is symmetric if:

```text
Left.Left  == Right.Right
Left.Right == Right.Left
```

and values are equal.

---

## Mirror Visualization

```text
        1
      /   \
     2     2
    / \   / \
   3  4  4   3
```

Mirror Checks:

```text
3 == 3 ✅

4 == 4 ✅

2 == 2 ✅
```

Tree is symmetric.

---

# Key Observation

This problem is almost identical to:

```text
Check if Two Trees are Identical
```

The only difference:

Instead of comparing:

```text
Left.Left with Right.Left
Left.Right with Right.Right
```

we compare:

```text
Left.Left with Right.Right
Left.Right with Right.Left
```

because we are checking a mirror image.

---

# Recursive DFS Approach (Optimal)

For two nodes to be mirrors:

### Condition 1

Both nodes are null.

```java
left == null && right == null
```

Return:

```text
true
```

---

### Condition 2

One node is null.

```java
left == null || right == null
```

Return:

```text
false
```

---

### Condition 3

Values differ.

```java
left.val != right.val
```

Return:

```text
false
```

---

### Condition 4

Mirror Subtrees Match

```java
isMirror(left.left, right.right)
&&
isMirror(left.right, right.left)
```

---

# Algorithm

```text
isSymmetric(root)

return isMirror(root.left, root.right)

-----------------------------------

isMirror(left, right)

if both null
    return true

if one null
    return false

if values differ
    return false

return
    isMirror(left.left, right.right)
    &&
    isMirror(left.right, right.left)
```

---

# Dry Run

## Input

```text
        1
      /   \
     2     2
    / \   / \
   3  4  4   3
```

---

### Step 1

Compare:

```text
2 vs 2
```

Equal

---

### Step 2

Compare:

```text
3 vs 3
```

Equal

---

### Step 3

Compare:

```text
4 vs 4
```

Equal

---

### Step 4

Compare null nodes:

```text
null vs null
```

Return:

```text
true
```

---

All comparisons succeed.

Output:

```text
true
```

---

# Visualization

```text
         1
       /   \
      2     2
     / \   / \
    3  4 4   3
```

Mirror Comparisons:

```text
2 ↔ 2

3 ↔ 3

4 ↔ 4
```

Every pair matches.

Result:

```text
Symmetric ✅
```

---

# Recursive Java Solution

```java
class Solution {

    public boolean isSymmetric(TreeNode root) {

        if (root == null) {
            return true;
        }

        return isMirror(root.left, root.right);
    }

    private boolean isMirror(
            TreeNode left,
            TreeNode right) {

        if (left == null && right == null) {
            return true;
        }

        if (left == null || right == null) {
            return false;
        }

        if (left.val != right.val) {
            return false;
        }

        return isMirror(left.left, right.right)
            && isMirror(left.right, right.left);
    }
}
```

---

# Why Does This Work?

For the tree to be symmetric:

```text
Outer Nodes Match
AND
Inner Nodes Match
```

Example:

```text
      2      2
     / \    / \
    3  4   4  3
```

Checks:

```text
3 ↔ 3

4 ↔ 4
```

This mirrors exactly how a reflection behaves.

---

# Complexity Analysis

## Time Complexity

```text
O(N)
```

Every node is visited once.

---

## Space Complexity

```text
O(H)
```

where:

```text
H = Height of Tree
```

Recursive call stack.

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

# Iterative BFS Approach

Use a queue to compare mirror nodes level by level.

---

## Algorithm

```text
Add:

root.left
root.right

to queue

while queue not empty

    remove two nodes

    if both null
        continue

    if one null
        return false

    if values differ
        return false

    add:

    left.left
    right.right

    left.right
    right.left
```

---

# Iterative Java Solution

```java
import java.util.*;

class Solution {

    public boolean isSymmetric(TreeNode root) {

        if (root == null) {
            return true;
        }

        Queue<TreeNode> queue =
                new LinkedList<>();

        queue.offer(root.left);
        queue.offer(root.right);

        while (!queue.isEmpty()) {

            TreeNode left = queue.poll();
            TreeNode right = queue.poll();

            if (left == null && right == null) {
                continue;
            }

            if (left == null || right == null) {
                return false;
            }

            if (left.val != right.val) {
                return false;
            }

            queue.offer(left.left);
            queue.offer(right.right);

            queue.offer(left.right);
            queue.offer(right.left);
        }

        return true;
    }
}
```

---

# DFS vs BFS

| Approach | Time | Space |
|-----------|--------|--------|
| Recursive DFS | O(N) | O(H) |
| Iterative BFS | O(N) | O(N) |

---

# Relationship To Same Tree Problem

## Same Tree

Compare:

```text
left.left  ↔ right.left

left.right ↔ right.right
```

---

## Symmetric Tree

Compare:

```text
left.left  ↔ right.right

left.right ↔ right.left
```

---

# Edge Cases

---

## Empty Tree

```text
null
```

Output:

```text
true
```

---

## Single Node

```text
1
```

Output:

```text
true
```

---

## Different Values

```text
        1
      /   \
     2     3
```

Output:

```text
false
```

---

## Different Structure

```text
        1
      /   \
     2     2
      \     \
      3      3
```

Output:

```text
false
```

---

# Interview Questions

## Q1. What is a Symmetric Binary Tree?

A tree whose left subtree is a mirror reflection of its right subtree.

---

## Q2. How does it differ from Same Tree?

Same Tree:

```text
Left ↔ Left
Right ↔ Right
```

Symmetric Tree:

```text
Left ↔ Right
Right ↔ Left
```

---

## Q3. Why compare:

```java
left.left
```

with

```java
right.right
```

?

Because mirror images are reflected around the center.

---

## Q4. Can BFS solve this problem?

✅ Yes

By processing nodes in mirrored pairs.

---

# Related Problems

- Same Tree
- Balanced Binary Tree
- Subtree of Another Tree
- Invert Binary Tree
- Diameter of Binary Tree
- Maximum Depth of Binary Tree

---

# Pattern Recognition

Whenever you hear:

```text
Mirror
Reflection
Symmetric
```

think:

```text
Compare opposite children

left.left  ↔ right.right

left.right ↔ right.left
```

---

# Key Takeaway

A binary tree is symmetric if its left and right subtrees are mirror images of each other.

The recursive solution compares:

```text
left.left  ↔ right.right

left.right ↔ right.left
```

for every node.

This yields:

```text
Time Complexity  : O(N)
Space Complexity : O(H)
```

making it the standard interview solution for **LeetCode 101: Symmetric Tree**.
