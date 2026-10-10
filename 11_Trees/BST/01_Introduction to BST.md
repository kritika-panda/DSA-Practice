# Introduction to Binary Search Tree (BST)

## What is a Binary Search Tree?

A **Binary Search Tree (BST)** is a special type of **Binary Tree** where nodes are organized according to a specific ordering rule.

For every node in a BST:

- All values in the **left subtree** are smaller than the node's value.
- All values in the **right subtree** are greater than the node's value.
- Both left and right subtrees must also be Binary Search Trees.

### Example

```text
        50
       /  \
     30    70
    / \   / \
   20 40 60 80
```

For node `50`:

- Left subtree values: `20, 30, 40` (< 50)
- Right subtree values: `60, 70, 80` (> 50)

Therefore, the tree satisfies the BST property.

---

## Properties of a Binary Search Tree

1. Each node can have at most two children.
2. Left child value is smaller than the parent node.
3. Right child value is greater than the parent node.
4. Both left and right subtrees must also obey BST rules.
5. Inorder traversal of a BST always produces values in **sorted order**.

### Example

```text
        50
       /  \
     30    70
    / \   / \
   20 40 60 80
```

**Inorder Traversal:**

```text
20 → 30 → 40 → 50 → 60 → 70 → 80
```

Output:

```text
[20, 30, 40, 50, 60, 70, 80]
```

Notice that the elements appear in ascending order.

---

## Why Use a BST?

BSTs provide efficient operations for:

- Searching
- Insertion
- Deletion

The BST property allows us to eliminate half of the remaining tree at every step, similar to **Binary Search** on a sorted array.

### Search Example

Search for value `60`.

```text
        50
           \
            70
           /
         60
```

Steps:

1. Compare `60` with `50`.
2. Since `60 > 50`, move to the right subtree.
3. Compare `60` with `70`.
4. Since `60 < 70`, move to the left subtree.
5. Node found.

---

## Basic Operations in BST

### 1. Search

Find whether a key exists in the BST.

```java
public boolean search(TreeNode root, int key) {
    while (root != null) {
        if (root.val == key) return true;
        root = key < root.val ? root.left : root.right;
    }
    return false;
}
```

**Time Complexity:** `O(h)`  
**Space Complexity:** `O(1)`

where `h` is the height of the tree.

---

### 2. Insertion

Insert a new value while maintaining BST properties.

```java
public TreeNode insert(TreeNode root, int val) {
    if (root == null) return new TreeNode(val);

    if (val < root.val)
        root.left = insert(root.left, val);
    else
        root.right = insert(root.right, val);

    return root;
}
```

**Time Complexity:** `O(h)`  
**Space Complexity:** `O(h)` (recursive call stack)

---

### 3. Deletion

Deletion in a BST has three cases.

#### Case 1: Node with No Children (Leaf Node)

Before deletion:

```text
    50
   /
 30
```

Delete `30`.

After deletion:

```text
50
```

---

#### Case 2: Node with One Child

Before deletion:

```text
    50
   /
 30
 /
20
```

Delete `30`.

After deletion:

```text
    50
   /
 20
```

The child replaces the deleted node.

---

#### Case 3: Node with Two Children

Before deletion:

```text
        50
       /  \
     30    70
          /  \
         60   80
```

Delete `70`.

Steps:

1. Find the inorder successor (smallest node in the right subtree) or inorder predecessor (largest node in the left subtree).
2. Replace the node's value.
3. Delete the duplicate node.

Result:

```text
        50
       /  \
     30    80
          /
         60
```

---

## Time Complexity Analysis

| Operation | Average Case | Worst Case |
| ---------- | ------------ | ---------- |
| Search | O(log n) | O(n) |
| Insert | O(log n) | O(n) |
| Delete | O(log n) | O(n) |

---

## Why Does the Worst Case Become O(n)?

If elements are inserted in sorted order, the BST becomes skewed.

Example:

```text
10
  \
   20
     \
      30
        \
         40
           \
            50
```

This structure behaves like a linked list.

Searching for `50` requires visiting every node.

Therefore:

```text
Time Complexity = O(n)
```

---

## Balanced vs Skewed BST

### Balanced BST

```text
        40
       /  \
      20   60
     / \   / \
   10 30 50 70
```

Height:

```text
≈ log₂(n)
```

Complexities:

```text
Search  : O(log n)
Insert  : O(log n)
Delete  : O(log n)
```

---

### Skewed BST

```text
10
 \
 20
  \
   30
    \
     40
```

Height:

```text
n
```

Complexities:

```text
Search  : O(n)
Insert  : O(n)
Delete  : O(n)
```

---

## Applications of Binary Search Tree

- Database indexing
- Symbol tables
- Dictionaries and maps
- Maintaining sorted data
- Range queries
- Dynamic searching operations
- File system organization

---

## Advantages of BST

- Faster search compared to ordinary binary trees.
- Maintains data in sorted order.
- Efficient insertion and deletion.
- Supports range-based queries efficiently.

---

## Disadvantages of BST

- Can become skewed and lose efficiency.
- Performance depends on tree height.
- Balanced BSTs require additional logic for balancing.

---

## BST vs Binary Tree

| Feature | Binary Tree | Binary Search Tree |
|----------|------------|-------------------|
| Ordering Rule | No specific order | Left < Root < Right |
| Searching | O(n) | O(log n) average |
| Sorted Traversal | Not guaranteed | Inorder gives sorted order |
| Insertion Position | Any valid position | Must follow BST property |

---

## Key Takeaways

- A BST is a Binary Tree with the rule:

```text
Left Subtree < Root < Right Subtree
```

- Every subtree of a BST must also be a BST.
- Inorder traversal always produces sorted output.
- Search, Insert, and Delete operations take **O(log n)** on average.
- Worst-case complexity becomes **O(n)** when the tree becomes skewed.
- BST serves as the foundation for advanced trees such as:
  - AVL Tree
  - Red-Black Tree
  - Treap
  - Splay Tree
  - B-Tree

---

## Visualization

```text
              BST
               50
             /    \
           30      70
          /  \    /  \
        20   40 60   80

Inorder Traversal:
20 → 30 → 40 → 50 → 60 → 70 → 80
```

### BST Rule

```text
Left Subtree  <  Root  <  Right Subtree
```

### Remember

A **Binary Search Tree** combines the hierarchical structure of a Binary Tree with the efficient searching capability of Binary Search, making it one of the most important data structures in computer science.
