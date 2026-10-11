# ArrayList in Java

## What is ArrayList?

`ArrayList` is a core resizable-array implementation of the `List` interface in Java. It belongs to the package:

```java
java.util.ArrayList
```

Unlike primitive arrays in Java, which have a fixed capacity determined at instantiation, an `ArrayList` manages a dynamic internal array that automatically grows or shrinks as elements are added or removed. It permits all elements, including `null`, and maintains the exact **insertion order** of elements.

---

# Why Do We Need ArrayList?

Standard arrays in Java require an explicit capacity specification during declaration:

```java
int[] arr = new int[5]; // Rigid allocation boundary
```

If the data stream size is highly volatile or unpredictable at runtime, standard arrays fail:
- If the count of incoming elements exceeds 5, the program crashes with an `ArrayIndexOutOfBoundsException`.
- If the count of incoming elements is significantly less than 5, memory slots are wasted.

`ArrayList` solves this structural restriction by encapsulating a primitive array wrapper that reallocates and mirrors data elements dynamically, providing predictable, balanced linear capacity management.

---

# ArrayList vs Vector vs LinkedList

| Feature | `ArrayList` | `Vector` | `LinkedList` |
| :--- | :--- | :--- | :--- |
| **Underlying Layout**| Resizable primitive array | Resizable primitive array | Doubly Linked List nodes |
| **Thread Safety** | **Non-thread-safe** | **Thread-safe** (synchronized) | **Non-thread-safe** |
| **Performance** | **Fastest** for random lookups | Slower due to lock overhead | Slow lookups (requires traversal) |
| **Data Modifications**| Slower for random element shifts | Slower due to lock overhead | **Fastest** insertion/deletion at nodes |
| **Growth Strategy** | Grows by **50%** of current size | Grows by **100%** (doubles size) | No array resizing overhead |
| **Introduced In** | Java 1.2 | Java 1.0 | Java 1.2 |

---

# Internals & Capacity Resizing Mechanics

An `ArrayList` manages a flat `Object[] elementData` array buffer under the hood. When you instantiate an `ArrayList` using the default constructor, Java optimizes memory by mapping it to an empty shared array singleton reference:

```java
List<Integer> list = new ArrayList<>(); // Size = 0, initial capacity = 0
```

## The Capacity Threshold Jump
The moment the first element is appended via `.add()`, the internal array dynamically inflates to a default baseline capacity:

```text
DEFAULT_CAPACITY = 10
```

As more elements fill the collection, the array eventually reaches its maximum active boundary. When the `(size + 1)` element is offered, `ArrayList` triggers its **Growth Policy** inside the `grow()` method:

```text
newCapacity = oldCapacity + (oldCapacity >> 1) // Grows by exactly 50%
```

The right-shift bitwise operator `oldCapacity >> 1` is mathematically equivalent to integer division by 2, computing the 50% expansion step in a single CPU clock cycle. 

A brand-new primitive `Object[]` array is instantiated with this `newCapacity` value, and the existing elements are duplicated into the fresh block using `Arrays.copyOf()`.

```text
Initial Array (Capacity 10):  [A][B][C][D][E][F][G][H][I][J]
Trigger .add(K) -> Dynamic Growth:
New Array     (Capacity 15):  [A][B][C][D][E][F][G][H][I][J][K][ ][ ][ ][ ]
```

---

# Core ArrayList Syntax & Operational Workflows

## Initialization and Generics

```java
// Always declare using the List interface reference type for clean abstraction decoupling
List<String> tags = new ArrayList<>(); 
tags.add("FAANG");
tags.add("Greedy");
```

---

## Accessing and Updating Elements

```java
String firstTag = tags.get(0); // Constant-time random index lookup ("FAANG")
tags.set(1, "SlidingWindow");  // Replaces the element at index 1 in O(1) time
```

---

## Interstitial Insertion and Deletion

```java
tags.add(1, "TwoPointers"); // Inserts at index 1, shifting trailing elements right: O(n)
tags.remove(0);             // Removes element at index 0, shifting trailing elements left: O(n)
```

---

# Time Complexity Metrics

| Operation | Time Complexity | Space Complexity | Technical Reason |
| :--- | :--- | :--- | :--- |
| **`list.get(idx)`** | **O(1)** | **O(1)** | Direct pointer memory offset calculation based on array index alignment. |
| **`list.set(idx, val)`**| **O(1)** | **O(1)** | Direct memory address slot overwrite. |
| **`list.add(val)`** | **Amortized O(1)**| **O(1)** | Appends directly to the trailing edge pointer; occasionally incurs an O(n) array copy cost during capacity expansion. |
| **`list.add(idx, val)`**| **O(n)** | **O(1)** | Requires running a `System.arraycopy()` sweep to shift all trailing elements to the right. |
| **`list.remove(idx)`** | **O(n)** | **O(1)** | Requires running a `System.arraycopy()` sweep to shift all trailing elements to the left. |
| **`list.contains(val)`**| **O(n)** | **O(1)** | Performs an un-indexed linear search scan from index 0 across the entire array loop. |
| **`list.size()`** | **O(1)** | **O(1)** | Reads a cached local integer count variable property directly. |

---

# Key Interview Takeaways

```text
Single-Thread Dominance -> Always default to ArrayList over Vector or LinkedList for linear collections in technical interviews unless node splitting is strictly required.

Pre-Sizing Performance -> If the total volume of elements is predictable, pass an explicit initial capacity to the constructor (e.g., new ArrayList<>(1000)) to completely eliminate internal array growth resizing overhead.

The Linear Removal Trap -> Avoid calling list.remove(index) or list.add(index, val) inside long loops, as shifting trailing elements creates an expensive O(n²) runtime bottleneck.

Iterative Safe Removal -> Never remove elements from an ArrayList inside a standard for-each loop, as it violates internal state counters and throws a ConcurrentModificationException. Always use an explicit Iterator or the list.removeIf() predicate API.
```

`ArrayList` forms the structural baseline for handling variable-length sequential tracks in technical evaluations. It is indispensable for writing optimized, low-latency lookups in problems involving:

```text
Sliding Window Boundaries & Sub-Interval Collections
Two-Pointer Axis Traversals (Merging Sort Inputs)
Backtracking Path State Trackers (Assembling Permutations / Subsets)
Graph Breadth-First Search (BFS) Layer Queue Initializations
Dynamic Programming Subproblem Target State Caches
```

<FollowUp>
If you want to continue mapping out core Java structural layouts or technical problem trackers, let me know:
* Would you like to see how to solve **Merge Intervals (56)** or **Subsets (78)** using an `ArrayList` to manage dynamic output bounds?
* Do you want a raw markdown template for the internal node-linking mechanics of **LinkedList**?
* Or should we transition to a different collection domain like **HashMap internals**?

Tell me which **key terms** or interview targets you want to structure next!
</FollowUp>
