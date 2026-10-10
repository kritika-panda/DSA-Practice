# B-Tree

## Concept Overview

A **B-Tree** is a self-balancing **M-way search tree** designed to work efficiently on secondary storage devices (like hard drives and SSDs). Unlike binary trees, a B-Tree node can contain **multiple keys** and **more than two child pointers**. This keeps the tree broad and short, minimizing expensive disk input/output (I/O) operations.

### Properties of a B-Tree of Order M

To maintain absolute balance, a B-Tree of order M strictly enforces these structural rules:

1. **Root Node:** The root has at least 1 key and at least 2 children (unless it is a leaf).
2. **Internal Nodes:** Every node (except the root and leaves) must have at least \(\lceil M/2 \rceil\) children.
3. **Key Boundaries:** An internal node with K children always contains exactly K - 1 sorted keys.
4. **Capacity Limit:** No node can hold more than M - 1 keys or point to more than M children.
5. **Perfect Leaf Balance:** All leaf nodes must reside at the **exact same depth/level**.

---

## Example

### A Valid B-Tree of Order 4

Each node can hold a maximum of 3 keys (M - 1) and point to a maximum of 4 children.

```text
                     [ 20 | 50 ]
                    /     |     \
         [ 10 | 15 ]  [ 30 ]     [ 60 | 70 | 80 ]
```

* **Properties Check:** Keys within every node are sorted. Children values match search boundaries (e.g., elements in the middle branch are strictly between 20 and 50). All leaf nodes rest perfectly on the bottom level.

---

# Restoring Balance After Insertion

When inserting a key into a B-Tree, it is **always added down into a leaf node**. If the target leaf is already full (contains M - 1 keys), adding an extra item violates the capacity limit. This triggers a **Node Split**:

### The Splitting Mechanism
1. Find the **median (middle) key** among the overflowed collection.
2. Push this median key up into the node's parent.
3. Split the remaining keys evenly into two separate sibling nodes (left and right).
4. If the parent node fills up and overflows from this addition, cascade the split operation upward toward the root. If the root splits, a new root is made, increasing the tree height by 1.

```text
Inserting key 25 into a full node of Order 4:

    [ 10 | 20 | 30 ]  +  (25)  --->  [ 10 | 20 | 25 | 30 ] (Overflow!)
                                              ^ (Median is 25)
                                              
                                         [ 25 ] (Pushed up)
                                        /      \
                                   [ 10 | 20 ]  [ 30 ]
```

---

# Java Implementation (Insertion)

```java
import java.util.ArrayList;
import java.util.Collections;

class BTreeNode {
    int M; // Order of the tree
    ArrayList<Integer> keys;
    ArrayList<BTreeNode> children;
    boolean isLeaf;

    BTreeNode(int order, boolean isLeaf) {
        this.M = order;
        this.isLeaf = isLeaf;
        this.keys = new ArrayList<>();
        this.children = new ArrayList<>();
    }
}

class BTree {
    private BTreeNode root;
    private final int M; // Order

    public BTree(int order) {
        this.M = order;
        this.root = new BTreeNode(M, true);
    }

    // Public method to insert a key
    public void insert(int key) {
        BTreeNode r = root;

        // If root is full, tree grows in height
        if (r.keys.size() == M - 1) {
            BTreeNode s = new BTreeNode(M, false);
            root = s;
            s.children.add(r);
            splitChild(s, 0, r);
            insertNonFull(s, key);
        } else {
            insertNonFull(r, key);
        }
    }

    // Helper to insert into a node that is guaranteed not to be full
    private void insertNonFull(BTreeNode node, int key) {
        int i = node.keys.size() - 1;

        if (node.isLeaf) {
            // Find insertion position in leaf
            node.keys.add(key);
            Collections.sort(node.keys);
        } else {
            // Find child index that should receive the key
            while (i >= 0 && key < node.keys.get(i)) {
                i--;
            }
            i++;

            BTreeNode child = node.children.get(i);
            if (child.keys.size() == M - 1) {
                // If child is full, split it first
                splitChild(node, i, child);
                if (key > node.keys.get(i)) {
                    i++;
                }
            }
            insertNonFull(node.children.get(i), key);
        }
    }

    // Utility to split a full child node
    private void splitChild(BTreeNode parent, int index, BTreeNode fullChild) {
        BTreeNode newNode = new BTreeNode(M, fullChild.isLeaf);
        int medianIndex = (M - 1) / 2;
        int medianKey = fullChild.keys.get(medianIndex);

        // Move the right side of fullChild keys into newNode
        for (int i = medianIndex + 1; i < fullChild.keys.size(); i++) {
            newNode.keys.add(fullChild.keys.get(i));
        }
        
        // Move corresponding children pointers if it is an internal node
        if (!fullChild.isLeaf) {
            for (int i = medianIndex + 1; i < fullChild.children.size(); i++) {
                newNode.children.add(fullChild.children.get(i));
            }
            // Trim trailing children from fullChild
            fullChild.children.subList(medianIndex + 1, fullChild.children.size()).clear();
        }

        // Trim keys from fullChild including the median key
        fullChild.keys.subList(medianIndex, fullChild.keys.size()).clear();

        // Put the median key and new child pointer into the parent
        parent.keys.add(index, medianKey);
        parent.children.add(index + 1, newNode);
    }
}
```

### Complexity Analysis

* **Time Complexity:** 
  * **Search, Insertion, Deletion:** O(logM N). Because the node branching factor matches $M$, the tree depth is kept extremely shallow compared to basic binary structures.
* **Space Complexity:** O(N) auxiliary space total across system resources to map out node allocations.
