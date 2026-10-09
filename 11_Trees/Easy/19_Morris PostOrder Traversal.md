# Morris Postorder Traversal

## Problem Statement

Perform a **Postorder Traversal (Left → Right → Root)** of a Binary Tree **without using recursion or a stack**.

Traditional approaches:

- Recursion → O(H) Space
- Stack → O(H) Space

**Morris Postorder Traversal** achieves:

```text
Time Complexity  : O(N)
Space Complexity : O(1)
```

using temporary threads and path reversal.

---

# Why is Morris Postorder Difficult?

For:

### Inorder

```text
Left → Root → Right
```

We can visit while removing threads.

---

### Preorder

```text
Root → Left → Right
```

We can visit while creating threads.

---

### Postorder

```text
Left → Right → Root
```

The root must be visited **after both subtrees**.

This makes Morris Postorder significantly more complicated.

---

# Core Idea

Instead of directly generating:

```text
Left → Right → Root
```

we generate nodes in:

```text
Root → Right → Left
```

and then reverse the answer.

OR

More formally:

1. Create a dummy node.
2. Build temporary threads.
3. Reverse edges of a path.
4. Collect nodes.
5. Restore original structure.

---

# Trick Used

Add a dummy root.

Original:

```text
        1
       / \
      2   3
```

Becomes:

```text
          D
         /
        1
       / \
      2   3
```

This simplifies handling the real root.

---

# Key Observation

Whenever a thread is removed:

```text
predecessor.right == current
```

the entire left subtree has already been processed.

At that moment we:

1. Reverse the right boundary path.
2. Visit nodes.
3. Restore the path.

---

# Example

Consider:

```text
        1
       / \
      2   3
     / \
    4   5
```

Expected Postorder:

```text
4 5 2 3 1
```

---

# High-Level Flow

### Step 1

Create dummy node.

```text
          D
         /
        1
       / \
      2   3
     / \
    4   5
```

---

### Step 2

Find predecessor.

Create threads.

```text
5.right = 1

4.right = 2
```

---

### Step 3

When revisiting through thread:

```text
4.right == 2
```

Process path:

```text
4
```

Restore.

---

### Step 4

When:

```text
5.right == 1
```

Process:

```text
5 → 2
```

Restore.

---

### Step 5

When:

```text
3.right == D
```

Process:

```text
3 → 1
```

Restore.

Final output:

```text
4 5 2 3 1
```

---

# Path Reversal Concept

Suppose:

```text
2
 \
  5
   \
    7
```

Reverse:

```text
7
 \
  5
   \
    2
```

Traverse:

```text
7 → 5 → 2
```

Restore original structure afterward.

---

# Morris Postorder Algorithm

```text
Create dummy node

dummy.left = root

current = dummy

while current != null

    if current.left == null

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

            collect reverse path

            predecessor.right = null

            current = current.right
```

---

# Java Implementation

```java
import java.util.*;

class TreeNode {
    int *al;
    TreeNode left, right;

    TreeNode(int val) {
        this.val = val;
    }
}

class Solution {

    public List<Integer> postorderTraversal(TreeNode root) {

        List<Integer> result = new ArrayList<>();

        TreeNode dummy = new TreeNode(0);
        dummy.left = root;

        TreeNode curr = dummy;

        while (curr != null) {

            if (curr.left == null) {

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

                    addReverse(curr.left, pred, result);

                    pred.right = null;
                    curr = curr.right;
                }
            }
        }

        return result;
    }

    private void addReverse(
            TreeNode from,
            TreeNode to,
            List<Integer> result) {

        reverse(from, to);

        TreeNode node = to;

        while (true) {

            result.add(node.val);

            if (node == from) {
                break;
            }

            node = node.right;
        }

        reverse(to, from);
    }

    private void reverse(
            TreeNode start,
            TreeNode end) {

        if (start == end) {
            return;
        }

        TreeNode prev = null;
        TreeNode curr = start;

        while (prev != end) {

            TreeNode next = curr.right;

            curr.right = prev;

            prev = curr;
            curr = next;
        }
    }
}
```

---

# Simpler Alternative

A very popular interview trick:

### Morris Traversal

Generate:

```text
Root → Right → Left
```

Store result.

Reverse final answer.

Example:

Generated:

```text
1 3 2 5 4
```

Reverse:

```text
4 5 2 3 1
```

Postorder obtained.

This version is easier for interviews.

---

# Why Does Morris Postorder Work?

When a thread:

```text
predecessor.right = current
```

is encountered again,

we know:

```text
Entire left subtree completed.
```

The right boundary of that subtree contains exactly the nodes that must be output now.

By reversing that boundary:

```text
Visit Correct Postorder Sequence
```

Restore it afterward.

Thus:

```text
No recursion
No stack
O(1) extra space
```

---

# Complexity Analysis

## Time Complexity

```text
O(N)
```

Every node:

- Visited constant times
- Reversed constant times
- Restored constant times

Overall:

```text
O(N)
```

---

## Space Complexity

```text
O(1)
```

Only pointers used.

No:

```text
Stack
Queue
Recursion
```

---

# Morris Traversal Comparison

| Traversal | Visit Rule |
|------------|------------|
| Morris Inorder | Visit while removing thread |
| Morris Preorder | Visit while creating thread |
| Morris Postorder | Reverse boundary path, visit, restore |

---

# Interview Questions

## Q1. Why is Morris Postorder harder than Inorder and Preorder?

Because:

```text
Root must be visited last.
```

The node cannot be processed during:

- Thread creation
- Thread removal

Instead we must process an entire boundary path.

---

## Q2. Why is a dummy node needed?

It ensures:

```text
Real root is processed correctly
```

and avoids special handling for the root.

---

## Q3. Why reverse the path?

Postorder requires:

```text
Left → Right → Root
```

The boundary is naturally available in reverse order.

Path reversal allows visiting nodes in the required sequence.

---

## Q4. Is the original tree modified?

Temporarily.

Threads are created and removed.

Reversed paths are restored.

Final tree remains unchanged.

---

# Morris Traversal Summary

## Morris Inorder

```text
Left → Root → Right
```

Visit when:

```text
Removing thread
```

---

## Morris Preorder

```text
Root → Left → Right
```

Visit when:

```text
Creating thread
```

---

## Morris Postorder

```text
Left → Right → Root
```

Visit by:

```text
Reversing boundary path
Traversing it
Restoring it
```

---

# Key Takeaway

Morris Postorder Traversal is the most advanced Morris Traversal variant. It achieves **O(N) time and O(1) space** by using temporary threads and reversing subtree boundaries during traversal. While considerably more complex than Morris Inorder and Morris Preorder, it completely eliminates recursion and stack usage while restoring the tree to its original structure.
