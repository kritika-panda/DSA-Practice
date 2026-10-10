# House Robber III

## Problem Statement

The thief has found himself a new place for his thievery again. There is only one entrance to this area, called `root`. Besides the `root`, each house has one and only one parent house. After a tour, the smart thief realized that all houses in this place form a **Binary Tree**. It will automatically contact the police if **two directly-linked houses were broken into on the same night**.

Given the `root` of the binary tree, return the **maximum amount of money** the thief can rob without alerting the police.

---

## Example

### Binary Tree

```text
           3
         /   \
        2     3
         \     \ 
          3     1
```

### Input

```text
root = [3, 2, 3, null, 3, null, 1]
```

### Output

```text
7
```

Because:

```text
Maximum money given by robbing houses colored below:
           [3]
         /     \
        2       3
         \       \ 
         [3]     [1]

Total amount = 3 + 3 + 1 = 7.
```

---

## Another Example

### Binary Tree

```text
           3
         /   \
        4     5
       / \     \ 
      1   3     1
```

### Input

```text
root = [3, 4, 5, 1, 3, null, 1]
```

### Output

```text
9
```

Because:

```text
Maximum money given by robbing houses colored below:
            3
         /     \
       [4]     [5]
       / \       \ 
      1   3       1

Total amount = 4 + 5 = 9.
```

---

# Key Dynamic Programming Property

```text
Decision at Node = Rob Current vs Skip Current
```

Since we cannot select two adjacent nodes, the optimal solution for any subtree depends on whether we decide to rob its root node or skip it. This relationship allows us to build a bottom-up solution using structural recursion.

---

# Intuition

A naive recursive solution solves this by calculating redundant subproblems repeatedly. Instead, we can use a **bottom-up post-order traversal (`Left -> Right -> Root`)** that returns two strategic values from every subtree.

At each house (node), the subtrees pass up an array of size 2:
1. `choices[0]`: The maximum money obtained if we **do NOT rob** this house.
2. `choices[1]`: The maximum money obtained if we **DO rob** this house.

---

### Verification Logic

When we stand at a parent node, we calculate its two options based on the results of its left and right children:

* **Case 1: We DO rob the current house**
  * We cannot rob its immediate children. 
  * Therefore, we must accept the maximum money from the children's *skipped* states.
  ```text
  rob_current = current.val + leftSubtree[0] + rightSubtree[0]
  ```

* **Case 2: We do NOT rob the current house**
  * The children are free to be either robbed or skipped.
  * We greedily pick the maximum possible outcome from each child independently.
  ```text
  skip_current = max(leftSubtree[0], leftSubtree[1]) + max(rightSubtree[0], rightSubtree[1])
  ```

---

# Visualization

Evaluating the second example tree bottom-up:

```text
           3
         /   \
        4     5
       / \     \ 
      1   3     1
```

```text
1. Leaf Nodes (1, 3, 1):
   - If skipped: 0. If robbed: leaf value.
   - Leaves return:, [0, 3], and [0, 1] respectively.

2. Evaluate Node 4:
   - skip_4 = max(left leaf) + max(right leaf) = max(0,1) + max(0,3) = 1 + 3 = 4
   - rob_4  = 4 + left[0] + right[0] = 4 + 0 + 0 = 4
   - Node 4 returns: [4, 4]

3. Evaluate Node 5:
   - skip_5 = max(left null) + max(right leaf) = 0 + max(0,1) = 1
   - rob_5  = 5 + null[0] + right[0] = 5 + 0 + 0 = 5
   - Node 5 returns: [1, 5]

4. Evaluate Root Node 3:
   - skip_root = max(Node 4 options) + max(Node 5 options) = max(4,4) + max(1,5) = 4 + 5 = 9
   - rob_root  = 3 + Node 4 skipped + Node 5 skipped = 3 + 4 + 1 = 8

Final Answer: max(skip_root, rob_root) = max(9, 8) = 9
```

---

# Recursive Solution

## Algorithm

1. Implement a helper function that performs a post-order traversal and returns an `int[]` array of size 2.
2. **Base Case:** If the node is `null`, return `new int[]{0, 0}`.
3. Recursively call the helper on the left and right subtrees to fetch their decision arrays.
4. Calculate the skipped option for the current node by adding the best outcomes of its children.
5. Calculate the robbed option for the current node by adding its own value to the skipped outcomes of its children.
6. Return the pair `[skip_current, rob_current]` up to the parent.
7. The final result from the root node will be `Math.max(rootResult[0], rootResult[1])`.

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
    public int rob(TreeNode root) {
        int[] result = robHelper(root);
        // Return the maximum value between robbing or skipping the root house
        return Math.max(result[0], result[1]);
    }

    private int[] robHelper(TreeNode root) {
        // Base case: An empty house gives 0 money whether robbed or skipped
        if (root == null) {
            return new int[]{0, 0};
        }

        // Post-order traversal: Collect profit choices from subtrees first
        int[] leftChoices = robHelper(root.left);
        int[] rightChoices = robHelper(root.right);

        int[] currentChoices = new int[2];

        // currentChoices[0] represents SKIPPING the current house
        // We can safely choose the maximum money track from each child
        currentChoices[0] = Math.max(leftChoices[0], leftChoices[1]) + 
                            Math.max(rightChoices[0], rightChoices[1]);

        // currentChoices[1] represents ROBBING the current house
        // We must strictly skip both immediate children houses
        currentChoices[1] = root.val + leftChoices[0] + rightChoices[0];

        return currentChoices;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N) where N is the total number of houses (nodes) in the binary tree. We perform a single post-order traversal, evaluating every node exactly once.
* **Space Complexity:** O(H) where H is the height of the tree, representing the memory used by the system recursion stack. This scales to O(log N) for balanced trees and O(N) for completely skewed trees.
