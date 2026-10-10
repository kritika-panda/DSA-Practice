# Largest BST in a Binary Tree

## Problem Statement

Given a Binary Tree, find the size of the **largest subtree** that is a valid Binary Search Tree (BST). The size of a subtree is defined as the total number of nodes it contains.

---

## Example

### Binary Tree

```text
            10
           /  \
          5    15
         / \     \
        1   8     7
```

### Input

```text
root = [10, 5, 15, 1, 8, null, 7]
```

### Output

```text
3
```

Because:

```text
The subtree rooted at 5:
      5
     / \
    1   8
is a valid BST because 1 < 5 < 8. Its size is 3.

The entire tree rooted at 10 is NOT a valid BST because 
the right child (15) has a right child (7) which is smaller than 10.
```

---

# Key BST Property

```text
Left Subtree Max < Root < Right Subtree Min
```

For any node to form a valid BST, the largest value in its left subtree must be strictly less than the node's value, and the smallest value in its right subtree must be strictly greater than the node's value. 

---

# Intuition

A naive top-down approach validates every subtree independently, leading to an inefficient \(O(N^2)\) solution. 

Instead, we can use a **bottom-up post-order traversal (`Left -> Right -> Root`)**. By visiting children first, each node can collect the properties of its subtrees and validate itself in \(O(1)\) time.

At each node, the subtrees pass up a structure containing:
1. `isBST`: A boolean flag checking if the subtree is a valid BST.
2. `size`: The total node count of that subtree.
3. `minVal`: The minimum value within that subtree (needed by the parent node).
4. `maxVal`: The maximum value within that subtree (needed by the parent node).

---

### Verification Logic

A node forms a valid BST if and only if:
* The left subtree is a valid BST.
* The right subtree is a valid BST.
* `leftSubtree.maxVal < root.val < rightSubtree.minVal`

If these conditions are met, the size of the BST at the current node becomes:
```text
current_size = 1 + leftSubtree.size + rightSubtree.size
```

If the conditions fail, the current node is not a BST. We pass up the maximum size found so far between the left and right subtrees:
```text
current_size = max(leftSubtree.size, rightSubtree.size)
```

---

# Visualization

Evaluating the example tree bottom-up:

```text
            10
           /  \
          5    15
         / \     \
        1   8     7
```

```text
1. Leaf Nodes (1, 8, 7):
   - All leaves are valid BSTs of size 1.
   - Node 1 returns: {isBST: true, size: 1, min: 1, max: 1}
   - Node 8 returns: {isBST: true, size: 1, min: 8, max: 8}
   - Node 7 returns: {isBST: true, size: 1, min: 7, max: 7}

2. Evaluate Node 5:
   - Left child max (1) < 5 < Right child min (8)  -> VALID!
   - Size = 1 + 1 + 1 = 3
   - Node 5 returns: {isBST: true, size: 3, min: 1, max: 8}

3. Evaluate Node 15:
   - Left child is null (valid). Right child min (7) is NOT greater than 15 -> INVALID!
   - Max size found here is from right child = 1.
   - Node 15 returns: {isBST: false, size: 1, min: -infinity, max: infinity}

4. Evaluate Root Node 10:
   - Left subtree is BST, but right subtree is NOT a BST -> INVALID!
   - Max size = max(left.size, right.size) = max(3, 1) = 3.

Final Answer: 3
```

---

# Recursive Solution

## Algorithm

1. Define a helper class `NodeInfo` to hold `minVal`, `maxVal`, `maxSize`, and `isBST`.
2. Implement a post-order traversal function.
3. **Base Case:** For `null` nodes, return a `NodeInfo` marked as a valid BST, size `0`, `minVal = Integer.MAX_VALUE`, and `maxVal = Integer.MIN_VALUE`.
4. Recursively collect info from the left and right subtrees.
5. If the current node validates against the boundaries of its left and right children, update the structural limits and calculate `1 + left.maxSize + right.maxSize`.
6. Otherwise, mark `isBST = false` and set the current size to `Math.max(left.maxSize, right.maxSize)`.

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
    // Helper class to store information passed up the tree
    private static class NodeInfo {
        int minVal;
        int maxVal;
        int maxSize;
        boolean isBST;

        NodeInfo(int minVal, int maxVal, int maxSize, boolean isBST) {
            this.minVal = minVal;
            this.maxVal = maxVal;
            this.maxSize = maxSize;
            this.isBST = isBST;
        }
    }

    public int largestBST(TreeNode root) {
        return traverse(root).maxSize;
    }

    private NodeInfo traverse(TreeNode root) {
        // Base case: An empty tree is a valid BST of size 0
        if (root == null) {
            return new NodeInfo(Integer.MAX_VALUE, Integer.MIN_VALUE, 0, true);
        }

        // Post-order traversal: Collect details from subtrees first
        NodeInfo left = traverse(root.left);
        NodeInfo right = traverse(root.right);

        // Check if current node satisfies the BST condition
        if (left.isBST && right.isBST && left.maxVal < root.val && root.val < right.minVal) {
            // Current node forms a valid BST
            int currentSize = 1 + left.maxSize + right.maxSize;
            
            // Calculate minimum and maximum values for the current subtree
            int currentMin = Math.min(root.val, left.minVal);
            int currentMax = Math.max(root.val, right.maxVal);
            
            return new NodeInfo(currentMin, currentMax, currentSize, true);
        }

        // If it is not a valid BST, pass up the maximum size found so far
        return new NodeInfo(
            Integer.MIN_VALUE, 
            Integer.MAX_VALUE, 
            Math.max(left.maxSize, right.maxSize), 
            false
        );
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N) where N is the total number of nodes in the binary tree. We perform a single post-order traversal, visiting each node exactly once.
* **Space Complexity:** O(H) where H is the height of the tree, representing the memory used by the system recursion stack. This scales to O(log N) for balanced trees and O(N) for completely skewed trees.
