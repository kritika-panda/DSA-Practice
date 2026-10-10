# Maximum Sum BST in Binary Tree

## Problem Statement

Given a Binary Tree, find the **maximum sum** of all keys among all subtrees that are valid **Binary Search Trees (BST)**.

### Definition

A subtree of a binary tree is a tree consisting of a node and all of its descendants. A valid BST satisfies the condition that for every node, all values in its left subtree are strictly less than the node's value, and all values in its right subtree are strictly greater than the node's value.

---

## Example

### Binary Tree

```text
            1
           /  \
          4    3
         / \  / \
        2   4 2   5
                 / \
                4   6
```

### Input

```text
root = [1, 4, 3, 2, 4, 2, 5, null, null, null, null, null, null, 4, 6]
```

### Output

```text
20
```

Because:

```text
The maximum sum BST is the subtree rooted at node 3:
            3
           / \
          2   5
             / \
            4   6

Sum = 3 + 2 + 5 + 4 + 6 = 20.
The entire tree rooted at 1 is not a valid BST.
```

---

## Another Example

### Binary Tree

```text
           -4
           / \
          -2  -5
```

### Input

```text
root = [-4, -2, -5]
```

### Output

```text
0
```

Because:

```text
All node values are negative. An empty tree is technically a valid BST 
with a sum of 0, which is larger than any negative node sum here.
```

---

# Key BST Property

```text
Left Subtree Max < Root < Right Subtree Min
```

For any node to form a valid BST, the largest value in its left subtree must be strictly less than the node's value, and the smallest value in its right subtree must be strictly greater than the node's value.

---

# Intuition

Evaluating each node top-down results in redundant checks and poor efficiency. Instead, we can use a **bottom-up post-order traversal (`Left -> Right -> Root`)**. By validating the children first, each node can determine if it forms a valid BST in O(1) time.

At each node, the subtrees pass up a structure containing:
1. `isBST`: A boolean flag checking if the subtree is a valid BST.
2. `minVal`: The minimum value within that subtree.
3. `maxVal`: The maximum value within that subtree.
4. `sum`: The total sum of all node values within that subtree.

---

### Verification Logic

A node forms a valid BST if and only if:
* The left subtree is a valid BST.
* The right subtree is a valid BST.
* `leftSubtree.maxVal < root.val < rightSubtree.minVal`

If these conditions are met, the current node forms a new valid BST, and its sum is calculated as:
```text
current_sum = root.val + leftSubtree.sum + rightSubtree.sum
```
We track the global maximum of all these calculated valid BST sums.

If the conditions fail, we mark `isBST = false`. Its sum won't be used to update the global maximum, but we pass the data up so parent nodes know their subtree configuration is invalid.

---

# Visualization

Evaluating part of the example tree bottom-up:

```text
            3
           / \
          2   5
             / \
            4   6
```

```text
1. Leaf Nodes (2, 4, 6):
   - All leaves are valid BSTs.
   - Node 2 returns: {isBST: true, min: 2, max: 2, sum: 2}
   - Node 4 returns: {isBST: true, min: 4, max: 4, sum: 4}
   - Node 6 returns: {isBST: true, min: 6, max: 6, sum: 6}
   - Global Max Sum updates to 6.

2. Evaluate Node 5:
   - Left max (4) < 5 < Right min (6) -> VALID!
   - Current Sum = 5 + 4 + 6 = 15
   - Global Max Sum updates to 15.
   - Node 5 returns: {isBST: true, min: 4, max: 6, sum: 15}

3. Evaluate Node 3:
   - Left max (2) < 3 < Right min (4) -> VALID!
   - Current Sum = 3 + 2 + 15 = 20
   - Global Max Sum updates to 20.
   - Node 3 returns: {isBST: true, min: 2, max: 6, sum: 20}

Final Answer: 20
```

---

# Recursive Solution

## Algorithm

1. Maintain a global variable `maxSum` initialized to 0.
2. Implement a post-order traversal function.
3. **Base Case:** For `null` nodes, return a structure marked as a valid BST, `minVal = Integer.MAX_VALUE`, `maxVal = Integer.MIN_VALUE`, and `sum = 0`.
4. Recursively collect info from the left and right subtrees.
5. If the current node validates against the boundaries of its left and right children, calculate the total sum, update `maxSum`, and pass up the collective boundary details.
6. Otherwise, mark `isBST = false` and pass it upward.

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
        boolean isBST;
        int minVal;
        int maxVal;
        int sum;

        NodeInfo(boolean isBST, int minVal, int maxVal, int sum) {
            this.isBST = isBST;
            this.minVal = minVal;
            this.maxVal = maxVal;
            this.sum = sum;
        }
    }

    private int maxSum = 0;

    public int maxSumBST(TreeNode root) {
        maxSum = 0; // Reset for each call
        traverse(root);
        return maxSum;
    }

    private NodeInfo traverse(TreeNode root) {
        // Base case: An empty tree is a valid BST with a sum of 0
        if (root == null) {
            return new NodeInfo(true, Integer.MAX_VALUE, Integer.MIN_VALUE, 0);
        }

        // Post-order traversal: Collect details from subtrees first
        NodeInfo left = traverse(root.left);
        NodeInfo right = traverse(root.right);

        // Check if current node satisfies the BST condition
        if (left.isBST && right.isBST && left.maxVal < root.val && root.val < right.minVal) {
            // Current node forms a valid BST
            int currentSum = root.val + left.sum + right.sum;
            
            // Track the maximum sum found across all valid BSTs
            maxSum = Math.max(maxSum, currentSum);
            
            // Calculate minimum and maximum values for the current subtree
            int currentMin = Math.min(root.val, left.minVal);
            int currentMax = Math.max(root.val, right.maxVal);
            
            return new NodeInfo(true, currentMin, currentMax, currentSum);
        }

        // If it is not a valid BST, mark it false (boundaries become irrelevant)
        return new NodeInfo(false, 0, 0, 0);
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N) where N is the total number of nodes in the binary tree. We perform a single post-order traversal, visiting each node exactly once.
* **Space Complexity:** O(H) where H is the height of the tree, representing the memory used by the system recursion stack. This scales to O(log N) for balanced trees and O(N) for completely skewed trees.
