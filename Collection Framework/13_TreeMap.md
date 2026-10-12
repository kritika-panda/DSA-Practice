# TreeMap in Java

## What is TreeMap?

`TreeMap` is a core collection class in Java that implements the `NavigableMap` interface, which in turn extends the `SortedMap` and `Map` interfaces. It belongs to the package:

```java
java.util.TreeMap
```

It is a data structure used to store data in **Key-Value pairs** where the keys are automatically maintained in a **sorted order**. By default, keys are arranged according to their **natural ordering** (e.g., ascending numerical order for integers, alphabetical order for strings), but you can also provide a custom sorting strategy via a `Comparator`. 

Unlike `HashMap` and `LinkedHashMap`, a `TreeMap` **does not allow null keys** (attempting to add a `null` key will instantly throw a `NullPointerException`), though it allows multiple `null` values.

---

# Why Do We Need TreeMap?

While a standard `HashMap` provides exceptional constant-time $O(1)$ performance, it completely strips away the sorted relationships between keys:
- **The Sorting Dilemma:** If you use a `HashMap` and constantly need to extract the smallest key, largest key, or keep keys sorted after additions, you must continuously call a sorting routine, which takes a costly $O(n \log n)$ time penalty.
- **Dynamic Sorted Queries:** If an algorithm requires you to dynamically answer range queries based on keys—such as finding the closest entry greater than or equal to a target timestamp—linear structures force a full scan loop.

`TreeMap` completely resolves this. It maintains its sorted structure dynamically on every insertion and deletion, allowing you to run lookups, boundary checks, and range operations in optimized **logarithmic $O(\log n)$ time**.

---

# HashMap vs LinkedHashMap vs TreeMap

| Feature | `HashMap` | `LinkedHashMap` | `TreeMap` |
| :--- | :--- | :--- | :--- |
| **Underlying Layout**| Hash Table (Bucket array) | Hash Table + Doubly-linked list | **Red-Black Tree (Balanced BST)** |
| **Iteration Order** | Unordered / Chaotic | Insertion Order or Access Order | **Sorted Order** (Keys) |
| **Time Complexity** | **Fastest: O(1)** constant time | O(1) constant time | **O(log n) logarithmic time** |
| **Null Keys/Values** | Allows 1 `null` key, multiple values | Allows 1 `null` key, multiple values | **No null keys allowed** (throws error) |
| **Memory Overhead** | Low (Bucket arrays + Nodes) | Highest (Bidirectional pointer links) | Medium (Tree node references) |

---

# Internals & The Red-Black Tree Architecture

A `TreeMap` is implemented as a self-balancing binary search tree called a **Red-Black Tree**. 

```text
Logical Balanced Tree Structure:
                 20: "Value2" (Root / Balanced)
               /    \
10: "Value1" (Left)  30: "Value3" (Right)
```

Each key-value pair is wrapped in an internal node object that encapsulates six core fields:
- `K key`: The actual storage key pointer.
- `V value`: The associated data payload pointer.
- `Entry<K,V> left`: Pointer to the left child node.
- `Entry<K,V> right`: Pointer to the right child node.
- `Entry<K,V> parent`: Pointer to the parent node.
- `boolean color`: The color flag (RED or BLACK) used by the tree re-balancing algorithms.

## How Does Tree Balancing Work?
Every time you call `map.put(key, value)`, the tree evaluates the relative size values of the keys to position the node. 

- The structure utilizes color properties and strict rotation rules to ensure the tree remains completely balanced.
- The tree depth is strictly capped at $O(\log n)$, preventing branches from skewing into a slow, inefficient linear line.
- Key uniqueness and matching are determined strictly by the key's `compareTo()` or `compare()` return values rather than `equals()`. If `compare(key1, key2) == 0`, the `TreeMap` considers them matching keys and overwrites the existing value.

---

# Core TreeMap Syntax & Range Workflows

## Initialization & Basic Sorting

