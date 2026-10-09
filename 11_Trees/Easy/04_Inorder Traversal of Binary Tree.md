# Inorder Traversal of Binary Tree

## What is Inorder Traversal?

**Inorder Traversal** is a Depth First Search (DFS) traversal technique in which nodes are visited in the following order:

```text
Left → Root → Right
```

This traversal is commonly denoted as:

```text
LNR
```

Where:

- **L** = Left Subtree
- **N** = Current Node (Root)
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

### Inorder Traversal

```text
4 → 2 → 5 → 1 → 3
```

### Explanation

```text
1. Traverse Left Subtree of 1
2. Traverse Left Subtree of 2
3. Visit 4
4. Visit 2
5. Visit 5
6. Visit 1
7. Visit 3
```

Result:

```text
[4, 2, 5, 1, 3]
```

---

# Recursive Approach

## Algorithm

```text
inorder(node):

    1. Traverse Left Subtree
    2. Visit Current Node
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

public class InorderTraversal {

    public static void inorder(TreeNode root) {
        if (root == null) {
            return;
        }

        inorder(root.left);
        System.out.print(root.val + " ");
        inorder(root.right);
    }

    public static void main(String[] args) {

        TreeNode root = new TreeNode(1);
        root.left = new TreeNode(2);
        root.right = new TreeNode(3);
        root.left.left = new TreeNode(4);
        root.left.right = new TreeNode(5);

        inorder(root);
    }
}
```

### Output

```text
4 2 5 1 3
```

---

# Dry Run

### Tree

```text
        1
       / \
      2   3
     / \
    4   5
```

### Execution Flow

```text
inorder(1)
|
|-- inorder(2)
|    |
|    |-- inorder(4)
|    |    |
|    |    |-- null
|    |    |-- print 4
|    |    |-- null
|    |
|    |-- print 2
|    |
|    |-- inorder(5)
|         |
|         |-- null
|         |-- print 5
|         |-- null
|
|-- print 1
|
|-- inorder(3)
     |
     |-- null
     |-- print 3
     |-- null
```

Output:

```text
4 2 5 1 3
```

---

# Iterative Approach Using Stack

## Intuition

Recursion uses the call stack internally.

We can simulate the same behavior using an explicit stack.

### Steps

```text
1. Go to the leftmost node and push nodes into stack.
2. Pop a node and visit it.
3. Move to its right subtree.
4. Repeat until stack becomes empty.
```

---

## Java Implementation

```java
import java.util.ArrayList;
import java.util.List;
import java.util.Stack;

class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;

    TreeNode(int val) {
        this.val = val;
    }
}

public class InorderTraversal {

    public static List<Integer> inorderTraversal(TreeNode root) {

        List<Integer> result = new ArrayList<>();
        Stack<TreeNode> stack = new Stack<>();

        TreeNode current = root;

        while (current != null || !stack.isEmpty()) {

            while (current != null) {
                stack.push(current);
                current = current.left;
            }

            current = stack.pop();
            result.add(current.val);

            current = current.right;
        }

        return result;
    }
}
```

---

# Dry Run (Iterative)

### Tree

```text
        1
       / \
      2   3
     / \
    4   5
```

### Stack Operations

```text
Push 1
Push 2
Push 4

Pop 4 -> Visit

Pop 2 -> Visit

Push 5
Pop 5 -> Visit

Pop 1 -> Visit

Push 3
Pop 3 -> Visit
```

Result:

```text
[4, 2, 5, 1, 3]
```

---

# Complexity Analysis

## Recursive Solution

### Time Complexity

```text
O(n)
```

Each node is visited exactly once.

### Space Complexity

```text
O(h)
```

Where:

- h = height of tree

Worst Case (Skewed Tree):

```text
O(n)
```

Best Case (Balanced Tree):

```text
O(log n)
```

---

## Iterative Solution

### Time Complexity

```text
O(n)
```

Each node is pushed and popped exactly once.

### Space Complexity

```text
O(h)
```

Where:

- h = height of tree

Worst Case:

```text
O(n)
```

Best Case:

```text
O(log n)
```

---



# Inorder Traversal in Binary Search Tree (BST)

One of the most important properties of Inorder Traversal is:

> Performing Inorder Traversal on a Binary Search Tree (BST) produces nodes in sorted order.

### Example BST

```text
        4
       / \
      2   6
     / \ / \
    1  3 5  7
```

### Inorder Traversal

```text
1 2 3 4 5 6 7
```

Notice that the output is sorted.

---

# Key Takeaways

- Inorder Traversal follows **Left → Root → Right (LNR)**.
- It is a DFS traversal technique.
- Recursive solution is simple and intuitive.
- Iterative solution uses an explicit stack.
- Time Complexity is **O(n)**.
- Space Complexity is **O(h)**.
- In a **Binary Search Tree (BST)**, Inorder Traversal returns elements in **sorted order**.
