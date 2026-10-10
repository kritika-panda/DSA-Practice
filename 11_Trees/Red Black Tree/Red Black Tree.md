# Red-Black Tree (Self-Balancing Binary Search Tree)

## Concept Overview

A **Red-Black Tree** is a self-balancing **Binary Search Tree (BST)** where each node contains an extra attribute tracking its color: either **Red** or **Black**. These color properties enforce structural constraints ensuring the tree remains balanced during insertions and deletions, preventing the tree from degenerating into a skewed chain.

### Red-Black Tree Properties

To be a valid Red-Black Tree, the structure must strictly adhere to the following **five rules**:

1. **Node Color:** Every node is either Red or Black.
2. **Root Property:** The root of the tree is always Black.
3. **Leaf Property:** Every leaf node (represented by `null` / `NIL` pointers) is Black.
4. **Red Property:** If a node is Red, both of its children must be Black. (No two adjacent Red nodes are allowed on any path).
5. **Black Height Property:** For each node, all simple paths from that node to descendant leaves contain the exact same number of Black nodes.

---

## Example

### A Valid Red-Black Tree

```text
               15 (Black)
             /    \
       6 (Red)     20 (Black)
       /    \         /    \
  3 (Black) 9 (Black) NIL  NIL
```

* **Properties Check:** The root (`15`) is Black. No Red node has a Red child. The number of Black nodes on any path from root to `NIL` is exactly 2.

---

# Restoring Balance After Insertion

When a new node is inserted into a Red-Black Tree, it is always colored **Red** initially to avoid violating the *Black Height Property (Rule 5)*. However, this may cause a violation of the *Red Property (Rule 4)* if its parent is also Red.

To fix double-red violations, we look up at the parent's sibling node (the **Uncle**):

### Case 1: The Uncle is RED
If the uncle node is Red, we can fix the violation by performing a **recolor**:
1. Change the **Parent** and **Uncle** colors to Black.
2. Change the **Grandparent** color to Red.
3. Repeat the structural check at the Grandparent node to resolve secondary conflicts.

```text
       G (Black)                    G (Red)  <-- Check again
      /         \                  /       \
  P (Red)     U (Red)   --->   P (Black)  U (Black)
    /                            /
X (Red)                      X (Red)
```

### Case 2: The Uncle is BLACK (or NIL)
If the uncle node is Black, recoloring alone cannot resolve the structural conflict. We must perform **Tree Rotations** paired with recoloring based on the alignment of the new node `X`:

* **Left-Left (LL) Shape:** Right-rotate around the Grandparent `G`. Swap the colors of the Parent `P` and Grandparent `G`.
* **Left-Right (LR) Shape:** Left-rotate around the Parent `P` to turn it into an LL shape, then fix it like an LL case.
* **Right-Right (RR) Shape:** Left-rotate around the Grandparent `G`. Swap the colors of the Parent `P` and Grandparent `G`.
* **Right-Left (RL) Shape:** Right-rotate around the Parent `P` to turn it into an RR shape, then fix it like an RR case.

---

# Java Implementation (Insertion)

```java
class Node {
    int data;
    Node left, right, parent;
    boolean isRed; // true for RED, false for BLACK

    Node(int data) {
        this.data = data;
        this.isRed = true; // New nodes are always inserted as RED
    }
}

class RedBlackTree {
    private Node root;
    private final Node NIL;

    public RedBlackTree() {
        NIL = new Node(-1);
        NIL.isRed = false; // NIL leaves are always BLACK
        root = NIL;
    }

    // Left Rotate utility
    private void leftRotate(Node x) {
        Node y = x.right;
        x.right = y.left;
        
        if (y.left != NIL) {
            y.left.parent = x;
        }
        
        y.parent = x.parent;
        
        if (x.parent == null) {
            root = y;
        } else if (x == x.parent.left) {
            x.parent.left = y;
        } else {
            x.parent.right = y;
        }
        
        y.left = x;
        x.parent = y;
    }

    // Right Rotate utility
    private void rightRotate(Node y) {
        Node x = y.left;
        y.left = x.right;
        
        if (x.right != NIL) {
            x.right.parent = y;
        }
        
        x.parent = y.parent;
        
        if (y.parent == null) {
            root = x;
        } else if (y == y.parent.left) {
            y.parent.left = x;
        } else {
            y.parent.right = y;
        }
        
        x.right = y;
        y.parent = x;
    }

    // Fix up structural violations after standard BST insertion
    private void fixInsert(Node x) {
        while (x.parent != null && x.parent.isRed) {
            // Parent is a left child of Grandparent
            if (x.parent == x.parent.parent.left) {
                Node uncle = x.parent.parent.right;
                
                // Case 1: Uncle is RED -> Recolor
                if (uncle.isRed) {
                    x.parent.isRed = false;
                    uncle.isRed = false;
                    x.parent.parent.isRed = true;
                    x = x.parent.parent;
                } else {
                    // Case 2: Uncle is BLACK
                    // Sub-case: LR shape -> convert to LL shape
                    if (x == x.parent.right) {
                        x = x.parent;
                        leftRotate(x);
                    }
                    // Sub-case: LL shape -> rotate and swap colors
                    x.parent.isRed = false;
                    x.parent.parent.isRed = true;
                    rightRotate(x.parent.parent);
                }
            } else { // Parent is a right child of Grandparent
                Node uncle = x.parent.parent.left;
                
                // Case 1: Uncle is RED -> Recolor
                if (uncle.isRed) {
                    x.parent.isRed = false;
                    uncle.isRed = false;
                    x.parent.parent.isRed = true;
                    x = x.parent.parent;
                } else {
                    // Case 2: Uncle is BLACK
                    // Sub-case: RL shape -> convert to RR shape
                    if (x == x.parent.left) {
                        x = x.parent;
                        rightRotate(x);
                    }
                    // Sub-case: RR shape -> rotate and swap colors
                    x.parent.isRed = false;
                    x.parent.parent.isRed = true;
                    leftRotate(x.parent.parent);
                }
            }
        }
        // Force Root to stay BLACK (Rule 2)
        root.isRed = false;
    }

    public void insert(int data) {
        Node node = new Node(data);
        node.left = NIL;
        node.right = NIL;

        Node y = null;
        Node x = this.root;

        // Standard BST Insertion
        while (x != NIL) {
            y = x;
            if (node.data < x.data) {
                x = x.left;
            } else {
                x = x.right;
            }
        }

        node.parent = y;
        if (y == null) {
            root = node;
        } else if (node.data < y.data) {
            y.left = node;
        } else {
            y.right = node;
        }

        // Fix potential violations
        if (node.parent == null) {
            node.isRed = false;
            return;
        }
        if (node.parent.parent == null) {
            return;
        }

        fixInsert(node);
    }
}
```

### Complexity Analysis

* **Time Complexity:** 
  * **Search, Insertion, Deletion:** O(log N). Because the tree rules tightly restrict height limits (H <= 2*log_2(N + 1)), operations preserve logarithmic search paths.
  * **Rotations:** O(1) amortized. While fixing an item path, we perform at most 2 structural rotations during an insertion cycle.
* **Space Complexity:** O(1) for iterative data insertions. The iterative repair path bypasses the function call stack allocations seen in standard recursive trees.
