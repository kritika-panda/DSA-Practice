# Morris Preorder Traversal

## Problem Statement

Perform a **Preorder Traversal (Root → Left → Right)** of a Binary Tree **without using recursion or a stack**.

Traditional approaches require:

- Recursion → O(H) space
- Explicit Stack → O(H) space

**Morris Preorder Traversal** achieves:

```text
Time Complexity  : O(N)
Space Complexity : O(1)
```

by temporarily creating threads in the tree.

---

# What is Morris Preorder Traversal?

Morris Preorder Traversal is a variation of Morris Traversal that traverses a binary tree in:

```text
Root → Left → Right
```

without recursion or an explicit stack.

It works by:

1. Finding the inorder predecessor.
2. Creating a temporary thread.
3. Visiting the node when the thread is created.
4. Removing the thread when revisited.

---

# Difference Between Morris Inorder & Morris Preorder

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
FIRST visiting the node
(before moving left)
```

This is the only major difference.

---

# Key Idea

For every node:

### Case 1: No Left Child

Visit current node.

Move right.

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

Find the inorder predecessor (rightmost node in left subtree).

If thread doesn't exist:

```text
predecessor.right = current
```

Visit current node.

Move left.

---

### Case 3: Thread Exists

Remove thread.

Move right.

Unlike inorder traversal:

```text
DO NOT VISIT AGAIN
```

because current node was already visited when thread was created.

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

Expected Preorder:

```text
4 2 1 3 6 5 7
```

---

# Dry Run

---

## Current = 4

Left subtree exists.

Predecessor:

```text
3
```

Create thread:

```text
3.right = 4
```

Visit:

```text
4
```

Move left.

```text
Current = 2
```

Output:

```text
4
```

---

## Current = 2

Predecessor:

```text
1
```

Create thread:

```text
1.right = 2
```

Visit:

```text
2
```

Move left.

Output:

```text
4 2
```

---

## Current = 1

No left child.

Visit:

```text
1
```

Move right via thread.

Output:

```text
4 2 1
```

---

## Current = 2

Thread exists:

```text
1.right == 2
```

Remove thread.

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

Output:

```text
4 2 1 3
```

---

## Current = 4

Thread exists:

```text
3.right == 4
```

Remove thread.

Move right.

```text
Current = 6
```

---

Continue similarly.

Final output:

```text
4 2 1 3 6 5 7
```

---

# Visualization

## Create First Thread

```text
        4
       /
      2
     / \
    1   3

Create:

3 ----------> 4
```

Visit:

```text
4
```

---

## Create Second Thread

```text
      2
     /
    1

Create:

1 ----------> 2
```

Visit:

```text
2
```

---

## Output Order

```text
Visit 4

Visit 2

Visit 1

Visit 3

Visit 6

Visit 5

Visit 7
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

            visit(current)

            predecessor.right = current

            current = current.left

        else

            predecessor.right = null

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

    public List<Integer> preorderTraversal(TreeNode root) {

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

                    result.add(curr.val);

                    pred.right = curr;
                    curr = curr.left;

                } else {

                    pred.right = null;
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

Preorder traversal requires:

```text
Root → Left → Right
```

Therefore we must process a node before entering its left subtree.

When we first encounter a node:

```text
Visit it immediately
```

and create a temporary thread to return later.

When we revisit through the thread:

```text
Thread exists
```

so we know the left subtree has already been processed.

Remove the thread and continue to the right subtree.

---

# Complexity Analysis

## Time Complexity

```text
O(N)
```

Every edge is traversed at most twice:

- Once while creating thread
- Once while removing thread

---

## Space Complexity

```text
O(1)
```

No:

- Stack
- Queue
- Recursion

is used.

---

# Comparison

| Approach | Time | Space |
|-----------|--------|--------|
| Recursive Preorder | O(N) | O(H) |
| Iterative Stack | O(N) | O(H) |
| Morris Preorder | O(N) | O(1) |

---

# Morris Inorder vs Morris Preorder

| Operation | Inorder | Preorder |
|------------|----------|----------|
| Traversal Order | Left → Root → Right | Root → Left → Right |
| Visit Node When | Removing Thread | Creating Thread |
| Visit Current if Left Null | Yes | Yes |
| Extra Space | O(1) | O(1) |

---

# Interview Questions

## Q1. What's the only change from Morris Inorder?

In Morris Inorder:

```java
result.add(curr.val);
```

is executed when removing the thread.

In Morris Preorder:

```java
result.add(curr.val);
```

is executed when creating the thread.

---

## Q2. Why visit before moving left?

Because preorder requires:

```text
Root → Left → Right
```

Current node must be processed before entering the left subtree.

---

## Q3. Does Morris Preorder modify the tree?

Temporarily.

It creates:

```text
predecessor.right = current
```

and later removes it.

Original tree is fully restored.

---

## Q4. Why is time complexity O(N)?

Each thread:

```text
Created once
Removed once
```

Every edge is processed at most twice.

Hence:

```text
O(N)
```

---

# Key Takeaway

Morris Preorder Traversal performs **Preorder Traversal (Root → Left → Right)** in **O(N) time and O(1) space** by temporarily creating threads from a node's inorder predecessor back to the current node. The core idea is simple:

```text
Morris Inorder  -> Visit while removing thread
Morris Preorder -> Visit while creating thread
```

This small change converts Morris Inorder Traversal into Morris Preorder Traversal while maintaining **O(1) auxiliary space**.
