# HashMap in Java

## What is HashMap?

`HashMap` is a core collection class in Java that implements the `Map` interface. It belongs to the package:

```java
java.util.HashMap
```

It is a hash table-based data structure used to store data in **Key-Value pairs**. A `HashMap` permits `null` values and a single `null` key, makes **no guarantees regarding the iteration order** of its entries, and guarantees that keys remain strictly unique.

---

# Why Do We Need HashMap?

While linear structures like lists or arrays let you store associations, searching for a specific key-value record creates expensive processing bottlenecks:
- **The Linear Association Penalty:** Finding a value associated with a key inside an array of pairs forces an $O(n)$ loop traversal from the beginning, checking matching criteria item by item.
- **Constant Time Mapping:** If an algorithm requires real-time frequency distribution mapping, caching, or instant record lookups, computing this binding must bypass the latency of array shifts or linear loops.

`HashMap` completely resolves this. By applying a mechanism called **Hashing**, it converts keys into optimized bucket array coordinates, allowing you to insert, delete, and retrieve key-value pairs in **constant $O(1)$ time**.

---

# HashMap vs LinkedHashMap vs TreeMap

| Feature | `HashMap` | `LinkedHashMap` | `TreeMap` |
| :--- | :--- | :--- | :--- |
| **Underlying Layout**| Hash Table (Bucket array) | Hash Table + Doubly-linked list | Red-Black Tree (Balanced BST) |
| **Iteration Order** | **Unordered / Chaotic** | **Insertion Order** preserved | **Sorted Order** (Keys) |
| **Time Complexity** | **Fastest: O(1)** constant time | O(1) constant time (slight link overhead) | O(log n) logarithmic time |
| **Null Keys/Values** | Allows 1 `null` key, multiple `null` values | Allows 1 `null` key, multiple `null` values | **No null keys allowed** |
| **Memory Overhead** | Low (Bucket arrays + Nodes) | Higher (Bidirectional reference links) | Medium (Tree node references) |

---

# Internals & Core Architecture Mechanics

A `HashMap` acts internally as an array of buckets, where each bucket holds a linked list (or a balanced tree) of node entries.

```text
Node Array (Buckets)
[0] ---> Node(Key: "A", Val: 10, Hash) ---> Node(Key: "E", Val: 40) [Collision list]
[1] ---> null
[2] ---> Node(Key: "B", Val: 20)
[3] ---> Node(Key: "C", Val: 30)
```

An internal node object (historically `Entry<K,V>`, modeled as `Node<K,V>`) encapsulates four core properties:
- `int hash`: The calculated hash code of the key.
- `K key`: The actual storage key object pointer.
- `V value`: The associated data payload pointer.
- `Node<K,V> next`: Reference link to the succeeding node in the case of a collision.

---

## The Core Operational Cycle

### 1. Bucket Index Calculation
When you call `map.put(key, value)` or `map.get(key)`, the `HashMap` routes the key through a supplemental secondary hashing function to distribute values evenly and minimize collisions. It calculates the target bucket slot using bitwise AND partitioning matching the array's maximum capacity boundary:
```text
index = hash(key) & (capacity - 1)
```

### 2. Handling Collisions
When two completely different keys calculate into the exact same bucket index, a **Hash Collision** occurs. `HashMap` resolves this using **Separate Chaining**:
- **Before Java 8:** The new node is appended to the bucket's linked list. If many collisions collapse into the same bucket, lookups degrade to an inefficient linear $O(n)$ time.
- **Java 8+ Optimization (Treeification):** If a bucket's linked list length crosses a threshold of **8 elements** (`TREEIFY_THRESHOLD`) and the overall map capacity is at least **64**, the linked list converts dynamically into a self-balancing **Red-Black Tree**. This guarantees that even under catastrophic hash collisions, lookup performance remains bounded at an optimized **$O(\log n)$** runtime.

