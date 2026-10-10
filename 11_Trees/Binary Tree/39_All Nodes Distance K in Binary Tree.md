  # All Nodes Distance K in Binary Tree - Java

 

## Problem Statement

 

Given the root of a binary tree, a target node `target`, and an integer `k`, return an array of the values of all nodes that have a distance `k` from the target node.

 

The answer can be returned in **any order**.

 

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

`target = 5`, `k = 2`

 

### Output

 

```text

[7, 4, 1]

```

 

### Explanation

 

The nodes that are a distance of `2` from the target node `5` are:

- Moving down the subtrees: Node `7` and Node `4`

- Moving up through the parent: Node `1`

 

---

 

# Approach

 

## Idea

 

In a standard binary tree, you can only traverse downwards from a node to its children. To find nodes at a distance `k` that are located "above" or "across" from the target node, we need to be able to traverse **upwards** to a node's parent.

 

We can convert this problem into a standard graph traversal (BFS) problem using a two-pass approach:

1. **First Pass (DFS/BFS)**: Traverse the entire tree to map each child node to its respective parent node using a `HashMap`. This effectively turns our directional binary tree into an undirected graph.

2. **Second Pass (BFS)**: Start a traditional Breadth First Search starting from the `target` node. Explore neighbors (left child, right child, and parent) level by level up to distance `k`. Maintain a `HashSet` to avoid processing the same node multiple times.

 

---

 

## Algorithm

 

1. **Map Parents**: Create a helper function using DFS or BFS to populate a `Map<TreeNode, TreeNode>` representing `child -> parent` relationships.

2. **Initialize BFS Queue**: Create a queue for BFS and enqueue the `target` node along with a tracker for distance (`currentDistance = 0`). Alternatively, process level-by-level using the queue size.

3. **Track Visited Nodes**: Create a `Set<TreeNode>` and add the `target` node to it immediately to prevent looping backwards.

4. **Traverse Graph**:

   - Loop while the queue is not empty.

   - If our current level reaches `k`, collect all node values currently sitting inside the queue and break.

   - For every node at the current level, expand out to its 3 possible directions:

     - **Left Child** (`node.left`)

     - **Right Child** (`node.right`)

     - **Parent Node** (`parentMap.get(node)`)

   - If any of these directions are non-null and haven't been visited yet, add them to both the `visited` set and the `queue`.

5. Return the collected values.

 

---

 

## Java Solution

 

```java

import java.util.*;

 

public class NodesAtDistanceK {

    static class TreeNode {

        int val;

        TreeNode left;

        TreeNode right;

        TreeNode(int val) {

            this.val = val;

        }

    }

 

    // Helper method to map parent pointers for every child node

    private static void populateParentMap(TreeNode node, TreeNode parent, Map<TreeNode, TreeNode> parentMap) {

        if (node == null) return;

        if (parent != null) {

            parentMap.put(node, parent);

        }

        populateParentMap(node.left, node, parentMap);

        populateParentMap(node.right, node, parentMap);

    }

 

    public static List<Integer> distanceK(TreeNode root, TreeNode target, int k) {

        List<Integer> result = new ArrayList<>();

        if (root == null || target == null) {

            return result;

        }

 

        // Step 1: Map child nodes to their parents

        Map<TreeNode, TreeNode> parentMap = new HashMap<>();

        populateParentMap(root, null, parentMap);

 

        // Step 2: Initialize BFS from target node

        Queue<TreeNode> queue = new LinkedList<>();

        Set<TreeNode> visited = new HashSet<>();

       

        queue.offer(target);

        visited.add(target);

        int currentDistance = 0;

 

        // Step 3: BFS level-order traversal up to distance k

        while (!queue.isEmpty()) {

            if (currentDistance == k) {

                // If we reached the target distance, all elements in the queue are answers

                for (TreeNode node : queue) {

                    result.add(node.val);

                }

                return result;

            }

 

            int size = queue.size();

            for (int i = 0; i < size; i++) {

                TreeNode current = queue.poll();

 

                // Direction 1: Left Child

                if (current.left != null && !visited.contains(current.left)) {

                    visited.add(current.left);

                    queue.offer(current.left);

                }

 

                // Direction 2: Right Child

                if (current.right != null && !visited.contains(current.right)) {

                    visited.add(current.right);

                    queue.offer(current.right);

                }

 

                // Direction 3: Parent Node

                TreeNode parent = parentMap.get(current);

                if (parent != null && !visited.contains(parent)) {

                    visited.add(parent);

                    queue.offer(parent);

                }

            }

            currentDistance++;

        }

 

        return result;

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

        int k = 2;

 

        System.out.println("Nodes at distance " + k + ": " + distanceK(root, target, k));

    }

}

```

 

