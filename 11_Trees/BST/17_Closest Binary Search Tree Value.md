# Closest Binary Search Tree Value

## Problem

Given the root of a Binary Search Tree (BST) and a target value, return the value in the BST that is closest to the target.

### Example

**Input**

```text
       4
      / \
     2   5
    / \
   1   3

target = 3.714286
```

**Output**

```text
4
```

---

## Approach

Leverage the BST property.

For every node:

- If the current value is closer to the target, update the answer.
- If the target is smaller, move left.
- Otherwise move right.

This allows us to follow only one path from root to leaf.

---

## Java Solution

```java
class Solution {

    public int closestValue(TreeNode root, double target) {
        int closest = root.val;

        while (root != null) {

            if (Math.abs(root.val - target) < Math.abs(closest - target)) {
                closest = root.val;
            }

            if (target < root.val) {
                root = root.left;
            } else {
                root = root.right;
            }
        }

        return closest;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** `O(h)`

  Where `h` is the height of the BST.

  - Balanced BST: `O(log n)`
  - Skewed BST: `O(n)`

- **Space Complexity:** `O(1)`

  No additional data structures are used.

---

## Key Idea

Use the BST ordering property to eliminate half of the search space at every level while tracking the closest value seen so far.
