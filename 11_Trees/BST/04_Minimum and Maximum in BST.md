# Minimum and Maximum in a Binary Search Tree (BST)

## Problem Statement

Given the root of a Binary Search Tree (BST), find:

- **Minimum Value** in the BST
- **Maximum Value** in the BST

Because of BST properties, these operations can be performed efficiently without traversing the entire tree.

---

## BST Property

```text
Left Subtree < Root < Right Subtree
```

From this property:

- The **leftmost node** contains the minimum value.
- The **rightmost node** contains the maximum value.

---

## Example BST

```text
          50
         /  \
       30    70
      / \   / \
    20 40 60 80
```

### Minimum Value

Move continuously to the left:

```text
50 → 30 → 20
```

Answer:

```text
20
```

---

### Maximum Value

Move continuously to the right:

```text
50 → 70 → 80
```

Answer:

```text
80
```

---

# Finding Minimum in BST

## Intuition

The smallest value is always present in the leftmost node.

Keep moving left until no further left child exists.

---

## Algorithm

1. Start from the root.
2. Move to the left child repeatedly.
3. When left becomes null, current node contains the minimum value.
4. Return the value.

---

## Java Code

```java
public int findMin(TreeNode root) {

    if (root == null)
        throw new IllegalArgumentException("Tree is empty");

    while (root.left != null) {
        root = root.left;
    }

    return root.val;
}
```

---

## Dry Run

### BST

```text
          50
         /  \
       30    70
      / \   / \
    20 40 60 80
```

### Steps

```text
Current = 50
Move Left → 30

Current = 30
Move Left → 20

Current = 20
Left = null
```

Answer:

```text
Minimum = 20
```

---

# Finding Maximum in BST

## Intuition

The largest value is always present in the rightmost node.

Keep moving right until no further right child exists.

---

## Algorithm

1. Start from the root.
2. Move to the right child repeatedly.
3. When right becomes null, current node contains the maximum value.
4. Return the value.

---

## Java Code

```java
public int findMax(TreeNode root) {

    if (root == null)
        throw new IllegalArgumentException("Tree is empty");

    while (root.right != null) {
        root = root.right;
    }

    return root.val;
}
```

---

## Dry Run

### BST

```text
          50
         /  \
       30    70
      / \   / \
    20 40 60 80
```

### Steps

```text
Current = 50
Move Right → 70

Current = 70
Move Right → 80

Current = 80
Right = null
```

Answer:

```text
Maximum = 80
```

---

# Recursive Solution

## Minimum

```java
public int findMin(TreeNode root) {

    if (root.left == null)
        return root.val;

    return findMin(root.left);
}
```

---

## Maximum

```java
public int findMax(TreeNode root) {

    if (root.right == null)
        return root.val;

    return findMax(root.right);
}
```

---

# Combined Solution

## Java Code

```java
public int[] minMax(TreeNode root) {

    TreeNode minNode = root;
    TreeNode maxNode = root;

    while (minNode.left != null)
        minNode = minNode.left;

    while (maxNode.right != null)
        maxNode = maxNode.right;

    return new int[]{minNode.val, maxNode.val};
}
```

---

## Example

### BST

```text
          15
         /  \
        8   20
       / \    \
      5  12   25
```

Output:

```text
Minimum = 5
Maximum = 25
```

---

# Complexity Analysis

| Operation | Time Complexity | Space Complexity |
|------------|----------------|------------------|
| Find Minimum | O(h) | O(1) |
| Find Maximum | O(h) | O(1) |

where:

```text
h = height of BST
```

---

## Balanced BST

```text
Height = log n
```

Complexities:

```text
Minimum = O(log n)
Maximum = O(log n)
```

---

## Skewed BST

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
Minimum = O(1)
Maximum = O(n)
```

Similarly, for a left-skewed BST:

```text
40
/
30
/
20
/
10
```

```text
Minimum = O(n)
Maximum = O(1)
```

---

# Visualization

```text
          50
         /  \
       30    70
      / \   / \
    20 40 60 80

Minimum Path:
50 → 30 → 20

Maximum Path:
50 → 70 → 80
```

---

# Relation with Floor and Ceil

| Operation | Meaning |
|------------|----------|
| Minimum | Smallest value in BST |
| Maximum | Largest value in BST |
| Floor(x) | Greatest value ≤ x |
| Ceil(x) | Smallest value ≥ x |

Example:

```text
BST = [20, 30, 40, 50, 60, 70, 80]

Minimum = 20
Maximum = 80

Floor(55) = 50
Ceil(55)  = 60
```

---

# Key Takeaways

- The **minimum element** in a BST is the **leftmost node**.
- The **maximum element** in a BST is the **rightmost node**.
- No full traversal is required.
- Iterative solution is preferred due to **O(1)** space usage.
- These operations are frequently used in:
  - BST Deletion
  - Inorder Successor
  - Inorder Predecessor
  - Floor and Ceil Problems
  - Range Queries

### Quick Rule

```text
Minimum  → Keep Going Left
Maximum  → Keep Going Right
```
