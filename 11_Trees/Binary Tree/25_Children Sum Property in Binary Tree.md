# Children Sum Property in Binary Tree

## Problem Statement

A binary tree satisfies the **Children Sum Property** if for every node:

```text
Node Value = Left Child Value + Right Child Value
```

For leaf nodes, the property is considered satisfied automatically.

---

## Example 1

### Input

```text
        10
       /  \
      8    2
     / \
    3   5
```

### Output

```text
True
```

### Explanation

```text
10 = 8 + 2

8 = 3 + 5

3, 5, 2 are leaf nodes
```

Every node satisfies the Children Sum Property.

---

## Example 2

### Input

```text
        10
       /  \
      8    3
```

### Output

```text
False
```

### Explanation

```text
8 + 3 = 11

10 ≠ 11
```

Children Sum Property is violated.

---

# What is Children Sum Property?

For each non-leaf node:

```text
Node = Left Child + Right Child
```

If only one child exists:

```text
Node = Existing Child
```

---

## Valid Example

```text
         35
        /  \
      20    15
     / \    /
    15  5  15
```

Check:

```text
20 = 15 + 5

15 = 15

35 = 20 + 15
```

Property holds.

---

## Invalid Example

```text
        30
       /  \
      10   15
```

Check:

```text
10 + 15 = 25

30 ≠ 25
```

Property fails.

---

# Approach

For every node:

1. Compute sum of children.
2. Compare with current node value.
3. Recursively check left subtree.
4. Recursively check right subtree.

---

# Recursive Algorithm

```text
isSumProperty(root)

if root is null
    return true

if leaf node
    return true

childSum = left value + right value

if root.value != childSum
    return false

return
    isSumProperty(left)
    &&
    isSumProperty(right)
```

---

# Dry Run

## Input

```text
        10
       /  \
      8    2
     / \
    3   5
```

---

### Node 8

```text
3 + 5 = 8
```

Valid ✅

---

### Node 10

```text
8 + 2 = 10
```

Valid ✅

---

### Leaf Nodes

```text
3
5
2
```

Automatically valid ✅

---

Output:

```text
true
```

---

# Visualization

```text
        10
       /  \
      8    2
     / \
    3   5
```

Check:

```text
10 = 8 + 2 ✅

8 = 3 + 5 ✅
```

Result:

```text
Children Sum Property Satisfied ✅
```

---

# Java Solution

```java
class Solution {

    public boolean isSumProperty(TreeNode root) {

        if (root == null) {
            return true;
        }

        if (root.left == null &&
            root.right == null) {
            return true;
        }

        int left = 0;
        int right = 0;

        if (root.left != null) {
            left = root.left.val;
        }

        if (root.right != null) {
            right = root.right.val;
        }

        return root.val == left + right
            && isSumProperty(root.left)
            && isSumProperty(root.right);
    }
}
```

---

# Complexity Analysis

## Time Complexity

```text
O(N)
```

Every node is visited exactly once.

---

## Space Complexity

```text
O(H)
```

Where:

```text
H = Height of Tree
```

due to recursion stack.

---

# Optimized Version

The above solution is already optimal because:

```text
Every node must be checked once.
```

Therefore:

```text
Time Complexity = O(N)
```

cannot be improved further.

---

# Follow-Up: Convert Binary Tree into Children Sum Tree

A very popular interview variation is:

## Given

```text
        50
       /  \
      7    2
     / \  / \
    3  5 1  30
```

Convert tree such that:

```text
Node = Sum of Children
```

Result:

```text
        50
       /  \
      19   31
     / \  / \
    14  5 1  30
```

---

# Difference Between Checking and Conversion

| Problem | Action |
|----------|---------|
| Check Children Sum Property | Validate Tree |
| Convert To Children Sum Tree | Modify Tree |
| Balanced Tree | Height Difference Check |
| Diameter | Longest Path |
| Maximum Path Sum | Maximum Sum Path |

---

# Edge Cases

---

## Empty Tree

```text
null
```

Output:

```text
true
```

---

## Single Node

```text
5
```

Output:

```text
true
```

Leaf nodes always satisfy the property.

---

## One Child

```text
    10
   /
  10
```

Output:

```text
true
```

Since:

```text
10 = 10
```

---

## One Child (Invalid)

```text
    10
   /
  5
```

Output:

```text
false
```

Because:

```text
10 ≠ 5
```

---

# Interview Questions

## Q1. What is the Children Sum Property?

For every non-leaf node:

```text
Node Value
=
Sum of Child Values
```

---

## Q2. Are leaf nodes checked?

No.

Leaf nodes automatically satisfy the property.

---

## Q3. Why is time complexity O(N)?

Each node is visited only once.

---

## Q4. Can a node with one child satisfy the property?

✅ Yes

Example:

```text
    10
   /
  10
```

The missing child contributes:

```text
0
```

So:

```text
10 = 10 + 0
```

---

# Pattern Recognition

Whenever a problem states:

```text
Validate a condition for every node
```

Think:

```text
DFS Traversal
+
Check current node
+
Recursively check subtrees
```

This pattern appears in:

- Children Sum Property
- Balanced Binary Tree
- Same Tree
- Symmetric Tree
- BST Validation

---

# Key Takeaway

A binary tree satisfies the **Children Sum Property** if every non-leaf node follows:

```text
Node Value = Left Child Value + Right Child Value
```

The optimal solution performs a simple DFS and verifies the property at each node.

```text
Time Complexity  : O(N)
Space Complexity : O(H)
```

where **H** is the height of the tree.
