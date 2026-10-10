# Properties of Binary Tree

## Overview

A Binary Tree is a hierarchical data structure where each node can have at most two children.

> **Note:** Height of the root node is considered **0**.

---

# 1. Maximum Nodes at Level `l`

A binary tree can have at most:

```text
2^l
```

nodes at level `l`.

### Level Definition

- Level = Number of edges from the root to the node.
- Root is at level `0`.

### Example

```text
Level 0 → 1 node
Level 1 → 2 nodes
Level 2 → 4 nodes
Level 3 → 8 nodes
```

### Formula

```text
Maximum Nodes at Level l = 2^l
```

---

# 2. Maximum Nodes in a Binary Tree of Height `h`

A binary tree of height `h` can contain at most:

```text
2^(h + 1) - 1
```

nodes.

### Height Definition

Height = Number of edges on the longest path from root to leaf.

```text
Root Only Tree → Height = 0
Empty Tree     → Height = -1
```

### Derivation

When all levels are completely filled:

```text
1 + 2 + 4 + ... + 2^h
```

This forms a geometric progression:

```text
Total Nodes = 2^(h + 1) - 1
```

### Example

```text
Height = 3

Nodes
= 2^(3 + 1) - 1
= 16 - 1
= 15
```

---

# 3. Minimum Height for `N` Nodes

The minimum possible height for a binary tree with `N` nodes is:

```text
⌊log₂(N)⌋
```

### Explanation

Since:

```text
N ≤ 2^(h + 1) - 1
```

Rearranging:

```text
2^(h + 1) ≥ N + 1

h ≥ log₂(N + 1) - 1
```

Therefore:

```text
Minimum Height = ⌊log₂(N)⌋
```

### Example

```text
N = 15

Minimum Height
= ⌊log₂(15)⌋
= 3
```

---

# 4. Minimum Levels for `L` Leaves

A binary tree containing `L` leaf nodes requires at least:

```text
⌊log₂(L)⌋
```

levels.

### Why?

Maximum leaf nodes at level `l`:

```text
L ≤ 2^l
```

Taking logarithm:

```text
l = ⌊log₂(L)⌋
```

### Example

```text
Leaves = 8

Minimum Levels
= ⌊log₂(8)⌋
= 3
```

---

# 5. Relationship Between Leaf Nodes and Full Nodes

For a **Full Binary Tree**:

```text
Number of Leaf Nodes = Number of Nodes with Two Children + 1
```

or

```text
L = T + 1
```

Where:

- `L` = Leaf Nodes
- `T` = Internal Nodes having exactly two children

### Example

```text
Nodes with 2 children = 4

Leaf Nodes
= 4 + 1
= 5
```

---

# 6. Total Edges in a Binary Tree

For any non-empty binary tree:

```text
Edges = Nodes - 1
```

or

```text
Edges = n - 1
```

### Explanation

Every node except the root has exactly one parent.

```text
n nodes
⇒ n - 1 parent-child connections
⇒ n - 1 edges
```

### Example

```text
Nodes = 10

Edges = 10 - 1
      = 9
```

---

# Additional Properties

## Node Classification

A node can have:

### 0 Children

```text
Leaf Node
```

### 1 Child

```text
Unary Node
```

### 2 Children

```text
Binary Node
```

---

# Types of Binary Trees

## Full Binary Tree

Every non-leaf node has exactly two children.

```text
        1
       / \
      2   3
     / \
    4   5
```

---

## Complete Binary Tree

- All levels completely filled except possibly the last.
- Last level filled from left to right.

```text
        1
      /   \
     2     3
    / \   /
   4   5 6
```

---

## Perfect Binary Tree

- Every level is completely filled.
- All leaves are at the same depth.

```text
        1
      /   \
     2     3
    / \   / \
   4  5  6  7
```

---

## Balanced Binary Tree

Height difference between left and right subtree is at most 1.

```text
| Height(Left) - Height(Right) | ≤ 1
```

---

# Tree Traversal Methods

Traversal is broadly divided into:

## 1. Depth First Search (DFS)

### Inorder Traversal (LNR)

```text
Left → Node → Right
```

Used in BST to retrieve elements in sorted order.

---

### Preorder Traversal (NLR)

```text
Node → Left → Right
```

Used for tree reconstruction and serialization.

---

### Postorder Traversal (LRN)

```text
Left → Right → Node
```

Used for:

- Tree deletion
- Expression evaluation

---

## 2. Breadth First Search (BFS)

### Level Order Traversal

```text
Level by Level
```

Example:

```text
        1
      /   \
     2     3

Traversal:
1 2 3
```

---

### Zig-Zag Traversal

Alternate traversal direction at every level.

```text
Level 0 → Left to Right
Level 1 → Right to Left
Level 2 → Left to Right
...
```

Example:

```text
        1
      /   \
     2     3
    / \   / \
   4  5  6  7

Output:
1 3 2 4 5 6 7
```

---

# Quick Revision Sheet

| Property | Formula |
|-----------|---------|
| Maximum Nodes at Level `l` | `2^l` |
| Maximum Nodes for Height `h` | `2^(h+1) - 1` |
| Minimum Height for `N` Nodes | `⌊log₂(N)⌋` |
| Minimum Levels for `L` Leaves | `⌊log₂(L)⌋` |
| Edges in Binary Tree | `n - 1` |
| Full Binary Tree Relation | `Leaf Nodes = Two-Child Nodes + 1` |

---

# Reference

- [Properties of Binary Tree - GeeksforGeeks](https://www.geeksforgeeks.org/dsa/properties-of-binary-tree/)
