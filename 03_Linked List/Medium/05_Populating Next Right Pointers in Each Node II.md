# Populating Next Right Pointers in Each Node II ([LeetCode 117](https://leetcode.com))

## Problem Statement

Given a binary tree:

```id
struct Node {
  int val;
  Node *left;
  Node *right;
  Node *next;
}
```

Populate each `next` pointer to point to its **next right node**. If there is no next right node, the `next` pointer should be set to `NULL`.

Initially, all `next` pointers are set to `NULL`.

**Constraints & Performance Targets:**
- Time Complexity: strictly bounded by **O(N)**.
- Auxiliary Space Complexity: highly optimal at **O(1)**.

---

## Examples

### Example 1

```text
Input: root = [1,2,3,4,5,null,7]
Output: [1,#,2,3,#,4,5,7,#]
```

### Explanation

```text
Given the binary tree layout:
        1 -> NULL
       / \
      2 -> 3 -> NULL
     / \    \
    4 -> 5 -> 7 -> NULL

The serialization output uses '#' to represent the completion of each horizontal level tier.
```

---

# Intuition

A standard level-order traversal (Breadth-First Search) using a `Queue` solves this problem effortlessly by linking adjacent elements at each level. However, a queue stores up to O(N) leaf nodes, which violates the strict **O(1) constant auxiliary space** constraint.

To solve this in true constant space, we can leverage the **already established `next` pointers of the parent level** to build the link chain for the child level. 
- Think of each level as a dynamically forming **Singly Linked List**.
- As we traverse the nodes of the current level horizontally using their `next` pointers, we look downward at their children (`left` and `right`).
- We can introduce a **Dummy Sentinel Node** (`dummy`) at the start of the next level. We attach a moving tracking cursor pointer (`prev`) to this dummy node.
- Every time we spot a valid child node on the layer below, we splice it into our expanding chain: `prev.next = child`, then advance `prev = prev.next`.
- Once our parent level scan hits `NULL`, the entire child level list has been successfully woven together. We shift our master parent pointer down to the head of the new level (`dummy.next`), reset our dummy links, and repeat the process layer by layer.

---

# Brute Force Solution (Queue-Based BFS)

## Idea

We use a standard level-order BFS traversal driven by an explicit `Queue` container. For each level layer, we iterate through its node count, pulling elements out and tying their `.next` fields to the next item waiting in line inside the queue.

---

## Java Code

```java
import java.util.LinkedList;
import java.util.Queue;

class Solution {
    public Node connect(Node root) {
        if (root == null) {
            return null;
        }

        Queue<Node> queue = new LinkedList<>();
        queue.offer(root);

        // Core Step: Level-order level pass sweep
        while (!queue.isEmpty()) {
            int levelSize = queue.size();
            
            for (int i = 0; i < levelSize; i++) {
                Node current = queue.poll();
                
                // If this is not the final terminal element of the layer, link it to the queue front
                if (i < levelSize - 1) {
                    current.next = queue.peek();
                }
                
                if (current.left != null) queue.offer(current.left);
                if (current.right != null) queue.offer(current.right);
            }
        }

        return root;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) because the algorithm visits every single node in the tree exactly once.
- **Space Complexity:** O(N) auxiliary space allocation penalty to hold the leaf node level layer footprints inside the queue collection on heap memory.

---

# State Definition (Optimal Approach)

To drop memory usage down to a flat O(1) floor, we track level boundaries entirely in-place by maintaining a shifting list builder framework:

```java
Node parent = root;    // Cursor pointer navigating horizontally across the current active tier
Node dummy = new Node(); // Sentinel dummy node anchoring the head of the next layer below
Node prev = dummy;       // Tracking cursor weaving node links across the next layer below
```

---

# Recurrence / Structural Level-Weaving Condition

```text
While parent != null:
    If parent.left != null  -> prev.next = parent.left,  prev = prev.next
    If parent.right != null -> prev.next = parent.right, prev = prev.next
    parent = parent.next // Slide parent horizontally
    
    If parent == null (Level Finished):
        parent = dummy.next // Drop down to the next level head
        dummy.next = null   // Reset sentinel pointer
        prev = dummy        // Reset list cursor
```

---

# Optimal Single-Pass In-Place Weaving Solution

## Idea

We use the parent tier as a horizontal track to step from left to right. As the parent slides along via its existing `next` links, it maps out its children below, attaching them to a `dummy` sentinel. When the parent hits `NULL`, the child layer is fully linked. We then drop our parent pointer down to `dummy.next` and repeat.

---

## Java Code

```java
class Solution {
    // Structural framework definition for LeetCode Node component block
    private static class Node {
        public int val;
        public Node left;
        public Node right;
        public Node next;
        public Node() {}
        public Node(int _val) { val = _val; }
    }

    public Node connect(Node root) {
        if (root == null) {
            return null;
        }

        // 'parent' acts as the horizontal cursor traversing the already connected upper level
        Node parent = root;
        
        // Sentinel placeholder node to cleanly anchor the absolute first element of the lower tier
        Node dummy = new Node(0);
        // 'prev' tracks the moving tail of our growing lower level list chain
        Node prev = dummy;

        // Vertical Loop: Iterates down the height levels of the tree structure
        while (parent != null) {
            
            // Horizontal Loop: Slides left-to-right across the active upper list tier
            while (parent != null) {
                if (parent.left != null) {
                    prev.next = parent.left; // Connect trailing list to left child
                    prev = prev.next;        // Advance lower level cursor
                }
                if (parent.right != null) {
                    prev.next = parent.right; // Connect trailing list to right child
                    prev = prev.next;         // Advance lower level cursor
                }
                
                // Move parent horizontally onto the next right-hand sibling node
                parent = parent.next;
            }

            // Phase Transition: Current level completed. Drop down to the next level head
            parent = dummy.next;
            
            // Reset the sentinel markers to isolate tracking states for the subsequent tier
            dummy.next = null; 
            prev = dummy;
        }

        return root;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The code performs a complete level-order horizontal sweep. Every single node and pointer reference field in the binary tree is processed exactly once, keeping the operational execution time bounded strictly linearly relative to the total node size N.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimized. By using the tree's pre-populated level links as a natural traversal queue, the algorithm fully drops the need for external collections or arrays, running inside an absolute constant space footprint.

---

# Key Takeaways

✅ Advanced tree transformation pattern reducing multi-dimensional graph paths to simple linear linked lists.

✅ **The Unified General Solution Invariant:** While *Populating Next Right Pointers I* assumes a perfectly complete binary tree (where children are guaranteed to exist symmetrically), this Level-Weaving Sentinel strategy handles non-complete, skewed, and radically sparse trees flawlessly. The dummy pointer naturally bridges any missing structural gaps on the lower layer.

✅ Directly drives real-world architectures like **Spatial Bounding Octree acceleration matrices** in 3D graphics rendering pipelines, breadth-first thread group message passing configurations, and hierarchical routing index lookups.

✅ Common Architectural Paradigm:

```text
Bilateral Multi-Level Leapfrogging via Stateful Sentinel-Chain Restructurings
```

### Similar Problems

- Populating Next Right Pointers in Each Node ([LeetCode 116](https://leetcode.com))
- Binary Tree Level Order Traversal ([LeetCode 102](https://leetcode.com))
- Design Linked List ([LeetCode 707](https://leetcode.com))
- Flatten a Multilevel Doubly Linked List ([LeetCode 430](https://leetcode.com))
