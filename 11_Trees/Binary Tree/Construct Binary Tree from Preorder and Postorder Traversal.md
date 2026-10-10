# Construct Binary Tree from Preorder and Postorder Traversal

## Problem Statement

Given two integer arrays, `preorder` and `postorder`, where `preorder` is the preorder traversal of a binary tree and `postorder` is the postorder traversal of the same tree, reconstruct and return the original binary tree.

If there are multiple answers, you can return **any** of them.

### Definition

* **Preorder Traversal:** Visits the nodes in the order: `Root -> Left -> Right`.
* **Postorder Traversal:** Visits the nodes in the order: `Left -> Right -> Root`.

---

## Example

### Input

```text
preorder = [1, 2, 4, 5, 3, 6, 7]
postorder = [4, 5, 2, 6, 7, 3, 1]
```

### Output

A valid root node representing the following binary tree:

```text
           1
         /   \
        2     3
       / \   / \
      4   5 6   7
```

Because:

```text
1 is the first element in preorder and last in postorder, making it the root.
The left child of 1 is 2 (the next element in preorder).
By finding 2 in postorder, we can see that [4, 5, 2] belongs to the left subtree.
The remaining elements [6, 7, 3] belong to the right subtree.
```

---

# Key Traversal Properties

```text
Preorder: [ Root | Left child | ... Left Subtree ... | ... Right Subtree ... ]
Postorder: [ ... Left Subtree ... | Left child | ... Right Subtree ... | Root ]
```

These structural alignment rules allow us to partition the traversal arrays and determine exactly where subtrees split.

---

# Intuition

We can solve this problem using a **Divide and Conquer** approach by tracing subtrees through their structural boundaries:

### Step 1: Locate the Subtree Root
The first element of the current `preorder` segment is always the `Root` of that subtree. We create a new node with this value.

### Step 2: Identify the Left Child Boundary
If there are elements remaining in the segment, the element immediately following the root in `preorder` must be the root of the **left child subtree**.

### Step 3: Partition the Subtrees
We find the value of this left child inside the `postorder` array. 
* All elements in `postorder` from the beginning of the current subtree up to this left child's index belong exclusively to the **left subtree**.
* By counting how many elements are in this range, we can determine the exact boundary sizing to split both `preorder` and `postorder` arrays for the next recursive steps.

---

# Visualization

Reconstructing a binary tree from `preorder = [1, 2, 4, 5, 3, 6, 7]` and `postorder = [4, 5, 2, 6, 7, 3, 1]`:

```text
1. Root Node Identification
   - '1' is the first element in preorder -> Root = 1.
   - The element after '1' in preorder is '2'. This means '2' is the root of the left subtree.

2. Partition via Postorder Search
   - Find '2' in postorder: [4, 5, 2, 6, 7, 3, 1]
   - The left subtree elements are [4, 5, 2] (3 elements total).
   - The remaining elements [6, 7, 3] belong to the right subtree.

3. Split and Recurse
   - Left Subtree Arrays:  preorder =, postorder = [4, 5, 2]
   - Right Subtree Arrays: preorder =, postorder = [6, 7, 3]

4. Process Left Subtree:
   - '2' is root. Next element is '4' (left child).
   - Find '4' in postorder -> Left subtree has 1 element ([4]), right has 1 element ([5]).
   - Tree expands:
           1
         /   \
        2     3
       / \   
      4   5 

5. Process Right Subtree:
   - '3' is root. Next element is '6' (left child).
   - Find '6' in postorder -> Left subtree has 1 element ([6]), right has 1 element ([7]).
   - Final Tree Completed:
           1
         /   \
        2     3
       / \   / \
      4   5 6   7
```

---

# Recursive Solution

## Algorithm

1. Build a HashMap to map values to their indices in the `postorder` array for O(1) lookups.
2. Define a helper function `construct(preStart, preEnd, postStart, postEnd)`.
3. **Base Case:** If `preStart > preEnd`, return `null`.
4. Create the root node using `preorder[preStart]`.
5. If `preStart == preEnd`, return the single root node.
6. Find the value `preorder[preStart + 1]` (the left child root) in the postorder index map.
7. Calculate the size of the left subtree: `numLeft = postIndex - postStart + 1`.
8. Recursively build the left child using the calculated range boundaries.
9. Recursively build the right child using the remaining range boundaries.
10. Return the root.

---

## Java Implementation

```java
import java.util.HashMap;
import java.util.Map;

// Definition for a binary tree node.
class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    
    TreeNode() {}
    
    TreeNode(int val) { 
        this.val = val; 
    }
    
    TreeNode(int val, TreeNode left, TreeNode right) {
        this.val = val;
        this.left = left;
        this.right = right;
    }
}

class Solution {
    // Map to cache value-to-index pairs of the postorder array for fast lookups
    private Map<Integer, Integer> postMap;

    public TreeNode constructFromPrePost(int[] preorder, int[] postorder) {
        postMap = new HashMap<>();
        for (int i = 0; i < postorder.length; i++) {
            postMap.put(postorder[i], i);
        }
        
        return buildTree(preorder, 0, preorder.length - 1, 0, postorder.length - 1);
    }

    private TreeNode buildTree(int[] preorder, int preStart, int preEnd, int postStart, int postEnd) {
        // Base case: segment boundaries have crossed over
        if (preStart > preEnd) {
            return null;
        }

        // The first element in preorder is always the root of the current subtree
        TreeNode root = new TreeNode(preorder[preStart]);

        // If this node has no children, return it immediately
        if (preStart == preEnd) {
            return root;
        }

        // The element next to the root in preorder is the root of the left subtree
        int leftChildVal = preorder[preStart + 1];
        int postIndex = postMap.get(leftChildVal);

        // Calculate how many nodes exist in the left subtree
        int numLeft = postIndex - postStart + 1;

        // Recursively divide and conquer both subtrees using calculated offsets
        root.left = buildTree(preorder, preStart + 1, preStart + numLeft, postStart, postIndex);
        root.right = buildTree(preorder, preStart + numLeft + 1, preEnd, postIndex + 1, postEnd - 1);

        return root;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N) where N is the total number of nodes in the binary tree. We cache the postorder array indices in a HashMap up front, allowing us to split the arrays and build each node in O(1) time.
* **Space Complexity:** O(N) auxiliary space. This accounts for the O(N) storage inside the HashMap index lookup dictionary, plus O(H) space for the recursive program execution stack frames.
