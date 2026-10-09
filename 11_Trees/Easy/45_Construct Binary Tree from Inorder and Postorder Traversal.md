  # Construct Binary Tree from Inorder and Postorder Traversal - Java

 

## Problem Statement

 

Given two integer arrays `inorder` and `postorder` where `inorder` is the inorder traversal of a binary tree and `postorder` is the postorder traversal of the same tree, construct and return the binary tree.

 

Both arrays are guaranteed to contain unique values.

 

---

 

## Example

 

### Input

 

```text

inorder   = [9, 3, 15, 20, 7]

postorder = [9, 15, 7, 20, 3]

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

1. **Postorder Traversal** follows the sequence **Left → Right → Root**. Therefore, the last element of the current `postorder` array segment is always the root of the current subtree. By moving backwards from the end of the `postorder` array, we encounter roots sequentially.

2. **Inorder Traversal** follows the sequence **Left → Root → Right**. Once we locate our root element within the `inorder` array, all elements to its right belong to the right subtree, and all elements to its left belong to the left subtree.

 

To avoid costly linear array scans to find the root's index inside the `inorder` array at every recursive tier, we pre-populate an external `HashMap` mapping node values to their corresponding index coordinates.

 

---

 

## Algorithm

 

1. Build a `HashMap` storing the mapping of `value -> index` for the `inorder` array.

2. Maintain a global or tracking index `postorderIndex` initialized to the last index of the array (`postorder.length - 1`).

3. Create a recursive helper function `buildSubtree(inStart, inEnd)`:

   - If `inStart > inEnd`, return `null` (Base case: empty subtree region).

   - Fetch the root value from `postorder[postorderIndex]` and decrement `postorderIndex`.

   - Instantiate a new `TreeNode` using this value.

   - Look up the root value's index inside the `inorder` map (`rootIndex`).

   - **Crucial Step**: Recursively construct the **Right Subtree** first by calling `buildSubtree(rootIndex + 1, inEnd)`.

   - Recursively construct the **Left Subtree** second by calling `buildSubtree(inStart, rootIndex - 1)`.

   - Return the constructed node.

4. Call `buildSubtree(0, inorder.length - 1)` from the main entry function and return the root.

 

---

 

## Java Solution

 

```java

import java.util.*;

 

public class ConstructTreeInPost {

    static class TreeNode {

        int val;

        TreeNode left;

        TreeNode right;

        TreeNode(int val) {

            this.val = val;

        }

    }

 

    // Tracker to point to the current root value inside the postorder sequence (processed backwards)

    private static int postorderIndex;

    // Map to achieve O(1) index lookups for inorder elements

    private static Map<Integer, Integer> inorderMap;

 

    public static TreeNode buildTree(int[] inorder, int[] postorder) {

        if (inorder == null || postorder == null || inorder.length != postorder.length) {

            return null;

        }

       

        postorderIndex = postorder.length - 1;

        inorderMap = new HashMap<>();

       

        // Populate the lookup index map

        for (int i = 0; i < inorder.length; i++) {

            inorderMap.put(inorder[i], i);

        }

 

        return buildSubtree(postorder, 0, inorder.length - 1);

    }

 

    private static TreeNode buildSubtree(int[] postorder, int inStart, int inEnd) {

        // Base case: If the boundary pointers cross over, no elements exist in this segment

        if (inStart > inEnd) {

            return null;

        }

 

        // The last element in the active postorder segment is our root

        int rootValue = postorder[postorderIndex--];

        TreeNode root = new TreeNode(rootValue);

 

        // Locate the split coordinate inside the inorder sequence

        int rootIndex = inorderMap.get(rootValue);

 

        // Crucial: Build the Right subtree first because we are traversing postorder backwards (Root -> Right -> Left)

        root.right = buildSubtree(postorder, rootIndex + 1, inEnd);

        root.left = buildSubtree(postorder, inStart, rootIndex - 1);

 

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

        int[] inorder = {9, 3, 15, 20, 7};

        int[] postorder = {9, 15, 7, 20, 3};

 

        TreeNode root = buildTree(inorder, postorder);

        System.out.print("Constructed Tree Inorder: ");

        printInorder(root);

        System.out.println();

    }

}

