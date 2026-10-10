# Delete a Node in a Binary Search Tree (BST)

## Problem Statement

Given the root of a Binary Search Tree (BST) and a key, delete the node with the given key while maintaining the BST property.

A BST follows:

```text
Left Subtree < Root < Right Subtree
```

After deletion, the resulting tree must still be a valid BST.

---

## Example

### Input BST

```text
        50
       /  \
     30    70
    / \   / \
   20 40 60 80
```

Delete:

```text
70
```

### Output

```text
        50
       /  \
     30    80
    / \   /
   20 40 60
```

---

# Cases in BST Deletion

Deletion can occur in three different scenarios.

---

## Case 1: Node with No Children (Leaf Node)

### Before

```text
      50
     /
    30
```

Delete:

```text
30
```

### After

```text
50
```

Simply remove the node.

---

## Case 2: Node with One Child

### Before

```text
      50
     /
    30
   /
  20
```

Delete:

```text
30
```

### After

```text
      50
     /
    20
```

Replace the node with its child.

---

## Case 3: Node with Two Children

### Before

```text
        50
       /  \
     30    70
          /  \
         60   80
```

Delete:

```text
70
```

### Approach

Replace the node with:

- Inorder Successor (smallest node in right subtree)

OR

- Inorder Predecessor (largest node in left subtree)

Most implementations use the inorder successor.

---

### Inorder Successor

```text
Successor of 70 = 80
```

Replace:

```text
70 → 80
```

Delete the duplicate `80`.

### After

```text
        50
       /  \
     30    80
          /
         60
```

---

# Intuition

While searching for the node:

- Move left if key is smaller.
- Move right if key is larger.
- Once found:
  - No child → return null.
  - One child → return the existing child.
  - Two children → replace with successor and delete successor.

---

# Helper Function (Find Minimum)

The inorder successor is the minimum node in the right subtree.

```java
private int findMin(TreeNode root) {

    while (root.left != null)
        root = root.left;

    return root.val;
}
```

---

# Recursive Solution

## Java Code

```java
class Solution {

    public TreeNode deleteNode(TreeNode root, int key) {

        if (root == null)
            return null;

        if (key < root.val) {

            root.left = deleteNode(root.left, key);

        } else if (key > root.val) {

            root.right = deleteNode(root.right, key);

        } else {

            // Case 1 & 2

            if (root.left == null)
                return root.right;

            if (root.right == null)
                return root.left;

            // Case 3

            int successor = findMin(root.right);

            root.val = successor;

            root.right = deleteNode(root.right, successor);
        }

        return root;
    }

    private int findMin(TreeNode root) {

        while (root.left != null)
            root = root.left;

        return root.val;
    }
}
```

---

# Dry Run

## BST

```text
        50
       /  \
     30    70
    / \   / \
   20 40 60 80
```

Delete:

```text
70
```

---

### Step 1

Search for `70`.

```text
70 > 50

Move Right
```

---

### Step 2

Found node `70`.

```text
70 has two children
```

Find successor:

```text
Minimum in right subtree = 80
```

---

### Step 3

Replace:

```text
70 → 80
```

Tree becomes:

```text
        50
       /  \
     30    80
          /  \
         60   80
```

---

### Step 4

Delete duplicate `80`.

Final BST:

```text
        50
       /  \
     30    80
          /
         60
```

---

# Optimized Interview Solution

This is the approach commonly used in Striver's BST series.

### Java Code

```java
class Solution {

    public TreeNode deleteNode(TreeNode root, int key) {

        if (root == null)
            return null;

        if (root.val == key)
            return helper(root);

        TreeNode curr = root;

        while (curr != null) {

            if (key < curr.val) {

                if (curr.left != null && curr.left.val == key) {
                    curr.left = helper(curr.left);
                    break;
                }

                curr = curr.left;

            } else {

                if (curr.right != null && curr.right.val == key) {
                    curr.right = helper(curr.right);
                    break;
                }

                curr = curr.right;
            }
        }

        return root;
    }

    private TreeNode helper(TreeNode root) {

        if (root.left == null)
            return root.right;

        if (root.right == null)
            return root.left;

        TreeNode rightChild = root.right;
        TreeNode lastRight = findLastRight(root.left);

        lastRight.right = rightChild;

        return root.left;
    }

    private TreeNode findLastRight(TreeNode root) {

        while (root.right != null)
            root = root.right;

        return root;
    }
}
```

---

# Complexity Analysis

| Operation | Time Complexity | Space Complexity |
|------------|----------------|------------------|
| Delete Node | O(h) | O(h) Recursive |
| Delete Node | O(h) | O(1) Iterative |

where:

```text
h = height of BST
```

---

## Balanced BST

```text
h = log n
```

```text
Deletion = O(log n)
```

---

## Skewed BST

```text
h = n
```

```text
Deletion = O(n)
```

---

# Visualization

### Delete 70

```text
Before

        50
       /  \
     30    70
          /  \
         60   80
```

Successor:

```text
80
```

Replace:

```text
70 → 80
```

Final Tree:

```text
        50
       /  \
     30    80
          /
         60
```

---

# Why Use the Inorder Successor?

The inorder successor is:

```text
Smallest element in the right subtree
```

Therefore:

```text
All Left Values < Successor < All Right Values
```

The BST property remains valid after replacement.

---

# Key Takeaways

- BST deletion has **3 cases**:
  - Leaf Node
  - One Child
  - Two Children
- For two children, replace with:
  - Inorder Successor (most common)
  - Inorder Predecessor
- Finding the successor requires moving to the:
  ```text
  Leftmost node of the right subtree
  ```
- Time Complexity:
  - Balanced BST → **O(log n)**
  - Skewed BST → **O(n)**
- BST deletion is one of the most important BST interview problems and is frequently asked in FAANG interviews.
