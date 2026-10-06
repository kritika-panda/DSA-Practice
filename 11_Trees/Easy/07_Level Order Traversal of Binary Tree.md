# Level Order Traversal (Breadth First Search) of Binary Tree

## What is Level Order Traversal?

**Level Order Traversal** is a Breadth First Search (**BFS**) traversal technique in which nodes are visited level by level from top to bottom.

Traversal order:

```text
Level 0 → Level 1 → Level 2 → ...
```

Unlike DFS traversals (Inorder, Preorder, Postorder), BFS explores all nodes at the current level before moving to the next level.

---

## Example

### Binary Tree

```text
        1
       / \
      2   3
     / \   \
    4   5   6
```

### Level Order Traversal

```text
1 → 2 → 3 → 4 → 5 → 6
```

### Explanation

```text
Level 0: 1

Level 1: 2, 3

Level 2: 4, 5, 6
```

Result:

```text
[1, 2, 3, 4, 5, 6]
```

---

# Intuition

To process nodes level by level, we need to remember the nodes discovered but not yet visited.

A **Queue** follows the FIFO (First In First Out) principle, making it perfect for BFS traversal.

---

# Algorithm

```text
1. Create an empty queue.
2. Insert root node into queue.
3. While queue is not empty:
      a. Remove front node.
      b. Visit it.
      c. Add its left child.
      d. Add its right child.
4. Continue until queue becomes empty.
```

---

# Java Implementation

```java
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;

class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;

    TreeNode(int val) {
        this.val = val;
    }
}

public class LevelOrderTraversal {

    public static List<Integer> levelOrder(TreeNode root) {

        List<Integer> result = new ArrayList<>();

        if (root == null) {
            return result;
        }

        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);

        while (!queue.isEmpty()) {

            TreeNode current = queue.poll();
            result.add(current.val);

            if (current.left != null) {
                queue.offer(current.left);
            }

            if (current.right != null) {
                queue.offer(current.right);
            }
        }

        return result;
    }

    public static void main(String[] args) {

        TreeNode root = new TreeNode(1);
        root.left = new TreeNode(2);
        root.right = new TreeNode(3);
        root.left.left = new TreeNode(4);
        root.left.right = new TreeNode(5);
        root.right.right = new TreeNode(6);

        System.out.println(levelOrder(root));
    }
}
```

### Output

```text
[1, 2, 3, 4, 5, 6]
```

---

# Dry Run

### Tree

```text
        1
       / \
      2   3
     / \   \
    4   5   6
```

### Queue Simulation

```text
Queue = [1]

Poll 1
Visit 1
Add 2, 3

Queue = [2, 3]

Poll 2
Visit 2
Add 4, 5

Queue = [3, 4, 5]

Poll 3
Visit 3
Add 6

Queue = [4, 5, 6]

Poll 4
Visit 4

Queue = [5, 6]

Poll 5
Visit 5

Queue = [6]

Poll 6
Visit 6

Queue = []
```

Output:

```text
1 2 3 4 5 6
```

---

# Level Wise Traversal

Sometimes interviewers ask for nodes grouped by levels.

### Output Format

```text
[
  [1],
  [2, 3],
  [4, 5, 6]
]
```

---

## Java Implementation

```java
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;

public class LevelOrderTraversal {

    public List<List<Integer>> levelOrder(TreeNode root) {

        List<List<Integer>> result = new ArrayList<>();

        if (root == null) {
            return result;
        }

        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);

        while (!queue.isEmpty()) {

            int size = queue.size();
            List<Integer> level = new ArrayList<>();

            for (int i = 0; i < size; i++) {

                TreeNode node = queue.poll();
                level.add(node.val);

                if (node.left != null) {
                    queue.offer(node.left);
                }

                if (node.right != null) {
                    queue.offer(node.right);
                }
            }

            result.add(level);
        }

        return result;
    }
}
```

### Output

```text
[
  [1],
  [2, 3],
  [4, 5, 6]
]
```

---

# Why Does `queue.size()` Work?

At the start of each iteration:

```java
int size = queue.size();
```

The queue contains all nodes belonging to the current level.

By processing exactly `size` nodes:

```java
for (int i = 0; i < size; i++)
```

we ensure that only nodes from the current level are processed.

Any newly added children automatically belong to the next level.

---

# Complexity Analysis

## Standard BFS Traversal

### Time Complexity

```text
O(n)
```

Each node is visited exactly once.

### Space Complexity

```text
O(n)
```

In the worst case, the queue may store an entire level of the tree.

For a complete binary tree:

```text
Maximum Queue Size ≈ n/2
```

---

# BFS vs DFS

| Traversal | Order |
|------------|---------|
| Preorder | Root → Left → Right |
| Inorder | Left → Root → Right |
| Postorder | Left → Right → Root |
| Level Order | Level by Level |

---

# Applications of Level Order Traversal

## 1. Finding Nodes Level by Level

```text
Level 0
Level 1
Level 2
...
```

---

## 2. Shortest Path Problems

BFS naturally finds the shortest path in unweighted graphs.

---

## 3. Binary Tree Serialization

Used to convert trees into arrays/strings.

Example:

```text
[1,2,3,4,5,null,6]
```

---

## 4. Finding Tree Width

Maximum number of nodes at any level.

---

## 5. Zigzag Traversal

A variation of Level Order Traversal.

```text
Left → Right
Right → Left
Left → Right
...
```

---

# Visualization

```text
                1
             /     \
            2       3
          /   \      \
         4     5      6

Level 0 → 1

Level 1 → 2, 3

Level 2 → 4, 5, 6
```

Traversal:

```text
1 → 2 → 3 → 4 → 5 → 6
```

---

# Key Takeaways

- Level Order Traversal is a **Breadth First Search (BFS)** technique.
- Nodes are visited **level by level**.
- A **Queue** is used to maintain traversal order.
- Every node is visited exactly once.
- Time Complexity is **O(n)**.
- Space Complexity is **O(n)**.
- Forms the basis for:
  - Level-wise traversal
  - Zigzag traversal
  - Serialization/Deserialization
  - Width calculation
  - Shortest path algorithms
