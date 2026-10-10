# Insert a Node in a Binary Search Tree (BST)

## Problem Statement

Given the root of a Binary Search Tree (BST) and a value `val`, insert the value into the BST while maintaining the BST property.

A BST follows the rule:

```text
Left Subtree < Root < Right Subtree
```

After insertion, the resulting tree must still satisfy this property.

---

## Example

### Input BST

```text
        50
       /  \
     30    70
    / \   /
   20 40 60
```

Insert:

```text
80
```

### Output BST

```text
        50
       /  \
     30    70
    / \   / \
   20 40 60 80
```

---

## Intuition

To insert a value:

- If the value is smaller than the current node, move left.
- If the value is greater than the current node, move right.
- Continue until a null position is found.
- Insert the new node there.

Because of BST ordering, the insertion position is unique.

---

## Recursive Solution

### Algorithm

1. If the current node is null, create a new node.
2. If `val < root.val`, insert in the left subtree.
3. Otherwise insert in the right subtree.
4. Return the root.

---

## Java Code

```java
class Solution {

    public TreeNode insertIntoBST(TreeNode root, int val) {

        if (root == null)
            return new TreeNode(val);

        if (val < root.val)
            root.left = insertIntoBST(root.left, val);
        else
            root.right = insertIntoBST(root.right, val);

        return root;
    }
}
```

---

## Dry Run

### BST

```text
        50
       /  \
     30    70
```

Insert:

```text
40
```

### Steps

```text
40 < 50
Move Left

40 > 30
Move Right

Right Child = null
Insert 40
```

Result:

```text
        50
       /  \
     30    70
       \
        40
```

---

## Iterative Solution (Optimal)

### Intuition

Instead of recursion, traverse the BST iteratively until a suitable null position is found.

---

## Java Code

```java
class Solution {

    public TreeNode insertIntoBST(TreeNode root, int val) {

        if (root == null)
            return new TreeNode(val);

        TreeNode curr = root;

        while (true) {

            if (val < curr.val) {

                if (curr.left == null) {
                    curr.left = new TreeNode(val);
                    break;
                }

                curr = curr.left;

            } else {

                if (curr.right == null) {
                    curr.right = new TreeNode(val);
                    break;
                }

                curr = curr.right;
            }
        }

        return root;
    }
}
```

---

## Dry Run

### BST

```text
        50
       /  \
     30    70
    / \   /
   20 40 60
```

Insert:

```text
65
```

### Traversal

```text
65 > 50
Move Right

65 < 70
Move Left

65 > 60
Move Right

Right = null
Insert 65
```

### Result

```text
        50
       /  \
     30    70
    / \   /
   20 40 60
            \
             65
```

---

## Complexity Analysis

| Approach | Time Complexity | Space Complexity |
|-----------|----------------|------------------|
| Recursive | O(h) | O(h) |
| Iterative | O(h) | O(1) |

where:

```text
h = height of BST
```

---

## Balanced BST

```text
Height ≈ log n
```

Complexities:

```text
Insertion = O(log n)
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

Complexities:

```text
Insertion = O(n)
```

---

## Why Does Insertion Work?

At every step:

```text
val < node.val  → Left
val > node.val  → Right
```

Eventually a null position is reached where the new node can be safely inserted while preserving:

```text
Left < Root < Right
```

---

## Visualization

### Insert 65

```text
Before

        50
       /  \
     30    70
          /
        60
```

Traversal Path:

```text
50 → 70 → 60
```

Insert:

```text
        50
       /  \
     30    70
          /
        60
          \
           65
```

---

## Recursive vs Iterative

| Approach | Advantages | Disadvantages |
|-----------|------------|---------------|
| Recursive | Cleaner and easier to understand | Uses recursion stack |
| Iterative | O(1) extra space | Slightly longer code |

For interviews, both solutions are accepted, but the **iterative solution is generally considered optimal because it avoids recursion overhead.**

---

## Key Takeaways

- Inserting a node must preserve the BST property.
- Move left when the value is smaller.
- Move right when the value is larger.
- Insert at the first null position encountered.
- Time Complexity:
  - Balanced BST → **O(log n)**
  - Skewed BST → **O(n)**
- Iterative solution uses **O(1)** extra space.
- BST insertion forms the basis for:
  - BST Construction
  - Self-Balancing Trees (AVL, Red-Black Tree)
  - Dynamic Ordered Sets
