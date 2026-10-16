# LFU Cache ([LeetCode 460](https://leetcode.com))

## Problem Statement

Design and implement a data structure for a **Least Frequently Used (LFU)** cache.

Implement the `LFUCache` class:
1. `LFUCache(int capacity)` Initializes the object with the `capacity` of the data structure.
2. `int get(int key)` Returns the value of the `key` if the key exists in the cache. Otherwise, returns `-1`.
3. `void put(int key, int value)` Update the value of the `key` if present, or inserts the `key` if not already present. When the cache reaches its `capacity`, it should **evict** the least frequently used key before inserting a new item. For this problem, when there is a **tie** (i.e., two or more keys with the same frequency), the **least recently used** key should be evicted.

The functions `get` and `put` must each run in **O(1)** average time complexity.

---

## Example Execution Trace

Consider an `LFUCache` initialized with a `capacity` of 2.

Our internal frequencies map tracks independent doubly linked lists. The items in each list can be visualized from left (Most Recently Used / MRU) to right (Least Recently Used / LRU) as `[Head] <-> ... <-> [Tail]`.

| Operation | Arguments | Cache State Map | Frequencies Map (Freq -> Node Order: MRU -> LRU) | Min Freq | Output | Explanation |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `LFUCache` | `2` | `{}` | `{}` | `0` | `null` | Cache initialized with capacity 2. |
| `put` | `1, 1` | `{1:Node(1)}` | `1 -> [1:1]` | `1` | `null` | Key 1 inserted with freq 1. `minFreq` becomes 1. |
| `put` | `2, 2` | `{1:Node(1), 2:Node(2)}` | `1 -> [2:2] <-> [1:1]` | `1` | `null` | Key 2 inserted with freq 1. Placed at the front of the freq 1 list as MRU. |
| `get` | `1` | `{1:Node(1), 2:Node(2)}` | `1 -> [2:2]`, `2 -> [1:1]` | `1` | `1` | Key 1 accessed. Freq increments to 2. Moved to freq 2 list. `minFreq` stays 1. |
| `put` | `3, 3` | `{1:Node(1), 3:Node(3)}` | `1 -> [3:3]`, `2 -> [1:1]` | `1` | `null` | Key 3 added. Capacity overflow (3 > 2). Freq 1 list tail LRU Node(2) is evicted. |
| `get` | `2` | `{1:Node(1), 3:Node(3)}` | `1 -> [3:3]`, `2 -> [1:1]` | `1` | `-1` | Key 2 lookup failed because it was previously evicted. |
| `get` | `3` | `{1:Node(1), 3:Node(3)}` | `2 -> [3:3] <-> [1:1]` | `2` | `3` | Key 3 accessed. Freq increments to 2. Freq 1 list is now empty, so `minFreq` advances to 2. |
| `put` | `4, 4` | `{3:Node(3), 4:Node(4)}` | `1 -> [4:4]`, `2 -> [3:3]` | `1` | `null` | Key 4 added. Capacity overflow (3 > 2). `minFreq` is 2; tail LRU Node(1) in freq 2 list is evicted. |

---

# Intuition

To satisfy an average **O(1) time complexity** for an LFU cache, we must simultaneously coordinate two criteria:
1. **Frequency Indexing:** Quickly isolate items based on their access frequency.
2. **Recency Preservation:** Handle ties cleanly by identifying which element with the matching target frequency was accessed the least recently.

We can achieve this by pairing a master **Hash Map** directory tracking all active node keys with a secondary **Frequency Hash Map** mapping `Frequency -> Custom Doubly Linked List`:
- Each distinct frequency layer manages its own internal timeline using an independent Doubly Linked List containing a sentinel dummy `head` and `tail`.
- The main `cacheMap` stores a direct reference pointer straight to the target `Node`.
- When an item is read or updated, its frequency increases by 1. We lookup its node pointer, splice it out of its current frequency's list chain in O(1) time, and insert it at the head of its *new* frequency's list layer.
- We maintain a global scalar register pointer `minFreq` to keep track of the absolute lowest frequency layer in the system. If the cache overflows, we go straight to `frequenciesMap.get(minFreq).tail.prev` to evict the LRU node from that layer instantly.

