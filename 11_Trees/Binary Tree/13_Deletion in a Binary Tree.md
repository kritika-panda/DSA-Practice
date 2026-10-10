# Deletion in a Binary Tree

## Problem Statement

Given the root of a Binary Tree and a key, delete the node containing the key.

Unlike a Binary Search Tree (BST), a Binary Tree does **not** maintain any ordering property. Therefore, deletion is performed differently.

---

# Key Idea

To delete a node in a Binary Tree:

1. Find the node to be deleted.
2. Find the deepest rightmost node in the tree.
3. Replace the target node's value with the deepest node's value.
4. Delete the deepest node.

This preserves the structure of the Binary Tree.

---

# Example

### Original Tree

```text
            1
          /   \
         2     3
        / \   /
       4   5 6
```

Delete:

```text
2
```

---

## Step 1: Find Node to Delete

```text
Target Node = 2
```

---

## Step 2: Find Deepest Rightmost Node

```text
Deepest Rightmost Node = 6
```

```text
            1
          /   \
         2     3
        / \   /
       4   5 6
             ↑
```

---

## Step 3: Copy Value

Replace:

```text
2 → 6
```

Tree becomes:

```text
            1
          /   \
         6     3
        / \
       4   5
```

---

## Step 4: Delete Deepest Node

Remove original node 6.

Final Tree:

```text
            1
          /   \
         6     3
        / \
       4   5
```

---

# Why Not Directly Delete?

Suppose we directly remove node 2:

```text
            1
          /   \
         X     3
        / \
       4   5
```

This would disconnect an entire subtree.

Hence we replace the node with the deepest node.

---

# Approach: BFS (Level Order Traversal)

## Algorithm

```text
delete(root, key)

1. If tree is empty
      return null

2. Find:
      a. Target node
      b. Deepest node

3. Copy deepest node value into target node

4. Delete deepest node

5. Return root
```

---

# Java Solution

```java
import java.util.LinkedList;
import java.util.Queue;

class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;

    TreeNode(int val) {
        this.val = val;
    }
}

class Solution {

    public TreeNode deleteNode(TreeNode root, int key) {

        if (root == null) {
            return null;
        }

        if (root.left == null && root.right == null) {
            return root.val == key ? null : root;
        }

        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);

        TreeNode target = null;
        TreeNode current = null;

        while (!queue.isEmpty()) {

            current = queue.poll();

            if (current.val == key) {
                target = current;
            }

            if (current.left != null) {
                queue.offer(current.left);
            }

            if (current.right != null) {
                queue.offer(current.right);
            }
        }

        if (target != null) {

            int deepestValue = current.val;

            deleteDeepest(root, current);

            target.val = deepestValue;
        }

        return root;
    }

    private void deleteDeepest(TreeNode root, TreeNode deepest) {

        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);

        while (!queue.isEmpty()) {

            TreeNode current = queue.poll();

            if (current.left != null) {

                if (current.left == deepest) {
                    current.left = null;
                    return;
                }

                queue.offer(current.left);
            }

            if (current.right != null) {

                if (current.right == deepest) {
                    current.right = null;
                    return;
                }

                queue.offer(current.right);
            }
        }
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
        / \   /
       4   5 6
```

### Delete = 2

---

### BFS Traversal

```text
Visit 1
Visit 2   ← Target
Visit 3
Visit 4
Visit 5
Visit 6   ← Deepest Node
```

---

### Replace

```text
Target = 2

Deepest = 6
```

Change:

```text
2 → 6
```

Tree:

```text
            1
          /   \
         6     3
        / \   /
       4   5 6
```

---

### Delete Deepest Node

```text
Original 6 removed
```

Final:

```text
            1
          /   \
         6     3
        / \
       4   5
```

---

# Special Cases

## Empty Tree

```text
root = null
```

Output:

```text
null
```

---

## Tree Contains Only Root

Before:

```text
1
```

Delete:

```text
1
```

After:

```text
null
```

---

## Node Not Present

```text
Delete = 100
```

Tree remains unchanged.

---

# Complexity Analysis

## Time Complexity

### Searching Target Node

```text
O(n)
```

### Finding Deepest Node

```text
O(n)
```

### Deleting Deepest Node

```text
O(n)
```

Overall:

```text
O(n)
```

---

## Space Complexity

```text
O(n)
```

Because BFS queue may store an entire level.

---

# Difference Between Binary Tree and BST Deletion

## Binary Tree

```text
1. Find target node
2. Find deepest node
3. Replace value
4. Delete deepest node
```

---

## Binary Search Tree

Uses ordering property:

```text
Left < Root < Right
```

Cases:

1. Leaf Node
2. One Child
3. Two Children

Typically uses:

```text
Inorder Successor
or
Inorder Predecessor
```

---

# Visualization

### Before Deletion

```text
            1
          /   \
         2     3
        / \   /
       4   5 6
```

Delete:

```text
2
```

---

### After Replacement

```text
            1
          /   \
         6     3
        / \   /
       4   5 6
```

---

### After Deepest Node Removal

```text
            1
          /   \
         6     3
        / \
       4   5
```

---

# Related Interview Questions

1. Insert in Binary Tree
2. Search in Binary Tree
3. Level Order Traversal
4. Delete Node in BST
5. Complete Binary Tree Inserter
6. Counting Nodes
7. Deepest Node in Binary Tree

---

# Key Takeaways

- Binary Tree deletion is different from BST deletion.
- Find the target node and the deepest rightmost node.
- Replace the target node's value with the deepest node's value.
- Delete the original deepest node.
- BFS (Level Order Traversal) is used for both operations.
- Time Complexity = **O(n)**
- Space Complexity = **O(n)**
- This approach preserves the structure of the Binary Tree after deletion.
