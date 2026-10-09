# Minimum Time to Burn a Binary Tree from a Leaf/Target Node - Java

 

## Problem Statement

 

Given the root of a binary tree and a target node `target` (representing the starting point of a fire), calculate the **minimum time** required to burn the entire binary tree.

 

In one second, the fire spreads from a burning node to all of its immediate unburned neighbors:

- Left child

- Right child

- Parent node

 

---

 

## Example

 

### Input

 

```text

        3

       / \

      5   1

     / \ / \

    6   2 0  8

       / \

      7   4

```

`target = 5`

 

### Output

 

```text

3

```

 

### Explanation

 

- **0 Seconds**: Node `5` catches fire.

- **1 Second**: Fire spreads to adjacent neighbors: `6`, `2`, and parent `3`.

- **2 Seconds**: Fire spreads to their neighbors: `7` (from 2), `4` (from 2), and `1` (from 3).

- **3 Seconds**: Fire spreads to final remaining neighbors: `0` (from 1) and `8` (from 1).

The entire tree is completely burned in `3` seconds.

 

---

 

# Approach

 

## Idea

 

This problem is isomorphic to finding the maximum distance from a starting node to any other node in an undirected graph. Because standard binary tree edges only point downwards, we need a way to navigate back up to a parent node.

 

We solve this using a two-pass approach:

1. **First Pass (BFS/DFS)**: Map each node to its parent pointer using a `HashMap`. This effectively models the binary tree as an undirected graph.

2. **Second Pass (Radial BFS)**: Begin a level-order traversal starting from the `target` node. Expand outward to all unburned neighbors (left child, right child, and parent pointer) concurrently. Each level loop expansion represents exactly `1` second of burning time.

 

---

 

## Algorithm

 

1. **Map Parents**: Traverse the tree from the root to build a `Map<TreeNode, TreeNode>` mapping `child -> parent`.

2. **BFS Queue Setup**: Initialize a queue for level-order traversal and a `Set<TreeNode>` to log already burned nodes. Enqueue the `target` node and mark it as visited.

3. **Radial Expansion**:

   - Initialize `timeTaken = 0`.

   - While the queue is not empty:

     - Check the queue size (`levelSize = queue.size()`).

     - Maintain a flag `hasSpread = false` to verify if the fire spreads to at least one new node during this level.

     - Loop `levelSize` times:

       - Dequeue the current node.

       - Check its 3 neighbor directions: **Left child**, **Right child**, and **Parent** (via parent map).

       - For any non-null and unvisited neighbor, mark it as visited, enqueue it, and toggle `hasSpread = true`.

     - If `hasSpread` is true, increment `timeTaken` by `1`.

4. Return `timeTaken`.

 

---

 

## Java Solution

 

```java

import java.util.*;

 

public class BurnBinaryTree {

    static class TreeNode {

        int val;

        TreeNode left;

        TreeNode right;

        TreeNode(int val) {

            this.val = val;

        }

    }

 

    // Helper method to link children nodes to their parents

    private static void mapParents(TreeNode node, TreeNode parent, Map<TreeNode, TreeNode> parentMap) {

        if (node == null) return;

        if (parent != null) {

            parentMap.put(node, parent);

        }

        mapParents(node.left, node, parentMap);

        mapParents(node.right, node, parentMap);

    }

 

    public static int minTimeToBurnTree(TreeNode root, TreeNode target) {

        if (root == null || target == null) {

            return 0;

        }

 

        // Step 1: Map child nodes to parent pointers

        Map<TreeNode, TreeNode> parentMap = new HashMap<>();

        mapParents(root, null, parentMap);

 

        // Step 2: Initialize BFS structures starting at target

        Queue<TreeNode> queue = new LinkedList<>();

        Set<TreeNode> burned = new HashSet<>();

 

        queue.offer(target);

        burned.add(target);

        int timeTaken = 0;

 

        // Step 3: Radial BFS loop tracking level steps

        while (!queue.isEmpty()) {

            int levelSize = queue.size();

            boolean hasSpread = false;

 

            for (int i = 0; i < levelSize; i++) {

                TreeNode current = queue.poll();

 

                // Direction 1: Left Child

                if (current.left != null && !burned.contains(current.left)) {

                    burned.add(current.left);

                    queue.offer(current.left);

                    hasSpread = true;

                }

 

                // Direction 2: Right Child

                if (current.right != null && !burned.contains(current.right)) {

                    burned.add(current.right);

                    queue.offer(current.right);

                    hasSpread = true;

                }

 

                // Direction 3: Parent Node

                TreeNode parent = parentMap.get(current);

                if (parent != null && !burned.contains(parent)) {

                    burned.add(parent);

                    queue.offer(parent);

                    hasSpread = true;

                }

            }

 

            // Only increment time if fire successfully caught a new neighbor branch

            if (hasSpread) {

                timeTaken++;

            }

        }

 

        return timeTaken;

    }

 

    public static void main(String[] args) {

        TreeNode root = new TreeNode(3);

        root.left = new TreeNode(5);

        root.right = new TreeNode(1);

        root.left.left = new TreeNode(6);

        root.left.right = new TreeNode(2);

        root.left.right.left = new TreeNode(7);

        root.left.right.right = new TreeNode(4);

        root.right.left = new TreeNode(0);

        root.right.right = new TreeNode(8);

 

        TreeNode target = root.left; // Node 5

 

        System.out.println("Minimum time to burn tree: " + minTimeToBurnTree(root, target) + " seconds");

    }

}

```

 

