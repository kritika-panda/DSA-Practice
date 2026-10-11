# HashSet in Java

## What is HashSet?

`HashSet` is a core collection class in Java that implements the `Set` interface. It belongs to the package:

```java
java.util.HashSet
```

It is a data structure used to store a collection of **unique elements**. A `HashSet` does **not allow duplicate values**, permits a single `null` element, and makes **no guarantees regarding the iteration order** of the elements (the order can change over time).

---

# Why Do We Need HashSet?

While linear structures like `ArrayList` or `LinkedList` allow you to store elements easily, checking whether a specific item exists inside them requires an expensive loop operation:
- **The Linear Search Trap:** Finding an element or checking for duplicates in a standard list takes \(O(n)\) time because the CPU must scan elements one by one from index 0.
- **Enforcing Uniqueness:** If a data stream requires storing only unique IDs or user tokens, a list forces you to run this linear check before *every single insertion*, degrading performance to an inefficient \(O(n^2)\) runtime.

`HashSet` completely eliminates this performance penalty. By leveraging a mechanism called **Hashing**, it can insert, delete, and check for the presence of elements in **constant \(O(1)\) time**, regardless of how many millions of elements are stored.

---

# HashSet vs LinkedHashSet vs TreeSet

| Feature | `HashSet` | `LinkedHashSet` | `TreeSet` |
| :--- | :--- | :--- | :--- |
| **Underlying Layout**| `HashMap` (Key slots) | `LinkedHashMap` (Key slots) | `TreeMap` (Red-Black Tree) |
| **Iteration Order** | **Unordered / Chaotic** | **Insertion Order** preserved | **Sorted Order** (Natural/Custom) |
| **Time Complexity** | **Fastest: O(1)** for core ops | O(1) constant time (slightly slower) | O(log n) logarithmic time |
| **Null Elements** | Allows a single `null` value | Allows a single `null` value | **No nulls allowed** (throws error) |
| **Memory Overhead** | Low (Map entry buckets) | Higher (due to bidirectional pointers) | Medium (Tree node references) |

---

# Internals & The HashMap Bridge Mechanics

An `HashSet` does not manage a brand-new hashing architecture from scratch. It is entirely a **wrapper around a `HashMap`**. 

When you instantiate a `HashSet`, it internally initializes a private `HashMap` instance hidden behind the scenes:

```java
// Simplified view of Java's internal HashSet source code
public class HashSet<E> implements Set<E> {
    private transient HashMap<E, Object> map;

    // A dummy value to associate with an Object in the backing Map
    private static final Object PRESENT = new Object();

    public HashSet() {
        map = new HashMap<>();
    }

    public boolean add(E e) {
        // Elements added to the HashSet are stored as KEYS in the internal HashMap
        return map.put(e, PRESENT) == null;
    }
}
```

## How Does Uniqueness Work?
Every time you call `set.add(element)`, the `HashSet` takes your element and inserts it as a **Key** into the internal `HashMap`. It pairs it with a constant dummy object singleton called `PRESENT`. 

Because a `HashMap` structurally guarantees that keys must be unique, the `HashSet` inherits its absolute duplicate-prevention properties for free. The element's hash code determines its bucket bucket coordinate location instantly.

---

# Core HashSet Syntax & Operational Workflows

## Initialization & Basic Operations

```java
// Always declare using the Set interface reference type for clean abstraction decoupling
Set<String> uniqueIds = new HashSet<>();

uniqueIds.add("USR101");
uniqueIds.add("USR102");
uniqueIds.add("USR101"); // Duplicate entry is rejected; returns false

System.out.println(uniqueIds.contains("USR101")); // Instant lookup: true
System.out.println(uniqueIds.size());             // Size is 2
```

---

## Iterating Over a HashSet

```java
// Remember: The output elements will print in an unpredictable, chaotic order
for (String id : uniqueIds) {
    System.out.println(id);
}
```

---

# Time Complexity Metrics

| Operation | Time Complexity | Space Complexity | Technical Reason |
| :--- | :--- | :--- | :--- |
| **`set.add(val)`** | **Amortized O(1)** | **O(1)** | Computes the hash code of the element and places it straight into a `HashMap` key bucket bucket coordinate. |
| **`set.contains(val)`**| **O(1)** | **O(1)** | Directly hashes the target item to verify its bucket position instantly without scanning other elements. |
| **`set.remove(val)`** | **O(1)** | **O(1)** | Locates and un-references the internal map key bucket in a single step. |
| **`set.size()`** | **O(1)** | **O(1)** | Reads a cached local integer count variable property directly. |

*(Note: In the worst-case scenario where multiple elements experience catastrophic hash collisions and collapse into the exact same bucket bucket list, lookups can degrade to O(log n) since Java 8 balances dense buckets using balanced trees).*

---

# Key Interview Takeaways

```text
Constant-Time Lookups -> Always default to HashSet when an algorithm requires checking for the presence of values repeatedly (e.g., tracking visited nodes in a graph).

Custom Object Contract -> If you store custom objects inside a HashSet, you MUST override both hashCode() and equals() inside that class. If you don't, identical objects will generate different references, breaking the uniqueness contract.

Initial Capacity Sizing -> If you know you will store 1000 items, initialize the set with an explicit capacity (e.g., new HashSet<>(1350)) to prevent the internal HashMap from undergoing expensive re-hashing resizing cycles.

Zero Index Access -> HashSet does not have a .get(index) method. Because elements are arranged by hash coordinates rather than linear indices, you must use an Iterator or a for-each loop to traverse values.
```

`HashSet` is a vital tool for eliminating time complexities in technical interviews. It is indispensable for writing fast, sub-linear lookup routines in problems involving:

```text
Tracking Visited State Nodes in Graph Traversals (BFS / DFS Cycles)
Identifying Contains Matches inside Sliding Window Tracks
Isolating Missing or Duplicate Elements in an array (e.g., Contains Duplicate)
Filtering out Intersections or Unions across multi-dimensional arrays
Dynamic Tracking of available tokens inside Greedy Strategy models
```

<FollowUp>
If you want to continue mapping out core Java structural layouts or technical problem configurations, let me know:
* Would you like to see how to solve **Contains Duplicate (217)** or **Intersection of Two Arrays (349)** using a `HashSet`?
* Do you want a raw markdown template for the deep hashing mechanics of **HashMap internals**?
* Or should we look into a sorted set collection like **TreeSet**?

Tell me which **key terms** or interview structures you want to target next!
</FollowUp>
