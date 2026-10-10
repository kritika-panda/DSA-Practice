# Maximum Depth (Height) of a Binary Tree

## Problem Statement

Given the root of a binary tree, return its **maximum depth**.

The **maximum depth** (or height) of a binary tree is the number of nodes along the longest path from the root node down to the farthest leaf node.

---

## Examples

### Example 1

```text
        3
       / \
      9   20
         /  \
        15   7
```

Output:

```text
3
```

Explanation:

```text
Longest Path:

3 → 20 → 15

Number of Nodes = 3
```

---

### Example 2

```text
    1
     \
      2
```

Output:

```text
2
```

---

# Understanding Height vs Depth

### Height of a Tree

The number of nodes in the longest path from root to leaf.

```text
        1
       / \
      2   3
     /
    4
```

Longest Path:

```text
1 → 2 → 4
```

Height:

```text
3
```

---

# Key Observation

For every node:

```text
Height(Node)
=
1 + max(
        Height(Left Subtree),
        Height(Right Subtree)
      )
```

---

## Visualization

```text
        1
       / \
      2   3
     /
    4
```

### Computing Bottom-Up

```text
Height(4) = 1

Height(2)
= 1 + max(1, 0)
= 2

Height(3)
= 1

Height(1)
= 1 + max(2, 1)
= 3
```

Answer:

```text
3
```

---

# Approach 1: Recursive DFS (Recommended)

## Intuition

To calculate height:

1. Calculate left subtree height.
2. Calculate right subtree height.
3. Return the larger height + 1 for the current node.

---

## Algorithm

```text
maxDepth(root)

If root is null:
    return 0

leftHeight = maxDepth(root.left)

rightHeight = maxDepth(root.right)

return 1 + max(leftHeight, rightHeight)
```

---

## Java Solution

```java
class Solution {

    public int maxDepth(TreeNode root) {

        if (root == null) {
            return 0;
        }

        int leftHeight = maxDepth(root.left);
        int rightHeight = maxDepth(root.right);

        return 1 + Math.max(leftHeight, rightHeight);
    }
}
```

---

# Dry Run

### Tree

```text
        1
       / \
      2   3
     /
    4
```

### Recursive Calls

```text
maxDepth(1)

    maxDepth(2)

        maxDepth(4)

            maxDepth(null) = 0
            maxDepth(null) = 0

        return 1

        maxDepth(null) = 0

    return 2

    maxDepth(3)

        maxDepth(null) = 0
        maxDepth(null) = 0

    return 1

return 1 + max(2, 1)
       = 3
```

Output:

```text
3
```

---

# Recursive Tree Visualization

```text
                1
             /     \
           2         3
         /         /   \
        4       null  null
      /  \
   null null
```

Returning:

```text
4 -> 1
2 -> 2
3 -> 1
1 -> 3
```

---

# Approach 2: Level Order Traversal (BFS)

## Intuition

In BFS, we process nodes level by level.

The number of levels processed equals the height of the tree.

---

## Algorithm

```text
1. Push root into queue.
2. Initialize depth = 0.
3. Process nodes level by level.
4. Increment depth after each level.
5. Return depth.
```

---

## Java Solution

```java
import java.util.LinkedList;
import java.util.Queue;

class Solution {

    public int maxDepth(TreeNode root) {

        if (root == null) {
            return 0;
        }

        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);

        int depth = 0;

        while (!queue.isEmpty()) {

            int size = queue.size();

            for (int i = 0; i < size; i++) {

                TreeNode node = queue.poll();

                if (node.left != null) {
                    queue.offer(node.left);
                }

                if (node.right != null) {
                    queue.offer(node.right);
                }
            }

            depth++;
        }

        return depth;
    }
}
```

---

# BFS Dry Run

### Tree

```text
        1
       / \
      2   3
     /
    4
```

### Iteration 1

```text
Queue = [1]

Process level

Depth = 1
```

### Iteration 2

```text
Queue = [2, 3]

Process level

Depth = 2
```

### Iteration 3

```text
Queue = [4]

Process level

Depth = 3
```

Final Answer:

```text
3
```

---

# Complexity Analysis

## Recursive DFS

### Time Complexity

```text
O(n)
```

Each node is visited exactly once.

### Space Complexity

```text
O(h)
```

Where:

```text
h = height of tree
```

Worst Case (Skewed Tree):

```text
O(n)
```

Balanced Tree:

```text
O(log n)
```

---

## BFS Solution

### Time Complexity

```text
O(n)
```

Each node is processed once.

### Space Complexity

```text
O(n)
```

Queue may contain an entire level.

---

# DFS vs BFS

| Approach | Time | Space |
|-----------|--------|--------|
| Recursive DFS | O(n) | O(h) |
| Level Order BFS | O(n) | O(n) |

---

# Why DFS is Preferred?

The recursive DFS solution:

- Simpler
- Cleaner
- Uses less code
- Directly follows the height definition

```text
Height
=
1 + max(Left Height, Right Height)
```

Hence it is the most common interview solution.

---

# LeetCode Pattern

Many binary tree problems follow the same pattern:

```java
return 1 + Math.max(
            solve(root.left),
            solve(root.right)
       );
```

Examples:

- Maximum Depth of Binary Tree
- Minimum Depth of Binary Tree
- Diameter of Binary Tree
- Balanced Binary Tree
- Maximum Path Sum

---

# Key Takeaways

- Maximum Depth = Length of the longest root-to-leaf path.
- Recursive DFS is the most intuitive solution.
- Recurrence Relation:

```text
Height(node)
=
1 + max(
        Height(left),
        Height(right)
      )
```

- DFS Time Complexity = **O(n)**
- DFS Space Complexity = **O(h)**
- BFS can also be used by counting levels.
- This is one of the most important foundational binary tree problems and serves as a building block for Diameter, Balanced Tree, and Path Sum problems.
