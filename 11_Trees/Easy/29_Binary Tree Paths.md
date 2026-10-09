# Binary Tree Paths

## Problem

Given the `root` of a binary tree, return all root-to-leaf paths in any order.

A leaf node is a node with no children.

### Example

**Input**

```text
      1
     / \
    2   3
     \
      5
```

**Output**

```text
["1->2->5", "1->3"]
```

---

## Approach

Use Depth First Search (DFS).

For each node:

1. Add its value to the current path.
2. If it is a leaf node, add the path to the result.
3. Otherwise continue exploring the left and right subtrees.

---

## Java Solution

```java
import java.util.*;

class Solution {

    public List<String> binaryTreePaths(TreeNode root) {
        List<String> result = new ArrayList<>();

        dfs(root, "", result);

        return result;
    }

    private void dfs(TreeNode node, String path, List<String> result) {
        if (node == null) {
            return;
        }

        path += node.val;

        if (node.left == null && node.right == null) {
            result.add(path);
            return;
        }

        path += "->";

        dfs(node.left, path, result);
        dfs(node.right, path, result);
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** `O(n)`
  
  Every node is visited once.

- **Space Complexity:** `O(h)`
  
  Due to recursion stack.

  - Balanced Tree: `O(log n)`
  - Skewed Tree: `O(n)`

---

## Key Idea

Build a path while traversing the tree. Whenever a leaf node is reached, store the completed root-to-leaf path.
