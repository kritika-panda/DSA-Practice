# Vertical Order Traversal of Binary Tree - Java

 

## Problem Statement

 

Given the root of a binary tree, return its vertical order traversal.

 

For every node:

- Root is at column `0`

- Left child is at column `column - 1`

- Right child is at column `column + 1`

 

Nodes belonging to the same vertical column should be grouped together.

 

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

[[9], [3, 15], [20], [7]]

```

 

### Column Mapping

 

```text

Column -1 : [9]

Column  0 : [3, 15]

Column +1 : [20]

Column +2 : [7]

```

 

---

 

# Approach

 

## Idea

 

Use Breadth First Search (Level Order Traversal) and track the column number of each node.

 

For each node:

- Left child → column - 1

- Right child → column + 1

 

Store nodes belonging to the same column in a map.

Finally, iterate through columns from leftmost to rightmost.

 

---

 

## Algorithm

 

1. If root is null, return empty result.

2. Create a queue to store `(node, column)` pairs.

3. Store nodes in a map where:

   - Key = column number

   - Value = list of nodes in that column

4. Track minimum and maximum column numbers.

5. Perform BFS traversal.

6. Add node values to their corresponding column.

7. Traverse from minColumn to maxColumn.

8. Return result.

 

---

 

## Java Solution

 

```java

import java.util.*;

 

public class VerticalOrderTraversal {

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

 

    public static List<List<Integer>> verticalOrder(TreeNode root) {

        List<List<Integer>> result = new ArrayList<>();

        if (root == null) {

            return result;

        }

 

        Map<Integer, List<Integer>> map = new HashMap<>();

        Queue<Pair> queue = new LinkedList<>();

        queue.offer(new Pair(root, 0));

        int minColumn = 0;

        int maxColumn = 0;

 

        while (!queue.isEmpty()) {

            Pair current = queue.poll();

            TreeNode node = current.node;

            int column = current.column;

 

            map.computeIfAbsent(column, k -> new ArrayList<>())

               .add(node.val);

 

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

        TreeNode root = new TreeNode(3);

        root.left = new TreeNode(9);

        root.right = new TreeNode(20);

        root.right.left = new TreeNode(15);

        root.right.right = new TreeNode(7);

        System.out.println(verticalOrder(root));

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

 

### Step 1

 

```text

Queue = [(3, 0)]

```

 

Map:

```text

0 -> [3]

```

 

---

 

### Step 2

 

Process left child:

```text

(9, -1)

```

 

Process right child:

```text

(20, +1)

```

 

Map:

```text

-1 -> [9]

0 -> [3]

+1 -> [20]

```

 

---

 

### Step 3

 

Process node 15

```text

Column = 0

```

 

Process node 7

```text

Column = 2

```

 

Map:

```text

-1 -> [9]

0 -> [3, 15]

+1 -> [20]

+2 -> [7]

```

 

---

 

### Final Output

 

```text

[[9], [3, 15], [20], [7]]

```

 

---

 

## Visual Representation

 

```text

            3(0)

           /    \

      9(-1)    20(+1)

               /   \

          15(0)   7(+2)

```

 

Vertical Lines:

```text

Column -1 : 9

Column  0 : 3, 15

Column +1 : 20

Column +2 : 7

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

    1

   /

  2

/

3

```

 

Output:

```text

[[3], [2], [1]]

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

[[1], [2], [3]]

```

 

---

 

## Time Complexity

 

```text

O(N)

```

Each node is visited exactly once.

 

---

 

## Space Complexity

 

```text

O(N)

```

Map and Queue together may store all nodes.

 

---

 

## Interview Follow-Up Questions

 

### Q1. Why use BFS instead of DFS?

BFS naturally preserves top-to-bottom order within the same column.

 

---

 

### Q2. What is the difference between Vertical Order Traversal and Vertical Traversal?

 

**Vertical Order Traversal**

```text

Nodes are grouped only by columns.

BFS order is maintained.

```

 

**Vertical Traversal (LeetCode 987)**

```text

Sort by:

1. Column

2. Row

3. Node Value

```

Additional sorting is required.

 

---

 

### Q3. Why track minimum and maximum columns?

To avoid sorting map keys later and directly construct the answer from leftmost to rightmost column.

 

---

 

## Important Interview Takeaways

 

- ✅ Assign column numbers to every node

- ✅ Left Child → column - 1

- ✅ Right Child → column + 1

- ✅ Use BFS to preserve level order

- ✅ Store nodes column-wise using HashMap

- ✅ Time Complexity = O(N)

- ✅ Space Complexity = O(N)

- ✅ Frequently Asked In:

  - Amazon

  - Microsoft

  - Google

  - Adobe

  - Walmart

  - Flipkart

- ✅ Related Problems:

  - Top View of Binary Tree

  - Bottom View of Binary Tree

  - Vertical Traversal of Binary Tree

  - Diagonal Traversal of Binary Tree

 
