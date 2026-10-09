# Bottom View of Binary Tree - Java

 

## Problem Statement

 

Given the root of a binary tree, return the values of the nodes visible from the bottom view, from left to right (leftmost column to rightmost column).

 

If two or more nodes are in the same vertical line, the node at the lowest level (i.e., the last one encountered from top to bottom or visible at the bottom) overrides the previous ones.

 

For every node:

- Root is at column `0`

- Left child is at column `column - 1`

- Right child is at column `column + 1`

 

---

 

## Example

 

### Input

 

```text

        1

       / \

      2   3

     / \   \

    4   5   6

```

 

### Output

 

```text

[[4], [2], [5], [3], [6]]

```

 

### Column Mapping

 

```text

Column -2 : [4]

Column -1 : [2]

Column  0 : [5]

Column +1 : [3]

Column +2 : [6]

```

 

---

 

# Approach

 

## Idea

 

Use Breadth First Search (Level Order Traversal) to track the column number of each node along with its reference.

 

Since BFS processes nodes level-by-level from top to bottom, **updating the map value on every encounter** of a column index ensures that deeper (lower-level) nodes overwrite upper-level nodes. By the time the BFS queue is empty, each vertical column key in the map will hold the value of the deepest node present in that column.

 

We store the results in a map where:

- Key = column number

- Value = latest/deepest node value seen at this column

 

Finally, iterate through the columns from `minColumn` to `maxColumn` to extract the bottom view elements in order.

 

---

 

## Algorithm

 

1. If root is null, return an empty result list.

2. Create a queue to store `(node, column)` pairs.

3. Use a map (`HashMap`) where the key is the column index and the value is the node's value.

4. Track minimum and maximum column numbers to maintain left-to-right order.

5. Perform BFS traversal:

   - Poll the current node and its column.

   - Unconditionally put/overwrite the node's value in the map for this column.

   - Push left child to queue with `column - 1`.

   - Push right child to queue with `column + 1`.

6. Traverse from `minColumn` to `maxColumn` to collect the final output sequence.

7. Return the result.

 

---

 

## Java Solution

 

```java

import java.util.*;

 

public class BottomViewOfBinaryTree {

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

        int column;

        Pair(TreeNode node, int column) {

            this.node = node;

            this.column = column;

        }

    }

 

    public static List<Integer> bottomView(TreeNode root) {

        List<Integer> result = new ArrayList<>();

        if (root == null) {

            return result;

        }

 

        Map<Integer, Integer> map = new HashMap<>();

        Queue<Pair> queue = new LinkedList<>();

        queue.offer(new Pair(root, 0));

 

        int minColumn = 0;

        int maxColumn = 0;

 

        while (!queue.isEmpty()) {

            Pair current = queue.poll();

            TreeNode node = current.node;

            int column = current.column;

 

            // Always overwrite to keep the deepest node for this vertical column

            map.put(column, node.val);

 

            minColumn = Math.min(minColumn, column);

            maxColumn = Math.max(maxColumn, column);

 

            if (node.left != null) {

                queue.offer(new Pair(node.left, column - 1));

            }

            if (node.right != null) {

                queue.offer(new Pair(node.right, column + 1));

            }

        }

 

        for (int column = minColumn; column <= maxColumn; column++) {

            result.add(map.get(column));

        }

 

        return result;

    }

 

    public static void main(String[] args) {

        TreeNode root = new TreeNode(1);

        root.left = new TreeNode(2);

        root.right = new TreeNode(3);

        root.left.left = new TreeNode(4);

        root.left.right = new TreeNode(5);

        root.right.right = new TreeNode(6);

 

        System.out.println(bottomView(root));

    }

}

```

 

---

 

## Dry Run

 

### Tree

 

```text

        1

       / \

      2   3

     / \   \

    4   5   6

```

 

---

 

### Step 1

 

```text

Queue = [(1, 0)]

```

 

Map:

```text

0 -> 1

```

 

---

 

### Step 2

 

Process left child:

```text

(2, -1)

```

 

Process right child:

```text

(3, +1)

```

 

Map:

```text

-1 -> 2

0 -> 1

+1 -> 3

```

 

---

 

### Step 3

 

Process node 4:

```text

Column = -2

```

 

Process node 5:

```text

Column = 0 (Overwrites 1)

```

 

Process node 6:

```text

Column = +2

```

 

Map:

```text

-2 -> 4

-1 -> 2

0 -> 5

+1 -> 3

+2 -> 6

```

 

---

 

### Final Output

 

```text

[4, 2, 5, 3, 6]

```

 

---

 

## Visual Representation

 

```text

          1 (0)

         / \

      2 (-1) 3 (+1)

      / \     \

   4 (-2) 5 (0) 6 (+2)

```

Vertical lines breakdown (showing bottom visibility):

- Column -2 : `4`

- Column -1 : `2`

- Column  0 : `5` (Node `1` at column 0 is hidden by deeper node `5`)

- Column +1 : `3`

- Column +2 : `6`

 

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

[1]

```

 

---

 

### Left Skewed Tree

```text

    1

   /

  2

/

3

```

Output:

```text

[3, 2, 1]

```

 

---

 

### Right Skewed Tree

```text

1

\

  2

   \

    3

```

Output:

```text

[1, 2, 3]

```

 

---

 

## Time Complexity

 

```text

O(N)

```

Each node in the binary tree is visited once via the queue during the BFS traversal.

 

---

 

## Space Complexity

 

```text

O(N)

```

The queue and map together store node references and coordinate values, scaling linearly with the total number of nodes `N`.

 

---

 

## Interview Follow-Up Questions

 

### Q1. Why does unconditional map overwriting work for the Bottom View?

Because BFS explores the tree level by level from top to bottom. As we go deeper, we encounter nodes later in time that belong to the same column. Overwriting the map entry ensures that the final value retained is the one closest to the bottom.

 

---

 

### Q2. How can you implement this using Tree-based or DFS approaches?

While BFS is cleaner and iterative, you can use DFS if you also track the *level* (depth) of each node. If you encounter a node at the same column with a level greater than or equal to the recorded level, you update the map. However, DFS requires extra height-tracking logic and extra sorting or min/max tracking.

 

---

 

## Important Interview Takeaways

 

- ✅ Assign horizontal column coordinates relative to the root (`0`).

- ✅ Use BFS to process top-to-bottom so deeper nodes naturally replace upper nodes.

- ✅ Unconditionally update map values on every column occurrence (`map.put(column, node.val)`).

- ✅ Time Complexity = O(N)

- ✅ Space Complexity = O(N)

- ✅ Frequently Asked In:

  - Amazon

  - Microsoft

  - Walmart

  - Adobe

- ✅ Related Problems:

  - Top View of Binary Tree

  - Vertical Order Traversal of Binary Tree

  - Left/Right View of Binary Tree

 

  

  

  
