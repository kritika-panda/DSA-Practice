# Kth Smallest and Kth Largest Element in a Binary Search Tree (BST)

## Problem Statement

Given a Binary Search Tree (BST) and an integer `k`, find:

- The **Kth Smallest Element**
- The **Kth Largest Element**

in the BST.

---

## Key BST Property

The most important observation is:

### Inorder Traversal

```text
Left → Root → Right
```

produces elements in **ascending order**.

### Reverse Inorder Traversal

```text
Right → Root → Left
```

produces elements in **descending order**.

---

## Example BST

```text
           5
         /   \
        3     7
       / \   / \
      2   4 6   8
```

### Inorder Traversal

```text
2, 3, 4, 5, 6, 7, 8
```

### Reverse Inorder Traversal

```text
8, 7, 6, 5, 4, 3, 2
```

---

# Kth Smallest Element

## Intuition

Since inorder traversal gives elements in sorted order:

```text
1st Smallest → 2
2nd Smallest → 3
3rd Smallest → 4
4th Smallest → 5
```

We simply perform inorder traversal and stop when we visit the kth node.

---

## Recursive Solution

### Java Code

```java
class Solution {

    int count = 0;
    int ans = -1;

    public int kthSmallest(TreeNode root, int k) {
        inorder(root, k);
        return ans;
    }

    private void inorder(TreeNode root, int k) {

        if (root == null)
            return;

        inorder(root.left, k);

        if (++count == k) {
            ans = root.val;
            return;
        }

        inorder(root.right, k);
    }
}
```

---

## Dry Run

### BST

```text
           5
         /   \
        3     7
       / \   / \
      2   4 6   8
```

### k = 3

Inorder:

```text
2 → 3 → 4 → 5 → 6 → 7 → 8
```

Count:

```text
2 → count = 1
3 → count = 2
4 → count = 3
```

Answer:

```text
4
```

---

# Iterative Solution (Optimal)

Using a stack to simulate inorder traversal.

### Java Code

```java
class Solution {

    public int kthSmallest(TreeNode root, int k) {

        Stack<TreeNode> stack = new Stack<>();

        while (true) {

            while (root != null) {
                stack.push(root);
                root = root.left;
            }

            root = stack.pop();

            if (--k == 0)
                return root.val;

            root = root.right;
        }
    }
}
```

---

## Complexity

```text
Time  : O(H + K)
Space : O(H)
```

where:

```text
H = Height of BST
```

---

# Kth Largest Element

## Intuition

Reverse inorder traversal produces elements in descending order.

```text
Right → Root → Left
```

So the kth visited node is the kth largest element.

---

## Recursive Solution

### Java Code

```java
class Solution {

    int count = 0;
    int ans = -1;

    public int kthLargest(TreeNode root, int k) {
        reverseInorder(root, k);
        return ans;
    }

    private void reverseInorder(TreeNode root, int k) {

        if (root == null)
            return;

        reverseInorder(root.right, k);

        if (++count == k) {
            ans = root.val;
            return;
        }

        reverseInorder(root.left, k);
    }
}
```

---

## Dry Run

### BST

```text
           5
         /   \
        3     7
       / \   / \
      2   4 6   8
```

### k = 2

Reverse Inorder:

```text
8 → 7 → 6 → 5 → 4 → 3 → 2
```

Count:

```text
8 → count = 1
7 → count = 2
```

Answer:

```text
7
```

---

# Iterative Solution

### Java Code

```java
class Solution {

    public int kthLargest(TreeNode root, int k) {

        Stack<TreeNode> stack = new Stack<>();

        while (true) {

            while (root != null) {
                stack.push(root);
                root = root.right;
            }

            root = stack.pop();

            if (--k == 0)
                return root.val;

            root = root.left;
        }
    }
}
```

---

# Visual Understanding

### BST

```text
           5
         /   \
        3     7
       / \   / \
      2   4 6   8
```

### Sorted Order (Inorder)

```text
2   3   4   5   6   7   8
↑
1st Smallest

        ↑
      4th Smallest

                ↑
             7th Smallest
```

---

### Descending Order (Reverse Inorder)

```text
8   7   6   5   4   3   2
↑
1st Largest

    ↑
 2nd Largest

            ↑
         4th Largest
```

---

# Morris Traversal Solution (Follow-Up)

Interviewers may ask for:

```text
Can you solve this using O(1) space?
```

Use:

```text
Morris Inorder Traversal
```

for Kth Smallest and

```text
Reverse Morris Traversal
```

for Kth Largest.

### Complexity

```text
Time  : O(n)
Space : O(1)
```

---

# Complexity Analysis

## Kth Smallest

| Approach | Time | Space |
|-----------|------|--------|
| Recursive Inorder | O(H + K) | O(H) |
| Iterative Inorder | O(H + K) | O(H) |
| Morris Traversal | O(n) | O(1) |

---

## Kth Largest

| Approach | Time | Space |
|-----------|------|--------|
| Recursive Reverse Inorder | O(H + K) | O(H) |
| Iterative Reverse Inorder | O(H + K) | O(H) |
| Reverse Morris Traversal | O(n) | O(1) |

---

# Why Does This Work?

Because of BST ordering:

```text
Left < Root < Right
```

Therefore:

```text
Inorder Traversal
=
Sorted Order
```

and

```text
Reverse Inorder Traversal
=
Descending Order
```

Thus:

- Kth visited node in inorder = Kth Smallest
- Kth visited node in reverse inorder = Kth Largest

---

# Key Takeaways

- **Inorder Traversal (LNR)** gives nodes in ascending order.
- **Reverse Inorder Traversal (RNL)** gives nodes in descending order.
- Kth Smallest = kth node visited during inorder traversal.
- Kth Largest = kth node visited during reverse inorder traversal.
- Standard interview solution:
  - **O(H + K)** Time
  - **O(H)** Space
- Follow-up optimal solution:
  - **Morris Traversal**
  - **O(1)** Space
- This is one of the most frequently asked BST interview questions and is commonly seen in FAANG and product-based company interviews.