---

 

## Dry Run

 

Using the example tree with `target = 5`:

 

### Step 1: Mapping Parents

- Parent Map maps `5->3`, `1->3`, `6->5`, `2->5`, `7->2`, `4->2`, `0->1`, `8->1`.

 

### Step 2: Time = 0

- Queue = `[5]`, Burned = `{5}`, `timeTaken = 0`

- Loop level size = 1. Poll `5`.

- Neighbors checked: `6` (unburned), `2` (unburned), `3` (parent, unburned).

- All three get enqueued. `hasSpread = true`.

- Queue = `[6, 2, 3]`. `timeTaken` increments to `1`.

 

### Step 3: Time = 1

- Queue = `[6, 2, 3]`, level size = 3

- Poll `6`: No unburned neighbors.

- Poll `2`: Neighbors `7` and `4` are unburned. Enqueue both. `hasSpread = true`.

- Poll `3`: Neighbor `1` is unburned. Enqueue it.

- Queue = `[7, 4, 1]`. `timeTaken` increments to `2`.

 

### Step 4: Time = 2

- Queue = `[7, 4, 1]`, level size = 3

- Poll `7` and `4`: No unburned neighbors.

- Poll `1`: Neighbors `0` and `8` are unburned. Enqueue both. `hasSpread = true`.

- Queue = `[0, 8]`. `timeTaken` increments to `3`.

 

### Step 5: Time = 3

- Queue = `[0, 8]`, level size = 2

- Poll `0` and `8`: No unburned neighbors remain. `hasSpread` stays `false`.

- Queue becomes empty. Loop terminates.

- Final Output = `3`.

 

---

 

## Visual Representation

 

```text

                  3 (Time: 1)

                 / \

   (Target) 5 (0)   1 (Time: 2)

             / \   / \

   (Time: 1)6   2 0   8 (Time: 3)

               / \

     (Time: 2)7   4 (Time: 2)

```

 

---

 

## Edge Cases

 

### Single Node Tree

```text

Input: root =, target = 1

Output: 0 seconds (Target node burns instantly; no other nodes exist)

```

 

### Target is a Leaf Node on a Skewed Tree

```text

      1

     /

    2

   /

  3 (Target)

Output: 2 seconds (Fire travels straight up via parents: 3 -> 2 -> 1)

```

 

---

 

## Time Complexity

 

```text

O(N)

```

Where `N` is the total number of nodes in the binary tree. We visit each node once during parent pointer assignment and at most once during the expanding BFS simulation.

 

---

 

## Space Complexity

 

```text

O(N)

```

The space scales linearly because the parent pointer map, visited tracking set, and BFS queue store references matching up to `N` structural components.

 

---

 

## Interview Follow-Up Questions

 

### Q1. Is it possible to solve this in a single pass without a Parent Map?

Yes, using an tracking DFS approach. You can write a recursive function that returns the height of subtrees if the target isn't found. If the target is found, it calculates the down-facing height and returns a special distance flag up to its ancestors. The ancestors can use this value to see how long it takes for fire to route through them to reach the farthest leaf of their *opposite* subtrees.

 

### Q2. What is the difference between this problem and LeetCode 2385 ("Amount of Time for Binary Tree to Be Infected")?

They are identical. "Amount of Time for Binary Tree to Be Infected" uses the exact same mechanics, replacing the concept of "burning" with "infecting". The core implementation does not change.

 

---

 

## Important Interview Takeaways

 

- ✅ Build parent pointers to transform a directional tree structure into a navigable layout graph.

- ✅ Use radial BFS level loops to accurately represent synchronized time stepping.

- ✅ Implement a strict `hasSpread` check to keep boundary increments clean at the leaf nodes.

- ✅ Time Complexity = O(N)

- ✅ Space Complexity = O(N)

- ✅ Frequently Asked In:

  - Amazon

  - Microsoft

  - Uber

  - Flipkart

- ✅ Related Problems:

  - All Nodes Distance K in Binary Tree

  - Diameter of Binary Tree

  - Binary Tree Maximum Path Sum
