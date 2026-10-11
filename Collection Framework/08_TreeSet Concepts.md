# TreeSet in Java

## What is TreeSet?

`TreeSet` is a core collection class in Java that implements the `NavigableSet` interface, which in turn extends the `SortedSet` and `Set` interfaces. It belongs to the package:

```java
java.util.TreeSet
```

It is a data structure used to store a collection of **unique elements** that are automatically maintained in a **sorted order**. By default, elements are sorted according to their **natural ordering** (e.g., ascending numerical order for integers, alphabetical order for strings), but you can also provide a custom sorting strategy via a `Comparator`. 

Unlike `HashSet` and `LinkedHashSet`, a `TreeSet` **does not allow null elements** (attempting to add a `null` value will throw a `NullPointerException`).

---

# Why Do We Need TreeSet?

While a standard `HashSet` provides exceptional constant-time $O(1)$ performance, it completely strips away the sorted relationships between numbers or text:
- **The Sorting Dilemma:** If you use a `HashSet` or `ArrayList` and constantly need to find the smallest element, largest element, or keep the entire structure sorted after insertions, you must continuously call a sorting routine, which takes a costly $O(n \log n)$ time penalty.
- **Dynamic Sorted Windowing:** If an algorithm requires you to dynamically answer range queries—such as finding the closest number greater than or equal to a target value—linear structures force a full scan loop.

`TreeSet` completely resolves this. It maintains its sorted structure dynamically on every insertion and deletion, allowing you to run lookups, boundary checks, and range operations in optimized **logarithmic $O(\log n)$ time**.

---

# HashSet vs LinkedHashSet vs TreeSet

| Feature | `HashSet` | `LinkedHashSet` | `TreeSet` |
| :--- | :--- | :--- | :--- |
| **Underlying Layout**| `HashMap` (Key slots) | `LinkedHashMap` (Key slots) | **`TreeMap` (Red-Black Tree)** |
| **Iteration Order** | Unordered / Chaotic | Insertion Order preserved | **Sorted Order** (Natural/Custom) |
| **Time Complexity** | **Fastest: O(1)** | O(1) constant time | **O(log n) logarithmic time** |
| **Null Elements** | Allows a single `null` value | Allows a single `null` value | **No nulls allowed** (throws error) |
| **Memory Overhead** | Low (Map entry buckets) | Higher (bidirectional links) | Medium (Tree node references) |

---

# Internals & The Red-Black Tree Architecture

A `TreeSet` does not manage a distinct tree architecture from scratch. It is completely a lightweight **wrapper around a `TreeMap`**.

When you instantiate a `TreeSet`, it internally initializes a private `TreeMap` instance hidden behind the scenes:

```java
// Simplified view of Java's internal TreeSet source code
public class TreeSet<E> implements NavigableSet<E> {
    private transient NavigableMap<E, Object> m;

    // A dummy value to associate with an Object in the backing Map
    private static final Object PRESENT = new Object();

    public TreeSet() {
        this(new TreeMap<>());
    }

    public boolean add(E e) {
        // Elements added to the TreeSet are stored as KEYS in the internal TreeMap
        return m.put(e, PRESENT) == null;
    }
}
```

## How Does Tree Balancing Work?
Every time you call `set.add(element)`, the element is placed as a **Key** into a backing **TreeMap**, which is implemented as a self-balancing binary search tree called a **Red-Black Tree**. 

- The tree structure evaluates the relative size values of nodes to keep the tree completely balanced. 
- The tree depth is strictly capped at $O(\log n)$, preventing the tree branches from skewing into an inefficient linear line.
- Uniqueness is determined strictly by the element's `compareTo()` or `compare()` return values rather than `equals()`. If `compare(a, b) == 0`, the `TreeSet` considers them duplicates and rejects the duplicate entry.

---

# Core TreeSet Syntax & Range Workflows

## Initialization & Basic Sorting

