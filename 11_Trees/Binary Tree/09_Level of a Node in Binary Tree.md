# Level of a Node in Binary Tree

## Problem Statement

Given a Binary Tree and a target node, find the **Level** of that node.

The **Level of a Node** is the number of edges on the path from the root to that node.

---

## Definition

```text
Level(Node)
=
Number of edges from Root to Node
```

### Important Notes

```text
Root Level = 0
```

Some books consider:

```text
Root Level = 1
```

In most Data Structures and Algorithm problems:

```text
Root Level = 0
```

---

# Example

### Binary Tree

```text
            1
          /   \
         2     3
        / \     \
       4   5     6
```

### Levels

```text
Level(1) = 0

Level(2) = 1
Level(3) = 1

Level(4) = 2
Level(5) = 2
Level(6) = 2
```

---

# Visualization

```text
Level 0        1
             /   \
Level 1     2     3
           / \     \
Level 2   4   5     6
```

---

# Approach 1: Recursive DFS

## Intuition

While traversing the tree:

- Maintain the current level.
- Whenever the target node is found, return the current level.
- Otherwise search both subtrees.

---

## Algorithm

```text
findLevel(node, target, level)

1. If node is null
      return -1

2. If node value equals target
      return level

3. Search left subtree

4. If found
      return answer

5. Search right subtree

6. Return answer
```

---

## Java Solution

```java
class Solution {

    public int findLevel(TreeNode root, int target) {
        return dfs(root, target, 0);
    }

    private int dfs(TreeNode node, int target, int level) {

        if (node == null) {
            return -1;
        }

        if (node.val == target) {
            return level;
        }

        int left = dfs(node.left, target, level + 1);

        if (left != -1) {
            return left;
        }

        return dfs(node.right, target, level + 1);
    }
}
```

---

# Dry Run

### Tree

```text
            1
          /   \
         2     3
        / \
       4   5
```

### Target = 5

```text
dfs(1,5,0)

dfs(2,5,1)

dfs(4,5,2)
Not Found

dfs(5,5,2)
Found
```

Return:

```text
2
```

---

# Approach 2: Level Order Traversal (BFS)

## Intuition

Since BFS naturally processes nodes level by level:

- Track current level.
- When target is found, return the current level.

---

## Algorithm

```text
1. Insert root into queue.
2. Initialize level = 0.
3. Process nodes level-wise.
4. If current node equals target:
      return level
5. Move to next level.
```

---

## Java Solution

```java
import java.util.LinkedList;
import java.util.Queue;

class Solution {

    public int findLevel(TreeNode root, int target) {

        if (root == null) {
            return -1;
        }

        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);

        int level = 0;

        while (!queue.isEmpty()) {

            int size = queue.size();

            for (int i = 0; i < size; i++) {

                TreeNode node = queue.poll();

                if (node.val == target) {
                    return level;
                }

                if (node.left != null) {
                    queue.offer(node.left);
                }

                if (node.right != null) {
                    queue.offer(node.right);
                }
            }

            level++;
        }

        return -1;
    }
}
```

---

# BFS Dry Run

### Tree

```text
            1
          /   \
         2     3
        / \     \
       4   5     6
```

### Target = 5

#### Level 0

```text
Queue = [1]

Visit 1

Level = 0
```

#### Level 1

```text
Queue = [2, 3]

Visit 2
Visit 3

Level = 1
```

#### Level 2

```text
Queue = [4, 5, 6]

Visit 4
Visit 5

Found Target
```

Answer:

```text
2
```

---

# Complexity Analysis

## Recursive DFS

### Time Complexity

```text
O(n)
```

Worst case, every node is visited.

### Space Complexity

```text
O(h)
```

Where:

```text
h = Height of Tree
```

Worst Case:

```text
O(n)
```

Balanced Tree:

```text
O(log n)
```

---

## BFS

### Time Complexity

```text
O(n)
```

### Space Complexity

```text
O(n)
```

Queue may contain an entire level.

---

# Special Cases

## Root Node

```text
       10
```

```text
Level(10) = 0
```

---

## Node Not Present

```text
Target = 100
```

Output:

```text
-1
```

---

## Empty Tree

```text
root = null
```

Output:

```text
-1
```

---

# DFS vs BFS

| Approach | Time | Space |
|-----------|--------|--------|
| DFS | O(n) | O(h) |
| BFS | O(n) | O(n) |

---

# Relation Between Depth and Level

For a node:

```text
Depth = Level
```

Since both represent:

```text
Number of edges from root to that node
```

Example:

```text
            1
           /
          2
         /
        3
```

```text
Node 3

Depth = 2
Level = 2
```

---

# LeetCode / Interview Variations

Common interview questions related to node level:

1. Find Level of Given Node
2. Print Nodes at Kth Level
3. Average of Levels in Binary Tree
4. Maximum Width of Binary Tree
5. Deepest Node in Binary Tree
6. Level Order Traversal
7. Zigzag Level Order Traversal

---

# Key Takeaways

- Level of a node = Number of edges from root to the node.
- Root is generally considered at **Level 0**.
- DFS can find the level by carrying the current level during recursion.
- BFS naturally processes nodes level by level.
- Time Complexity is **O(n)** for both approaches.
- The level of a node is numerically equal to its depth.
