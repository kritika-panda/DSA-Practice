 # Maximum Width of Binary Tree - Java

 

## Problem Statement

 

Given the root of a binary tree, return the **maximum width** of the given tree.

 

The maximum width of a tree is the maximum width among all levels. The width of one level is defined as the length between the end-nodes (the leftmost and rightmost non-null nodes), where the null nodes between the end-nodes that would be present in a complete binary tree extending down to that level are also counted into the length calculation.

 

---

 

## Example

 

### Input

 

```text

        1

       / \

      3   2

     /     \

    5       9

   /         \

  6           7

```

 

### Output

 

```text

8

```

 

### Explanation

 

The maximum width exists at Level 3 (the bottom-most level).

- Leftmost node at Level 3 is `6`.

- Rightmost node at Level 3 is `7`.

- The nodes present if it were a complete binary tree would be: `[6, null, null, null, null, null, null, 7]`.

- Total width = `8`.

 

---

 

# Approach

 

## Idea

 

We use **Breadth First Search (BFS)** / Level Order Traversal to find the width of each level and keep track of the maximum width found.

 

To calculate the length between the leftmost and rightmost nodes of a level including the intermediate null gaps, we can use a **heap-based indexing system** similar to how binary trees are indexed in an array layout:

- If a parent node has an index of `i`:

  - Its left child will have an index of `2 * i`.

  - Its right child will have an index of `2 * i + 1`.

 

For any given level, the width is calculated using the formula:

\[\text{Width} = \text{Rightmost Index} - \text{Leftmost Index} + 1\]

 

### Index Overflow Prevention

To prevent integer overflow in highly skewed or deep trees, we can normalize indices at the start of each level. By subtracting the leftmost node's index from all node indices at that level, the level's index mapping resets to start at `0`.

 

---

 

## Algorithm

 

1. If the root is null, return `0`.

2. Initialize a queue to store `(node, index)` pairs. Push the root node with an index of `0`.

3. Initialize `maxWidth = 0`.

4. While the queue is not empty:

   - Find the size of the current level (`levelSize = queue.size()`).

   - Peak the first element in the queue to find the baseline index of this level (`levelMinIndex`).

   - Initialize trackers `firstIndex = 0` and `lastIndex = 0`.

   - Loop `levelSize` times to process all elements at the current level:

     - Dequeue the front `Pair`.

     - Normalize the index: `normalizedIndex = current.index - levelMinIndex`.

     - If it's the first node of the loop, set `firstIndex = normalizedIndex`.

     - If it's the last node of the loop, set `lastIndex = normalizedIndex`.

     - If a left child exists, enqueue it with index `2 * normalizedIndex`.

     - If a right child exists, enqueue it with index `2 * normalizedIndex + 1`.

   - Update `maxWidth = Math.max(maxWidth, lastIndex - firstIndex + 1)`.

5. Return `maxWidth`.

 

---

 

## Java Solution

 

```java

import java.util.*;

 

public class MaximumWidthOfBinaryTree {

    static class TreeNode {

        int val;

        TreeNode left;

        TreeNode right;

        TreeNode(int val) {

            this.val = val;

        }

    }

 

    static class Pair {

        TreeNode node;

        int index;

        Pair(TreeNode node, int index) {

            this.node = node;

            this.index = index;

        }

    }

 

    public static int widthOfBinaryTree(TreeNode root) {

        if (root == null) {

            return 0;

        }

 

        int maxWidth = 0;

        Queue<Pair> queue = new LinkedList<>();

        // Enqueue root with an initial index of 0

        queue.offer(new Pair(root, 0));

 

        while (!queue.isEmpty()) {

            int levelSize = queue.size();

            int levelMinIndex = queue.peek().index; // Minimum index at this level

            int firstIndex = 0, lastIndex = 0;

 

            for (int i = 0; i < levelSize; i++) {

                Pair current = queue.poll();

                TreeNode node = current.node;

                // Normalize index to prevent integer overflow

                int normalizedIndex = current.index - levelMinIndex;

 

                if (i == 0) {

                    firstIndex = normalizedIndex;

                }

                if (i == levelSize - 1) {

                    lastIndex = normalizedIndex;

                }

 

                if (node.left != null) {

                    queue.offer(new Pair(node.left, 2 * normalizedIndex));

                }

                if (node.right != null) {

                    queue.offer(new Pair(node.right, 2 * normalizedIndex + 1));

                }

            }

            // Calculate width and update maxWidth

            maxWidth = Math.max(maxWidth, lastIndex - firstIndex + 1);

        }

 

        return maxWidth;

    }

 

    public static void main(String[] args) {

        TreeNode root = new TreeNode(1);

        root.left = new TreeNode(3);

        root.right = new TreeNode(2);

        root.left.left = new TreeNode(5);

        root.right.right = new TreeNode(9);

        root.left.left.left = new TreeNode(6);

        root.right.right.right = new TreeNode(7);

 

        System.out.println("Maximum Width: " + widthOfBinaryTree(root));

    }

}

```

 

