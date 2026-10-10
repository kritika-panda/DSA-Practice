# Types of Binary Tree

A **Binary Tree** is a hierarchical data structure in which each node has at most two children:

- Left Child
- Right Child

---

# Classification of Binary Trees

Binary Trees can be classified based on:

1. Number of Children
2. Level Completion
3. Special Binary Search Tree Variants

---

# 1. Based on Number of Children

## Full Binary Tree

A Binary Tree is called a **Full Binary Tree** when every node has either:

- 0 children, or
- 2 children

No node can have only one child.

### Example

```text
        1
      /   \
     2     3
    / \
   4   5
```

### Characteristics

- Every internal node has exactly two children.
- Also known as:
  - Proper Binary Tree
  - Strict Binary Tree

---

## Degenerate Binary Tree

A tree where every internal node has exactly one child.

### Example

```text
1
 \
  2
   \
    3
     \
      4
```

### Characteristics

- Behaves like a Linked List.
- Search operations may degrade to O(n).

---

## Skewed Binary Tree

A special form of Degenerate Tree where nodes are concentrated on one side.

### Left Skewed Tree

```text
      1
     /
    2
   /
  3
 /
4
```

### Right Skewed Tree

```text
1
 \
  2
   \
    3
     \
      4
```

### Characteristics

- Worst-case height = Number of Nodes - 1
- Search may become O(n)

---

# 2. Based on Level Completion

## Complete Binary Tree

A Binary Tree is Complete if:

- Every level is completely filled except possibly the last.
- Nodes of the last level appear from left to right.

### Example

```text
        1
      /   \
     2     3
    / \   /
   4   5 6
```

### Characteristics

✅ All levels filled except possibly the last

✅ Last level filled from left to right

✅ Used in Heap Data Structure

---

## Perfect Binary Tree

A Binary Tree is Perfect when:

- Every internal node has exactly 2 children.
- All leaf nodes are at the same level.

### Example

```text
          1
       /     \
      2       3
     / \     / \
    4   5   6   7
```

### Properties

If height = h

```text
Total Nodes = 2^(h+1) - 1

Leaf Nodes = 2^h
```

### Characteristics

✅ Completely filled

✅ All leaves at same depth

✅ Maximum nodes for given height

---

## Balanced Binary Tree

A Binary Tree is Balanced when its height remains proportional to:

```text
O(log n)
```

### Example

```text
        10
       /  \
      5    15
     / \     \
    2   7     20
```

### Characteristics

✅ Faster searching

✅ Faster insertion

✅ Faster deletion

✅ Height remains logarithmic

---

# Special Types of Binary Trees

These are advanced tree structures derived from binary tree concepts.

---

## Binary Search Tree (BST)

A BST follows:

```text
Left Subtree < Root < Right Subtree
```

### Example

```text
        8
       / \
      3   10
     / \    \
    1   6    14
```

### Rules

- Left child values are smaller.
- Right child values are greater.
- Subtrees must also satisfy BST properties.

### Time Complexity

| Operation | Average |
|------------|----------|
| Search | O(log n) |
| Insert | O(log n) |
| Delete | O(log n) |

---

## AVL Tree

AVL Tree is a self-balancing BST.

### Balancing Condition

```text
| Height(Left) - Height(Right) | ≤ 1
```

### Characteristics

✅ Always balanced

✅ Guaranteed O(log n) operations

✅ Uses rotations to maintain balance

---

## Red-Black Tree

A self-balancing Binary Search Tree.

Each node contains:

- Data
- Color (Red or Black)

### Characteristics

✅ Height remains approximately balanced

✅ Efficient insertion and deletion

✅ Search complexity remains O(log n)

---

## B-Tree

A self-balancing multi-way search tree.

### Characteristics

✅ Multiple keys per node

✅ Efficient disk access

✅ Used in databases and file systems

### Applications

- Databases
- Storage Engines
- Indexing Systems

---

## B+ Tree

An extension of B-Tree.

### Characteristics

✅ Data stored only at leaf nodes

✅ Internal nodes store indexing keys

✅ Leaf nodes are linked together

### Advantages

- Efficient range queries
- Sequential access support
- Commonly used in database indexing

---

# Quick Comparison

| Type | Key Property |
|--------|-------------|
| Full Binary Tree | Each node has 0 or 2 children |
| Degenerate Tree | Every node has only one child |
| Skewed Tree | Nodes only on one side |
| Complete Binary Tree | Last level filled from left to right |
| Perfect Binary Tree | All levels completely filled |
| Balanced Binary Tree | Height remains O(log n) |
| BST | Left < Root < Right |
| AVL Tree | Height-balanced BST |
| Red-Black Tree | Color-balanced BST |
| B-Tree | Multi-way balanced search tree |
| B+ Tree | Data stored in leaf nodes |

---

# Key Takeaways

- Full, Complete, Perfect, and Balanced trees are structural classifications.
- BST, AVL, and Red-Black Trees are search-oriented trees.
- B-Tree and B+ Tree are widely used in databases and file systems.
- Maintaining balance is essential for keeping search, insertion, and deletion operations efficient.
