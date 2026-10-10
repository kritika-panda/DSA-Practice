# Two Sum IV - Input is a BST

## Problem Statement

Given the root of a Binary Search Tree (BST) and a target number `k`, return `true` if there exist two elements in the BST such that their sum is equal to the given target. Otherwise, return `false`.

---

## Example

### BST

```text
           5
         /   \
        3     6
       / \     \
      2   4     7
```

### Input

```text
k = 9
```

### Output

```text
true
```

Because:

```text
The tree contains the nodes 3 and 6.
3 + 6 = 9
```

---

## Another Example

### Input

```text
k = 28
```

### Output

```text
false
```

No two distinct nodes in the tree add up to 28.

---

# Key BST Property

```text
Left Subtree < Root < Right Subtree
```

An in-order traversal of a BST generates a sorted sequence. For a standard sorted array, the **Two-Pointer Approach** yields the most optimal space profile for finding a pair sum. By scaling our previous **BST Iterator** strategy, we can implement a dynamic two-pointer technique directly on the tree structure.

---

# Intuition

Instead of flattening the entire tree into an array, we initialize two separate custom iterators:
1. **Forward Iterator (`next`)**: Simulates the left pointer, yielding elements from smallest to largest.
2. **Reverse Iterator (`before`)**: Simulates the right pointer, yielding elements from largest to smallest.

Using these two dynamic pointers:
* If `left.val + right.val == k`, we found the target pair. Return `true`.
* If `left.val + right.val < k`, we need a larger value. Advance the forward iterator.
* If `left.val + right.val > k`, we need a smaller value. Move the reverse iterator backward.

---

# Visualization

Finding pair sum for:

```text
k = 9
```

```text
           5
         /   \
        3     6
       / \     \
      2   4     7
```

```text
1. Initialize Iterators:
   - Forward Stack tracks left-most path: [5, 3, 2] -> Left value = 2
   - Reverse Stack tracks right-most path: [5, 6, 7] -> Right value = 7

2. First Comparison:
   - 2 + 7 = 9
   - Target reached!

Answer: true
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

class Solution {
    // Specialized BST Iterator class that supports both forward and reverse directions
    class BSTIterator {
        private Stack<TreeNode> stack = new Stack<>();
        private boolean isReverse;

        public BSTIterator(TreeNode root, boolean isReverse) {
            this.isReverse = isReverse;
            pushAll(root);
        }

        public int next() {
            TreeNode node = stack.pop();
            if (!isReverse) {
                pushAll(node.right);
            } else {
                pushAll(node.left);
            }
            return node.val;
        }

        private void pushAll(TreeNode node) {
            while (node != null) {
                stack.push(node);
                if (!isReverse) {
                    node = node.left; // Forward moves down the left chain
                } else {
                    node = node.right; // Reverse moves down the right chain
                }
            }
        }
    }

    public boolean findTarget(TreeNode root, int k) {
        if (root == null) return false;

        // Initialize two pointers/iterators
        BSTIterator leftIterator = new BSTIterator(root, false);  // Forward
        BSTIterator rightIterator = new BSTIterator(root, true);  // Reverse

        int left = leftIterator.next();
        int right = rightIterator.next();

        // Standard two-pointer loop condition
        while (left < right) {
            if (left + right == k) {
                return true;
            } else if (left + right < k) {
                left = leftIterator.next();
            } else {
                right = rightIterator.next();
            }
        }

        return false;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N) in the worst-case scenario where we might have to process almost all nodes to search for the target sum. 
* **Space Complexity:** O(H) where H is the height of the BST. We maintain two stacks, each containing up to H nodes at any point in time. This is more optimal than the O(N) HashSet or full array flattening approaches.
