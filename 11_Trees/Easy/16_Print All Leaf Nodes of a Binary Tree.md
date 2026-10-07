# Print All Leaf Nodes of a Binary Tree

## Problem Statement

Given a Binary Tree, print all the **leaf nodes** from left to right.

A **leaf node** is a node that has:

```text
No Left Child
AND
No Right Child
```

---

# Example 1

### Binary Tree

```text
            1
          /   \
         2     3
        / \   / \
       4   5 6   7
```

### Output

```text
4 5 6 7
```

Explanation:

```text
Leaf Nodes = 4, 5, 6, 7
```

---

# Example 2

### Binary Tree

```text
            1
          /   \
         2     3
        /
       4
```

### Output

```text
4 3
```

Explanation:

```text
Leaf Nodes = 4, 3
```

---

# What is a Leaf Node?

A node is a leaf if:

```java
node.left == null && node.right == null
```

### Example

```text
        10
       /  \
      20   30
          /  \
         40  50
```

Leaf Nodes:

```text
20, 40, 50
```

---

# Approach 1: DFS (Recursive)

## Intuition

Traverse the entire tree.

For each node:

1. Check if it is a leaf.
2. Print it if it is.
3. Continue traversal.

Using DFS naturally prints leaves from left to right.

---

## Algorithm

```text
printLeaves(node)

1. If node is null
      return

2. If node is a leaf
      print node value
      return

3. Recur on left subtree

4. Recur on right subtree
```

---

## Java Solution

```java
class Solution {

    public void printLeaves(TreeNode root) {
        dfs(root);
    }

    private void dfs(TreeNode node) {

        if (node == null) {
            return;
        }

        if (node.left == null && node.right == null) {
            System.out.print(node.val + " ");
            return;
        }

        dfs(node.left);
        dfs(node.right);
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
        / \   / \
       4   5 6   7
```

### Recursive Flow

```text
dfs(1)

dfs(2)

dfs(4)
Print 4

dfs(5)
Print 5

dfs(3)

dfs(6)
Print 6

dfs(7)
Print 7
```

Output:

```text
4 5 6 7
```

---

# Recursive Tree Visualization

```text
                1
             /     \
            2       3
          /   \   /   \
         4     5 6     7

Visit Order:

1 -> 2 -> 4 -> 5 -> 3 -> 6 -> 7
```

Printed Nodes:

```text
4 5 6 7
```

---

# Approach 2: Iterative DFS Using Stack

## Intuition

Simulate DFS using an explicit stack.

Whenever a leaf node is encountered:

```text
Print it
```

---

## Java Solution

```java
import java.util.Stack;

class Solution {

    public void printLeaves(TreeNode root) {

        if (root == null) {
            return;
        }

        Stack<TreeNode> stack = new Stack<>();
        stack.push(root);

        while (!stack.isEmpty()) {

            TreeNode current = stack.pop();

            if (current.left == null &&
                current.right == null) {

                System.out.print(current.val + " ");
            }

            if (current.right != null) {
                stack.push(current.right);
            }

            if (current.left != null) {
                stack.push(current.left);
            }
        }
    }
}
```

---

# Approach 3: BFS (Level Order Traversal)

## Intuition

Traverse all nodes level by level.

Whenever a leaf node is encountered:

```text
Print it
```

---

## Java Solution

```java
import java.util.LinkedList;
import java.util.Queue;

class Solution {

    public void printLeaves(TreeNode root) {

        if (root == null) {
            return;
        }

        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);

        while (!queue.isEmpty()) {

            TreeNode current = queue.poll();

            if (current.left == null &&
                current.right == null) {

                System.out.print(current.val + " ");
            }

            if (current.left != null) {
                queue.offer(current.left);
            }

            if (current.right != null) {
                queue.offer(current.right);
            }
        }
    }
}
```

---

# Complexity Analysis

## Recursive DFS

### Time Complexity

```text
O(n)
```

Every node is visited once.

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

## Iterative DFS

### Time Complexity

```text
O(n)
```

### Space Complexity

```text
O(h)
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

---

# Follow-up: Count Leaf Nodes

Instead of printing:

```java
class Solution {

    public int countLeaves(TreeNode root) {

        if (root == null) {
            return 0;
        }

        if (root.left == null &&
            root.right == null) {
            return 1;
        }

        return countLeaves(root.left)
             + countLeaves(root.right);
    }
}
```

### Example

```text
            1
          /   \
         2     3
        / \   / \
       4   5 6   7
```

Output:

```text
4
```

---

# Visualization

```text
                1
             /     \
            2       3
          /   \   /   \
         4     5 6     7

Leaf Nodes:
4 5 6 7
```

---

# Related Interview Questions

1. Count Leaf Nodes
2. Sum of Leaf Nodes
3. Deepest Leaf Node
4. Left Leaf Sum
5. Right Leaf Sum
6. Boundary Traversal
7. Root to Leaf Paths
8. Leaf Similar Trees

---

# Key Takeaways

- A leaf node has no children.

```java
node.left == null && node.right == null
```

- DFS naturally prints leaf nodes from left to right.
- Recursive DFS is the most common interview solution.
- Every node is visited exactly once.
- Time Complexity = **O(n)**
- Space Complexity = **O(h)**
- Printing leaves is a common subproblem used in Boundary Traversal and Root-to-Leaf path problems.