### 3. Resizing & The Load Factor
A `HashMap` initializes with a default base capacity of **16** and a default **Load Factor of 0.75**. The load factor defines when the map will automatically scale:
```text
Threshold = Capacity * Load Factor  (16 * 0.75 = 12 entries)
```
When the total count of key entries crosses this threshold, the map triggers an internal resizing routine (`resize()`). It **doubles the array capacity** (100% growth) and undergoes an internal **Re-hashing** pass where all existing nodes are re-distributed into their new bucket index slots.

---

# Core HashMap Syntax & Operational Workflows

## Initialization & Basic Association

```java
// Always declare using the Map interface reference type for clean abstraction decoupling
Map<String, Integer> inventory = new HashMap<>();

inventory.put("Apples", 50);
inventory.put("Bananas", 30);
inventory.put("Apples", 70); // Overwrites the existing key "Apples" with the new value 70

System.out.println(inventory.get("Apples"));   // Instant lookup: 70
System.out.println(inventory.containsKey("Oranges")); // Instant check: false
```

---

## Elegant Default & Frequency Mapping Patterns

```java
// Best practice pattern for calculating frequencies or logging counts in interviews
Map<Character, Integer> counts = new HashMap<>();
String text = "leetcode";

for (char c : text.toCharArray()) {
    // getOrDefault(key, defaultValue) avoids explicit null check branching logic
    counts.put(c, counts.getOrDefault(c, 0) + 1);
}
System.out.println(counts); // Outputs calculated frequencies
```

---

# Time Complexity Metrics

| Operation | Time Complexity | Space Complexity | Technical Reason |
| :--- | :--- | :--- | :--- |
| **`map.put(key, val)`**| **Amortized O(1)** | **O(1)** | Hashes the key, finds the bucket index, and places the node entry instantly. Incurs an rare $O(n)$ cost during re-hashing resizing. |
| **`map.get(key)`** | **O(1)** | **O(1)** | Directly hashes to the exact target bucket cell coordinate, bypassing long sequential search lines. |
| **`map.containsKey(k)`**| **O(1)** | **O(1)** | Directly tests the existence of the hashed bucket slot coordinates. |
| **`map.remove(key)`** | **O(1)** | **O(1)** | Evaluates the hash index coordinates and un-links the targeted entry node in-place. |
| **`map.size()`** | **O(1)** | **O(1)** | Directly reads an internally cached local tracking field. |

---

# Key Interview Takeaways

```text
The Custom Key Contract -> If you use a custom object class as a HashMap key, you MUST override both hashCode() and equals() inside that class. If you don't, identical logical instances will yield different reference hash codes, breaking data retrieval pathways completely.

Immutable Key Selection -> Always prefer immutable classes (like String or Integer) as HashMap keys. If a key's fields mutate after insertion, its calculated hashCode changes, permanently marooning the entry node inside the wrong bucket slot.

Initial Capacity Pre-Sizing -> If your algorithm expects to store 10,000 records, initialize the map with an explicit capacity (e.g., new HashMap<>(14000)) to completely eliminate expensive intermediate array doubling and re-hashing loops.

Iteration Ordering Warning -> Never assume any specific ordering when iterating through map keys via keySet() or values() collections. The output follows hash positioning rules, which shuffle dynamically whenever a map resize occurs.
```

`HashMap` is arguably the single most critical structural container for eliminating costly time-complexity penalties in coding evaluations. It is indispensable for writing optimized, fast-lookup routines in problems involving:

```text
Two-Sum Complement Tracking Problems (Isolating variable matching gaps)
Dynamic Frequency Distribution & Character Counting Maps
Caching Memoization state tables inside top-down Dynamic Programming models
Custom Key-Value structures inside Sweep Line Event Tracking pipelines
Sub-Interval boundary logging filters across complex sliding window tracks
```

<FollowUp>
If you want to continue mapping out these vital interview structures, let me know:
* Would you like to see how to solve **Two Sum (1)** or **Group Anagrams (49)** using a `HashMap`?
* Do you want a raw markdown template explaining **how to implement a basic custom HashMap from scratch**?
* Or should we move on to an ordered collection variant like **TreeMap**?

Tell me which **key terms** or design rules you want to structure next!
</FollowUp>```
