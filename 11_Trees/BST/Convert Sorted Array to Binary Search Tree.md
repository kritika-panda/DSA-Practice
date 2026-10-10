# Convert Sorted Array to Binary Search Tree

## Problem Statement

Given an integer array `nums` where the elements are sorted in **ascending order**, convert it to a **height-balanced** Binary Search Tree (BST).

### Definition

A **height-balanced** binary tree is a binary tree in which the depth of the two subtrees of every node never differs by more than one.

---

## Example

### Input

```text
nums = [-10, -3, 0, 5, 9]
```

### Output

A valid root node representing the following height-balanced BST:

```text
           0
         /   \
       -3     9
       /     /
     -10    5
```

Because:

```text
The tree is a valid BST (Left < Root < Right).
The maximum depth difference between any sibling subtrees is <= 1.
Other valid structures like [0, -10, 5, null, -3, null, 9] are also acceptable.
```

---

# Key BST Property

```text
Left Subtree < Root < Right Subtree
```

To build a height-balanced tree from a sorted array, the element chosen as the `Root` must always be the **middle element** of the current array segment. This ensures that an equal number of elements are distributed to the left subtree and the right subtree.

---

# Intuition

We can solve this efficiently using a **Divide and Conquer** approach similar to Binary Search:

### Step 1: Find the Middle Element
Identify the middle index of the current array bounds:
```text
mid = left + (right - left) / 2
```
Create a new tree node with `nums[mid]`. This node serves as the root of the current subtree.

---

### Step 2: Divide and Conquer
* **Left Subtree:** The elements from index `left` to `mid - 1` are strictly smaller than `nums[mid]`. Recursively build the left child using this range.
* **Right Subtree:** The elements from index `mid + 1` to `right` are strictly larger than `nums[mid]`. Recursively build the right child using this range.

---

### Step 3: Base Case
When the `left` index exceeds the `right` index (`left > right`), it means there are no elements left to process in this segment. Return `null`.

---

# Visualization

Constructing a balanced BST from `[-10, -3, 0, 5, 9]`:

```text
1. Initial Range: left = 0, right = 4
   mid = 0 + (4 - 0) / 2 = 2 (Value = 0)
   Create Root node: 0
           0
          / \

2. Left Range: left = 0, right = 1
   mid = 0 + (1 - 0) / 2 = 0 (Value = -10)
   Create Left Child of 0: -10
           0
          / \
        -10

3. Left-Right Sub-range: left = 1, right = 1
   mid = 1 + (1 - 1) / 2 = 1 (Value = -3)
   Create Right Child of -10: -3
           0
          / \
        -10
          \
          -3

4. Right Range: left = 3, right = 4
   mid = 3 + (4 - 3) / 2 = 3 (Value = 5)
   Create Right Child of 0: 5
           0
          / \
        -10  5
          \
          -3

5. Right-Right Sub-range: left = 4, right = 4
   mid = 4 + (4 - 4) / 2 = 4 (Value = 9)
   Create Right Child of 5: 9
           0
          / \
        -10  5
          \   \
          -3   9

(Note: The visual layout above is completely height-balanced and satisfies all BST rules).
```

---

# Recursive Solution

## Algorithm

1. Define a helper function `buildBST(nums, left, right)`.
2. Check the base case: if `left > right`, return `null`.
3. Calculate the midpoint to pick the subtree root.
4. Construct a new `TreeNode` with the midpoint value.
5. Assign its left child by recursively calling `buildBST` on the left partition `[left, mid - 1]`.
6. Assign its right child by recursively calling `buildBST` on the right partition `[mid + 1, right]`.
7. Return the node.

---

## Java Implementation

```java
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
    public TreeNode sortedArrayToBST(int[] nums) {
        if (nums == null || nums.length == 0) {
            return null;
        }
        // Initiate the recursive tree building with full array bounds
        return buildBST(nums, 0, nums.length - 1);
    }

    private TreeNode buildBST(int[] nums, int left, int right) {
        // Base case: segment boundaries have crossed over
        if (left > right) {
            return null;
        }

        // Avoid integer overflow during midpoint calculation
        int mid = left + (right - left) / 2;

        // Make the middle element the root of this subtree
        TreeNode root = new TreeNode(nums[mid]);

        // Recursively build out the balanced left and right structures
        root.left = buildBST(nums, left, mid - 1);
        root.right = buildBST(nums, mid + 1, right);

        return root;
    }
}
```

### Complexity Analysis

* **Time Complexity:** O(N) where N is the total number of elements in the `nums` array. The algorithm processes each element exactly once to construct its corresponding tree node.
* **Space Complexity:** O(log N) auxiliary space matching the maximum recursion depth. Since the array is split down the exact middle at every recursive frame, the depth ceiling is strictly bounded to log N recursive stacks.
