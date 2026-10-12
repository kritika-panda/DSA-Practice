# LinkedHashMap in Java

## What is LinkedHashMap?

`LinkedHashMap` is a core collection class in Java that extends `HashMap` and implements the `Map` interface. It belongs to the package:

```java
java.util.LinkedHashMap
```

It is a hash table and linked list-based data structure used to store **Key-Value pairs** while strictly maintaining a predictable iteration order. By default, it preserves the **insertion order** (the sequence in which keys are inserted into the map), but it can also be configured to maintain **access order** (ordering entries from least-recently accessed to most-recently accessed). Like `HashMap`, it permits `null` values, a single `null` key, and guarantees unique keys.

---

# Why Do We Need LinkedHashMap?

While a standard `HashMap` provides exceptional constant-time $O(1)$ performance, it carries a major architectural limitation:
- **The Chaos of HashMap:** A standard `HashMap` relies entirely on hash code distributions to position elements inside its bucket array. As a result, it completely scrambles the original sequence of your entries, and this order can change arbitrarily whenever the map resizes.
- **Order-Preserving Caching:** If an algorithm requires building a structure that enforces key uniqueness *while* remembering the exact timeline of entries—such as a "Recently Viewed Items" dashboard or a Least Recently Used (LRU) Cache—a standard hash table fails.

`LinkedHashMap` completely bridges this gap. It provides the lightning-fast constant-time $O(1)$ performance of a hash table alongside the strict structural sequential predictability of a linked list.

---

# HashMap vs LinkedHashMap vs TreeMap

| Feature | `HashMap` | `LinkedHashMap` | `TreeMap` |
| :--- | :--- | :--- | :--- |
| **Underlying Layout**| Hash Table (Bucket array) | **Hash Table + Doubly-linked list** | Red-Black Tree (Balanced BST) |
| **Iteration Order** | **Unordered / Chaotic** | **Insertion Order** or **Access Order** | **Sorted Order** (Keys) |
| **Time Complexity** | **Fastest: O(1)** constant time | **O(1)** constant time (slight pointer overhead) | O(log n) logarithmic time |
| **Null Keys/Values** | Allows 1 `null` key, multiple values | Allows 1 `null` key, multiple values | **No null keys allowed** |
| **Memory Overhead** | Low (Bucket arrays + Nodes) | **Highest** (Requires extra pointer links per node) | Medium (Tree node references) |

---

# Internals & The Doubly-Linked Chain Mechanics

`LinkedHashMap` inherits the entire structural bucket-array architecture from `HashMap`. To track the sequential timeline of entries, it introduces a **global doubly-linked list** that runs through every single node entry in the map.

```text
Bucket Array: [ 0 ] ---> Entry(Key: "A") [with before/after links]
              [ 1 ] ---> null
              [ 2 ] ---> Entry(Key: "B") [with before/after links]

Global Sequential Pointer Chain:
Head <---> [Entry "A"] <---> [Entry "B"] <---> Tail
```

To support this bidirectional layout, `LinkedHashMap` overrides `HashMap`'s internal entry node class to include two additional reference pointers:

```java
// Simplified view of LinkedHashMap's internal Entry structure
static class Entry<K,V> extends HashMap.Node<K,V> {
    Entry<K,V> before; // Reference pointer to the historically preceding entry
    Entry<K,V> after;  // Reference pointer to the historically succeeding entry

    Entry(int hash, K key, V value, Node<K,V> next) {
        super(hash, key, value, next);
    }
}
```

Every time a key-value record is added, removed, or accessed, the map manipulates these `before` and `after` pointers in-place, keeping the timeline tracked perfectly in constant time without corrupting the standard hash bucket paths.

---

# Advanced Capability: Access Order & LRU Caching

A unique capability of `LinkedHashMap` is its ability to transition its sorting rules from **Insertion Order** to **Access Order**. This behavior is unlocked by utilizing a specific 3-argument constructor:

```java
public LinkedHashMap(int initialCapacity, float loadFactor, boolean accessOrder)
```

