# LRU Cache ([LeetCode 146](https://leetcode.com))

## Problem Statement

Design a data structure that follows the constraints of a **Least Recently Used (LRU) Cache**.

Implement the `LRUCache` class:
1. `LRUCache(int capacity)` Initializes the LRU cache with **positive** size `capacity`.
2. `int get(int key)` Returns the value of the `key` if the key exists, otherwise return `-1`.
3. `void put(int key, int value)` Update the value of the `key` if the `key` exists. Otherwise, add the `key-value` pair to the cache. If the number of keys exceeds the `capacity` from this operation, **evict** the least recently used key.

The functions `get` and `put` must each run in **O(1)** average time complexity.

---

## Example Execution Trace

Consider an `LRUCache` initialized with a `capacity` of 2. 

Our internal doubly linked list can be visualized from left (Most Recently Used / MRU) to right (Least Recently Used / LRU) as: `[Head] <-> ... <-> [Tail]`.

| Operation | Arguments | Cache Map State | Doubly Linked List Order (MRU -> LRU) | Output | Explanation |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `LRUCache` | `2` | `{}` | `[Head] <-> [Tail]` | `null` | Cache initialized with capacity 2. Sentinel links configured. |
| `put` | `1, 1` | `{1:Node(1)}` | `[Head] <-> [1:1] <-> [Tail]` | `null` | Key 1 inserted. Node(1) linked right behind `Head`. |
| `put` | `2, 2` | `{1:Node(1), 2:Node(2)}` | `[Head] <-> [2:2] <-> [1:1] <-> [Tail]` | `null` | Key 2 inserted. Node(2) spliced at the front as the new MRU. |
| `get` | `1` | `{1:Node(1), 2:Node(2)}` | `[Head] <-> [1:1] <-> [2:2] <-> [Tail]` | `1` | Key 1 exists. Node(1) unlinked from its position and bumped to MRU. |
| `put` | `3, 3` | `{1:Node(1), 3:Node(3)}` | `[Head] <-> [3:3] <-> [1:1] <-> [Tail]` | `null` | Key 3 added. Capacity overflow (3 > 2). LRU Node(2) evicted from tail. |
| `get` | `2` | `{1:Node(1), 3:Node(3)}` | `[Head] <-> [3:3] <-> [1:1] <-> [Tail]` | `-1` | Key 2 lookup failed because it was previously evicted. |
| `put` | `4, 4` | `{3:Node(3), 4:Node(4)}` | `[Head] <-> [4:4] <-> [3:3] <-> [Tail]` | `null` | Key 4 added. Capacity overflow (3 > 2). LRU Node(1) evicted from tail. |
| `get` | `1` | `{3:Node(3), 4:Node(4)}` | `[Head] <-> [4:4] <-> [3:3] <-> [Tail]` | `-1` | Key 1 lookup failed because it was previously evicted. |
| `get` | `3` | `{3:Node(3), 4:Node(4)}` | `[Head] <-> [3:3] <-> [4:4] <-> [Tail]` | `3` | Key 3 exists. Node(3) shifted to the front of the list as MRU. |
| `get` | `4` | `{3:Node(3), 4:Node(4)}` | `[Head] <-> [4:4] <-> [3:3] <-> [Tail]` | `4` | Key 4 exists. Node(4) shifted to the front of the list as MRU. |

---

# Intuition

To achieve true **O(1) constant time** for all operations, we must satisfy two different structural requirements simultaneously:
1. **High-Speed Key Lookup:** We need to find if an element exists in our cache instantly. A standard `HashMap` fulfills this requirement perfectly.
2. **Strict Recency Ordering:** We need to constantly track which item was accessed most recently and which item sits at the tail end of utilization. A linear list works well for ordering, but standard arrays or singly linked lists require O(N) linear time to find and shift nodes around during updates.

The perfect hybrid architecture is a **Hash Map paired with a Custom Doubly Linked List**:
- The **Doubly Linked List** maintains our precise access timeline. We establish a virtual dummy `head` node (representing the most recently used boundary) and a virtual dummy `tail` node (representing the least recently used eviction zone).
- The **Hash Map** stores mappings from `key -> Node`. This gives us an instant O(1) pointer directly to the middle of our doubly linked list chain.
- Because the list nodes contain both `prev` and `next` pointers, having a reference to a node allows us to instantly splice it out of its current position and move it straight to the `head` in O(1) time whenever it is read or updated.

---

# Brute Force Solution

## Idea

We allocate a single standard linear `ArrayList` containing pair objects or an array log. Each time `get()` or `put()` triggers, we scan linearly through the list to look up the key. For updates or reads, we remove the matching entry from its current index and append it to the absolute tail of our list to record recency. If a capacity overflow triggers, we wipe the element sitting at index 0.

---

## Java Code