---

# Brute Force Solution

## Idea

We maintain a standard collection map to store our cache payload and a secondary map or object wrapper tracking individual numerical frequency hits. Each time `get()` or `put()` triggers, we update hit values. When an eviction boundary cuts across a full cache capacity overflow, we iterate linearly through all records to scan for the lowest frequency parameter value, picking the oldest timestamp entry if matching bounds collide.

---

## Java Code

```java
import java.util.HashMap;
import java.util.Map;

class LFUCache {
    private static class CacheNode {
        int key, value, frequency, timestamp;
        CacheNode(int key, int value, int frequency, int timestamp) {
            this.key = key;
            this.value = value;
            this.frequency = frequency;
            this.timestamp = timestamp;
        }
    }

    private final Map<Integer, CacheNode> cache;
    private final int capacity;
    private int globalTimestamp;

    public LFUCache(int capacity) {
        this.cache = new HashMap<>();
        this.capacity = capacity;
        this.globalTimestamp = 0;
    }
    
    public int get(int key) {
        if (!cache.containsKey(key)) return -1;
        CacheNode node = cache.get(key);
        node.frequency++;
        node.timestamp = ++globalTimestamp;
        return node.value;
    }
    
    public void put(int key, int value) {
        if (capacity == 0) return;
        globalTimestamp++;
        
        if (cache.containsKey(key)) {
            CacheNode node = cache.get(key);
            node.value = value;
            node.frequency++;
            node.timestamp = globalTimestamp;
            return;
        }
        
        if (cache.size() == capacity) {
            int minFreq = Integer.MAX_VALUE;
            int oldestTimestamp = Integer.MAX_VALUE;
            int keyToEvict = -1;
            
            // Linear loop sweep searching for the optimal LFU/LRU combination candidate
            for (CacheNode node : cache.values()) {
                if (node.frequency < minFreq) {
                    minFreq = node.frequency;
                    oldestTimestamp = node.timestamp;
                    keyToEvict = node.key;
                } else if (node.frequency == minFreq && node.timestamp < oldestTimestamp) {
                    oldestTimestamp = node.timestamp;
                    keyToEvict = node.key;
                }
            }
            cache.remove(keyToEvict);
        }
        
        cache.put(key, new CacheNode(key, value, 1, globalTimestamp));
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N) worst-case per individual `put()` call due to the linear loops tracking and sweeping all values to find eviction constraints.
- **Space Complexity:** O(Capacity) to maintain records concurrently within the cache map directory.

---

# State Definition (Optimal Approach)

To lock our operational transactions securely to an O(1) constant tier, we organize our system tracking states using linked data structures:

```java
class Node {
    int key, val, freq;
    Node prev, next;
}

class DoublyLinkedList {
    Node head = new Node(), tail = new Node(); // Boundary placeholders
    int size = 0;
}

Map<Integer, Node> cacheMap = new HashMap<>(); // Directory mapping key -> Node
Map<Integer, DoublyLinkedList> freqMap = new HashMap<>(); // Frequency mapping freq -> List
int minFreq = 0; // Globally tracks the minimum frequency layer
```

---

# Recurrence / Structural State Condition

```text
On updateFrequency(Node target):
    int currentFreq = target.freq
    freqMap.get(currentFreq).remove(target)
    if (currentFreq == minFreq && freqMap.get(currentFreq).size == 0) minFreq++
    
    target.freq++
    freqMap.computeIfAbsent(target.freq, k -> new DoublyLinkedList()).insertAtHead(target)
```

---

# Optimal Dual-Map + Linked-List Layer Solution

## Idea

We encapsulate a standard `DoublyLinkedList` constructor class. When keys are touched, we increment their internal reference counts, slice them out of their old frequency list, and advance them to the head of the succeeding frequency layer. If the old layer runs completely empty and matches our `minFreq` index pointer, we increment `minFreq`. Evictions extract the tail node directly from the list anchored at `freqMap.get(minFreq)`.

---

## Java Code

```java
import java.util.HashMap;
import java.util.Map;

class LFUCache {
    // Internal blueprint defining our doubly linked list nodes
    private static class Node {
        int key, val, freq;
        Node prev, next;

