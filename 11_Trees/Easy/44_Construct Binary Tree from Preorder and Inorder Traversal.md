# Construct Binary Tree from Preorder and Inorder Traversal - Java

 

## Problem Statement

 

Given two integer arrays `preorder` and `inorder` where `preorder` is the preorder traversal of a binary tree and `inorder` is the inorder traversal of the same tree, construct and return the binary tree.

 

Both arrays are guaranteed to contain unique values.

 

---

 

## Example

 

### Input

 

```text

preorder = [3, 9, 20, 15, 7]

inorder  = [9, 3, 15, 20, 7]

```

 

### Output

 

```text

        3

       / \

      9   20

         /  \

        15   7

```

 

---

 

# Approach

 

## Idea

 

We can solve this problem using a divide-and-conquer strategy via **recursion**:

1. **Preorder Traversal** follows the sequence **Root → Left → Right**. Therefore, the first element of the current `preorder` array segment is always the root of the current subtree.

2. **Inorder Traversal** follows the sequence **Left → Root → Right**. Once we locate our root element within the `inorder` array, all elements to its left belong to the left subtree, and all elements to its right belong to the right subtree.

 

To avoid costly linear array scans to find the root's index inside the `inorder` array at every recursive tier, we pre-populate an external `HashMap` mapping node values to their corresponding index coordinates.

 

---

 

## Algorithm

 

1. Build a `HashMap` storing the mapping of `value -> index` for the `inorder` array.

2. Maintain a global or tracking index `preorderIndex` initialized to `0`.

3. Create a recursive helper function `buildSubtree(inStart, inEnd)`:

   - If `inStart > inEnd`, return `null` (Base case: empty subtree region).

   - Fetch the root value from `preorder[preorderIndex]` and increment `preorderIndex`.

   - Instantiate a new `TreeNode` using this value.

   - Look up the root value's index inside the `inorder` map (`rootIndex`).

   - Recursively construct the left subtree by calling `buildSubtree(inStart, rootIndex - 1)`.

   - Recursively construct the right subtree by calling `buildSubtree(rootIndex + 1, inEnd)`.

   - Return the constructed node.

4. Call `buildSubtree(0, inorder.length - 1)` from the main entry function and return the root.

 

---

 

## Java Solution

 

```java

import java.util.*;

 

public class ConstructTreePreIn {

    static class TreeNode {

        int val;

        TreeNode left;

        TreeNode right;

        TreeNode(int val) {

            this.val = val;

        }

    }

 

    // Tracker to point to the current root value inside the preorder sequence

    private static int preorderIndex;

    // Map to achieve O(1) index lookups for inorder elements

    private static Map<Integer, Integer> inorderMap;

 

    public static TreeNode buildTree(int[] preorder, int[] inorder) {

        preorderIndex = 0;

        inorderMap = new HashMap<>();

       

        // Populate the lookup index map

        for (int i = 0; i < inorder.length; i++) {

            inorderMap.put(inorder[i], i);

        }

 

        return buildSubtree(preorder, 0, inorder.length - 1);

    }

 

    private static TreeNode buildSubtree(int[] preorder, int inStart, int inEnd) {

        // Base case: If the boundary pointers cross over, no elements exist in this segment

        if (inStart > inEnd) {

            return null;

        }

 

        // The first element in the active preorder segment is our root

        int rootValue = preorder[preorderIndex++];

        TreeNode root = new TreeNode(rootValue);

 

        // Locate the split coordinate inside the inorder sequence

        int rootIndex = inorderMap.get(rootValue);

 

        // Recursively build out the children boundaries

        root.left = buildSubtree(preorder, inStart, rootIndex - 1);

        root.right = buildSubtree(preorder, rootIndex + 1, inEnd);

 

        return root;

    }

 

    // Helper method to print inorder traversal for confirmation loops

    public static void printInorder(TreeNode node) {

        if (node == null) return;

        printInorder(node.left);

        System.out.print(node.val + " ");

        printInorder(node.right);

    }

 

    public static void main(String[] args) {

        int[] preorder = {3, 9, 20, 15, 7};

        int[] inorder = {9, 3, 15, 20, 7};

 

        TreeNode root = buildTree(preorder, inorder);

        System.out.print("Constructed Tree Inorder: ");

        printInorder(root);

        System.out.println();

    }

}

```

 

