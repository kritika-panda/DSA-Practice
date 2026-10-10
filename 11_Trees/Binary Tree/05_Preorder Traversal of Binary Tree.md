# Preorder Traversal of Binary Tree

## What is Preorder Traversal?

**Preorder Traversal** is a Depth First Search (DFS) traversal technique in which nodes are visited in the following order:

```text
Root → Left → Right
```

This traversal is commonly denoted as:

```text
NLR
```

Where:

- **N** = Current Node (Root)
- **L** = Left Subtree
- **R** = Right Subtree

---

## Example

### Binary Tree

```text
        1
       / \
      2   3
     / \
    4   5
```

### Preorder Traversal

```text
1 → 2 → 4 → 5 → 3
```

### Explanation

```text
1. Visit 1
2. Traverse Left Subtree
3. Visit 2
4. Visit 4
5. Visit 5
6. Traverse Right Subtree
7. Visit 3
```

Result:

```text
[1, 2, 4, 5, 3]
```

---

# Recursive Approach

## Algorithm

```text
preorder(node):

    1. Visit Node
    2. Traverse Left Subtree
    3. Traverse Right Subtree
```

---

## Java Implementation

```java
class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;

    TreeNode(int val) {
        this.val = val;
    }
}

public class PreorderTraversal {

    public static void preorder(TreeNode root) {
        if (root == null) {
            return;
        }

        System.out.print(root.val + " ");
        preorder(root.left);
        preorder(root.right);
    }

    public static void main(String[] args) {

        TreeNode root = new TreeNode(1);
        root.left = new TreeNode(2);
        root.right = new TreeNode(3);
        root.left.left = new TreeNode(4);
        root.left.right = new TreeNode(5);

        preorder(root);
    }
}
```

### Output

```text
1 2 4 5 3
```

---

# Dry Run

```text
preorder(1)

Visit 1

preorder(2)
Visit 2

preorder(4)
Visit 4

preorder(5)
Visit 5

preorder(3)
Visit 3
```

Output:

```text
1 2 4 5 3
```

---

# Iterative Approach Using Stack

## Intuition

Unlike Inorder Traversal, we immediately visit the node before processing children.

### Steps

```text
1. Push root into stack.
2. Pop node and visit it.
3. Push right child.
4. Push left child.
5. Repeat until stack becomes empty.
```

---

## Java Implementation

```java
import java.util.ArrayList;
import java.util.List;
import java.util.Stack;

public class PreorderTraversal {

    public List<Integer> preorderTraversal(TreeNode root) {

        List<Integer> result = new ArrayList<>();

        if (root == null) {
            return result;
        }

        Stack<TreeNode> stack = new Stack<>();
        stack.push(root);

        while (!stack.isEmpty()) {

            TreeNode node = stack.pop();
            result.add(node.val);

            if (node.right != null) {
                stack.push(node.right);
            }

            if (node.left != null) {
                stack.push(node.left);
            }
        }

        return result;
    }
}
```

---

# Complexity Analysis

## Recursive Solution

### Time Complexity

```text
O(n)
```

### Space Complexity

```text
O(h)
```

Where:

- h = Height of Tree

Worst Case:

```text
O(n)
```

Balanced Tree:

```text
O(log n)
```

---

## Iterative Solution

### Time Complexity

```text
O(n)
```

### Space Complexity

```text
O(h)
```

---

# Applications of Preorder Traversal

- Tree Serialization
- Tree Copying / Cloning
- Prefix Expression Evaluation
- Constructing Trees

---

# Key Takeaways

- Preorder follows **Root → Left → Right (NLR)**.
- Root is processed before its children.
- Recursive and iterative solutions both run in **O(n)**.
- Iterative solution uses a stack.
