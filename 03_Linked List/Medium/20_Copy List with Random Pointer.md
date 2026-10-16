# Copy List with Random Pointer ([LeetCode 138](https://leetcode.com))

## Problem Statement

A linked list of length `n` is given such that each node contains an additional random pointer, which could point to any node in the list, or `null`.

Construct a **deep copy** of the list. The deep copy should consist of exactly `n` brand new nodes, where each new node has its value set to the value of its corresponding original node. Both the `next` and `random` pointers of the new nodes should point to new nodes in the copied list such that the pointers in the original list and copied list represent the same list state. **None of the pointers in the new list should point to nodes in the original list**.

For example, if there are two nodes `X` and `Y` in the original list, where `X.random --> Y`, then for the corresponding two nodes `x` and `y` in the copied list, `x.random --> y`.

Return *the head of the copied linked list*.

---

## Examples

### Example 1

```text
Input: head = [[7,null],[13,0],[11,4],[10,2],[1,0]]
Output: [[7,null],[13,0],[11,4],[10,2],[1,0]]
```

### Explanation

```text
The input/output format is represented as [val, random_index].
- Node 0 (7) has a random pointer pointing to null.
- Node 1 (13) has a random pointer pointing to Node 0.
- Node 2 (11) has a random pointer pointing to Node 4.
The cloned list must mirror this exact structural topology entirely using brand-new heap objects.
```

---

# Intuition

A deep copy of a standard linked list is trivial because we can just parse from front to back, allocating new nodes sequentially. However, the presence of a **random pointer** introduces a major circular graph dependency puzzle: a node's random pointer can point backwards to a node we already copied, or forwards to a node that hasn't even been created yet.

We can solve this problem using two primary strategies:
1. **Hash Map Lookups:** We can use an external `HashMap<Node, Node>` to store a direct translation mapping from `Original Node -> Cloned Node`. We make an initial pass to instantiate all new node objects, and then a secondary pass to stitch together their `next` and `random` pointers by querying the map directory.
2. **In-Place Interleaving Interweaving Pattern:** We can fully eliminate the $O(N)$ auxiliary space penalty of a hash map by weaving cloned nodes directly into the original list structure itself! 
   - **Phase 1 (Interweave):** Create a copy of each node and insert it right behind its original counterpart (`Orig1 -> Clone1 -> Orig2 -> Clone2 -> null`).
   - **Phase 2 (Map Random Pointers):** Because the copy node is always positioned exactly one step ahead of its original parent, we can copy random pointers easily: `orig.next.random = orig.random.next`.
   - **Phase 3 (Unweave):** Separate the interleaved chains back out to completely isolate the original list and the brand-new cloned list.

---

# Brute Force Solution (HashMap Directory Mapping)

## Idea

We pass through the array sequentially to allocate a new standalone clone node for every original item we find, logging the references inside a master `HashMap`. We then loop through a second time, pulling cloned targets out of the directory to tie up all the loose `.next` and `.random` fields.

---

## Java Code

```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    public Node copyRandomList(Node head) {
        if (head == null) return null;

        Map<Node, Node> map = new HashMap<>();
        Node current = head;

        // Step 1: Create all clone node shells and store them in the directory map
        while (current != null) {
            map.put(current, new Node(current.val));
            current = current.next;
        }

        current = head;
        // Step 2: Stitch together reference links by querying map keys
        while (current != null) {
            map.get(current).next = map.get(current.next);
            map.get(current).random = map.get(current.random);
            current = current.next;
        }

        return map.get(head);
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** $O(N)$ because the code performs two separate clean linear passes.
- **Space Complexity:** $O(N)$ auxiliary storage memory tax to map reference pointers inside the `HashMap`.

---

# State Definition (Optimal In-Place Approach)

To drop the memory cost down to a true constant floor, we track state transformations by restructuring object links directly on the heap:

```java
Node current = head; // Parsing cursor navigating reference hops
```

---

# Recurrence / Structural Interleaving Condition

```text
1. Interweave: While current != null:
                   clone = new Node(current.val)
                   clone.next = current.next, current.next = clone
                   current = clone.next

2. Randomize:  While current != null:
                   if current.random != null -> current.next.random = current.random.next
                   current = current.next.next

3. Unweave:    While current != null:
                   nextOriginal = current.next.next
                   clone = current.next
                   current.next = nextOriginal
                   if nextOriginal != null -> clone.next = nextOriginal.next
                   current = nextOriginal
```

---

# Optimal Three-Phase In-Place Interleaving Solution

## Idea

We clone nodes and splice them directly inside the original list chain. We then sweep across the modified list to link the cloned random pointers using their local neighbor offsets. Finally, we unweave the two intertwined chains to cleanly isolate the final deep copy list.

---

## Java Code

```java
class Solution {
    // Structural framework definition for LeetCode Node component block
    private static class Node {
        int val;
        Node next;
        Node random;

        public Node(int val) {
            this.val = val;
            this.next = null;
            this.random = null;
        }
    }

    public Node copyRandomList(Node head) {
        if (head == null) {
            return null;
        }

        Node current = head;

        // Phase 1: Interweave cloned nodes directly behind their original parents
        while (current != null) {
            Node clone = new Node(current.val);
            clone.next = current.next; // Link clone forward to original's successor
            current.next = clone;      // Link original forward to clone
            
            current = clone.next;      // Advance cursor past clone to next original node
        }

        current = head;
        // Phase 2: Copy random pointers using interleaved neighbor offsets
        while (current != null) {
            if (current.random != null) {
                // The original's clone is at current.next
                // The random's clone is at current.random.next
                current.next.random = current.random.next;
            }
            current = current.next.next; // Advance by 2 steps to hit the next original node
        }

        current = head;
        Node dummyCloneHead = new Node(0);
        Node cloneTail = dummyCloneHead;

        // Phase 3: Unweave and isolate the intertwined list tracks
        while (current != null) {
            Node nextOriginal = current.next.next; // Cache lookahead original node
            Node cloneNode = current.next;          // Isolate clone node reference

            // Stitch the clone list together
            cloneTail.next = cloneNode;
            cloneTail = cloneNode;

            // Restore original list forward pointer links
            current.next = nextOriginal;
            
            current = nextOriginal; // Advance cursor to next original segment
        }

        return dummyCloneHead.next; // Return the head of the isolated deep copy list
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The algorithm performs exactly three sequential passes over the nodes: Phase 1 interweaves nodes, Phase 2 wires up random pointers, and Phase 3 unweaves the lists. Since each pass runs strictly linearly without any nested backtracking, total time scales inside true linear boundaries.

---

### Space Complexity

```text
Space Complexity: O(1) auxiliary space
```
*Explanation:* Highly optimal. Excluding the memory required to instantiate the new output clone nodes themselves (which is an inherent requirement of any deep copy task), the algorithm operates inside a completely flat constant space footprint by managing all pointer conversions using local variable registers.

---

# Key Takeaways

✅ Advanced data structure cloning template demonstrating high-tier pointer manipulation.

✅ **The Random Pointer Lookup Offset Rule:** When lists are interleaved, the copy of an arbitrary node `X` is *always* located at `X.next`. This structural invariant removes the need to use an external hash directory lookup, allowing you to establish arbitrary references in constant time via simple relative node steps (`current.next.random = current.random.next`).

✅ Directly drives real-world architectures like **object graph serialization tools** (deep copying nested object topologies with cyclic references), virtual machine memory cloning, compiler abstract syntax tree replication blocks, and database query cache snapshot generation routines.

✅ Common Architectural Paradigm:

```text
Symmetric Link Interleaving with Phase-Shifted Graph Reference Unweavings
```

