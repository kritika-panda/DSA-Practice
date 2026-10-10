# Binary Search Tree (BST) Iterator

## Problem Statement

Design an iterator class over a **Binary Search Tree (BST)** that represents an in-order traversal of the tree. 

The iterator must implement the following operations:
* `BSTIterator(TreeNode root)` Initializes an object of the iterator class. The `root` of the BST is given as part of the constructor.
* `boolean hasNext()` Returns `true` if there are still numbers left in the traversal to the right of the pointer, otherwise returns `false`.
* `int next()` Moves the pointer to the right, then returns the number at the pointer.

### Design Constraint

* `next()` and `hasNext()` must run in **O(1) average time** complexity.
* The structure must use **O(H) memory** space, where `H` is the height of the tree.

---

## Example

### BST

```text
           7
         /   \
        3     15
             /  \
            9    20
```

### Initial State

```text
BSTIterator bstIterator = new BSTIterator(root);
```

### Call Sequences

```text
bstIterator.next();    // Returns 3
bstIterator.next();    // Returns 7
bstIterator.hasNext(); // Returns true
bstIterator.next();    // Returns 9
bstIterator.hasNext(); // Returns true
bstIterator.next();    // Returns 15
bstIterator.hasNext(); // Returns true
bstIterator.next();    // Returns 20
bstIterator.hasNext(); // Returns false
```

---

# Key BST Property

```text
Left Subtree < Root < Right Subtree
```

An in-order traversal follows the path `Left -> Root -> Right` to produce a sorted list. To implement an efficient iterator with an O(H) space constraint, we cannot flatten the tree into an array ahead of time. Instead, we simulate the standard recursive tree call stack using a **custom stack structure**.

---

# Intuition

Instead of visiting all nodes at the start, we lazily populate our stack using a process called **controlled recursion**:

### Controlled Left Traversal
Starting from any given node, we push the node and all its sequential **left descendants** onto a stack until we hit a `null` child. 

This guarantees that the node at the top of the stack is always the smallest unvisited element in that subtree.

---

### Handling Next Elements
When `next()` is called:
1. The smallest element is sitting directly on top of the stack. Pop it.
2. If this popped node has a **right child**, it represents a new subtree whose elements are larger than the popped node but smaller than its ancestors. 
3. We run the controlled left traversal on that right child to push its elements onto the stack.

---

# Visualization

Using the example BST:

```text
           7
         /   \
        3     15
             /  \
            9    20
```

### 1. Initialization
Push 7, then push its left child 3. Stack bottom to top:

```text
Stack: [7, 3]
```

### 2. First `next()` Call
Pop `3`. It has no right child. Stack becomes:

```text
Stack: [7]
Return: 3
```

### 3. Second `next()` Call
Pop `7`. It has a right child (`15`). 
Run left traversal on `15`: push 15, then push its left child 9. Stack becomes:

```text
Stack: [15, 9]
Return: 7
```

### 4. Third `next()` Call
Pop `9`. It has no right child. Stack becomes:

```text
Stack: [15]
Return: 9
```

---

# Java Implementation

```java
import java.util.Stack;

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

class BSTIterator {
    // Custom stack to store the path to the current smallest node
    private Stack<TreeNode> stack;

    public BSTIterator(TreeNode root) {
        this.stack = new Stack<>();
        // Partially push nodes to establish O(H) memory footprint
        pushAllLeft(root);
    }
    
    /** @return the next smallest number */
    public int next() {
        // The top node contains the current smallest value
        TreeNode node = stack.pop();
        
        // If the node has a right child, process its left descendants
        if (node.right != null) {
            pushAllLeft(node.right);
        }
        
        return node.val;
    }
    
    /** @return whether we have a next smallest number */
    public boolean hasNext() {
        return !stack.isEmpty();
    }

    // Helper method to push a node and all of its left-side descendants
    private void pushAllLeft(TreeNode node) {
        while (node != null) {
            stack.push(node);
            node = node.left;
        }
    }
}
```

### Complexity Analysis

* **Time Complexity:** 
  * `hasNext()`: O(1) time as it checks if the stack container is empty.
  * `next()`: **O(1) amortized / average** time. Although a single call can loop to push multiple left children, every node in the tree is pushed onto the stack exactly once and popped exactly once over the total iterator lifecycle (2N operations total across N calls).
* **Space Complexity:** O(H) where H is the height of the BST. The stack keeps track of the current path down to a leaf node, which scales directly with tree depth rather than node count.