If `accessOrder` is set to `true`, every time a key is queried via `.get()` or modified via `.put()`, the `LinkedHashMap` unlinks that entry node from its current position in the doubly-linked chain and moves it straight to the absolute **tail** end. This keeps the most recently accessed items at the back, while the least recently used (LRU) elements naturally drift to the absolute **head** of the chain.

## Automatic Eviction via `removeEldestEntry`
By overriding the protected method `removeEldestEntry()`, developers can instruct the map to automatically evict the absolute oldest record at the head of the chain whenever a new item is inserted:

```java
// A simple, bulletproof LRU Cache implementation from scratch in Java
class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private final int maxCapacity;

    public LRUCache(int maxCapacity) {
        // True flags the map to activate Access Order instead of Insertion Order
        super(maxCapacity, 0.75f, true); 
        this.maxCapacity = maxCapacity;
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        // Automatically drops the least-recently used head node if capacity is breached
        return this.size() > maxCapacity; 
    }
}
```

---

# Core LinkedHashMap Syntax & Operational Workflows

## Initialization & Insertion Order Verification

```java
// Always declare using the Map interface reference type for clean abstraction decoupling
Map<String, String> cache = new LinkedHashMap<>();

cache.put("Key1", "Value1");
cache.put("Key2", "Value2");
cache.put("Key3", "Value3");
cache.put("Key1", "NewValue1"); // Updating an existing key modifies its value but preserves its insertion index position

// Iteration is guaranteed to print in the exact sequence of insertion:
for (Map.Entry<String, String> entry : cache.entrySet()) {
    System.out.println(entry.getKey() + " -> " + entry.getValue());
}
// Output: Key1 -> NewValue1, Key2 -> Value2, Key3 -> Value3
```

---

# Time Complexity Metrics

| Operation | Time Complexity | Space Complexity | Technical Reason |
| :--- | :--- | :--- | :--- |
| **`map.put(key, val)`**| **Amortized O(1)** | **O(1)** | Hashes the key to map the bucket instantly while linking the node entry at the end of the global pointer chain. |
| **`map.get(key)`** | **O(1)** | **O(1)** | Jumps straight to the exact target hash bucket coordinate. If access order is active, updates local pointers in constant time. |
| **`map.remove(key)`** | **O(1)** | **O(1)** | Unlinks the entry from its bucket and updates its adjacent pointer references (`before`/`after`). |
| **`map.entrySet()` (Iteration)** | **O(size)** | **O(1)** | **Faster than HashMap for sparse arrays** because the loop walks the direct linked list chain, ignoring empty array bucket slots entirely. |

---

# Key Interview Takeaways

```text
Sequential Uniqueness -> Choose LinkedHashMap when your algorithm must enforce absolute key uniqueness while strictly preserving the chronological order of entry modifications.

Memory Consumption Cost -> Because every entry node must store two extra pointer fields (before and after), a LinkedHashMap consumes significantly more heap memory space than a standard HashMap.

LRU Cache Foundation -> In technical interviews, if you are asked to design an LRU Cache from scratch, using LinkedHashMap combined with removeEldestEntry() provides a bulletproof, elegant solution.

Collision Optimization -> Just like HashMap since Java 8, if an individual bucket list overflows with more than 8 colliding keys, that localized bucket segment converts into a Red-Black Tree to secure O(log n) worst-case safety.
```

`LinkedHashMap` is an exceptional tool for writing time-sensitive, deterministic caching layers in tech evaluations. It is indispensable for managing sequential data pipelines in problems involving:

```text
Designing high-performance Least Recently Used (LRU) or Least Frequently Used (LFU) Caching Layers
Building unique, order-preserved history logs (e.g., tracking a user's recent search string timeline)
De-duplicating a stream of key-value maps while preserving the relative relative timeline intact
Constructing order-critical JSON/XML serialization token mapping configurations
```

<FollowUp>
If you want to continue mapping out these advanced collection layouts, let me know:
* Would you like to see how to solve **LRU Cache (146)** using a completely manual node linking strategy instead of the built-in wrapper?
* Do you want a raw markdown template for the sorted branch-balancing mechanics of **TreeMap and Red-Black Trees**?
* Or should we move onto a completely different computer science domain?

Tell me which **key terms** or design rules you want to structure next!
</FollowUp>
