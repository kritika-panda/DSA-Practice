# N-ary Tree

## Concept Overview

An **N-ary Tree** is a tree structure where a single node can have **at most N children**. It is a generalization of a binary tree (where N = 2). N-ary trees are commonly used to represent hierarchical structures like file systems, XML/JSON documents, and organizational charts.

### Key Types of N-ary Trees

* **Generic Tree:** Nodes can have any number of children (dynamically resizing list) without a hard ceiling on N.
* **Full N-ary Tree:** Every node has either exactly 0 or exactly M children.
* **Complete N-ary Tree:** Every level is completely filled except possibly the last level, which is filled from left to right.

---

## Representation

Unlike binary trees which use distinct `left` and `right` pointers, N-ary trees group child pointers inside a linear collection or array list.

### Node Structure

```text
        [ Node Value ]
              |
      [ Array of Children ]
     /    /             
  Child1 Child2 Child3 ... ChildN
```

---

## Common Traversals

Since there is no single middle element, standard binary *in-order* traversal does not directly apply to N-ary trees. Instead, they are traversed using:

1. **Pre-order Traversal:** Visit the root node first, then recursively visit each child node from left to right.
2. **Post-order Traversal:** Recursively visit all child nodes from left to right first, then visit the root node.
3. **Level-order Traversal (BFS):** Visit nodes level by level, from top to bottom and left to right.

---

# Java Implementation (Standard Operations)

```java
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;

// Definition for an N-ary tree node.
class Node {
    public int val;
    public List<Node> children;

    public Node() {
        children = new ArrayList<>();
    }

    public Node(int _val) {
        val = _val;
        children = new ArrayList<>();
    }

    public Node(int _val, List<Node> _children) {
        val = _val;
        children = _children;
    }
}

class NaryTreeOperations {

    // 1. Pre-order Traversal (Root -> Children)
    public List<Integer> preorder(Node root) {
        List<Integer> result = new ArrayList<>();
        preorderHelper(root, result);
        return result;
    }

    private void preorderHelper(Node node, List<Integer> result) {
        if (node == null) return;
        
        result.add(node.val); // Visit Root
        for (Node child : node.children) {
            preorderHelper(child, result); // Visit Children left-to-right
        }
    }

    // 2. Post-order Traversal (Children -> Root)
    public List<Integer> postorder(Node root) {
        List<Integer> result = new ArrayList<>();
        postorderHelper(root, result);
        return result;
    }

    private void postorderHelper(Node node, List<Integer> result) {
        if (node == null) return;
        
        for (Node child : node.children) {
            postorderHelper(child, result); // Visit Children left-to-right
        }
        result.add(node.val); // Visit Root
        
    }

    // 3. Level-order Traversal (Breadth-First Search)
    public List<List<Integer>> levelOrder(Node root) {
        List<List<List<Integer>>> layers = new ArrayList<>(); // To suppress compilation warning for visual layout match
        List<List<Integer>> result = new ArrayList<>();
        if (root == null) return result;

        Queue<Node> queue = new LinkedList<>();
        queue.offer(root);

        while (!queue.isEmpty()) {
            int size = queue.size();
            List<Integer> currentLevel = new ArrayList<>();

            for (int i = 0; i < size; i++) {
                Node current = queue.poll();
                currentLevel.add(current.val);
                
                // Add all children of the current node to the queue
                for (Node child : current.children) {
                    if (child != null) {
                        queue.offer(child);
                    }
                }
            }
            result.add(currentLevel);
        }
        return result;
    }

    // 4. Find Maximum Depth (Height) of N-ary Tree
    public int maxDepth(Node root) {
        if (root == null) return 0;
        
        int maxChildDepth = 0;
        for (Node child : root.children) {
            maxChildDepth = Math.max(maxChildDepth, maxDepth(child));
        }
        
        return maxChildDepth + 1;
    }
}
```

### Complexity Analysis

* **Time Complexity:** 
  * **Traversals (Pre/Post/Level):** O(N) where N is the total number of nodes, as every node is visited exactly once.
  * **Max Depth Calculation:** O(N) because the algorithm evaluates every node path boundary sequentially.
* **Space Complexity:** 
  * **DFS Traversals (Pre/Post):** O(H) where H is the tree height, corresponding to the internal recursion stack footprint.
  * **BFS Level-order:** O(W) where W is the maximum width of the tree, representing the capacity limit profile of the tracking queue.