---

 

## Dry Run

 

Using the example tree with `target = 5` and `k = 2`:

 

### Step 1: Mapping Parents

- Parent Map maps `5->3`, `1->3`, `6->5`, `2->5`, `7->2`, `4->2`, `0->1`, `8->1`.

 

### Step 2: Distance = 0 (Starting BFS)

- Queue = `[5]`, Visited = `{5}`

- `currentDistance != k (0 != 2)`.

- Poll `5`. Explore paths from `5`:

  - Left (`6`) -> Not visited. Add to queue & visited.

  - Right (`2`) -> Not visited. Add to queue & visited.

  - Parent (`3`) -> Not visited. Add to queue & visited.

- Queue becomes `[6, 2, 3]`. `currentDistance` increments to `1`.

 

### Step 3: Distance = 1

- Queue = `[6, 2, 3]`

- `currentDistance != k (1 != 2)`.

- Poll `6`: No unvisited neighbors.

- Poll `2`: Neighbors are `7`, `4`, and parent `5` (visited). Add `7` and `4` to queue & visited.

- Poll `3`: Neighbors are `5` (visited), `1`, and parent `null`. Add `1` to queue & visited.

- Queue becomes `[7, 4, 1]`. `currentDistance` increments to `2`.

 

### Step 4: Distance = 2

- Queue = `[7, 4, 1]`

- `currentDistance == k (2 == 2)`.

- Match detected! Collect all items inside the queue: `[7, 4, 1]`.

- Return result.

 

---

 

## Visual Representation

 

```text

                  3 (Dist: 1)

                 / \

   (Target) 5 (0)   1 (Dist: 2)  =============> Visible Output Node!

             / \   / \

  (Dist: 1) 6   2 0   8

               / \

   (Dist: 2)  7   4 (Dist: 2)  ===============> Visible Output Nodes!

```

 

---

 

## Edge Cases

 

### K = 0

```text

Input: k = 0, target = 5

Output: [5] (Distance 0 from target is always the target itself)

```

 

### Target is Root Node

```text

Input: target = 3, k = 1

Output: [5, 1] (Only moves downward since parent is null)

```

 

### K exceeds Max Leaf Depth

```text

Input: target = 7, k = 5

Output: [] (No structural nodes exist at that length boundary)

```

 

---

 

## Time Complexity

 

```text

O(N)

```

Where `N` is the total number of nodes in the binary tree. We visit each node once during the parent-mapping phase, and at most once during the radial BFS graph traversal phase.

 

---

 

## Space Complexity

 

```text

O(N)

```

The parent map, visited set, and BFS queue each track up to `N` elements in their structures in the worst-case scenario.

 

---

 

## Interview Follow-Up Questions

 

### Q1. Can this problem be solved without explicitly creating a parent map?

Yes, using pure DFS recursion with a backtracking layout. The recursive function searches for the target node. Once found, it returns its distance back to its parents. The parent nodes then use this distance offset to calculate how far down their *opposite* subtree they need to look to find nodes exactly `k` distance away. However, the graph-conversion approach using BFS is generally considered much less error-prone during a live whiteboard interview.

 

### Q2. How would you handle a problem variant where edges have varying weights?

If the edges had different weights instead of a fixed unit of `1`, standard BFS would no longer work. We would treat the parent-mapped tree as a general weighted graph and replace our FIFO queue with a Min-Heap / Priority Queue, applying **Dijkstra's Algorithm** to find all nodes matching an exact shortest-path distance metric.

 

---

 

## Important Interview Takeaways

 

- ✅ Use a preliminary traversal to store parent pointers to unlock upward navigation.

- ✅ Treat the final structure as an unweighted, undirected graph.

- ✅ Implement standard level-order BFS extending outward from the target location.

- ✅ Time Complexity = O(N)

- ✅ Space Complexity = O(N)

- ✅ Frequently Asked In:

  - Amazon

  - Meta (Facebook)

  - Google

  - Microsoft

- ✅ Related Problems:

  - Amount of Time for Binary Tree to Be Infected

  - All Nodes Distance K in Graph

  - Subtree of Another Tree
