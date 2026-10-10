# Lowest Common Ancestor (LCA) in a Binary Search Tree (BST)

## Problem Statement

Given a Binary Search Tree (BST) and two nodes `p` and `q`, find their **Lowest Common Ancestor (LCA)**.

### Definition

The **Lowest Common Ancestor** of two nodes is the lowest node in the tree that has both nodes as descendants (where a node can be a descendant of itself).

---

## Example

### BST

```text
           6
         /   \
        2     8
       / \   / \
      0   4 7   9
         / \
        3   5
```

### Input

```text
p = 2
q = 8
```

### Output

```text
6
```

Because:

```text
2 is in left subtree of 6
8 is in right subtree of 6
```

Therefore:

```text
LCA = 6
```

---

## Another Example

### Input

```text
p = 2
q = 4
```

### Output

```text
2
```

Tree:

```text
       2
      / \
     0   4
        / \
       3   5
```

Node `2` itself is the ancestor of `4`.

Therefore:

```text
LCA = 2
```

---

# Key BST Property

```text
Left Subtree < Root < Right Subtree
```

This property allows us to find the LCA without visiting every node.

---

# Intuition

At every node:

### Case 1

Both nodes are smaller than the current node.

```text
p < root
q < root
```

LCA must lie in the left subtree.

Move left.

---

### Case 2

Both nodes are greater than the current node.

```text
p > root
q > root
```

LCA must lie in the right subtree.

Move right.

---

### Case 3

The nodes split at the current node.

```text
p < root < q
```

OR

```text
q < root < p
```

Current node is the first common ancestor.

Therefore:

```text
LCA = root
```

---

# Visualization

Find LCA of:

```text
p = 2
q = 8
```

```text
           6
         /   \
        2     8
```

At node:

```text
6
```

Observe:

```text
2 < 6 < 8
```

The nodes split here.

Answer:

```text
LCA = 6
```

---

# Recursive Solution

## Algorithm

1. If both nodes are smaller, go left.
2. If both nodes are greater, go right.
3. Otherwise current node is the answer.

---

## Java Code

```java
class Solution {

    public TreeNode lowestCommonAncestor(TreeNode root,
                                         TreeNode p,
                                         TreeNode q) {

        if (root == null)
            return null;

        if (p.val < root.val && q.val < root.val)
            return lowestCommonAncestor(root.left, p, q);

        if (p.val > root.val && q.val > root.val)
            return lowestCommonAncestor(root.right, p, q);

        return root;
    }
}
```

---

# Iterative Solution (Optimal)

## Java Code

```java
class Solution {

    public TreeNode lowestCommonAncestor(TreeNode root,
                                         TreeNode p,
                                         TreeNode q) {

        while (root != null) {

            if (p.val < root.val && q.val < root.val) {
                root = root.left;

            } else if (p.val > root.val && q.val > root.val) {
                root = root.right;

            } else {
                return root;
            }
        }

        return null;
    }
}
```

---

# Dry Run

## Input

```text
           6
         /   \
        2     8
       / \   / \
      0   4 7   9
         / \
        3   5

p = 3
q = 5
```

---

### Step 1

Current Node:

```text
6
```

Both values are smaller.

```text
3 < 6
5 < 6
```

Move left.

---

### Step 2

Current Node:

```text
2
```

Both values are greater.

```text
3 > 2
5 > 2
```

Move right.

---

### Step 3

Current Node:

```text
4
```

Observe:

```text
3 < 4 < 5
```

Split occurs.

Answer:

```text
LCA = 4
```

---

# Why Does This Work?

Consider:

```text
           6
         /   \
        2     8
       / \
      0   4
```

If:

```text
p = 0
q = 4
```

At node 2:

```text
0 < 2 < 4
```

One node lies on the left.

One node lies on the right.

Therefore no lower node can be their common ancestor.

Hence:

```text
2 is the LCA
```

---

# Complexity Analysis

## Recursive Solution

| Complexity | Value |
|------------|--------|
| Time | O(h) |
| Space | O(h) |

---

## Iterative Solution

| Complexity | Value |
|------------|--------|
| Time | O(h) |
| Space | O(1) |

where:

```text
h = height of BST
```

---

## Balanced BST

```text
h = log n
```

Time:

```text
O(log n)
```

---

## Skewed BST

```text
10
  \
   20
     \
      30
        \
         40
```

Height:

```text
n
```

Time:

```text
O(n)
```

---

# BST LCA vs Binary Tree LCA

| Feature | BST | Normal Binary Tree |
|----------|------|-------------------|
| Uses Ordering Property | Yes | No |
| Need to Search Entire Tree | No | Often Yes |
| Time Complexity | O(h) | O(n) |
| Space Complexity | O(1) Iterative | O(h) |

---

# Visualization

### LCA(3, 5)

```text
           6
          /
         2
          \
           4
          / \
         3   5
```

Traversal:

```text
6 → 2 → 4
```

At `4`:

```text
3 < 4 < 5
```

Answer:

```text
LCA = 4
```

---

# Key Takeaways

- BST property allows us to avoid traversing the entire tree.
- If both nodes are smaller, move left.
- If both nodes are greater, move right.
- The first node where the paths diverge is the LCA.
- Time Complexity:
  - Balanced BST → **O(log n)**
  - Skewed BST → **O(n)**
- Iterative solution is preferred because it uses **O(1)** extra space.
- LCA in BST is significantly simpler than LCA in a normal Binary Tree due to the ordering property.
