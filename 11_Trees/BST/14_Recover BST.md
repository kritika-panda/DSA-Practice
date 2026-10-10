# Recover Binary Search Tree (Correct BST with 2 Nodes Swapped)

## Problem Statement

You are given the root of a Binary Search Tree (BST) where exactly **two nodes** were swapped by mistake. Recover the tree **without changing its structure** (i.e., only swap the values of the two nodes).

---

## Example

### BST with Swapped Nodes

```text
           3
         /   \
        1     4
             /
            2
```

### Input

```text
root = [3, 1, 4, null, null, 2]
```

### Output

The values 2 and 3 are swapped back to correct the BST structure:

```text
           2
         /   \
        1     4
             /
            3
```

Because:

```text
An in-order traversal of the broken tree gives: 1, 3, 2, 4
The sequence is not sorted because 3 and 2 are out of place.
Swapping 3 and 2 yields: 1, 2, 3, 4 (Correct sorted order).
```

---

# Key BST Property

```text
Left Subtree < Root < Right Subtree
```

An in-order traversal of a valid BST must always produce a **strictly increasing sorted sequence**. When exactly two nodes are swapped, it creates either **one or two violations** (where a value is larger than the next value) in this sorted sequence.

---

# Intuition

We can track the traversal sequence dynamically by maintaining a pointer to the `prev` (previously visited) node. At any point, if `prev.val >= current.val`, we have found a violation.

### Case 1: Swapped nodes are not adjacent
If the swapped nodes are separated by other elements (e.g., in sequence `1, [3], 2, [4]` swapped to `1, [4], 2, [3]`), it causes **two violations**:
* **First violation:** `4 > 2`. The incorrect large element is the `prev` node (`4`).
* **Second violation:** `2 > 3`. The incorrect small element is the `current` node (`3`).

---

### Case 2: Swapped nodes are adjacent
If the swapped nodes are right next to each other (e.g., in sequence `1, [2], [3], 4` swapped to `1, [3], [2], 4`), it causes only **one violation**:
* **Only violation:** `3 > 2`. The first node is `prev` (`3`) and the second node is `current` (`2`).

---

# Visualization

Tracking violations on traversal sequence: `1, 3, 2, 4`

```text
1. Visit 1: prev = null -> prev = 1
2. Visit 3: 1 < 3 (Valid) -> prev = 3
3. Visit 2: 3 > 2 (Violation!)
   - This is the FIRST violation.
   - Mark 'first' = prev (3)
   - Mark 'middle' = current (2)
   - prev = 2
4. Visit 4: 2 < 4 (Valid) -> prev = 4

End of Traversal:
- 'second' is null (only one violation occurred).
- Swap the values of 'first' (3) and 'middle' (2).
```

---

# Recursive Solution

## Algorithm

1. Initialize three node pointers: `first = null`, `second = null`, and `prev = null`.
2. Perform a standard in-order traversal (`Left -> Root -> Right`).
3. Inside the root processing step:
   - If `prev` is not null and `prev.val >= root.val`:
     - If `first` is null, this is the first violation. Set `first = prev` and `second = root`.
     - If `first` is already set, this is the second violation. Update `second = root`.
   - Update `prev = root`.
4. After completing the traversal, swap the values of the nodes pointed to by `first` and `second`.

---

## Java Implementation

```java
// Definition for a binary tree node.
class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    
    TreeNode() {}
    
    TreeNode(int val) { 
        this.val = val; 
    }
}

class Solution {
    private TreeNode first = null;
    private TreeNode second = null;
    private TreeNode prev = null;

    public void recoverTree(TreeNode root) {
        // Traverse the tree in-order to locate the two swapped nodes
        inorder(root);

        // Swap the values of the two misplaced nodes
        if (first != null && second != null) {
            int temp = first.val;
            first.val = second.val;
            second.val = temp;
        }
    }

    private void inorder(TreeNode root) {
        if (root == null) {
            return;
        }

        // Traverse left subtree
        inorder(root.left);

        // Process current node
        if (prev != null && prev.val >= root.val) {
            // If this is the first violation, mark the two nodes
            if (first == null) {
                first = prev;
                second = root;
            } else {
                // If a second violation is found, update the second node
                second = root;
            }
        }
        
        // Track the current node as previous for the next iteration
        prev = root;

        // Traverse right subtree
        inorder(root.right);
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N) where N is the total number of nodes in the tree, as we visit every node exactly once during the in-order traversal sequence.
* **Space Complexity:** O(H) where H is the height of the BST, representing the internal recursion stack footprint. This takes O(log N) for a balanced tree and O(N) in the worst-case skewed scenario.
