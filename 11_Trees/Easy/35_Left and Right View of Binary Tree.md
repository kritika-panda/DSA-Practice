# Left and Right View of Binary Tree - Java

 

## Problem Statement

 

Given the root of a binary tree, return:

1. The **Left View**: The values of the nodes visible when the tree is visited from the left side (first node of each level).

2. The **Right View**: The values of the nodes visible when the tree is visited from the right side (last node of each level).

 

The results for each view should be returned as a list of integers from top to bottom (root to deepest level).

 

---

 

## Example

 

### Input

 

```text

        1

       / \

      2   3

     / \   \

    4   5   6

       /

      7

```

 

### Left View Output

```text

[1, 2, 4, 7]

```

 

### Right View Output

```text

[1, 3, 6, 7]

```

 

### Level Mapping

 

```text

Level 0 : Left =, Right = [1]

Level 1 : Left =, Right = [3]

Level 2 : Left =, Right = [6]

Level 3 : Left =, Right = [7]

```

 

---

 

# Approach

 

## Idea

 

While these problems can be solved iteratively using Breadth First Search (BFS) level-order traversal, the most **elegant and optimal** approach in an interview setting uses a modified **Depth First Search (DFS)**.

 

We traverse the tree recursively while tracking the `currentLevel` (starting at 0). We also maintain the size of our result list to know if we are visiting a level for the first time:

- **For Left View**: We traverse **Root → Left → Right**. The first time we reach a new level, the current node is the leftmost node of that level.

- **For Right View**: We traverse **Root → Right → Left**. The first time we reach a new level, the current node is the rightmost node of that level.

 

A level is encountered for the first time if `currentLevel == result.size()`.

 

---

 

## Algorithm (Right View DFS Example)

 

1. If the root is null, return an empty list.

2. Initialize an empty list `result`.

3. Call the recursive helper function `rightViewDFS(root, 0, result)`.

4. In the helper function:

   - If the current node is null, return.

   - If `currentLevel == result.size()`, this is the first time we are visiting this level from the right side. Add `node.val` to `result`.

   - Recursively call the helper on the **Right child** first: `rightViewDFS(node.right, currentLevel + 1, result)`.

   - Recursively call the helper on the **Left child** second: `rightViewDFS(node.left, currentLevel + 1, result)`.

5. Return the `result` list.

 

---

 

## Java Solution

 

```java

import java.util.*;

 

public class BinaryTreeViews {

    static class TreeNode {

        int val;

        TreeNode left;

        TreeNode right;

        TreeNode(int val) {

            this.val = val;

        }

    }

 

    // === RIGHT VIEW IMPLEMENTATION ===

    public static List<Integer> rightSideView(TreeNode root) {

        List<Integer> result = new ArrayList<>();

        rightViewDFS(root, 0, result);

        return result;

    }

 

    private static void rightViewDFS(TreeNode node, int level, List<Integer> result) {

        if (node == null) {

            return;

        }

 

        // If this is the first time visiting this level from the right

        if (level == result.size()) {

            result.add(node.val);

        }

 

        // Traverse Right first, then Left

        rightViewDFS(node.right, level + 1, result);

        rightViewDFS(node.left, level + 1, result);

    }

 

    // === LEFT VIEW IMPLEMENTATION ===

    public static List<Integer> leftSideView(TreeNode root) {

        List<Integer> result = new ArrayList<>();

        leftViewDFS(root, 0, result);

        return result;

    }

 

    private static void leftViewDFS(TreeNode node, int level, List<Integer> result) {

        if (node == null) {

            return;

        }

 

        // If this is the first time visiting this level from the left

        if (level == result.size()) {

            result.add(node.val);

        }

 

        // Traverse Left first, then Right

        leftViewDFS(node.left, level + 1, result);

        leftViewDFS(node.right, level + 1, result);

    }

 

    public static void main(String[] args) {

        TreeNode root = new TreeNode(1);

        root.left = new TreeNode(2);

        root.right = new TreeNode(3);

        root.left.left = new TreeNode(4);

        root.left.right = new TreeNode(5);

        root.right.right = new TreeNode(6);

        root.left.right.left = new TreeNode(7);

 

        System.out.println("Right View: " + rightSideView(root));

        System.out.println("Left View:  " + leftSideView(root));

    }

}

```

 