```

 

---

 

## Dry Run

 

Given `inorder = [9, 3, 15, 20, 7]` and `postorder = [9, 15, 7, 20, 3]`:

 

### Step 1: Initial Call

- `postorderIndex = 4`. `inStart = 0, inEnd = 4`.

- `rootValue = postorder[4] = 3`. `postorderIndex` decrements to `3`.

- Root Node `3` created. `rootIndex` for 3 in inorder is `1`.

- Triggers Right child: `buildSubtree(2, 4)`.

- Triggers Left child: `buildSubtree(0, 0)`.

 

### Step 2: Right Child of Node 3

- `inStart = 2, inEnd = 4`.

- `rootValue = postorder[3] = 20`. `postorderIndex` decrements to `2`.

- Node `20` created. `rootIndex` for 20 in inorder is `3`.

- Triggers Right child: `buildSubtree(4, 4)`.

- Triggers Left child: `buildSubtree(2, 2)`.

- Node `20` connects to `3.right`.

 

### Step 3: Right Child of Node 20

- `inStart = 4, inEnd = 4`.

- `rootValue = postorder[2] = 7`. `postorderIndex` decrements to `1`.

- Node `7` created. `rootIndex` for 7 is `4`.

- Left & Right children calls cross boundaries (`inStart > inEnd`), returning `null`.

- Node `7` connects to `20.right`.

 

### Step 4: Left Child of Node 20

- `inStart = 2, inEnd = 2`.

- `rootValue = postorder[1] = 15`. `postorderIndex` decrements to `0`.

- Node `15` created. Returns up to connect to `20.left`.

 

### Step 5: Left Child of Node 3

- `inStart = 0, inEnd = 0`.

- `rootValue = postorder[0] = 9`. `postorderIndex` decrements to `-1`.

- Node `9` created. Returns up to connect to `3.left`.

 

---

 

## Visual Representation

 

```text

Postorder: [9, 15, 7, 20, 3]

                          ^

                         Root

 

Inorder:,   3,   [15, 20, 7]

 

          |___|        |__________|

       Left Subtree    Right Subtree

```

 

---

 

## Edge Cases

 

### Empty Input

```text

inorder = [], postorder = []

Output: null

```

 

### Single Element

```text

inorder =, postorder = [1]

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

 

### Q1. Why MUST we construct the right subtree before the left subtree in this problem?

Because we are processing the `postorder` array from right to left (backwards). Since postorder traversal visits nodes in the order **Left → Right → Root**, scanning it backwards yields the sequence **Root → Right → Left**. Therefore, the element immediately preceding a root in the postorder array belongs to its right subtree (if it exists).

 

### Q2. How does this problem differ from constructing a tree from Preorder and Inorder traversal?

In the Preorder variant, the root is at the *beginning* of the array, and scanning it forwards yields a **Root → Left → Right** sequence, requiring you to construct the left subtree first. In this Postorder variant, the root is at the *end*, requiring a reverse scan and building the right subtree first.

 

---

 

## Important Interview Takeaways

 

- ✅ Process the `postorder` array backwards from `length - 1` down to `0`.

- ✅ Always evaluate the **Right Subtree first** when decoding postorder arrays in reverse.

- ✅ Use a boundary crossover condition (`inStart > inEnd`) to safely terminate recursion branches.

- ✅ Time Complexity = O(N)

- ✅ Space Complexity = O(N)

- ✅ Frequently Asked In:

  - Microsoft

  - Amazon

  - Adobe

  - Walmart

- ✅ Related Problems:

  - Construct Binary Tree from Preorder and Inorder Traversal

  - Construct Binary Tree from Preorder and Postorder Traversal

  - Serialize and Deserialize Binary Tree
