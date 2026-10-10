# Binary Tree Cameras

## Problem Statement

Given the `root` of a binary tree, we install cameras on the tree nodes. Each camera at a node can monitor **itself, its parent, and its immediate children**.

Return the **minimum number of cameras** needed to monitor all nodes of the tree.

---

## Example

### Binary Tree

```text
           0
          /
         0
        / \
       0   0
```

### Input

```text
root = [0, 0, null, 0, 0]
```

### Output

```text
1
```

Because:

```text
One camera placed at the second-level node (parent of the leaves) 
can monitor all nodes in this structure.
```

---

## Another Example

### Binary Tree

```text
           0
          /
         0
        /
       0
      /
     0
```

### Input

```text
root = [0, 0, null, 0, null, 0]
```

### Output

```text
2
```

Because:

```text
To cover the chain optimally, we place cameras at the 
second and fourth levels from the top.
```

---

# Key Greedy Property

```text
Place cameras as high up as possible by avoiding leaf nodes.
```

A camera placed on a leaf node can only cover itself and its parent (maximum 2 nodes). However, a camera placed on the parent of a leaf node can cover itself, its parent, and all of its children. Therefore, we should **greedily place cameras starting from the bottom up**, ensuring we never place cameras on leaves if we can avoid it.

---

# Intuition

We can use a **bottom-up post-order traversal (`Left -> Right -> Root`)** to let child nodes communicate their coverage needs to their parents.

At any given node, there are three possible states:
1. `STATE_HAS_CAMERA` (Value: 1): The node contains a camera.
2. `STATE_COVERED` (Value: 2): The node does not have a camera but is successfully monitored by a child node's camera.
3. `STATE_NEEDS_CAMERA` (Value: 0): The node does not have a camera and is not monitored by any child node.

---

### State Transitions (Bottom-Up Logic)

When evaluating a parent node based on its children's states:

* **Case 1:** If either the left or right child is `STATE_NEEDS_CAMERA` (0):
  * The parent **must** install a camera to cover that child.
  * Increment the camera count.
  * Return `STATE_HAS_CAMERA` (1).

* **Case 2:** If either the left or right child is `STATE_HAS_CAMERA` (1):
  * The parent is automatically monitored by the child's camera.
  * Return `STATE_COVERED` (2).

* **Case 3:** If both the left and right children are safely `STATE_COVERED` (2):
  * The parent doesn't need to put a camera here yet. It can defer its coverage responsibility to its own parent above.
  * Return `STATE_NEEDS_CAMERA` (0).

---

# Visualization

Evaluating the first example tree bottom-up:

```text
           A
          /
         B
        / \
       C   D
```

```text
1. Virtual Leaf Children (Null pointers):
   - Null nodes don't need coverage and don't have cameras. They return STATE_COVERED (2).

2. Evaluate Leaves C and D:
   - Both of their children are null (STATE_COVERED).
   - Leaves C and D return STATE_NEEDS_CAMERA (0) to defer tracking up to parent B.

3. Evaluate Node B:
   - Children C and D both return STATE_NEEDS_CAMERA (0).
   - Node B must install a camera! Camera count = 1.
   - Node B returns STATE_HAS_CAMERA (1).

4. Evaluate Root Node A:
   - Its left child B has STATE_HAS_CAMERA (1).
   - Therefore, Root A is automatically monitored. It returns STATE_COVERED (2).

Final Answer: 1
```

---

# Recursive Solution

## Algorithm

1. Initialize a global or instance variable `cameras = 0`.
2. Implement a post-order helper function that returns the state of the node.
3. **Base Case:** If a node is `null`, return `2` (`STATE_COVERED`).
4. Recursively determine the states of the left and right subtrees.
5. Apply the conditional matching state rules.
6. **Edge Case:** If the final state returned by the root node itself is `0` (`STATE_NEEDS_CAMERA`), it has no parent to cover it. We must place one last camera directly at the root.

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
    // State definitions for clarity
    private final int STATE_NEEDS_CAMERA = 0;
    private final int STATE_HAS_CAMERA = 1;
    private final int STATE_COVERED = 2;
    
    private int cameras = 0;

    public int minCameraCover(TreeNode root) {
        // If the root node itself is left unmonitored, place a camera on it
        if (dfs(root) == STATE_NEEDS_CAMERA) {
            cameras++;
        }
        return cameras;
    }

    private int dfs(TreeNode node) {
        // Base case: An empty node is safely considered covered
        if (node == null) {
            return STATE_COVERED;
        }

        // Post-order traversal: check children statuses first
        int leftState = dfs(node.left);
        int rightState = dfs(node.right);

        // Rule 1: If any child needs a camera, this parent node must deploy one
        if (leftState == STATE_NEEDS_CAMERA || rightState == STATE_NEEDS_CAMERA) {
            cameras++;
            return STATE_HAS_CAMERA;
        }

        // Rule 2: If any child has a camera, this parent node is safely monitored
        if (leftState == STATE_HAS_CAMERA || rightState == STATE_HAS_CAMERA) {
            return STATE_COVERED;
        }

        // Rule 3: If both children are covered but have no cameras, 
        // this parent node is exposed and needs its parent node to cover it
        return STATE_NEEDS_CAMERA;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N) where N is the total number of nodes in the binary tree. We perform a clean single-pass depth-first search, visiting each node exactly once.
* **Space Complexity:** O(H) where H is the height of the tree, representing the memory used by the recursion call stack. This ranges from O(log N) for a balanced tree up to O(N) for a skewed tree.
