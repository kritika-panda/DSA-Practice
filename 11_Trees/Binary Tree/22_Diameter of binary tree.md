# Diameter of Binary Tree

## Problem Statement

Given the root of a binary tree, return the **diameter** of the tree.

The **diameter** of a binary tree is the length of the **longest path between any two nodes** in the tree.

The path:

- May or may not pass through the root.
- Is measured in terms of **number of edges**.

---

## Example 1

### Input

```text
         1
        / \
       2   3
      / \
     4   5
```

### Output

```text
3
```

### Explanation

Longest path:

```text
4 → 2 → 1 → 3
```

Number of edges:

```text
3
```

---

## Example 2

### Input

```text
    1
   /
  2
```

### Output

```text
1
```

Longest path:

```text
2 → 1
```

Edges:

```text
1
```

---

# Understanding Diameter

For every node,

a possible longest path passing through it is:

```text
Left Height + Right Height
```

Example:

```text
         1
        / \
       2   3
      / \
     4   5
```

For node:

```text
1
```

```text
Left Height  = 2
Right Height = 1

Diameter = 2 + 1 = 3
```

---

# Key Observation

At every node:

```text
Potential Diameter =
Height(left)
+
Height(right)
```

The answer is the maximum such value across all nodes.

---

# Brute Force Approach

For every node:

1. Calculate left height.
2. Calculate right height.
3. Compute diameter.
4. Recursively check every node.

---

## Algorithm

```text
For every node

diameter =
height(left)
+
height(right)

answer = maximum diameter found
```

---

## Complexity

### Time Complexity

```text
O(N²)
```

Because height is recalculated repeatedly.

---

### Space Complexity

```text
O(H)
```

---

# Optimal Approach (Single DFS)

## Core Idea

While computing height, simultaneously update the diameter.

For every node:

```text
leftHeight
rightHeight

diameter =
leftHeight + rightHeight
```

Update global maximum.

Then return:

```text
1 + max(leftHeight, rightHeight)
```

---

# Visualization

```text
         1
        / \
       2   3
      / \
     4   5
```

---

### Node 4

```text
Left Height  = 0
Right Height = 0

Diameter = 0
Height = 1
```

---

### Node 5

```text
Left Height  = 0
Right Height = 0

Diameter = 0
Height = 1
```

---

### Node 2

```text
Left Height  = 1
Right Height = 1

Diameter = 2
Height = 2
```

Current Maximum:

```text
2
```

---

### Node 3

```text
Height = 1
```

---

### Node 1

```text
Left Height  = 2
Right Height = 1

Diameter = 3
```

Current Maximum:

```text
3
```

---

Final Answer:

```text
3
```

---

# Dry Run

## Input

```text
         1
        / \
       2   3
      / \
     4   5
```

---

### dfs(4)

```text
left = 0
right = 0

diameter = 0

height = 1
```

---

### dfs(5)

```text
left = 0
right = 0

diameter = 0

height = 1
```

---

### dfs(2)

```text
left = 1
right = 1

diameter = 2

height = 2
```

---

### dfs(3)

```text
height = 1
```

---

### dfs(1)

```text
left = 2
right = 1

diameter = 3

height = 3
```

Maximum:

```text
3
```

Return:

```text
3
```

---

# Optimal Java Solution

```java
class Solution {

    private int diameter = 0;

    public int diameterOfBinaryTree(TreeNode root) {

        height(root);

        return diameter;
    }

    private int height(TreeNode root) {

        if (root == null) {
            return 0;
        }

        int left = height(root.left);
        int right = height(root.right);

        diameter = Math.max(
                diameter,
                left + right);

        return 1 + Math.max(left, right);
    }
}
```

---

# Why Does This Work?

For every node:

```text
Longest path through node

=
height(left)
+
height(right)
```

Example:

```text
      X
     / \
    L   R
```

If:

```text
L height = 3
R height = 2
```

Longest path through X:

```text
3 + 2 = 5 edges
```

We check this value for every node and keep the maximum.

---

# Height vs Diameter

## Height

```text
Longest path
from node to leaf
```

Example:

```text
      1
     /
    2
   /
  3
```

Height:

```text
3 nodes
```

or

```text
2 edges
```

depending on definition.

---

## Diameter

```text
Longest path
between any two nodes
```

Example:

```text
4 → 2 → 1 → 3
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

# Edge Cases

---

## Empty Tree

```text
null
```

Output:

```text
0
```

---

## Single Node

```text
1
```

Output:

```text
0
```

No edges exist.

---

## Skewed Tree

```text
1
 \
  2
   \
    3
     \
      4
```

Diameter:

```text
3
```

---

## Diameter Doesn't Pass Through Root

```text
            1
           /
          2
         / \
        3   4
       / \
      5   6
```

Longest path:

```text
5 → 3 → 2 → 4
```

Diameter:

```text
3
```

Notice the path does not necessarily involve both sides of the root.

---

# Interview Questions

## Q1. Can the diameter pass through the root?

✅ Yes

But not always.

---

## Q2. Why do we use a global variable?

Because every node contributes a possible diameter.

We need the maximum across the entire tree.

---

## Q3. Why is diameter calculated as:

```java
leftHeight + rightHeight
```

?

Because:

```text
Longest node in left subtree
→ current node →
Longest node in right subtree
```

forms the longest path passing through that node.

---

## Q4. Why is the solution O(N)?

Each node:

- Computes left height once.
- Computes right height once.

No repeated calculations.

---

# Related Problems

- Maximum Depth of Binary Tree
- Balanced Binary Tree
- Binary Tree Maximum Path Sum
- Same Tree
- Path Sum
- Lowest Common Ancestor
- Height of Binary Tree

---

# Pattern Recognition

Whenever you see:

```text
Compute height
and simultaneously compute another property
```

think:

```text
Bottom-Up DFS
```

This pattern appears in:

- Diameter of Binary Tree
- Balanced Binary Tree
- Maximum Path Sum
- Longest Univalue Path
- Tree DP problems

---

# Key Takeaway

The optimal solution uses a **Bottom-Up DFS** where each recursive call returns the height of a subtree while updating a global diameter.

For every node:

```text
Diameter Through Node
=
Left Height + Right Height
```

The largest value found across all nodes is the answer.

```text
Time Complexity  : O(N)
Space Complexity : O(H)
```

This is the standard interview solution and a classic example of combining **Height Calculation + DFS Tree DP**.
