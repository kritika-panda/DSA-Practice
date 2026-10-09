# Zig-Zag (Spiral) Order Traversal of Binary Tree - Java

 

## Problem Statement

 

Given the root of a binary tree, return its zig-zag level order traversal.

 

Nodes belonging to the same level should be grouped together, but alternate level sequences from left to right, then right to left for the next level, and keep alternating:

- Level 0 (Root): Left to Right

- Level 1: Right to Left

- Level 2: Left to Right

- ... and so on.

 

---

 

## Example

 

### Input

 

```text

        3

       / \

      9   20

         /  \

        15   7

```

 

### Output

 

```text

[[3], [20, 9], [15, 7]]

```

 

### Level Mapping

 

```text

Level 0 (L -> R) : [3]

Level 1 (R -> L) : [20, 9]

Level 2 (L -> R) : [15, 7]

```

 

---

 

# Approach

 

## Idea

 

Use Breadth First Search (Level Order Traversal) using a normal `Queue` to traverse level by level.

 

To handle the alternating spiral directions efficiently without reversing lists later:

- Track the current level index or a boolean flag `leftToRight`.

- When insertion happens at each level, use a **LinkedList** or **Deque** as a temporary container.

- If traversing **Left to Right**, add elements to the **end** of the list (`addLast`).

- If traversing **Right to Left**, add elements to the **front** of the list (`addFirst`).

 

---

 

## Algorithm

 

1. If root is null, return an empty result.

2. Create a queue to store nodes for classic level order traversal. Push the root node.

3. Maintain a boolean flag `leftToRight = true`.

4. While the queue is not empty:

   - Determine the size of the current level (`levelSize = queue.size()`).

   - Create a sub-list (using `LinkedList`) to store the current level's nodes.

   - Loop `levelSize` times to process all nodes at the current level.

   - Dequeue the front node.

   - If `leftToRight` is true, add the node value to the end of the sub-list. Otherwise, add it to the front.

   - Enqueue the left and right children if they exist.

5. Invert the `leftToRight` flag (`leftToRight = !leftToRight`) after finishing the level loop.

6. Append the sub-list to the final result matrix.

7. Return the final collection.

 

---

 

## Java Solution

 

```java

import java.util.*;

 

public class ZigZagTraversal {

    static class TreeNode {

        int val;

        TreeNode left;

        TreeNode right;

        TreeNode(int val) {

            this.val = val;

        }

    }

 

    public static List<List<Integer>> zigzagLevelOrder(TreeNode root) {

        List<List<Integer>> result = new ArrayList<>();

        if (root == null) {

            return result;

        }

 

        Queue<TreeNode> queue = new LinkedList<>();

        queue.offer(root);

        boolean leftToRight = true;

 

        while (!queue.isEmpty()) {

            int levelSize = queue.size();

            // Use LinkedList to leverage efficient addFirst() operations

            LinkedList<Integer> currentLevel = new LinkedList<>();

 

            for (int i = 0; i < levelSize; i++) {

                TreeNode node = queue.poll();

 

                if (leftToRight) {

                    currentLevel.addLast(node.val); // Normal Order

                } else {

                    currentLevel.addFirst(node.val); // Reversed Order

                }

 

                if (node.left != null) {

                    queue.offer(node.left);

                }

                if (node.right != null) {

                    queue.offer(node.right);

                }

            }

 

            result.add(currentLevel);

            leftToRight = !leftToRight; // Alternate direction for the next level

        }

 

        return result;

    }

 

    public static void main(String[] args) {

        TreeNode root = new TreeNode(3);

        root.left = new TreeNode(9);

        root.right = new TreeNode(20);

        root.right.left = new TreeNode(15);

        root.right.right = new TreeNode(7);

        System.out.println(zigzagLevelOrder(root));

    }

}

```

 

---

 

## Dry Run

 

### Tree

 

```text

        3

       / \

      9   20

         /  \

        15   7

```

 

---

 

### Level 0

```text

Queue =, leftToRight = true

levelSize = 1

Pop 3. Since leftToRight is true -> addLast(3).

Queue pushes children: [9, 20]

```

Current Level List:

```text

[3]

```

Flip flag: `leftToRight = false`

 

---

 

### Level 1

```text

Queue =, leftToRight = false

levelSize = 2

 

- Pop 9. Since leftToRight is false -> addFirst(9).

  Queue pushes children: [20] (No children for 9)

- Pop 20. Since leftToRight is false -> addFirst(20).

  Queue pushes children: [15, 7]

```

Current Level List:

```text

[20, 9]

```

Flip flag: `leftToRight = true`

 

---

 

### Level 2

```text

Queue =, leftToRight = true

levelSize = 2

 

- Pop 15. Since leftToRight is true -> addLast(15).

- Pop 7. Since leftToRight is true -> addLast(7).

```

Current Level List:

```text

[15, 7]

```

Flip flag: `leftToRight = false`

 

---

 

### Final Output

 

```text

[[3], [20, 9], [15, 7]]

```

 

---

 

## Visual Representation

 

```text

          (L -> R)        3  -------->  [3]

                         / \

          (R <- L)      9   20 <------  [20, 9]

                           /  \

          (L -> R)        15   7 ---->  [15, 7]

```

 

---

 

## Edge Cases

 

### Empty Tree

 

```text

Input: null

Output: []

```

 

---

 

### Single Node

 

```text

    1

```

 

Output:

```text

[[1]]

```

 

---

 

### Left Skewed Tree

 

```text

    1      (L->R)

   /

  2        (R->L)

/

3          (L->R)

```

 

Output:

```text

[[1], [2], [3]]

```

 

---

 

## Time Complexity

 

```text

O(N)

```

Each node is visited exactly once. Adding elements to the front or back of a `LinkedList` takes O(1) constant time.

 

---

 

## Space Complexity

 

```text

O(N)

```

The queue stores up to the maximum width of the tree (leaf level nodes), which is at most \(\lceil N/2 \rceil\) nodes in a complete binary tree.

 

---

 

## Interview Follow-Up Questions

 

### Q1. Can this be implemented using Two Stacks?

Yes. Instead of tracking a boolean direction flag, you can use two stacks (`s1` for Left-to-Right and `s2` for Right-to-Left). Pushing nodes onto alternate stacks naturally reverses their order when popped.

 

---

 

### Q2. Why is LinkedList preferred over ArrayList for the sub-lists?

`ArrayList.add(0, value)` takes O(K) time due to shifting elements rightward. `LinkedList.addFirst(value)` operates in O(1) constant time, keeping the overall level runtime highly efficient.

 

---

 

## Important Interview Takeaways

 

- ✅ Use a standard Level-Order BFS structure.

- ✅ Maintain a toggle flag (`leftToRight`) to dictate row insertion endpoints.

- ✅ Use `addFirst()` for right-to-left levels and `addLast()` for left-to-right levels.

- ✅ Time Complexity = O(N)

- ✅ Space Complexity = O(N)

- ✅ Frequently Asked In:

  - Amazon

  - Facebook (Meta)

  - Microsoft

  - LinkedIn

- ✅ Related Problems:

  - Binary Tree Level Order Traversal

  - Binary Tree Right Side View

  - Populating Next Right Pointers in Each Node

 

