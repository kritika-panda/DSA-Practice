# Morris Inorder Traversal

## Problem Statement

Perform an **Inorder Traversal (Left → Root → Right)** of a Binary Tree **without using recursion or a stack**.

Traditional approaches require:

- Recursion → O(H) space
- Explicit Stack → O(H) space

**Morris Traversal** achieves:

```text
Time Complexity  : O(N)
Space Complexity : O(1)
```

by temporarily modifying the tree structure.

---

# What is Morris Traversal?

Morris Traversal is a tree traversal technique that performs traversal without recursion and without an auxiliary stack.

It works by creating temporary links (threads) from a node's inorder predecessor back to the current node.

After traversal, all modifications are reverted, restoring the original tree.

---

# Key Idea

For every node:

### Case 1: Left Child is NULL

Visit the node and move right.

```text
Current
   5
    \
     7
```

Output:

```text
5
```

Move to:

```text
7
```

---

### Case 2: Left Child Exists

Find the inorder predecessor (rightmost node in the left subtree).

Create a temporary thread.

```text
        5
       /
      3
       \
        4
```

Inorder predecessor of `5` is `4`.

Create:

```text
4.right = 5
```

Then move left.

---

### Case 3: Thread Already Exists

When we come back through the thread:

```text
4.right == 5
```

Remove the thread.

Visit current node.

Move right.

---

# Example

Consider:

```text
        4
      /   \
     2     6
    / \   / \
   1  3  5   7
```

Expected Inorder:

```text
1 2 3 4 5 6 7
```

---

# Dry Run

---

## Current = 4

Left subtree exists.

Find predecessor:

```text
2 → 3
```

Predecessor = 3

Create thread:

```text
3.right = 4
```

Move left.

```text
Current = 2
```

---

## Current = 2

Find predecessor.

```text
1
```

Create thread:

```text
1.right = 2
```

Move left.

```text
Current = 1
```

---

## Current = 1

No left child.

Visit:

```text
1
```

Move right via thread.

```text
Current = 2
```

---

## Current = 2

Thread exists:

```text
1.right == 2
```

Remove thread.

Visit:

```text
2
```

Move right.

```text
Current = 3
```

---

## Current = 3

No left child.

Visit:

```text
3
```

Move right via thread.

```text
Current = 4
```

---

## Current = 4

Thread exists:

```text
3.right == 4
```

Remove thread.

Visit:

```text
4
```

Move right.

```text
Current = 6
```

---

Continue similarly.

Final Output:

```text
1 2 3 4 5 6 7
```

---

# Visualization

## First Thread

```text
        4
       /
      2
     / \
    1   3

Create:

3 ----------> 4
```

---

## Second Thread

```text
      2
     /
    1

Create:

1 ----------> 2
```

---

## Traversal Order

```text
1
↑ remove thread

2
↑ remove thread

3

4

5

6

7
```

---

# Algorithm

```text
current = root

while current != null

    if current.left == null

        visit(current)

        current = current.right

    else

        predecessor = current.left

        while predecessor.right != null
              and predecessor.right != current

              predecessor = predecessor.right

        if predecessor.right == null

            predecessor.right = current

            current = current.left

        else

            predecessor.right = null

            visit(current)

            current = current.right
```

---

# Java Implementation

```java
import java.util.*;

class TreeNode {
    int val;
    TreeNode left, right;

    TreeNode(int val) {
        this.val = val;
    }
}

class Solution {

    public List<Integer> inorderTraversal(TreeNode root) {

        List<Integer> result = new ArrayList<>();
        TreeNode curr = root;

        while (curr != null) {

            if (curr.left == null) {

                result.add(curr.val);
                curr = curr.right;

            } else {

                TreeNode pred = curr.left;

                while (pred.right != null &&
                       pred.right != curr) {
                    pred = pred.right;
                }

                if (pred.right == null) {

                    pred.right = curr;
                    curr = curr.left;

                } else {

                    pred.right = null;
                    result.add(curr.val);
                    curr = curr.right;
                }
            }
        }

        return result;
    }
}
```

---

# Why Does It Work?

The inorder traversal order is:

```text
Left → Root → Right
```

When a left subtree exists, we must return to the current node after finishing it.

Normally:

```text
Recursion Stack
```

stores this return path.

Morris Traversal creates:

```text
Predecessor.right = Current
```

which acts as a temporary return address.

Thus:

```text
Thread = Substitute for recursion stack
```

Once the node is revisited, the thread is removed.

Therefore the tree remains unchanged.

---

# Complexity Analysis

## Time Complexity

```text
O(N)
```

### Why?

Every edge is visited at most:

```text
2 times
```

- Once while creating thread
- Once while removing thread

Thus:

```text
O(N)
```

---

## Space Complexity

```text
O(1)
```

No:

- Recursion Stack
- Explicit Stack
- Queue

is used.

---

# Comparison of Inorder Traversal Approaches

| Approach | Time | Space |
|-----------|--------|--------|
| Recursive | O(N) | O(H) |
| Iterative Stack | O(N) | O(H) |
| Morris Traversal | O(N) | O(1) |

Where:

```text
H = Height of Tree
```

---

# Advantages

✅ O(1) Auxiliary Space

✅ No Recursion

✅ No Stack

✅ Restores Original Tree

✅ Useful for Memory-Constrained Systems

---

# Disadvantages

❌ Temporarily modifies tree structure

❌ More difficult to understand

❌ Not ideal when tree modifications are prohibited

---

# Interview Questions

## Q1. What is the inorder predecessor?

The rightmost node in the left subtree.

Example:

```text
        10
       /
      5
       \
        8
```

Inorder predecessor of `10`:

```text
8
```

---

## Q2. Why is space complexity O(1)?

Because Morris Traversal does not use:

```text
Recursion
Stack
Queue
```

Only a few pointers are used.

---

## Q3. Does Morris Traversal change the tree?

Temporarily.

It creates threads:

```text
predecessor.right = current
```

and later removes them.

The original tree is restored.

---

## Q4. Why isn't complexity O(N²)?

Although we search for predecessors repeatedly:

- Every thread is created once.
- Every thread is removed once.

Thus each edge is processed at most twice.

Overall:

```text
O(N)
```

---

# Morris Traversal Variants

## Morris Inorder

```text
Left → Root → Right
```

Visit node when:

```text
Returning from left subtree
```

---

## Morris Preorder

```text
Root → Left → Right
```

Visit node when:

```text
Creating thread
```

---

## Morris Postorder

```text
Left → Right → Root
```

More complex implementation involving reversed paths.

---

# Key Takeaway

Morris Inorder Traversal is an advanced tree traversal technique that performs **Inorder Traversal in O(N) time and O(1) space** by temporarily creating threads from a node's inorder predecessor back to the current node. It eliminates the need for recursion or an explicit stack while restoring the tree structure after traversal.
