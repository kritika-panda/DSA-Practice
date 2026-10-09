  # Top View of Binary Tree - Java

 

## Problem Statement

 

Given the root of a binary tree, return the values of the nodes visible from the top view, from left to right (leftmost column to rightmost column).

 

If two or more nodes are in the same vertical line, the node closest to the root (i.e., the first one encountered from the top) is visible, and the lower nodes are hidden.

 

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

[4, 2, 1, 3, 6]

```

 

### Column Mapping

 

```text

Column -2 : [4]

Column -1 : [2]

Column  0 : [1]

Column +1 : [3]

Column +2 : [6]

```

 

---

 

# Approach

 

## Idea

 

Use Breadth First Search (Level Order Traversal) to track the column number of each node along with its reference.

 

Since BFS processes nodes level-by-level from top to bottom, the **first time** we see a node at a specific column index, it is guaranteed to be the topmost visible node for that column. Any subsequent nodes arriving at the same column index are located deeper down and can be safely ignored.

 

We store the results in a map where:

- Key = column number

- Value = first node value seen at this column

 

Finally, iterate through the columns from `minColumn` to `maxColumn` to extract the top view elements in order.

 

---

 

## Algorithm

 

1. If root is null, return an empty result list.

2. Create a queue to store `(node, column)` pairs.

3. Use a map (or `HashMap` with `minColumn`/`maxColumn` trackers) where the key is the column index and the value is the node's value.

4. Track minimum and maximum column numbers to maintain left-to-right order.

5. Perform BFS traversal:

   - Poll the current node and its column.

   - If the column is not already present in the map, add the node's value to the map.

   - Push left child to queue with `column - 1`.

   - Push right child to queue with `column + 1`.

6. Traverse from `minColumn` to `maxColumn` to collect the final output sequence.

7. Return the result.

 

---

 

## Java Solution

 

```java

import java.util.*;

 

public class TopViewOfBinaryTree {

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

 

    public static List<Integer> topView(TreeNode root) {

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

 

            // Only insert if this vertical column doesn't have a top node yet

            if (!map.containsKey(column)) {

                map.put(column, node.val);

            }

 

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

 

        System.out.println(topView(root));

    }

}

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

Vertical lines breakdown:

- Column -2 : `4`

- Column -1 : `2`

- Column  0 : `1` (Node `5` at column 0 is hidden beneath `1`)

- Column +1 : `3`

- Column +2 : `6`

 

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

The queue and map together store node references and coordinate values, which scale with the total number of nodes `N` (or tree width/height boundaries).

 

---

 

## Interview Follow-Up Questions

 

### Q1. Why can't we use DFS for the Top View?

DFS does not process nodes strictly level-by-level from top to bottom. A lower-level node visited first via a deep left branch might incorrectly overwrite a higher-level node's vertical claim. BFS guarantees the correct top-to-bottom sequence.

 

---

 

### Q2. How does Bottom View differ from Top View?

For the **Bottom View**, you update the map value on *every* encounter of a column index instead of checking `!map.containsKey(column)`. The final nodes processed overwrite previous ones, keeping only the deepest nodes.

 

---

 

## Important Interview Takeaways

 

- ✅ Assign horizontal distance/column indexes relative to the root (`0`).

- ✅ Use BFS to ensure the first node seen at any column is the topmost element.

- ✅ Gate insertions using `map.containsKey(column)` so upper nodes are never overwritten.

- ✅ Time Complexity = O(N)

- ✅ Space Complexity = O(N)

- ✅ Frequently Asked In:

  - Amazon

  - Microsoft

  - Flipkart

  - Samsung

- ✅ Related Problems:

  - Bottom View of Binary Tree

  - Vertical Order Traversal of Binary Tree

  - Left/Right View of Binary Tree