```java
import java.util.ArrayList;
import java.util.List;

class LRUCache {
    private static class CacheEntry {
        int key, value;
        CacheEntry(int key, int value) {
            this.key = key;
            this.value = value;
        }
    }

    private final List<CacheEntry> list;
    private final int capacity;

    public LRUCache(int capacity) {
        this.list = new ArrayList<>();
        this.capacity = capacity;
    }
    
    public int get(int key) {
        for (int i = 0; i < list.size(); i++) {
            CacheEntry entry = list.get(i);
            if (entry.key == key) {
                // Refresh recency: lift and move to the end of the array list
                list.remove(i);
                list.add(entry);
                return entry.value;
            }
        }
        return -1;
    }
    
    public void put(int key, int value) {
        for (int i = 0; i < list.size(); i++) {
            CacheEntry entry = list.get(i);
            if (entry.key == key) {
                // Key exists: update value and shift to tail
                entry.value = value;
                list.remove(i);
                list.add(entry);
                return;
            }
        }
        
        // Handle eviction boundary cuts if capacity threshold overflows
        if (list.size() == capacity) {
            list.remove(0); // Evict the oldest index item
        }
        list.add(new CacheEntry(key, value));
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) per individual operational call because array lookup sweeps and index shifts (`list.remove(i)`) require linear time.
- **Space Complexity:** O(Capacity) to maintain the items concurrently inside the cache array list container.

---

# State Definition (Optimal Approach)

To optimize our runtime tracking down to a constant O(1) floor, we wrap our structural dependencies inside a compound mapping system layer:

```java
class Node {
    int key, val;
    Node prev, next; // Dual links enabling O(1) structural splicing mutations
}

Map<Integer, Node> map = new HashMap<>(); // High-speed lookup directory maps
Node head = new Node();                   // Virtual dummy head anchor (MRU)
Node tail = new Node();                   // Virtual dummy tail anchor (LRU)
```

---

# Recurrence / Structural State Condition

```text
On get(key) or put(key, value) update:
    Node target = map.get(key)
    disconnectNode(target) -> target.prev.next = target.next; target.next.prev = target.prev;
    insertAtHead(target)   -> target.next = head.next; target.next.prev = target; head.next = target; target.prev = head;
```

---

# Optimal Hash-Map + Doubly-Linked-List Solution

## Idea

We create an explicit internal doubly-linked-list node skeleton. When a key is queried or written, we extract its node pointer from our map in O(1) time, splice it out of its current position, and link it right behind our dummy `head`. If a cache overflow happens during a write, we cleanly delete the node sitting right before our dummy `tail` from both our list chain and our map directory.

---

## Java Code

```java
import java.util.HashMap;
import java.util.Map;

class LRUCache {
    // Internal blueprint defining our high-speed doubly linked list node structure
    private static class Node {
        int key;
        int val;
        Node prev;
        Node next;
        
        Node() {}
        Node(int key, int val) {
            this.key = key;
            this.val = val;
        }
    }

    private final Map<Integer, Node> map;
    private final int capacity;
    // Sentinel boundary placeholder nodes to avoid tracking null pointer exceptions
    private final Node head;
    private final Node tail;

    public LRUCache(int capacity) {
        this.capacity = capacity;
        this.map = new HashMap<>();
        
        this.head = new Node();
        this.tail = new Node();
        
        // Tie the sentinel markers together to form an initial empty chain layout
        head.next = tail;
        tail.prev = head;
    }
    
    public int get(int key) {
        if (!map.containsKey(key)) {
            return -1;
        }
        
        Node node = map.get(key);
        // Refresh structural context: detach node and push straight to the head
        remove(node);
        insertAtHead(node);
        
        return node.val;
    }
    
    public void put(int key, int value) {
        if (map.containsKey(key)) {
            Node node = map.get(key);
            node.val = value; // Update the internal state value
            
            remove(node);
            insertAtHead(node);
        } else {
            if (map.size() == capacity) {
                // Cache capacity exceeded: evict the least recently used item (near tail)
                Node lruNode = tail.prev;
                map.remove(lruNode.key);
                remove(lruNode);
            }
            
            Node newNode = new Node(key, value);
            map.put(key, newNode);
            insertAtHead(newNode);
        }
    }

    // High-speed constant-time node removal from the doubly linked list chain
    private void remove(Node node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }

    // High-speed constant-time node insertion directly behind the dummy head anchor (MRU Zone)
    private void insertAtHead(Node node) {
    node.next = head.next;
    node.next.prev = node;
    head.next = node;
    node.prev = head;
    }
    }
```

Complexity Analysis


Time Complexity


Operation	Time Complexity	Explanation
get(key)	O(1)	Direct HashMap lookups paired with simple pointer updates that change a fixed number of link reference flags.
put(key, val)	O(1)	Runs on the identical directory lookup and pointer rearrangement strategy.


Space Complexity

text Space Complexity: O(Capacity) 
Explanation: Auxiliary heap memory usage is bounded strictly by the specified maximum capacity constraint, maintaining at most capacity + 2 list nodes and hash map entries concurrently.

Key Takeaways

✅ Standard high-tier systems-level cache optimization design template.
✅ The Importance of Sentinel Dummies: Utilizing a dummy head and tail node eliminates the need for edge-case checks (e.g., verifying if the list is completely empty or if we are deleting the absolute last element). This allows all link updates to follow the same node splicing routine.
✅ Natively forms the backbone engine behind production systems like OS virtual memory page replacement managers, database query result buffer pools, web application content distribution networks (CDNs), and file system indexing blocks.
✅ Common Architectural Paradigm:
text HashMap Directory Mapping with Concurrent Doubly-Linked Recency Chaining 

Similar Problems

• LFU Cache (LeetCode 460)
• Design In-Memory File System (LeetCode 588)
• Design Hit Counter (LeetCode 362)
• Design Circular Queue (LeetCode 622)