```java
// Always declare using the Map or NavigableMap interface reference type for clean abstraction
NavigableMap<Integer, String> schedule = new TreeMap<>();

schedule.put(40, "Task D");
schedule.put(10, "Task A");
schedule.put(30, "Task C");
schedule.put(20, "Task B");

// Iteration is guaranteed to print keys in ascending order:
System.out.println(schedule); 
// Output: {10=Task A, 20=Task B, 30=Task C, 40=Task D}
```

---

## Powerful Navigable & Boundary Queries

```text
Active TreeMap State: {10=Task A, 20=Task B, 30=Task C, 40=Task D}
```

```java
Map.Entry<Integer, String> first = schedule.firstEntry(); // Returns 10=Task A (Minimum key boundary)
Map.Entry<Integer, String> last = schedule.lastEntry();   // Returns 40=Task D (Maximum key boundary)

// floorEntry(K): Returns the greatest key entry <= K
System.out.println(schedule.floorEntry(25)); // Output: 20=Task B

// ceilingEntry(K): Returns the smallest key entry >= K
System.out.println(schedule.ceilingEntry(25)); // Output: 30=Task C

// higherEntry(K): Returns the smallest key entry strictly > K
System.out.println(schedule.higherEntry(30)); // Output: 40=Task D

// lowerEntry(K): Returns the greatest key entry strictly < K
System.out.println(schedule.lowerEntry(20)); // Output: 10=Task A
```

---

# Time Complexity Metrics

| Operation | Time Complexity | Space Complexity | Technical Reason |
| :--- | :--- | :--- | :--- |
| **`map.put(key, val)`**| **O(log n)** | **O(1)** | Traverses down a balanced tree branch matching the tree height $\log_2(n)$ to insert and balance the node. |
| **`map.get(key)`** | **O(log n)** | **O(1)** | Performs a binary search lookup down the tree branches based on node key comparisons. |
| **`map.containsKey(k)`**| **O(log n)** | **O(1)** | Follows comparison paths down tree branches to verify node existence. |
| **`map.remove(key)`** | **O(log n)** | **O(1)** | Locates the node and triggers tree rotations/re-colorings if structural invariants are breached. |
| **`map.firstEntry()`**  | **O(log n)** | **O(1)** | Walks down the extreme leftmost branch path of the tree structure. |

---

# Key Interview Takeaways

```text
Dynamic Sorted Keys -> Choose TreeMap when your algorithm needs to continuously maintain a sorted collection of key-value maps while performing frequent insertions and deletions.

The Null Key Prohibition -> Never pass a null key to a TreeMap. Because the structure relies on calling compareTo() or compare() to position nodes, passing null will instantly throw a NullPointerException.

The Comparator Contract -> When using custom classes as keys, ensure your compare() or compareTo() implementation is consistent with equals(). If compare() returns 0 for two keys, the TreeMap will treat them as duplicate keys and overwrite the value, even if equals() says they are distinct.

Logarithmic Overhead -> While TreeMap provides magnificent range searching tools (floor, ceiling), its core operations take O(log n) time, making it asymptotically slower than the constant-time O(1) performance of a standard HashMap. Use it only when sorted keys matter.
```

`TreeMap` is an exceptional tool for eliminating boundary scanning bottlenecks in technical evaluations. It is indispensable for writing optimized range lookups in problems involving:

```text
Finding the next available chronological interval slot inside Sweep Line Algorithms
Tracking boundary thresholds inside dynamic Greedy Strategy models (e.g., My Calendar series)
Dynamic Sliding Window queries checking closest key ranges
Solving range cover tracking steps across overlapping coordinate maps
Maintaining a dynamic leaderboard list with instant floor/ceiling capability
```

<FollowUp>
If you want to continue mapping out these highly optimized structures, let me know:
* Would you like to see how to solve **My Calendar I (729)** using a `TreeMap`?
* Do you want a raw markdown template explaining **how to implement a basic binary search tree from scratch**?
* Or should we move on to another programming language's standard collections?

Tell me which **key terms** or design patterns you want to structure next!
</FollowUp>