---

 

## Dry Run

 

Using the example tree:

 

### Level 0

- Queue = `[(1, index: 0)]`

- `levelMinIndex = 0`

- Dequeue `1`: `normalizedIndex = 0 - 0 = 0`.

- `firstIndex = 0`, `lastIndex = 0`.

- Enqueue left child `3` with `2 * 0 = 0`. Enqueue right child `2` with `2 * 0 + 1 = 1`.

- `Width = 0 - 0 + 1 = 1`. `maxWidth = 1`.

 

### Level 1

- Queue = `[(3, index: 0), (2, index: 1)]`

- `levelMinIndex = 0`

- Dequeue `3`: `normalizedIndex = 0 - 0 = 0`. `firstIndex = 0`. Enqueue left child `5` with index `0`.

- Dequeue `2`: `normalizedIndex = 1 - 0 = 1`. `lastIndex = 1`. Enqueue right child `9` with index `2 * 1 + 1 = 3`.

- `Width = 1 - 0 + 1 = 2`. `maxWidth = 2`.

 

### Level 2

- Queue = `[(5, index: 0), (9, index: 3)]`

- `levelMinIndex = 0`

- Dequeue `5`: `normalizedIndex = 0 - 0 = 0`. `firstIndex = 0`. Enqueue left child `6` with index `0`.

- Dequeue `9`: `normalizedIndex = 3 - 0 = 3`. `lastIndex = 3`. Enqueue right child `7` with index `2 * 3 + 1 = 7`.

- `Width = 3 - 0 + 1 = 4`. `maxWidth = 4`.

 

### Level 3

- Queue = `[(6, index: 0), (7, index: 7)]`

- `levelMinIndex = 0`

- Dequeue `6`: `normalizedIndex = 0`. `firstIndex = 0`.

- Dequeue `7`: `normalizedIndex = 7`. `lastIndex = 7`.

- `Width = 7 - 0 + 1 = 8`. `maxWidth = 8`.

 

---

 

## Visual Representation

 

```text

                  1 (idx:0)                       -> Width = 1

                 /         \

          3 (idx:0)       2 (idx:1)               -> Width = 2

           /                 \

    5 (idx:0)               9 (idx:3)             -> Width = 4

     /                         \

6 (idx:0)                     7 (idx:7)           -> Width = 8

```

 

---

 

## Edge Cases

 

### Empty Tree

```text

Input: null

Output: 0

```

 

### Single Node

```text

Input: 1

Output: 1

```

 

### Skewed Tree (Left Skewed)

```text

    1

   /

  2

/

3

```

Output:

```text

1

```

*(Every level has only one node, so width at each level is 1)*

 

---

 

## Time Complexity

 

```text

O(N)

```

Where `N` is the total number of nodes in the binary tree. We visit each node exactly once during our level-order traversal.

 

---

 

## Space Complexity

 

```text

O(W)

```

Where `W` is the maximum width of the tree. The space is consumed by the queue holding nodes at any given level. In the worst-case scenario (a full binary tree), `W` is approximately equal to `N / 2`, which gives `O(N)`.

 

---

 

## Interview Follow-Up Questions

 

### Q1. Why is index normalization necessary?

If we do not normalize indices, a highly skewed tree (e.g., a tree with 64 consecutive right children) will cause the child index calculation `2 * i + 1` to double at every level. This will quickly exceed the maximum capacity of a 32-bit integer (`Integer.MAX_VALUE`), causing integer overflow. Normalizing the index keeps values relative and close to zero.

 

### Q2. Can this problem be solved using Depth First Search (DFS)?

Yes. You can traverse the tree using DFS while tracking the `level` and the node's `index`. You pass a list tracking the leftmost index recorded for each level. If it's the first time visiting a level, you store its index. For subsequent nodes at that level, you calculate the width using the current index and the stored leftmost index, updating the global maximum.

 

---

 

## Important Interview Takeaways

 

- ✅ Use heap-like indexing: Left child = `2 * i`, Right child = `2 * i + 1`.

- ✅ Always use **BFS (Level Order Traversal)** to calculate widths line by line.

- ✅ Normalize level indexes (`index - levelMinIndex`) to explicitly avoid integer overflow conditions.

- ✅ Time Complexity = O(N)

- ✅ Space Complexity = O(W)

- ✅ Frequently Asked In:

  - Amazon

  - Google

  - Microsoft

  - Bloomberg

- ✅ Related Problems:

  - Binary Tree Level Order Traversal

  - Minimum Depth of Binary Tree

  - Maximum Depth of Binary Tree