        Node(int key, int val) {
            this.key = key;
            this.val = val;
            this.freq = 1; // All brand new entries start with an initial frequency hit of 1
        }
    }

    // Custom helper class organizing list mutations per frequency layer
    private static class DoublyLinkedList {
        Node head, tail;
        int size;

        DoublyLinkedList() {
            this.head = new Node(-1, -1);
            this.tail = new Node(-1, -1);
            head.next = tail;
            tail.prev = head;
            this.size = 0;
        }

        void addFirst(Node node) {
            node.next = head.next;
            node.next.prev = node;
            head.next = node;
            node.prev = head;
            size++;
        }

        void remove(Node node) {
            node.prev.next = node.next;
            node.next.prev = node.prev;
            size--;
        }

        Node removeLast() {
            if (size == 0) return null;
            Node lruNode = tail.prev;
            remove(lruNode);
            return lruNode;
        }
    }

    private final Map<Integer, Node> cacheMap;
    private final Map<Integer, DoublyLinkedList> freqMap;
    private final int capacity;
    private int minFreq;

    public LFUCache(int capacity) {
        this.capacity = capacity;
        this.cacheMap = new HashMap<>();
    this.freqMap = new HashMap<>();
    this.minFreq = 0;
    }
    public int get(int key) {
    if (!cacheMap.containsKey(key)) {
    return -1;
    }
    Node node = cacheMap.get(key);
    updateFrequency(node);
    return node.val;
    }
    public void put(int key, int value) {
    if (capacity <= 0) return;
    if (cacheMap.containsKey(key)) {
    Node node = cacheMap.get(key);
    node.val = value;
    updateFrequency(node);
    return;
    }
    if (cacheMap.size() >= capacity) {
    // Cache full: evict the absolute LRU element from the minimum frequency list layer
    DoublyLinkedList minList = freqMap.get(minFreq);
    Node evictedNode = minList.removeLast();
    if (evictedNode != null) {
    cacheMap.remove(evictedNode.key);
    }
    }
    // Setup and index a fresh new node layer
    Node newNode = new Node(key, value);
    cacheMap.put(key, newNode);
    minFreq = 1; // Reset global minFreq register back to baseline floor
    freqMap.computeIfAbsent(1, k -> new DoublyLinkedList()).addFirst(newNode);
    }
    // Handles moving a node forward when its hit profile updates
    private void updateFrequency(Node node) {
    int oldFreq = node.freq;
    DoublyLinkedList oldList = freqMap.get(oldFreq);
    oldList.remove(node);
    // If the current minimum frequency list finishes empty, increment the tracking pointer
    if (oldFreq == minFreq && oldList.size == 0) {
    minFreq++;
    }
    node.freq++;
    freqMap.computeIfAbsent(node.freq, k -> new DoublyLinkedList()).addFirst(node);
    }
    }
```

```text
Complexity Analysis


Time Complexity


Operation	Time Complexity	Explanation
get(key)	O(1)	Direct map key directory reading matching standalone node pointer link mutations.
put(key, val)	O(1)	Executes inside predictable hash assignments and constant-time list updates.


Space Complexity

text Space Complexity: O(Capacity) 
Explanation: Memory usage is bounded strictly by the configured element threshold limit, maintaining at most O(Capacity) entries across both internal map tables and custom list nodes concurrently.

Key Takeaways

✅ Standard system-level cache configuration combining dynamic usage metrics with recency checks.
✅ The Modulo Frequency Reset Rule: When a completely new element is introduced via put(), minFreq must be reset straight back to 1, regardless of how high the previous minimum frequency had climbed. This is because every new item defaults to an initial baseline hit count of 1.
✅ Natively runs inside performance-critical software architectures like distributed web proxy buffer pools, high-speed database query planners caching heavy transaction logs, memory page virtualization managers, and network data packet storage layers.
✅ Common Architectural Paradigm:
text Layered Multi-Dimensional Frequency Cascades with Recency Linked Arrays 

Similar Problems

• LRU Cache (LeetCode 146)
• Design In-Memory File System (LeetCode 588)
• Design Skiplist (LeetCode 1206)
• Design Phone Directory (LeetCode 379)
```