---

 

## Dry Run (Right View DFS)

 

### Tree

```text

        1

       / \

      2   3

     / \   \

    4   5   6

       /

      7

```

 

---

 

### Step 1: Node 1 (Level 0)

- `level = 0`, `result.size() = 0` (Match!)

- `result = [1]`

- Moves to right child (Node 3).

 

---

 

### Step 2: Node 3 (Level 1)

- `level = 1`, `result.size() = 1` (Match!)

- `result = [1, 3]`

- Moves to right child (Node 6).

 

---

 

### Step 3: Node 6 (Level 2)

- `level = 2`, `result.size() = 2` (Match!)

- `result = [1, 3, 6]`

- Node 6 has no children. Unwinds back to Node 1, which now explores its left path (Node 2).

 

---

 

### Step 4: Node 2 (Level 1)

- `level = 1`, `result.size() = 3` (No match!)

- Skip adding node value.

- Moves to right child (Node 5).

 

---

 

### Step 5: Node 5 (Level 2)

- `level = 2`, `result.size() = 3` (No match!)

- Skip adding node value.

- Moves to left child (Node 7).

 

---

 

### Step 6: Node 7 (Level 3)

- `level = 3`, `result.size() = 3` (Match!)

- `result = [1, 3, 6, 7]`

 

---

 

### Final Output (Right View)

```text

[1, 3, 6, 7]

```

 

---

 

## Visual Representation

 

```text

            1   <--- Visible from both Left and Right View

           / \

          2   3 <--- 2 visible from Left, 3 visible from Right

         / \   \

        4   5   6 <-- 4 visible from Left, 6 visible from Right

           /

          7     <--- 7 visible from both Left and Right View

```

 

---

 

## Edge Cases

 

### Empty Tree

```text

Input: null

Output: []

```

 

### Single Node

```text

    1

```

Output:

```text

[1]

```

 

### Skewed Tree (Right Side Only)

```text

1

\

  2

   \

    3

```

Output (Both Left and Right views are identical here):

```text

[1, 2, 3]

```

 

---

 

## Time Complexity

 

```text

O(N)

```

Every single node in the binary tree is visited exactly once during the DFS traversal.

 

---

 

## Space Complexity

 

```text

O(H)

```

Where `H` is the height of the tree. The space is consumed by the recursive call stack. In the worst-case scenario (a highly skewed tree), the space complexity scales to `O(N)`.

 

---

 

## Interview Follow-Up Questions

 

### Q1. Can this be solved using an iterative BFS approach?

Yes. Perform standard level-order traversal using a queue. For the **Left View**, add the first element of each level loop (`i == 0`) to the result. For the **Right View**, add the last element of each level loop (`i == levelSize - 1`) to the result.

 

### Q2. Why is DFS often preferred over BFS for this problem by interviewers?

DFS uses `O(H)` space on the call stack, which is highly efficient for balanced trees (\(\log N\)). BFS requires a queue that stores an entire level, costing `O(W)` where `W` is the max-width of the tree (≈ N/2 for a full binary tree). DFS also avoids queue object allocation overhead.

 

---

 

## Important Interview Takeaways

 

- ✅ Track the `currentLevel` during tree traversal.

- ✅ Conditional gate check: `level == result.size()` identifies a new level's threshold.

- ✅ Left View path priority: **Root → Left → Right**

- ✅ Right View path priority: **Root → Right → Left**

- ✅ Time Complexity = O(N)

- ✅ Space Complexity = O(H)

- ✅ Frequently Asked In:

  - Amazon

  - Microsoft

  - Facebook (Meta)

  - Bloomberg

- ✅ Related Problems:

  - Top View of Binary Tree

  - Bottom View of Binary Tree

  - Level Order Traversal of Binary Tree
