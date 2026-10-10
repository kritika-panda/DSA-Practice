# Binary Tree using Array Representation

## Introduction

A Binary Tree can be represented using:

1. Linked List Representation (Nodes with left and right pointers)
2. Array Representation

Array representation is commonly used for:

- Complete Binary Trees
- Heaps
- Segment Trees

because child-parent relationships can be computed directly using indices.

---

# Why Array Representation?

Consider the following binary tree:

```text
            10
          /    \
         20     30
        / \    / \
       40 50  60 70
```

Instead of storing pointers, we can store the nodes in an array.

```text
Index : 0  1  2  3  4  5  6

Value : 10 20 30 40 50 60 70
```

Array:

```java
int[] tree = {10, 20, 30, 40, 50, 60, 70};
```

---

# Parent-Child Relationships

For a node at index `i`:

### Left Child

```text
2 * i + 1
```

### Right Child

```text
2 * i + 2
```

### Parent

```text
(i - 1) / 2
```

---

# Example

## Array

```text
Index : 0  1  2  3  4  5  6

Value : 10 20 30 40 50 60 70
```

---

## Tree Representation

```text
                10 (0)
              /        \
         20 (1)       30 (2)
         /   \         /   \
     40(3) 50(4) 60(5) 70(6)
```

---

# Formula Summary

| Relationship | Formula |
|-------------|---------|
| Left Child | `2*i + 1` |
| Right Child | `2*i + 2` |
| Parent | `(i - 1) / 2` |

---

# Example Calculation

### Node = 20

```text
Index = 1
```

#### Left Child

```text
2 × 1 + 1 = 3
```

```text
tree[3] = 40
```

#### Right Child

```text
2 × 1 + 2 = 4
```

```text
tree[4] = 50
```

#### Parent

```text
(1 - 1) / 2 = 0
```

```text
tree[0] = 10
```

---

# Java Implementation

```java
public class BinaryTreeArray {

    public static void main(String[] args) {

        int[] tree = {10, 20, 30, 40, 50, 60, 70};

        int index = 1;

        System.out.println("Current Node: " + tree[index]);

        System.out.println(
                "Left Child: " +
                tree[2 * index + 1]);

        System.out.println(
                "Right Child: " +
                tree[2 * index + 2]);

        System.out.println(
                "Parent: " +
                tree[(index - 1) / 2]);
    }
}
```

### Output

```text
Current Node: 20
Left Child: 40
Right Child: 50
Parent: 10
```

---

# Array Representation with Missing Nodes

Consider:

```text
        1
       /
      2
     /
    4
```

Array Representation:

```text
Index : 0  1  2  3

Value : 1  2  N  4
```

Here:

```text
N = null
```

Java:

```java
Integer[] tree = {1, 2, null, 4};
```

---

# Traversal using Array

## Preorder Traversal

```text
Root → Left → Right
```

### Java

```java
public static void preorder(int[] tree, int index) {

    if (index >= tree.length) {
        return;
    }

    System.out.print(tree[index] + " ");

    preorder(tree, 2 * index + 1);

    preorder(tree, 2 * index + 2);
}
```

---

## Inorder Traversal

```text
Left → Root → Right
```

### Java

```java
public static void inorder(int[] tree, int index) {

    if (index >= tree.length) {
        return;
    }

    inorder(tree, 2 * index + 1);

    System.out.print(tree[index] + " ");

    inorder(tree, 2 * index + 2);
}
```

---

## Postorder Traversal

```text
Left → Right → Root
```

### Java

```java
public static void postorder(int[] tree, int index) {

    if (index >= tree.length) {
        return;
    }

    postorder(tree, 2 * index + 1);

    postorder(tree, 2 * index + 2);

    System.out.print(tree[index] + " ");
}
```

---

# Level Order Traversal

Array already stores nodes level-wise.

```java
for (int node : tree) {
    System.out.print(node + " ");
}
```

Output:

```text
10 20 30 40 50 60 70
```

---

# Insertion in Array Representation

Insert at the next available position.

Example:

```text
Current Array

[10, 20, 30, 40, 50]
```

Insert:

```text
60
```

Result:

```text
[10, 20, 30, 40, 50, 60]
```

Tree:

```text
            10
          /    \
         20     30
        / \    /
       40 50  60
```

---

# Advantages

✅ Simple implementation

✅ No extra memory for pointers

✅ Fast parent-child lookup using formula

✅ Ideal for Complete Binary Trees

✅ Used in Heaps and Segment Trees

---

# Disadvantages

❌ Wastes space for sparse trees

Example:

```text
        1
       /
      2
     /
    3
```

Array:

```text
[1, 2, null, 3]
```

Many positions remain unused.

---

❌ Not suitable for highly skewed trees

```text
1
 \
  2
   \
    3
     \
      4
```

Large array required.

---

# Array vs Linked List Representation

| Feature | Array | Linked List |
|----------|--------|------------|
| Memory for Pointers | No | Yes |
| Parent Access | O(1) | O(1) (if stored) |
| Child Access | O(1) | O(1) |
| Sparse Trees | Poor | Good |
| Complete Trees | Excellent | Good |
| Heaps | Preferred | Rarely Used |

---

# Real-World Applications

## Binary Heap

```text
Priority Queue
Heap Sort
```

Uses array representation.

---

## Segment Tree

Used in:

```text
Range Sum Queries
Range Minimum Queries
```

---

## Complete Binary Trees

Array representation works efficiently when levels are mostly filled.

---

# Visualization

```text
Array

Index : 0  1  2  3  4  5  6
Value : 10 20 30 40 50 60 70
```

```text
                10
              /    \
            20      30
           / \     / \
         40  50  60  70
```

---

# Complexity Analysis

## Access Parent

```text
O(1)
```

---

## Access Left Child

```text
O(1)
```

---

## Access Right Child

```text
O(1)
```

---

## Insert at End

```text
O(1)
```

---

## Tree Traversal

```text
O(n)
```

---

# Key Takeaways

- Array representation stores tree nodes level by level.
- For a node at index `i`:
  - Left Child = `2*i + 1`
  - Right Child = `2*i + 2`
  - Parent = `(i - 1)/2`
- Best suited for **Complete Binary Trees**.
- Widely used in:
  - Binary Heaps
  - Priority Queues
  - Segment Trees
- Parent and child access operations are **O(1)**.
- Sparse and skewed trees are better represented using linked nodes.
