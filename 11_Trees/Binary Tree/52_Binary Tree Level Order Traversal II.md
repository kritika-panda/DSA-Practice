# Binary Tree Level Order Traversal II

## Problem Statement

Given the `root` of a binary tree, return the *bottom-up level order traversal* of its nodes' values. (i.e., from left to right, level by level from leaf to root).

The overall run time complexity should be:

```text
O(n)
```

*(where n is the total number of nodes in the binary tree)*

---

## Examples

### Example 1

**Input**

```java
root = [3, 9, 20, null, null, 15, 7]
```

**Output**

```java
[[15, 7], [9, 20], [3]]
```

**Explanation**

Given the tree:
```text
    3
   / \
  9  20
    /  \
   15   7
```
- Level 0 (top): `[3]`
- Level 1: `[9, 20]`
- Level 2 (bottom): `[15, 7]`

Returning them from leaf level to root level yields `[[15, 7], [9, 20], [3]]`.

---

### Example 2

**Input**

```java
root = [1]
```

**Output**

```java
[[1]]
```

---

### Example 3

**Input**

```java
root = []
```

**Output**

```java
[]
```

---

## Brute Force Approach

Perform a standard top-down level order traversal using a queue, collect the results in a standard list, and then reverse the entire collection.

### Steps

1. Use a standard Breadth-First Search (BFS) queue to group nodes level by level from the top down.
2. Store each level's list of values inside a master list container.
3. Once the traversal finishes completely, reverse the master container list.
4. Return the reversed collection.

### Complexity

```text
Time Complexity: O(n)
Space Complexity: O(n)
```

While this meets the optimal asymptotic time bound, explicitly reversing a large array list creates slight allocation overhead. We can optimize this by inserting levels directly at the front of a linked list or block during the traversal itself.

---

# Optimal Approach: BFS Queue with Head Insertion

## Key Idea

Instead of performing a top-down search followed by a separate list reversal, we can achieve bottom-up order natively during the **Breadth-First Search (BFS)** step. 

We utilize a double-ended list or linked list structure (`LinkedList` in Java) to hold our master result layout:
1. Initialize a FIFO `Queue` and push the `root` node into it.
2. Loop while the queue is not empty. At the beginning of each iteration, capture the `size` of the queue, which represents the exact number of nodes present at the current level.
3. Process all nodes for that level by popping them from the queue and collecting their values in a temporary level list. Push their child nodes back into the queue.
4. Instead of appending the temporary list to the end of our master collection, we **insert it at index 0 (the front)** using `addFirst()`. 

By continually pushing new levels to the head position, the levels naturally stack in a reverse, bottom-up sequence.

---

## Visual Understanding

Suppose we traverse the tree from Example 1:

```text
    3
   / \
  9  20
    /  \
   15   7
```

- **Level 1 (Root level):** Processes `3`. Temporary level list = `[3]`. Insert at head -> Master List = `[[3]]`.
- **Level 2:** Processes `9` and `20`. Temporary level list = `[9, 20]`. Insert at head -> Master List = `[[9, 20], [3]]`.
- **Level 3:** Processes `15` and `7`. Temporary level list = `[15, 7]`. Insert at head -> Master List = `[[15, 7], [9, 20], [3]]`.

The final output is structured correctly in a single pass.

---

## Partition Variables

Let:

```java
LinkedList<List<Integer>> result = new LinkedList<>();
Queue<TreeNode> queue = new LinkedList<>();
```

---

### Border Elements

If the initial tree pointer reference is null, return an empty tracking list container immediately:

```java
if (root == null) return result;
```

---

## Correct Partition Condition

The criterion to ensure that an entire horizontal tier of nodes has been completely flushed out before shifting our insertion window looks as follows:

```java
int levelSize = queue.size();
for (int i = 0; i < levelSize; i++) {
    TreeNode current = queue.poll();
    // Gather values and offer children...
}
```

---

## How to Move Binary Search

*(Note: This optimal strategy replaces standard binary range partitioning with a level-by-level Queue-driven Breadth-First Search walk to sort tree nodes by depth metrics in linear time).*

---

## Java Solution

```java
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;

class Solution {

    public class TreeNode {
        int val;
        TreeNode left;
        TreeNode right;
        TreeNode(int val) { this.val = val; }
    }

    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        
        // Using a LinkedList to easily insert levels at the front in O(1) time
        LinkedList<List<Integer>> result = new LinkedList<>();
        
        if (root == null) {
            return result;
        }

        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);

        while (!queue.isEmpty()) {
            int levelSize = queue.size();
            List<Integer> currentLevel = new ArrayList<>(levelSize);

            // Process all nodes belonging strictly to the current level depth
            for (int i = 0; i < levelSize; i++) {
                TreeNode current = queue.poll();
                currentLevel.add(current.val);

                // Offer child nodes to the queue for the next level level pass
                if (current.left != null) {
                    queue.offer(current.left);
                }
                if (current.right != null) {
                    queue.offer(current.right);
                }
            }

            // Insert the completed level at the front of our master collection
            result.addFirst(currentLevel);
        }

        return result;
    }
}
```

---

## Dry Run

### Input

```java
root = [3, 9, 20]
```

---

### Step Execution Traversal

- **Initialization:** `queue.offer(node_3)`. `result = []`.
- **Iteration 1:** `levelSize = 1`. 
  - Polls `node_3`. `currentLevel = [3]`. Offers `node_9` and `node_20` to queue.
  - `result.addFirst([3])` -> `result = [[3]]`.
- **Iteration 2:** `levelSize = 2`.
  - Polls `node_9`. `currentLevel = [9]`.
  - Polls `node_20`. `currentLevel = [9, 20]`.
  - `result.addFirst([9, 20])` -> `result = [[9, 20], [3]]`.
- Queue is empty. Loop terminates.

---

### Answer

```java
[[9, 20], [3]]
```

---

## Why Is the Time Complexity Linear?

The algorithm visits every node in the binary tree exactly once as it shifts through the FIFO queue. Level groupings are tracked using structural size boundaries, and inserting level lists at the head of a `LinkedList` takes constant time O(1) per level, avoiding any full-array comparison sorting costs.

This uniform traversal flow yields:

```text
O(n)
```

which satisfies the absolute optimal time constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(n)
```

Where `n` is the total number of nodes in the binary tree. Each node is added to and removed from the queue exactly once.

---

### Space Complexity

```text
O(n)
```

The queue holds at most the maximum number of nodes present at a single level depth tier, which scales to O(n/2) ≈ O(n) for a complete balanced tree structure at its leaf layer.

---

## Key Insight

Utilizing a double-ended container to prepend horizontal level slices dynamically during top-down queue walks removes the need for separate tracking reversals or secondary indexing passes.

```text
Time  : O(n) execution path
Space : O(n) queue storage boundary
```

---

## Similar Problems

1. Binary Tree Level Order Traversal (102)
2. Binary Tree Zigzag Level Order Traversal (103)
3. N-ary Tree Level Order Traversal (429)
4. Average of Levels in Binary Tree (637)
5. Populating Next Right Pointers in Each Node (116)