```java
// Always declare using the Set or NavigableSet interface reference type for clean abstraction
NavigableSet<Integer> sortedNumbers = new TreeSet<>();

sortedNumbers.add(40);
sortedNumbers.add(10);
sortedNumbers.add(30);
sortedNumbers.add(20);
sortedNumbers.add(20); // Duplicate entry is rejected

// Iteration is guaranteed to print elements in ascending order: [10, 20, 30, 40]
System.out.println(sortedNumbers); 
```

---

## Powerful Navigable & Boundary Queries

```text
Active TreeSet State: [10, 20, 30, 40]
```

```java
int lowest = sortedNumbers.first(); // Returns 10 (Minimum boundary element)
int highest = sortedNumbers.last(); // Returns 40 (Maximum boundary element)

// floor(X): Returns the greatest element <= X
System.out.println(sortedNumbers.floor(25)); // Output: 20

// ceiling(X): Returns the smallest element >= X
System.out.println(sortedNumbers.ceiling(25)); // Output: 30

// higher(X): Returns the smallest element strictly > X
System.out.println(sortedNumbers.higher(30)); // Output: 40

// lower(X): Returns the greatest element strictly < X
System.out.println(sortedNumbers.lower(20)); // Output: 10
```

---

# Time Complexity Metrics

| Operation | Time Complexity | Space Complexity | Technical Reason |
| :--- | :--- | :--- | :--- |
| **`set.add(val)`** | **O(log n)** | **O(1)** | Traverses down a balanced tree branch matching the tree height $\log_2(n)$ to locate the correct sorted position. |
| **`set.contains(val)`**| **O(log n)** | **O(1)** | Performs a binary search lookup down the tree branches based on node comparison paths. |
| **`set.remove(val)`** | **O(log n)** | **O(1)** | Locates the node target and re-balances tree pointers if a node deletion triggers a structural color violation. |
| **`set.first()` / `.last()`**| **O(log n)** | **O(1)** | Walks down the extreme leftmost or rightmost branch path of the tree. |

---

# Key Interview Takeaways

```text
Dynamic Sorted Tracking -> Always choose TreeSet when your algorithm needs to continuously maintain a sorted collection of unique values while performing frequent insertions and deletions.

The Null Rejection Trap -> Never add a null value to a TreeSet. Because the structure relies on calling compareTo() or compare() to position nodes, passing null will instantly trigger a NullPointerException.

The Comparator Contract Consistency -> When storing custom classes, ensure your compareTo() implementation is consistent with equals(). If compareTo() returns 0 for two items, the TreeSet will treat them as duplicates and drop one, even if their equals() method says they are different.

Logarithmic Over Cost -> While TreeSet provides magnificent range searching tools (floor, ceiling), its core operations take O(log n) time, making it asymptotically slower than the constant-time O(1) performance of a standard HashSet. Use it only when sorted relationships matter.
```

`TreeSet` is one of the most powerful structures for eliminating boundary scanning bottlenecks in technical evaluations. It is indispensable for writing optimized range lookups in problems involving:

```text
Dynamic Sliding Window Window queries checking closest neighbors (e.g., Contains Duplicate III)
Finding the next available chronological interval slot inside Sweep Line Algorithms
Tracking boundary thresholds inside dynamic Greedy Strategy models
Solving range cover tracking steps across overlapping coordinate maps
Maintaining a dynamic leaderboard list with instant floor/ceiling capability
```

<FollowUp>
If you want to continue mapping out these highly sorted structures or interview models, let me know:
* Would you like to see how to solve **Contains Duplicate III (220)** or **Merge Intervals** using a `TreeSet` or `TreeMap` variant?
* Do you want a raw markdown template for the balancing and structural mechanics of **Red-Black Trees**?
* Or should we transition to a different structure like **PriorityQueue / Heap internals**?

Tell me which **key terms** or design rules you want to structure next!
</FollowUp>
