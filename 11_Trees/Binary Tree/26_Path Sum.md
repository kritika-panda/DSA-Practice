# 112. Path Sum

## Problem Statement

Given the `root` of a binary tree and an integer `targetSum`, return:

```text
true
```

if the tree has a **root-to-leaf** path such that the sum of all node values along the path equals `targetSum`.

Otherwise, return:

```text
false
```

A **leaf** node is a node with:

```text
No left child
AND
No right child
```

---

## Example 1

### Input

```text
root = [5,4,8,11,null,13,4,7,2,null,null,null,1]
targetSum = 22
```

### Output

```text
true
```

### Explanation

Root-to-leaf path:

```text
5 → 4 → 11 → 2
```

Sum:

```text
5 + 4 + 11 + 2 = 22
```

Since the sum equals `targetSum`, return:

```text
true
```

---

## Example 2

### Input

```text
root = [1,2,3]
targetSum = 5
```

### Output

```text
false
```

### Explanation

Possible root-to-leaf paths:

```text
1 → 2 = 3

1 → 3 = 4
```

No path sums to:

```text
5
```

---

## Example 3

### Input

```text
root = []
targetSum = 0
```

### Output

```text
false
```

---

## Key Observation

At every node:

```text
Subtract current node value
from targetSum.
```

When we reach a leaf node:

```text
targetSum == node.val
```

means we found a valid path.

---

## Recursive DFS Approach

### Strategy

For every node:

1. Reduce targetSum by current node value.
2. Move to left and right child.
3. If at a leaf node, check whether remaining sum matches node value.

---

## Algorithm

```text
hasPathSum(root, targetSum)

if root is null
    return false

if leaf node

    return targetSum == root.val

remaining =
targetSum - root.val

return
    hasPathSum(left, remaining)
    OR
    hasPathSum(right, remaining)
```

---

## Dry Run

### Input

```text
        5
       / \
      4   8
     /   / \
    11 13  4
   / \       \
  7   2       1

targetSum = 22
```

---

### Node 5

```text
remaining = 22 - 5

= 17
```

---

### Node 4

```text
remaining = 17 - 4

= 13
```

---

### Node 11

```text
remaining = 13 - 11

= 2
```

---

### Node 2

Leaf node.

Check:

```text
2 == 2
```

True ✅

Return:

```text
true
```

---

## Visualization

```text
        5
       / \
      4   8
     /
    11
   /  \
  7    2
```

Path:

```text
5 → 4 → 11 → 2
```

Running Sum:

```text
22
↓
17
↓
13
↓
2
↓
0
```

Valid path found ✅

---

## Java Solution

```java
class Solution {

    public boolean hasPathSum(TreeNode root, int targetSum) {

        if (root == null) {
            return false;
        }

        if (root.left == null &&
            root.right == null) {
            return targetSum == root.val;
        }

        int remaining = targetSum - root.val;

        return hasPathSum(root.left, remaining)
            || hasPathSum(root.right, remaining);
    }
}
```

---

## Why Does This Work?

Each recursive call answers:

```text
Does a valid path exist
from this node to a leaf
for the remaining sum?
```

By subtracting the current node value at every step, we reduce the problem size until:

```text
Leaf node reached
```

Then we simply verify whether:

```text
remaining sum == leaf value
```

---

## Complexity Analysis

### Time Complexity

```text
O(N)
```

Where:

```text
N = Number of Nodes
```

Every node is visited at most once.

---

### Space Complexity

```text
O(H)
```

Where:

```text
H = Height of Tree
```

due to the recursion stack.

---

### Balanced Tree

```text
O(log N)
```

---

### Skewed Tree

```text
O(N)
```

---

## BFS Approach

We can also solve using level-order traversal.

Store:

```text
(node, currentSum)
```

in a queue.

When a leaf node is reached:

```text
currentSum == targetSum
```

Return:

```text
true
```

Otherwise continue traversal.

---

## BFS Java Solution

```java
import java.util.*;

class Solution {

    public boolean hasPathSum(TreeNode root,
                              int targetSum) {

        if (root == null) {
            return false;
        }

        Queue<TreeNode> nodes =
                new LinkedList<>();

        Queue<Integer> sums =
                new LinkedList<>();

        nodes.offer(root);
        sums.offer(root.val);

        while (!nodes.isEmpty()) {

            TreeNode node = nodes.poll();
            int currentSum = sums.poll();

            if (node.left == null &&
                node.right == null &&
                currentSum == targetSum) {
                return true;
            }

            if (node.left != null) {
                nodes.offer(node.left);
                sums.offer(currentSum +
                           node.left.val);
            }

            if (node.right != null) {
                nodes.offer(node.right);
                sums.offer(currentSum +
                           node.right.val);
            }
        }

        return false;
    }
}
```

---

## DFS vs BFS

| Approach | Time | Space |
|-----------|--------|--------|
| Recursive DFS | O(N) | O(H) |
| BFS | O(N) | O(N) |

---

## Edge Cases

### Empty Tree

```text
root = null
```

Output:

```text
false
```

---

### Single Node

```text
root = [5]
targetSum = 5
```

Output:

```text
true
```

---

### Single Node (No Match)

```text
root = [5]
targetSum = 10
```

Output:

```text
false
```

---

### Negative Values

```text
      1
     /
   -2
```

Target:

```text
-1
```

Output:

```text
true
```

The algorithm works correctly with negative values.

---

## Interview Questions

### Q1. What is a root-to-leaf path?

A path that starts from:

```text
Root
```

and ends at:

```text
Leaf Node
```

---

### Q2. Why can't we stop when current sum becomes greater than targetSum?

Because node values may be negative.

Example:

```text
10 → -5
```

The sum can decrease later.

---

### Q3. What is the base case?

```java
if (root == null)
    return false;
```

and

```java
if (leaf node)
    return targetSum == root.val;
```

---

### Q4. Which approach is preferred?

✅ Recursive DFS

Because:

- Simple
- Easy to implement
- Natural tree traversal

---

## Related Problems

- Path Sum II
- Path Sum III
- Maximum Path Sum
- Minimum Depth of Binary Tree
- Balanced Binary Tree
- Root to Leaf Numbers

---

## Pattern Recognition

Whenever a tree problem asks:

```text
Root-to-Leaf
Target Sum
Path Exists
```

Think:

```text
DFS
+
Reduce remaining target
at each node
```

---

# Key Takeaway

The optimal solution uses **DFS recursion**.

At every node:

```text
targetSum -= currentNodeValue
```

When a leaf node is reached:

```text
targetSum == node.val
```

indicates a valid root-to-leaf path.

```text
Time Complexity  : O(N)
Space Complexity : O(H)
```
