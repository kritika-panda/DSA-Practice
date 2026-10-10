# Find the Parent of a Node in a Binary Tree

## Problem Statement

Given a Binary Tree and a target node, find the **parent** of the target node.

The **Parent Node** of a node is the node that directly points to it.

---

## Example

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
2
```

Explanation:

```text
2 is the parent of 5
```

---

## Parent Node Visualization

```text
            1
          /   \
         2     3
        / \
       4   5
```

```text
Parent(2) = 1
Parent(3) = 1
Parent(4) = 2
Parent(5) = 2
```

---

# Special Cases

## Target is Root

```text
        1
       / \
      2   3
```

```text
Parent(1) = null
```

Reason:

```text
Root does not have a parent.
```

---

## Target Not Present

```text
Target = 100
```

Output:

```text
null
```

---

# Approach 1: Recursive DFS

## Intuition

For every node:

- Check whether its left child is the target.
- Check whether its right child is the target.
- If found, current node is the parent.
- Otherwise search left and right subtrees.

---

## Algorithm

```text
findParent(node, target)

1. If node is null
      return null

2. If node.left == target
      return node

3. If node.right == target
      return node

4. Search left subtree

5. If found
      return answer

6. Search right subtree

7. Return answer
```

---

## Java Solution

```java
class Solution {

    public TreeNode findParent(TreeNode root, int target) {

        if (root == null || root.val == target) {
            return null;
        }

        if ((root.left != null && root.left.val == target) ||
            (root.right != null && root.right.val == target)) {
            return root;
        }

        TreeNode left = findParent(root.left, target);

        if (left != null) {
            return left;
        }

        return findParent(root.right, target);
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
findParent(1, 5)

1 is not parent

Search Left

findParent(2, 5)

2.right = 5

Parent Found
```

Return:

```text
2
```

---

# Approach 2: Iterative BFS

## Intuition

Level-order traverse the tree.

For every node:

- Check its left child.
- Check its right child.

If any child matches the target, return the current node.

---

## Java Solution

```java
import java.util.LinkedList;
import java.util.Queue;

class Solution {

    public TreeNode findParent(TreeNode root, int target) {

        if (root == null || root.val == target) {
            return null;
        }

        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);

        while (!queue.isEmpty()) {

            TreeNode current = queue.poll();

            if ((current.left != null && current.left.val == target) ||
                (current.right != null && current.right.val == target)) {
                return current;
            }

            if (current.left != null) {
                queue.offer(current.left);
            }

            if (current.right != null) {
                queue.offer(current.right);
            }
        }

        return null;
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
        / \
       4   5
```

### Target = 5

```text
Queue = [1]

Poll 1
Check children

Queue = [2, 3]

Poll 2

2.right = 5

Parent Found
```

Return:

```text
2
```

---

# Approach 3: Store Parent Mapping

This approach is useful when multiple parent queries need to be answered.

### Idea

During traversal:

```text
Child -> Parent
```

Store mapping in a HashMap.

---

## Java Solution

```java
import java.util.HashMap;
import java.util.LinkedList;
import java.util.Map;
import java.util.Queue;

class Solution {

    public TreeNode findParent(TreeNode root, int target) {

        if (root == null || root.val == target) {
            return null;
        }

        Map<TreeNode, TreeNode> parentMap = new HashMap<>();

        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);

        while (!queue.isEmpty()) {

            TreeNode current = queue.poll();

            if (current.left != null) {
                parentMap.put(current.left, current);
                queue.offer(current.left);
            }

            if (current.right != null) {
                parentMap.put(current.right, current);
                queue.offer(current.right);
            }
        }

        for (TreeNode node : parentMap.keySet()) {
            if (node.val == target) {
                return parentMap.get(node);
            }
        }

        return null;
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

---

## Parent Mapping

### Time Complexity

```text
O(n)
```

### Space Complexity

```text
O(n)
```

---

# DFS vs BFS

| Approach | Time | Space |
|-----------|--------|--------|
| Recursive DFS | O(n) | O(h) |
| BFS | O(n) | O(n) |
| Parent Map | O(n) | O(n) |

---

# Related Interview Questions

1. Find Parent of a Node
2. Find Grandparent of a Node
3. Find Sibling of a Node
4. Lowest Common Ancestor (LCA)
5. Nodes at Distance K
6. Burn Binary Tree
7. Time Needed to Inform All Nodes

---

# Visualization

```text
                1
              /   \
             2     3
            / \   / \
           4   5 6   7

Parent(4) = 2
Parent(5) = 2
Parent(6) = 3
Parent(7) = 3
```

---

# Key Takeaways

- Parent of a node is the node directly connected above it.
- Root never has a parent.
- DFS recursively searches for the target's parent.
- BFS checks children level by level.
- Parent mapping is useful when multiple parent queries are required.
- Time Complexity for DFS and BFS is **O(n)**.
- Recursive DFS is usually the simplest interview solution.
