# Postorder Traversal of Binary Tree

## What is Postorder Traversal?

**Postorder Traversal** is a Depth First Search (DFS) traversal technique in which nodes are visited in the following order:

```text
Left → Right → Root
```

This traversal is commonly denoted as:

```text
LRN
```

Where:

- **L** = Left Subtree
- **R** = Right Subtree
- **N** = Current Node (Root)

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

### Postorder Traversal

```text
4 → 5 → 2 → 3 → 1
```

### Explanation

```text
1. Traverse Left Subtree
2. Visit 4
3. Visit 5
4. Visit 2
5. Traverse Right Subtree
6. Visit 3
7. Visit Root 1
```

Result:

```text
[4, 5, 2, 3, 1]
```

---

# Recursive Approach

## Algorithm

```text
postorder(node):

    1. Traverse Left Subtree
    2. Traverse Right Subtree
    3. Visit Node
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

public class PostorderTraversal {

    public static void postorder(TreeNode root) {
        if (root == null) {
            return;
        }

        postorder(root.left);
        postorder(root.right);
        System.out.print(root.val + " ");
    }

    public static void main(String[] args) {

        TreeNode root = new TreeNode(1);
        root.left = new TreeNode(2);
        root.right = new TreeNode(3);
        root.left.left = new TreeNode(4);
        root.left.right = new TreeNode(5);

        postorder(root);
    }
}
```

### Output

```text
4 5 2 3 1
```

---

# Dry Run

```text
postorder(1)

postorder(2)

postorder(4)
Visit 4

postorder(5)
Visit 5

Visit 2

postorder(3)
Visit 3

Visit 1
```

Output:

```text
4 5 2 3 1
```

---

# Iterative Approach Using Two Stacks

## Intuition

Postorder is difficult because the root is processed last.

Use:

- Stack 1 for traversal
- Stack 2 for reverse processing order

---

## Java Implementation

```java
import java.util.ArrayList;
import java.util.List;
import java.util.Stack;

public class PostorderTraversal {

    public List<Integer> postorderTraversal(TreeNode root) {

        List<Integer> result = new ArrayList<>();

        if (root == null) {
            return result;
        }

        Stack<TreeNode> stack1 = new Stack<>();
        Stack<TreeNode> stack2 = new Stack<>();

        stack1.push(root);

        while (!stack1.isEmpty()) {

            TreeNode node = stack1.pop();
            stack2.push(node);

            if (node.left != null) {
                stack1.push(node.left);
            }

            if (node.right != null) {
                stack1.push(node.right);
            }
        }

        while (!stack2.isEmpty()) {
            result.add(stack2.pop().val);
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
O(n)
```

Two stacks may store all nodes.

---

# Applications of Postorder Traversal

- Tree Deletion
- Expression Tree Evaluation
- Directory Size Calculation
- Bottom-Up Tree Processing

---

# Example: Deleting a Tree

To safely delete a tree:

```text
Delete Left Subtree
Delete Right Subtree
Delete Current Node
```

This exactly follows Postorder Traversal:

```text
Left → Right → Root
```

---

# Key Takeaways

- Postorder follows **Left → Right → Root (LRN)**.
- Root is processed last.
- Recursive solution is straightforward.
- Iterative solution commonly uses two stacks.
- Time Complexity is **O(n)**.
- Widely used when children must be processed before the parent.
