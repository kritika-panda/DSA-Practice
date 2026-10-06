# Search a Node in Binary Tree

## Problem Statement

Given the root of a Binary Tree and a target value, determine whether the target node exists in the tree.

Return:

```text
true  -> if node exists
false -> otherwise
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

### Input

```text
Target = 5
```

### Output

```text
true
```

Explanation:

```text
Node 5 exists in the tree.
```

---

# Example 2

### Binary Tree

```text
            1
          /   \
         2     3
        / \   / \
       4   5 6   7
```

### Input

```text
Target = 10
```

### Output

```text
false
```

Explanation:

```text
Node 10 is not present in the tree.
```

---

# Key Observation

Unlike a Binary Search Tree (BST), a normal Binary Tree does not follow any ordering.

Example:

```text
        10
       /  \
      50   5
```

Since nodes can appear anywhere:

```text
We must potentially visit every node.
```

---

# Approach 1: Recursive DFS (Recommended)

## Intuition

At every node:

1. Check if current node contains the target.
2. Search in the left subtree.
3. Search in the right subtree.
4. If found anywhere, return true.

---

## Algorithm

```text
search(node, target)

1. If node is null
      return false

2. If node value equals target
      return true

3. Search left subtree

4. Search right subtree

5. Return true if found in either subtree
```

---

## Java Solution

```java
class Solution {

    public boolean search(TreeNode root, int target) {

        if (root == null) {
            return false;
        }

        if (root.val == target) {
            return true;
        }

        return search(root.left, target)
                || search(root.right, target);
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
search(1)

1 != 5

Search Left

search(2)

2 != 5

search(4)

4 != 5

Return false

search(5)

5 == 5

Return true
```

Final Result:

```text
true
```

---

# Recursive Tree Visualization

```text
search(1)
   |
   +-- search(2)
          |
          +-- search(4)
          |      |
          |      +-- false
          |
          +-- search(5)
                 |
                 +-- true
```

Answer:

```text
true
```

---

# Approach 2: Iterative DFS Using Stack

## Intuition

The recursive solution uses the system call stack internally.

We can explicitly use a stack and perform DFS ourselves.

---

## Java Solution

```java
import java.util.Stack;

class Solution {

    public boolean search(TreeNode root, int target) {

        if (root == null) {
            return false;
        }

        Stack<TreeNode> stack = new Stack<>();
        stack.push(root);

        while (!stack.isEmpty()) {

            TreeNode current = stack.pop();

            if (current.val == target) {
                return true;
            }

            if (current.right != null) {
                stack.push(current.right);
            }

            if (current.left != null) {
                stack.push(current.left);
            }
        }

        return false;
    }
}
```

---

# DFS Dry Run

### Target = 5

```text
Stack = [1]

Pop 1

Push 3
Push 2

Stack = [3, 2]

Pop 2

Push 5
Push 4

Stack = [3, 5, 4]

Pop 4

Not Found

Pop 5

Found
```

Answer:

```text
true
```

---

# Approach 3: BFS Using Queue

## Intuition

Search nodes level by level.

Traverse the tree using Level Order Traversal.

---

## Java Solution

```java
import java.util.LinkedList;
import java.util.Queue;

class Solution {

    public boolean search(TreeNode root, int target) {

        if (root == null) {
            return false;
        }

        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);

        while (!queue.isEmpty()) {

            TreeNode current = queue.poll();

            if (current.val == target) {
                return true;
            }

            if (current.left != null) {
                queue.offer(current.left);
            }

            if (current.right != null) {
                queue.offer(current.right);
            }
        }

        return false;
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
        / \   / \
       4   5 6   7
```

### Target = 6

```text
Queue = [1]

Poll 1

Queue = [2,3]

Poll 2

Queue = [3,4,5]

Poll 3

Queue = [4,5,6,7]

Poll 4

Poll 5

Poll 6

Found
```

Answer:

```text
true
```

---

# Complexity Analysis

## Recursive DFS

### Time Complexity

```text
O(n)
```

Worst case every node is visited.

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

Worst Case:

```text
O(n)
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

Queue may contain all nodes on a level.

---

# DFS vs BFS

| Approach | Time | Space |
|-----------|--------|--------|
| Recursive DFS | O(n) | O(h) |
| Iterative DFS | O(n) | O(h) |
| BFS | O(n) | O(n) |

---

# Binary Tree vs Binary Search Tree Search

## Binary Tree

```text
No ordering exists.
```

Example:

```text
       10
      /  \
    100    5
```

Search Complexity:

```text
O(n)
```

---

## Binary Search Tree (BST)

```text
Left < Root < Right
```

Example:

```text
       10
      /  \
     5   20
```

Search Complexity:

```text
O(log n)
```

(Balanced BST)

---

# Related Interview Questions

1. Search in Binary Tree
2. Search in BST
3. Find Parent Node
4. Find Level of Node
5. Find Path from Root to Node
6. Lowest Common Ancestor
7. Nodes at Distance K

---

# Visualization

```text
                1
              /   \
             2     3
            / \   / \
           4   5 6   7

Search(5)

1 → 2 → 4 → 5

Found
```

---

# Key Takeaways

- Binary Trees do not maintain any ordering.
- To search a node, we may need to visit every node.
- Recursive DFS is the simplest and most common interview solution.
- Iterative DFS uses a stack.
- BFS uses a queue and searches level by level.
- Time Complexity is **O(n)** for all approaches.
- Space Complexity is:
  - **O(h)** for DFS
  - **O(n)** for BFS
- Binary Tree search is generally less efficient than BST search because no ordering property exists.
