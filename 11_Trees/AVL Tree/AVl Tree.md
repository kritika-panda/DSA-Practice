# AVL Tree (Self-Balancing Binary Search Tree)

## Problem Statement / Concept

An **AVL Tree** is a self-balancing **Binary Search Tree (BST)** where the difference between heights of left and right subtrees for any node cannot be more than 1. If at any time during insertion or deletion this balance condition is violated, tree rotations are performed to restore it.

### Definition

* **Balance Factor (BF):** The balance factor of a node is calculated as:
  ```text
  Balance Factor = Height(Left Subtree) - Height(Right Subtree)
  ```
* **Valid AVL Node:** A node is balanced if its Balance Factor belongs to the set `{-1, 0, 1}`. If `BF > 1` or `BF < -1`, the subtree rooted at this node is unbalanced.

---

## Example

### Balanced AVL Tree

```text
           30 (BF = 0)
          /  \
  (BF=0) 20  40 (BF = -1)
               \
                50 (BF = 0)
```

Every node has a balance factor of -1, 0, or 1.

### Unbalanced BST (Requires Rotation)

```text
             30 (BF = 2) -> Unbalanced
            /
    (BF=1) 20
          /
  (BF=0) 10
```

Node `30` has a Balance Factor of `2 - 0 = 2`. This requires a **Right Rotation** around 30 to restore balance.

---

# Rotations (Restoring Balance)

When an insertion or deletion causes a node's Balance Factor to become `2` or `-2`, four rotation types can fix the tree structure based on how the imbalance was created:

### 1. Left-Left (LL) Case -> Fix: Single Right Rotation
Imbalance caused by inserting into the left child of a left child.

```text
      T1 (BF=2)                 T2
     /                         /  \
    T2 (BF=1)   ------>       T3  T1
   /
  T3
```

### 2. Right-Right (RR) Case -> Fix: Single Left Rotation
Imbalance caused by inserting into the right child of a right child.

```text
  T1 (BF=-2)                    T2
    \                          /  \
     T2 (BF=-1)  ------>      T1  T3
       \
        T3
```

### 3. Left-Right (LR) Case -> Fix: Left Rotation, then Right Rotation
Imbalance caused by inserting into the right child of a left child.

```text
      T1 (BF=2)           T1 (BF=2)              T3
     /                   /                      /  \
    T2 (BF=-1)  ----->  T3 (BF=1)   ----->     T2  T1
      \                /
       T3             T2
```

### 4. Right-Left (RL) Case -> Fix: Right Rotation, then Left Rotation
Imbalance caused by inserting into the left child of a right child.

```text
  T1 (BF=-2)           T1 (BF=-2)                T3
    \                    \                      /  \
     T2 (BF=1)  ----->    T3 (BF=-1)  ----->   T1  T2
    /                       \
   T3                        T2
```

---

# Intuition

Since an AVL tree is a BST, insertion follows standard BST rules. However, as the recursion unwinds **bottom-up**, we recalculate the heights and balance factors of visited nodes.

At each node:
1. Update its height: `1 + max(Height(Left), Height(Right))`.
2. Compute its Balance Factor.
3. If `BF > 1` (Left Heavy):
   - If `BalanceFactor(Left Child) >= 0`, apply a **Right Rotation** (LL Case).
   - If `BalanceFactor(Left Child) < 0`, apply a **Left Rotation on Left Child**, then a **Right Rotation on Current** (LR Case).
4. If `BF < -1` (Right Heavy):
   - If `BalanceFactor(Right Child) <= 0`, apply a **Left Rotation** (RR Case).
   - If `BalanceFactor(Right Child) > 0`, apply a **Right Rotation on Right Child**, then a **Left Rotation on Current** (RL Case).

---

# Java Implementation (Insertion)

```java
class Node {
    int key, height;
    Node left, right;

    Node(int d) {
        key = d;
        height = 1;
    }
}

class AVLTree {

    // Helper to get the height of a node safely
    private int height(Node n) {
        return (n == null) ? 0 : n.height;
    }

    // Helper to get the balance factor of a node
    private int getBalance(Node n) {
        return (n == null) ? 0 : height(n.left) - height(n.right);
    }

    // Right Rotate utility
    private Node rightRotate(Node y) {
        Node x = y.left;
        Node T2 = x.right;

        // Perform rotation
        x.right = y;
        y.left = T2;

        // Update heights
        y.height = Math.max(height(y.left), height(y.right)) + 1;
        x.height = Math.max(height(x.left), height(x.right)) + 1;

        // Return new root
        return x;
    }

    // Left Rotate utility
    private Node leftRotate(Node x) {
        Node y = x.right;
        Node T2 = y.left;

        // Perform rotation
        y.left = x;
        x.right = T2;

        // Update heights
        x.height = Math.max(height(x.left), height(x.right)) + 1;
        y.height = Math.max(height(y.left), height(y.right)) + 1;

        // Return new root
        return y;
    }

    public Node insert(Node node, int key) {
        // 1. Perform standard BST insertion
        if (node == null) {
            return new Node(key);
        }

        if (key < node.key) {
            node.left = insert(node.left, key);
        } else if (key > node.key) {
            node.right = insert(node.right, key);
        } else {
            return node; // Duplicate keys are not allowed in this AVL setup
        }

        // 2. Update height of this ancestor node
        node.height = 1 + Math.max(height(node.left), height(node.right));

        // 3. Get the balance factor to check if it became unbalanced
        int balance = getBalance(node);

        // If unbalanced, 4 cases matching structural shift:

        // Left Left Case
        if (balance > 1 && key < node.left.key) {
            return rightRotate(node);
        }

        // Right Right Case
        if (balance < -1 && key > node.right.key) {
            return leftRotate(node);
        }

        // Left Right Case
        if (balance > 1 && key > node.left.key) {
            node.left = leftRotate(node.left);
            return rightRotate(node);
        }

        // Right Left Case
        if (balance < -1 && key < node.right.key) {
            node.right = rightRotate(node.right);
            return leftRotate(node);
        }

        return node;
    }
}
```

### Complexity Analysis

* **Time Complexity:** 
  * **Insertion / Deletion / Search:** O(log N). Because the tree height is strictly bound to logarithmic depth via balance updates, all key operations maintain an upper constraint of O(log N) operations.
* **Space Complexity:** O(H) = O(log N) for the recursion path tracking inside call stack configurations during basic structural updates.
