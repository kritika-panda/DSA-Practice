# B+ Tree

## Concept Overview

A **B+ Tree** is an advanced evolution of the self-balancing **B-Tree**. While it retains a similar multi-way search structure, it introduces a strict separation between data and indexing. 

In a B+ Tree, **internal nodes only store routing keys and child pointers**, while **all actual records/data values are stored exclusively in the leaf nodes**. Furthermore, all leaf nodes are linked together sequentially using a **singly or doubly linked list**, making range queries and sequential scans highly efficient.

### B+ Tree vs. B-Tree: Key Differences

| Feature | B-Tree | B+ Tree |
| :--- | :--- | :--- |
| **Data Storage** | Keys and actual data records can be stored in any node (internal or leaf). | Internal nodes only store routing keys. **All data resides in the leaves.** |
| **Leaf Connectivity** | Leaf nodes are separate and do not point to each other. | All leaf nodes are connected via a **linked list** for fast sequential access. |
| **Key Duplication** | Keys appear exactly once somewhere in the tree. | Keys can be duplicated; a key in an internal node serves as a placeholder and also appears in the leaf. |
| **Search Performance** | Can be faster if data is found near the root, but highly variable. | Constant-time depth search. Every query must traverse all the way down to a leaf. |

---

## Example

### A Valid B+ Tree of Order 3

Internal nodes act as a map index, while leaf nodes carry the values alongside sequential sibling links (`-->`).

```text
                     [  20  ]
                    /        
            [  10  ]          [  30  ]
           /                /        
      [5 | 10] --> [15] --> [20 | 25] --> [30 | 35]
```

* **Properties Check:** The key `20` exists as an index boundary at the root and reappears in the leaf data block. Every leaf node has a horizontal pointer pointing directly to its next neighbor.

---

# Restoring Balance After Insertion

Like a B-Tree, insertions always target a **leaf node**. When a leaf overflows beyond its maximum allowed capacity (M - 1 keys), it splits into two distinct nodes.

### The B+ Tree Split Rules

1. **Leaf Node Split:** 
   * Split the sorted keys into two equal parts.
   * **Copy** the lowest key of the right split up into the parent node.
   * Chain the new right-hand leaf node into the leaf linked list layer.

2. **Internal Node Split:** 
   * If an internal indexing node overflows, split its keys around the median.
   * **Move** (do not copy) the median key up into the parent node.

```text
Inserting key 15 into a full Leaf Node [5 | 10] of Order 3:

1. Temporary Overflow Array: [5 | 10 | 15]
2. Split Leaf: [5] and [10 | 15]
3. COPY the right side's lowest element (10) up to the parent:

          [ 10 ]
         /      
      [ 5 ] ---> [ 10 | 15 ]
```

---

# Java Implementation (Structural Model)

```java
import java.util.ArrayList;
import java.util.Collections;

class BPlusNode {
    boolean isLeaf;
    ArrayList<Integer> keys;
    
    // Internal node pointers
    ArrayList<BPlusNode> children;
    
    // Leaf node linked list pointer
    BPlusNode next;

    BPlusNode(boolean isLeaf) {
        this.isLeaf = isLeaf;
        this.keys = new ArrayList<>();
        if (!isLeaf) {
            this.children = new ArrayList<>();
        }
        this.next = null;
    }
}

class BPlusTree {
    private BPlusNode root;
    private final int M; // Order of the tree

    public BPlusTree(int order) {
        this.M = order;
        this.root = new BPlusNode(true);
    }

    // Public search operation
    public boolean search(int key) {
        BPlusNode current = root;
        // Drill down exclusively to leaf layer
        while (!current.isLeaf) {
            int i = 0;
            while (i < current.keys.size() && key >= current.keys.get(i)) {
                i++;
            }
            current = current.children.get(i);
        }
        
        // Search inside the targeted leaf node block
        return Collections.binarySearch(current.keys, key) >= 0;
    }

    // Public insertion entry point
    public void insert(int key) {
        BPlusNode r = root;
        if (r.keys.size() == M - 1) {
            BPlusNode newRoot = new BPlusNode(false);
            root = newRoot;
            newRoot.children.add(r);
            splitChild(newRoot, 0, r);
            insertNonFull(newRoot, key);
        } else {
            insertNonFull(r, key);
        }
    }

    private void insertNonFull(BPlusNode node, int key) {
        if (node.isLeaf) {
            node.keys.add(key);
            Collections.sort(node.keys);
        } else {
            int i = 0;
            while (i < node.keys.size() && key >= node.keys.get(i)) {
                i++;
            }
            BPlusNode child = node.children.get(i);
            if (child.keys.size() == M - 1) {
                splitChild(node, i, child);
                if (key >= node.keys.get(i)) {
                    i++;
                }
            }
            insertNonFull(node.children.get(i), key);
        }
    }

    private void splitChild(BPlusNode parent, int index, BPlusNode fullChild) {
        int mid = (M - 1) / 2;
        BPlusNode newNode = new BPlusNode(fullChild.isLeaf);

        if (fullChild.isLeaf) {
            // Leaf Split Strategy: COPY upward
            for (int i = mid; i < fullChild.keys.size(); i++) {
                newNode.keys.add(fullChild.keys.get(i));
            }
            fullChild.keys.subList(mid, fullChild.keys.size()).clear();

            // Link leaves together sequentially
            newNode.next = fullChild.next;
            fullChild.next = newNode;

            // Copy up the first key of the new leaf node
            parent.keys.add(index, newNode.keys.get(0));
        } else {
            // Internal Node Split Strategy: MOVE upward
            int upKey = fullChild.keys.get(mid);
            
            for (int i = mid + 1; i < fullChild.keys.size(); i++) {
                newNode.keys.add(fullChild.keys.get(i));
            }
            for (int i = mid + 1; i < fullChild.children.size(); i++) {
                newNode.children.add(fullChild.children.get(i));
            }

            fullChild.keys.subList(mid, fullChild.keys.size()).clear();
            fullChild.children.subList(mid + 1, fullChild.children.size()).clear();

            parent.keys.add(index, upKey);
        }
        parent.children.add(index + 1, newNode);
    }
}
```

### Complexity Analysis

* **Time Complexity:** 
  * **Search / Insertion / Deletion:** O(log_M N). Because all data is aligned on a shallow bottom level, lookups avoid structural variation and take exactly the same number of steps.
  * **Range Queries:** O(log_M N + K), where K is the number of elements in the range. Locating the starting element takes logarithmic time, and retrieving subsequent elements is O(1) per item via the linked leaf list.
* **Space Complexity:** O(N) for allocating system memory to house the index nodes and sequential data leaf arrays.
