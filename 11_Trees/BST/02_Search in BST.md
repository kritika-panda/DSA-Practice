# Search in a Binary Search Tree (BST)

## Problem Statement

Given the root of a Binary Search Tree (BST) and a target value, determine whether the value exists in the BST.

A Binary Search Tree follows the property:

```text
Left Subtree < Root < Right Subtree
```

This property allows us to efficiently search for a value by eliminating half of the tree at every step.

---

## Example

### BST

```text
          50
         /  \
       30    70
      / \   / \
    20  40 60 80
```

Search for `60`.

### Steps

1. Compare `60` with `50`.
2. Since `60 > 50`, move to the right subtree.
3. Compare `60` with `70`.
4. Since `60 < 70`, move to the left subtree.
5. Compare `60` with `60`.
6. Value found.

Result:

```text
true
```

---

## Intuition

At each node:

- If the current node value equals the target, return `true`.
- If the target is smaller, search in the left subtree.
- If the target is greater, search in the right subtree.

Since half of the tree is ignored at each comparison, searching is efficient.

---

## Recursive Solution

### Algorithm

1. If the current node is `null`, return `false`.
2. If the node value equals the target, return `true`.
3. If the target is smaller, search the left subtree.
4. Otherwise, search the right subtree.

### Java Code

```java
class Solution {

    public boolean searchBST(TreeNode root, int val) {
        if (root == null) return false;

        if (root.val == val) return true;

        return val < root.val
                ? searchBST(root.left, val)
                : searchBST(root.right, val);
    }
}
```

### Time Complexity

```text
O(h)
```

where `h` is the height of the BST.

### Space Complexity

```text
O(h)
```

due to the recursive call stack.

---

## Iterative Solution (Optimal)

### Algorithm

1. Start from the root.
2. Compare the target with the current node.
3. Move left if the target is smaller.
4. Move right if the target is greater.
5. Continue until the value is found or the node becomes `null`.

### Java Code

```java
class Solution {

    public boolean searchBST(TreeNode root, int val) {

        while (root != null) {

            if (root.val == val)
                return true;

            root = val < root.val
                    ? root.left
                    : root.right;
        }

        return false;
    }
}
```

### Time Complexity

```text
O(h)
```

### Space Complexity

```text
O(1)
```

---

## Dry Run

### Input

```text
BST:
          50
         /  \
       30    70
      / \   / \
    20 40 60 80

Target = 60
```

### Execution

| Current Node | Comparison | Move |
|-------------|------------|------|
| 50 | 60 > 50 | Right |
| 70 | 60 < 70 | Left |
| 60 | 60 == 60 | Found |

### Output

```text
true
```

---

## Search for a Non-Existing Value

### Input

```text
Target = 25
```

### Execution

```text
50 → Left
30 → Left
20 → Right
null
```

### Output

```text
false
```

---

## Complexity Analysis

| Scenario | Time Complexity |
|-----------|----------------|
| Balanced BST | O(log n) |
| Skewed BST | O(n) |

### Space Complexity

| Approach | Space Complexity |
|-----------|----------------|
| Recursive | O(h) |
| Iterative | O(1) |

---

## Why is Searching Fast in BST?

Because of the BST property:

```text
Left < Root < Right
```

When searching for a value:

- One entire subtree can be discarded.
- Similar to Binary Search on a sorted array.
- Average search time becomes `O(log n)`.

### Example

Searching for `80`:

```text
50 → 70 → 80
```

Only 3 nodes are visited instead of traversing the entire tree.

---

## Visualization

```text
Searching for 60

          50
            \
             70
            /
          60

Path Followed:
50 → 70 → 60
```

---

## Key Takeaways

- BST search leverages the property:

```text
Left < Root < Right
```

- Search moves only in one direction at each node.
- Average Time Complexity: `O(log n)`
- Worst Case Time Complexity: `O(n)`
- Iterative solution is generally preferred because it uses `O(1)` extra space.
- BST searching works similarly to Binary Search on a sorted array.
