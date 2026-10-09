 # Print Root to Leaf Paths in a Binary Tree - Java

 

## Problem Statement

 

Given the root of a binary tree, return all root-to-leaf paths in any order.

 

A leaf is a node with no children. Each path should be represented as a string or list showing the traversal sequence from the root down to the leaf node.

 

---

 

## Example

 

### Input

 

```text

        1

       / \

      2   3

       \

        5

```

 

### Output

 

```text

["1->2->5", "1->3"]

```

 

### Path Breakdown

 

```text

Path 1 : 1 -> 2 -> 5  (Ends at leaf node 5)

Path 2 : 1 -> 3       (Ends at leaf node 3)

```

 

---

 

# Approach

 

## Idea

 

We use **Depth First Search (DFS)** via recursion to explore paths from the root down to the leaf nodes.

 

As we descend the tree, we maintain a running list or string representing the current path sequence.

- At each node, we append its value to our current path.

- If we hit a **leaf node** (`left == null && right == null`), we have successfully mapped a complete path. We format the path and add it to our global result collection.

- If the node is not a leaf, we recursively continue down its available left and right child paths.

- **Backtracking**: When returning from a recursive call, we remove the current node from our running path list to clean up the state for alternative branching paths.

 

---

 

## Algorithm

 

1. If the root is null, return an empty list.

2. Initialize a global list `result` to hold all final path strings.

3. Initialize a dynamic list `currentPath` to act as a stack tracking our active traversal route.

4. Call the recursive helper function `findPaths(root, currentPath, result)`.

5. In the helper function:

   - If the node is null, return.

   - Add the current node's value to `currentPath`.

   - **Leaf Check**: If `node.left == null` and `node.right == null`, format the elements inside `currentPath` into a string separated by `->`, and add it to `result`.

   - If not a leaf, recursively call the helper on `node.left` and `node.right`.

   - **Backtrack**: Remove the last element added to `currentPath` before exiting the function call.

6. Return the `result` list.

 

---

 

## Java Solution

 

```java

import java.util.*;

 

public class RootToLeafPaths {

    static class TreeNode {

        int val;

        TreeNode left;

        TreeNode right;

        TreeNode(int val) {

            this.val = val;

        }

    }

 

    public static List<String> binaryTreePaths(TreeNode root) {

        List<String> result = new ArrayList<>();

        if (root == null) {

            return result;

        }

       

        List<String> currentPath = new ArrayList<>();

        findPaths(root, currentPath, result);

        return result;

    }

 

    private static void findPaths(TreeNode node, List<String> currentPath, List<String> result) {

        if (node == null) {

            return;

        }

 

        // Add the current node to the tracking path

        currentPath.add(String.valueOf(node.val));

 

        // If it's a leaf node, construct the path string and save it

        if (node.left == null && node.right == null) {

            result.add(String.join("->", currentPath));

        } else {

            // Otherwise, continue down the tree branches

            if (node.left != null) {

                findPaths(node.left, currentPath, result);

            }

            if (node.right != null) {

                findPaths(node.right, currentPath, result);

            }

        }

 

        // Backtrack: Remove the current node before moving up the recursion tree

        currentPath.remove(currentPath.size() - 1);

    }

 

    public static void main(String[] args) {

        TreeNode root = new TreeNode(1);

        root.left = new TreeNode(2);

        root.right = new TreeNode(3);

        root.left.right = new TreeNode(5);

 

        System.out.println(binaryTreePaths(root));

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

       \

        5

```

 

---

 

### Step 1: Node 1

- `currentPath = ["1"]`

- Not a leaf node. Invokes left child (Node 2).

 

---

 

### Step 2: Node 2

- `currentPath = ["1", "2"]`

- Not a leaf node. Left child is null. Invokes right child (Node 5).

 

---

 

### Step 3: Node 5

- `currentPath = ["1", "2", "5"]`

- **Leaf node detected!** (`left == null && right == null`)

- Formats path: `"1->2->5"`

- `result = ["1->2->5"]`

- Backtracks: Pops `"5"`. `currentPath` reverts to `["1", "2"]`.

 

---

 

### Step 4: Return to Node 2

- Finished exploring children of Node 2.

- Backtracks: Pops `"2"`. `currentPath` reverts to `["1"]`.

 

---

 

### Step 5: Node 3

- Invokes right child of Node 1.

- `currentPath = ["1", "3"]`

- **Leaf node detected!**

- Formats path: `"1->3"`

- `result = ["1->2->5", "1->3"]`

- Backtracks: Pops `"3"`. `currentPath` reverts to `["1"]`.

 

---

 

### Final Output

```text

["1->2->5", "1->3"]

```

 

---

 

## Visual Representation

 

```text

            1  [Path Starts]

           / \

          2   3  ==> Leaf Found! Path completed: "1->3"

           \

            5    ==> Leaf Found! Path completed: "1->2->5"

```

 

---

 

## Edge Cases

 

### Empty Tree

```text

Input: null

Output: []

```

 

### Single Node Tree

```text

    1

```

Output:

```text

["1"]

```

 

### Skewed Tree

```text

    1

   /

  2

/

3

```

Output:

```text

["1->2->3"]

```

 

---

 

## Time Complexity

 

```text

O(N)

```

Where `N` is the number of nodes in the tree. We visit each node exactly once. Building the path strings at the leaf nodes takes proportional time to the height of the tree, which stays bounded inside O(N) overall.

 

---

 

## Space Complexity

 

```text

O(H)

```

Where `H` is the height of the tree. This space is consumed by the recursion stack and the `currentPath` tracking list. In the worst case (a highly skewed tree), it evaluates to `O(N)`. For a balanced tree, the overhead stays minimal at `O(\log N)`.

 

---

 

## Interview Follow-Up Questions

 

### Q1. Can we solve this problem without backtracking?

Yes. You can pass the path by value as a string parameter (e.g., `path + node.val + "->"`). However, string concatenation in Java creates new string objects at every recursive level, leading to poor memory performance. Backtracking using a dynamic list preserves a single instance and avoids heavy allocations.

 

### Q2. How would you modify this to find a path that sums to a target value?

Instead of adding all paths to the final result, you can maintain a running calculation (`targetSum - node.val`). When you arrive at a leaf node, check if the remaining sum is exactly `0`. If true, collect or validate the path (This resolves the classic LeetCode *Path Sum II* variant).

 

---

 

## Important Interview Takeaways

 

- ✅ Leverage DFS (Preorder Traversal) to trace top-down structural paths.

- ✅ Always use a leaf validation condition (`left == null && right == null`) to terminate valid routes.

- ✅ Backtrack efficiently by removing the last element before returning up a stack level.

- ✅ Time Complexity = O(N)

- ✅ Space Complexity = O(H)

- ✅ Frequently Asked In:

  - Bloomberg

  - Amazon

  - Facebook (Meta)

  - Google

- ✅ Related Problems:

  - Path Sum (I, II, III)

  - Sum Root to Leaf Numbers

  - Lowest Common Ancestor of a Binary Tree
