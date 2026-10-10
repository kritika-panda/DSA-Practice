# Insertion in a Binary Tree (Level Order)

## Problem Statement

Given the root of a Binary Tree and a value, insert the new node into the tree using **Level Order Traversal**.

The new node should be inserted at the **first available position** to keep the tree as complete as possible.

---

# Example

### Before Insertion

```text
        1
       / \
      2   3
     / \
    4   5
```

### Insert

```text
6
```

### After Insertion

```text
        1
       / \
      2   3
     / \  /
    4  5 6
```

---

# Why Level Order Insertion?

Unlike a Binary Search Tree (BST), a Binary Tree has no ordering rules.

To keep the tree compact, insert the new node into the first empty position encountered during Level Order Traversal.

---

# Key Idea

Perform BFS using a queue.

For every node:

```text
1. If left child is null
      Insert new node there

2. Else if right child is null
      Insert new node there

3. Otherwise
      Continue BFS
```

---

# Visualization

### Tree

```text
        1
       / \
      2   3
     /
    4
```

### Insert 5

Level-order scan:

```text
Node 1
├── Left exists
└── Right exists

Node 2
├── Left exists
└── Right is null
```

Insert:

```text
        1
       / \
      2   3
     / \
    4   5
```

---

# Approach: BFS Using Queue

## Algorithm

```text
insert(root, value)

1. Create new node

2. If tree is empty
      return new node

3. Insert root into queue

4. While queue is not empty

      a. Remove front node

      b. If left child is null
             Insert node
             Return root

      c. Else add left child

      d. If right child is null
             Insert node
             Return root

      e. Else add right child

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

    public TreeNode insert(TreeNode root, int val) {

        TreeNode newNode = new TreeNode(val);

        if (root == null) {
            return newNode;
        }

        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);

        while (!queue.isEmpty()) {

            TreeNode current = queue.poll();

            if (current.left == null) {
                current.left = newNode;
                return root;
            } else {
                queue.offer(current.left);
            }

            if (current.right == null) {
                current.right = newNode;
                return root;
            } else {
                queue.offer(current.right);
            }
        }

        return root;
    }
}
```

---

# Dry Run

### Initial Tree

```text
        1
       / \
      2   3
     /
    4
```

### Insert

```text
5
```

---

### Step 1

```text
Queue = [1]

Poll 1

Left exists
Right exists

Add 2, 3
```

Queue:

```text
[2, 3]
```

---

### Step 2

```text
Poll 2

Left exists

Right = null
```

Insert:

```text
2.right = 5
```

Tree becomes:

```text
        1
       / \
      2   3
     / \
    4   5
```

---

# Example 2

### Initial Tree

```text
        1
       /
      2
```

### Insert

```text
3
```

### Process

```text
Poll 1

Left exists

Right = null
```

Insert:

```text
1.right = 3
```

Result:

```text
        1
       / \
      2   3
```

---

# Iterative Level Order Traversal

### Tree

```text
        1
       / \
      2   3
     / \
    4   5
```

Traversal Order:

```text
1 → 2 → 3 → 4 → 5
```

Insertion happens at:

```text
First missing child position
```

---

# Complexity Analysis

## Time Complexity

### Worst Case

```text
O(n)
```

We may need to traverse all nodes before finding an empty spot.

---

## Space Complexity

```text
O(n)
```

Queue may store an entire level of the tree.

---

# Special Cases

## Empty Tree

Input:

```text
root = null
value = 10
```

Output:

```text
10
```

Tree:

```text
10
```

---

## Tree Has Only Root

Before:

```text
1
```

Insert:

```text
2
```

After:

```text
    1
   /
  2
```

---

# Difference from BST Insertion

## Binary Tree Insertion

```text
Insert at first available position.
```

Example:

```text
        10
       / \
      5   20
```

Insert:

```text
100
```

Possible Result:

```text
        10
       / \
      5   20
     /
   100
```

---

## BST Insertion

Uses ordering property:

```text
Left < Root < Right
```

Example:

```text
        10
       / \
      5   20
```

Insert:

```text
100
```

Result:

```text
        10
       / \
      5   20
             \
             100
```

---

# Related Interview Questions

1. Insert in Binary Tree
2. Delete Node in Binary Tree
3. Binary Tree Level Order Traversal
4. Complete Binary Tree Inserter
5. Count Nodes in Complete Binary Tree
6. Binary Tree Serialization
7. Maximum Width of Binary Tree

---

# Visualization

```text
Before:

        1
       / \
      2   3
     /
    4

Insert 5

After:

        1
       / \
      2   3
     / \
    4   5
```

---

# Key Takeaways

- Binary Tree insertion is generally performed using **Level Order Traversal (BFS)**.
- Insert at the **first available position**.
- A **Queue** is used to process nodes level by level.
- Unlike BST insertion, Binary Tree insertion does **not** use node values.
- Time Complexity = **O(n)**
- Space Complexity = **O(n)**
- This approach helps maintain a compact and nearly complete tree structure.
