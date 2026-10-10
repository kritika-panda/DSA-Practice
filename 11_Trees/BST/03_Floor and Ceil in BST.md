# Floor and Ceil in a Binary Search Tree (BST)

## Problem Statement

Given a Binary Search Tree (BST) and a value `key`:

- **Floor** = Largest value in the BST that is **less than or equal to** `key`.
- **Ceil** = Smallest value in the BST that is **greater than or equal to** `key`.

---

## Example BST

```text
          8
        /   \
       4     12
      / \    / \
     2   6  10  14
```

### For key = 5

```text
Floor = 4
Ceil  = 6
```

### For key = 10

```text
Floor = 10
Ceil  = 10
```

### For key = 13

```text
Floor = 12
Ceil  = 14
```

---

# Floor in BST

## Definition

The **Floor** of a key is the greatest value in the BST that is less than or equal to the given key.

```text
Floor(key) <= key
```

and among all such values, it must be the largest.

---

## Intuition

At any node:

### Case 1

```text
node.val == key
```

The floor is the node itself.

---

### Case 2

```text
node.val > key
```

Current node cannot be the floor.

Move to the left subtree.

---

### Case 3

```text
node.val < key
```

Current node can be a potential floor.

Store it and try to find a larger value in the right subtree.

---

## Algorithm

1. Start from the root.
2. If node value equals key, return node value.
3. If node value is greater than key, move left.
4. If node value is less than key:
   - Save it as a possible floor.
   - Move right for a better candidate.
5. Continue until the node becomes null.

---

## Java Code

```java
public int floor(TreeNode root, int key) {

    int floor = -1;

    while (root != null) {

        if (root.val == key)
            return root.val;

        if (root.val > key) {
            root = root.left;
        } else {
            floor = root.val;
            root = root.right;
        }
    }

    return floor;
}
```

---

## Dry Run

### BST

```text
          8
        /   \
       4     12
      / \    / \
     2   6  10  14
```

### Key = 5

```text
Current = 8
8 > 5 → move left

Current = 4
4 < 5 → floor = 4
move right

Current = 6
6 > 5 → move left

null
```

Answer:

```text
Floor = 4
```

---

# Ceil in BST

## Definition

The **Ceil** of a key is the smallest value in the BST that is greater than or equal to the given key.

```text
Ceil(key) >= key
```

and among all such values, it must be the smallest.

---

## Intuition

At any node:

### Case 1

```text
node.val == key
```

The ceil is the node itself.

---

### Case 2

```text
node.val < key
```

Current node cannot be the ceil.

Move to the right subtree.

---

### Case 3

```text
node.val > key
```

Current node can be a potential ceil.

Store it and move left to find a smaller valid value.

---

## Algorithm

1. Start from the root.
2. If node value equals key, return node value.
3. If node value is smaller than key, move right.
4. If node value is larger than key:
   - Save it as a possible ceil.
   - Move left for a better candidate.
5. Continue until the node becomes null.

---

## Java Code

```java
public int ceil(TreeNode root, int key) {

    int ceil = -1;

    while (root != null) {

        if (root.val == key)
            return root.val;

        if (root.val < key) {
            root = root.right;
        } else {
            ceil = root.val;
            root = root.left;
        }
    }

    return ceil;
}
```

---

## Dry Run

### BST

```text
          8
        /   \
       4     12
      / \    / \
     2   6  10  14
```

### Key = 5

```text
Current = 8
8 > 5 → ceil = 8
move left

Current = 4
4 < 5 → move right

Current = 6
6 > 5 → ceil = 6
move left

null
```

Answer:

```text
Ceil = 6
```

---

# Combined Floor and Ceil

## Java Code

```java
public int[] floorAndCeil(TreeNode root, int key) {

    int floor = -1;
    int ceil = -1;

    TreeNode curr = root;

    while (curr != null) {

        if (curr.val == key)
            return new int[]{key, key};

        if (curr.val < key) {
            floor = curr.val;
            curr = curr.right;
        } else {
            ceil = curr.val;
            curr = curr.left;
        }
    }

    return new int[]{floor, ceil};
}
```

---

## Example

```text
          8
        /   \
       4     12
      / \    / \
     2   6  10  14
```

### Key = 5

```text
Floor = 4
Ceil  = 6
```

### Key = 11

```text
Floor = 10
Ceil  = 12
```

### Key = 14

```text
Floor = 14
Ceil  = 14
```

---

# Complexity Analysis

| Operation | Time Complexity | Space Complexity |
|------------|----------------|------------------|
| Floor | O(h) | O(1) |
| Ceil | O(h) | O(1) |
| Floor + Ceil | O(h) | O(1) |

where:

```text
h = height of BST
```

### Balanced BST

```text
h = log n

Time = O(log n)
```

### Skewed BST

```text
h = n

Time = O(n)
```

---

# Visualization

```text
          8
        /   \
       4     12
      / \    / \
     2   6  10  14

Key = 5

Floor Path:
8 → 4 → 6
Answer = 4

Ceil Path:
8 → 4 → 6
Answer = 6
```

---

# Key Takeaways

- **Floor** = Greatest value ≤ key.
- **Ceil** = Smallest value ≥ key.
- Use BST properties to discard half of the tree at each step.
- Both operations can be performed iteratively.
- Time Complexity is **O(log n)** for a balanced BST.
- Space Complexity is **O(1)** using the iterative approach.
- Floor and Ceil are common interview questions and form the basis for problems like:
  - Closest Value in BST
  - Inorder Predecessor
  - Inorder Successor
  - Lower Bound
  - Upper Bound
