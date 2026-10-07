# Introduction to Binary Tree

## What is a Binary Tree?

A **Binary Tree** is a hierarchical, non-linear data structure where each node can have **at most two children**:

- Left Child
- Right Child

The topmost node is called the **Root**, and nodes with no children are called **Leaf Nodes**.

---

## Binary Tree Structure

Each node consists of:

1. Data
2. Reference to Left Child
3. Reference to Right Child

### Java Representation

```java
class Node {
    int data;
    Node left;
    Node right;

    Node(int data) {
        this.data = data;
        this.left = null;
        this.right = null;
    }
}
```

---

## Basic Terminology

| Term | Description |
|--------|------------|
| Root | Topmost node of the tree |
| Parent Node | Direct ancestor of a node |
| Child Node | Direct descendant of a node |
| Sibling | Nodes having the same parent |
| Leaf Node | Node with no children |
| Internal Node | Node with at least one child |
| Edge | Connection between parent and child |
| Path | Sequence of connected nodes |
| Ancestor | Any node on the path from root to a node |
| Descendant | Any node in the subtree of a node |
| Subtree | Tree formed by a node and its descendants |
| Depth | Number of edges from root to the node |
| Height | Longest path from root to a leaf |

---

## Example Binary Tree

```text
        2
       / \
      3   4
     /
    5
```

### Creating the Above Tree in Java

```java
class Node {
    int data;
    Node left;
    Node right;

    Node(int data) {
        this.data = data;
    }
}

public class Main {
    public static void main(String[] args) {

        Node root = new Node(2);
        root.left = new Node(3);
        root.right = new Node(4);
        root.left.left = new Node(5);
    }
}
```

---

## Properties of Binary Tree

### 1. Maximum Nodes at Level L

```text
2^L
```

Where:

- Root is at Level 0

Example:

```text
Level 0 → 1 node
Level 1 → 2 nodes
Level 2 → 4 nodes
Level 3 → 8 nodes
```

---

### 2. Maximum Nodes in a Binary Tree of Height H

A binary tree has the maximum number of nodes when **every level is completely filled** (Perfect Binary Tree).

### Pattern

```text
Height = 0

Level 0 -> 1 node

Total Nodes = 1
```

```text
Height = 1

Level 0 -> 1 node
Level 1 -> 2 nodes

Total Nodes = 1 + 2 = 3
```

```text
Height = 2

Level 0 -> 1 node
Level 1 -> 2 nodes
Level 2 -> 4 nodes

Total Nodes = 1 + 2 + 4 = 7
```

```text
Height = 3

Level 0 -> 1 node
Level 1 -> 2 nodes
Level 2 -> 4 nodes
Level 3 -> 8 nodes

Total Nodes = 1 + 2 + 4 + 8 = 15
```

Notice that the number of nodes doubles at every level:

```text
Level 0 -> 2^0
Level 1 -> 2^1
Level 2 -> 2^2
...
Level H -> 2^H
```

Therefore:

```text
Maximum Nodes

= 2^0 + 2^1 + 2^2 + ... + 2^H
```

This is a Geometric Progression (GP).

### Derivation

Let

```text
S = 1 + 2 + 4 + 8 + ... + 2^H
```

Multiply both sides by 2:

```text
2S = 2 + 4 + 8 + ... + 2^H + 2^(H+1)
```

Now subtract:

```text
2S = 2 + 4 + 8 + ... + 2^H + 2^(H+1)
 S = 1 + 2 + 4 + ... + 2^H
----------------------------------
2S-S = 2^(H+1) - 1
```

Explanation:

```text
+2     -2     = 0
+4     -4     = 0
+8     -8     = 0
...
+2^H   -2^H   = 0
```

All middle terms cancel out.

The only surviving terms are:

```text
+2^(H+1)
-1
```

Therefore:

```text
S = 2^(H+1) - 1
```

Hence,

```text
Maximum Nodes in a Binary Tree of Height H

= 2^(H + 1) - 1
```

### Example

```text
Height = 3

Maximum Nodes
= 2^(3+1) - 1
= 2^4 - 1
= 16 - 1
= 15
```

### Quick Reference

| Height (H) | Maximum Nodes |
|------------|---------------|
| 0 | 1 |
| 1 | 3 |
| 2 | 7 |
| 3 | 15 |
| 4 | 31 |
| 5 | 63 |

---

### 3. Leaf Node Relationship

```text
Number of Leaf Nodes
=
(Number of Nodes Having Two Children) + 1
```

---

### 4. Minimum Height for N Nodes

```text
⌊log₂(N)⌋
```

---

### 5. Minimum Levels for L Leaves

```text
⌈log₂(L)⌉ + 1
```

---

## Common Operations on Binary Tree

### 1. Traversal

Visit all nodes in a tree.

#### Depth First Search (DFS)

- Preorder Traversal
- Inorder Traversal
- Postorder Traversal

#### Breadth First Search (BFS)

- Level Order Traversal

---

### 2. Search

Find a node containing a specific value.

---

### 3. Insertion

Add a new node while maintaining tree structure.

---

### 4. Deletion

Remove a node and reorganize the tree accordingly.

---

## Advantages

✅ Efficient searching in specialized forms such as BST

✅ Naturally represents hierarchical data

✅ Easy to understand and implement

✅ Useful in recursive algorithms

---

## Disadvantages

❌ Limited to two children per node

❌ Extra memory needed for child references

❌ Can become unbalanced leading to poor performance

---

## Applications

### 1. Hierarchical Data Representation

Examples:

- File Systems
- Organizational Structures

### 2. Binary Search Trees (BST)

Efficient searching, insertion, and deletion operations.

### 3. Huffman Coding

Used in data compression algorithms.

### 4. Decision Trees

Used in machine learning for classification and regression.

### 5. Expression Trees

Used in compilers and expression evaluation.

### 6. Database Indexing

Various indexing structures are derived from tree-based concepts.

---

## Key Takeaways

- A Binary Tree is a hierarchical structure where each node has at most two children.
- Every node contains data, a left child reference, and a right child reference.
- DFS and BFS are the primary traversal techniques.
- Binary Trees are widely used in searching, compression, indexing, and machine learning.
- Understanding Binary Trees is essential before learning:
  - Binary Search Trees (BST)
  - AVL Trees
  - Red-Black Trees
  - Heaps
  - Segment Trees
  - Tries

---

## Reference

- [Introduction to Binary Tree - GeeksforGeeks](https://www.geeksforgeeks.org/dsa/introduction-to-binary-tree/)