---

 

## Dry Run

 

Given `preorder = [3, 9, 20, 15, 7]` and `inorder = [9, 3, 15, 20, 7]`:

 

### Step 1: Initial Call

- `preorderIndex = 0`. `inStart = 0, inEnd = 4`.

- `rootValue = preorder[0] = 3`. `preorderIndex` increments to `1`.

- Root Node `3` created. `rootIndex` for 3 in inorder is `1`.

- Triggers Left child: `buildSubtree(0, 0)`.

- Triggers Right child: `buildSubtree(2, 4)`.

 

### Step 2: Left Child of Node 3

- `inStart = 0, inEnd = 0`.

- `rootValue = preorder[1] = 9`. `preorderIndex` increments to `2`.

- Node `9` created. `rootIndex` for 9 is `0`.

- Left: `buildSubtree(0, -1)` -> Returns `null`.

- Right: `buildSubtree(1, 0)` -> Returns `null`.

- Node `9` connects to `3.left`.

 

### Step 3: Right Child of Node 3

- `inStart = 2, inEnd = 4`.

- `rootValue = preorder[2] = 20`. `preorderIndex` increments to `3`.

- Node `20` created. `rootIndex` for 20 is `3`.

- Triggers Left child: `buildSubtree(2, 2)`.

- Triggers Right child: `buildSubtree(4, 4)`.

 

### Step 4: Left Child of Node 20

- `inStart = 2, inEnd = 2`.

- `rootValue = preorder[3] = 15`. `preorderIndex` increments to `4`.

- Node `15` created. Returns up to connect to `20.left`.

 

### Step 5: Right Child of Node 20

- `inStart = 4, inEnd = 4`.

- `rootValue = preorder[4] = 7`. `preorderIndex` increments to `5`.

- Node `7` created. Returns up to connect to `20.right`.

 

---

 

## Visual Representation

 

```text

Preorder: [ 3,  9,  20, 15, 7 ]

            ^  

            Root

 

Inorder:  [ 9 ],  3,  [ 15, 20, 7 ]

 

          |___|       |___________|

        Left Subtree   Right Subtree

```

 

---

 

## Edge Cases

 

### Empty Input

```text

preorder = [], inorder = []

Output: null

```

 

### Single Element

```text

preorder =, inorder = [1]

Output: 1 -> null

```

 

---

 

## Time Complexity

 

```text

O(N)

```

Where `N` is the number of elements in the tree arrays. Populating the index lookups takes `O(N)` time initially, and each node instantiation executes in an `O(1)` hash map look-up configuration frame.

 

---

 

## Space Complexity

 

```text

O(N)

```

The internal map tracks `N` discrete value elements. Additionally, the recursion frame consumes space proportional to the tree height `O(H)`, which yields `O(N)` for a completely skewed layout matrix.

 

---

 

## Interview Follow-Up Questions

 

### Q1. Why does this algorithm require left subtree evaluation before right subtree evaluation?

Because `preorderIndex` increments sequentially from left to right across the `preorder` array (**Root → Left → Right**). If you swap the execution sequences and attempt to build the right child first, `preorderIndex` will read values meant for the left subtree branch, mismatching array data structural integrity.

 

### Q2. Can you apply this logic to build a unique tree if duplicate values exist?

No. Duplicate values introduce boundary positioning ambiguity within the `inorder` sequence mapping. Without unique values, you cannot reliably verify which identical key signifies the boundary transition point, producing multiple valid structural variant topologies.

 

---

 

## Important Interview Takeaways

 

- ✅ Leverage `HashMap` indexing to eliminate inefficient nested loops.

- ✅ Sequence child processing paths strictly to match the **Preorder** format index alignment.

- ✅ Boundary pointers crossover markers (`inStart > inEnd`) establish the terminal recursive base case.

- ✅ Time Complexity = O(N)

- ✅ Space Complexity = O(N)

- ✅ Frequently Asked In:

  - Google

  - Amazon

  - Microsoft

  - Adobe

- ✅ Related Problems:

  - Construct Binary Tree from Inorder and Postorder Traversal

  - Construct Binary Tree from Preorder and Postorder Traversal

  - Validate Binary Search Tree
