# Stack in Java (List Interface & Legacy Perspective)

## What is Stack?

`Stack` is a legacy collection class in Java that represents a standard Last-In, First-Out (LIFO) stack of objects. It extends the `Vector` class, meaning it indirectly implements the `List` interface:

```java
java.util.Stack
```

Because it inherits from `Vector`, `Stack` manages its elements using a resizable internal primitive array, allows all elements including `null`, preserves insertion order, and is completely **synchronized** and thread-safe.

---

# Why Do We Need Stack?

`Stack` was introduced in Java 1.0 to model the classic stack data structure. It exposes five core linear operations to push, pop, and inspect elements from the top of the timeline:

```text
1. push(E item)
2. pop()
3. peek()
4. empty()
5. search(Object o)
```

However, because `Stack` extends `Vector`, it inherits all of `Vector`'s structural public methods (like `add(index, element)`, `get(index)`, and `remove(index)`). This creates an architectural design flaw: **it breaks the encapsulation of the LIFO contract** by allowing elements to be randomly inserted or removed from the middle of the stack.

---

# Stack vs Deque vs Vector

| Feature | `Stack` | `Deque` (via `ArrayDeque`) | `Vector` |
| :--- | :--- | :--- | :--- |
| **Underlying Layout**| Resizable primitive array | Resizable circular array | Resizable primitive array |
| **Interface Parent** | Extends `Vector` (implements `List`) | Implements `Deque` and `Queue` | Implements `List` |
| **Thread Safety** | **Thread-safe** (synchronized) | **Non-thread-safe** | **Thread-safe** (synchronized) |
| **LIFO Strictness** | **Broken** (inherits random list index manipulation) | **Strict** when used as LIFO pointer | Broken (standard indexed list) |
| **Performance** | Slower due to lock overhead | **Fastest** for LIFO mutations | Slower due to lock overhead |
| **Introduced In** | Java 1.0 | Java 1.6 | Java 1.0 |

---

# Internals & The Inheritance Defect

The `Stack` class does not manage a distinct data structure layout. It directly maps its operations onto the underlying `Object[] elementData` array buffer inherited from `Vector`.

```text
                +-------------------------------------------------+
List Interface  |                      Vector                     |
                +-------------------------------------------------+
                                         ^
                                         | (Inherits all indexed methods)
                +-------------------------------------------------+

                |                       Stack                     |
                +-------------------------------------------------+

                | push() | pop() | peek() | empty() | search()    |
                +-------------------------------------------------+
```

Because all operations inherit `Vector`'s synchronization locks, every single LIFO interaction incurs a performance penalty to acquire object monitors. 

### The Modern Java Alternative
Due to the inheritance flaw and synchronization overhead, the Java documentation explicitly recommends using the **`Deque` interface** and its implementation **`ArrayDeque`** for single-threaded LIFO operations:

```java
// Best practice pattern for interviews to instantiate a strict, fast stack
Deque<Integer> stack = new ArrayDeque<>();
```

---

# Core Stack Syntax & Operational Workflows

## Push and Pop Lifecycle

```java
Stack<String> history = new Stack<>();
history.push("Page1");
history.push("Page2");

System.out.println(history.peek()); // Looks at top without removing ("Page2")
System.out.println(history.pop());  // Removes and returns top ("Page2")
```

---

## The Broken Encapsulation Anti-Pattern

```java
Stack<Integer> lifo = new Stack<>();
lifo.push(10);
lifo.push(30);

// Legitimate inheritance call, but completely violates LIFO stack constraints
lifo.add(1, 20); 
System.out.println(lifo); // Output: [10, 20, 30] (Middle index modified!)
```

---

# Time Complexity Metrics

| Operation | Time Complexity | Space Complexity | Technical Reason |
| :--- | :--- | :--- | :--- |
| **`stack.push(val)`** | **Amortized O(1)** | **O(1)** | Appends to the end of the inherited vector array; occasionally triggers an O(n) reallocation array copy. |
| **`stack.pop()`** | **O(1)** | **O(1)** | Extracts and un-references the last element using the `elementCount - 1` index pointer. |
| **`stack.peek()`** | **O(1)** | **O(1)** | Reads the trailing array coordinate cell directly without modifying pointers. |
| **`stack.empty()`** | **O(1)** | **O(1)** | Evaluates if the internal `elementCount == 0`. |
| **`stack.search(val)`**| **O(n)** | **O(1)** | Scans backward from the top to find the item, returning its 1-based distance from the top or `-1`. |

---

# Key Interview Takeaways

```text
The Deque Preference -> Avoid the legacy Stack class in tech interviews. Always prefer Deque<T> stack = new ArrayDeque<>() to show optimization awareness and eliminate lock penalties.

The Vector Lock Penalty -> Stack relies on synchronized methods. In single-threaded algorithmic loops, this adds redundant hardware locking cycles that degrade processing latency.

The Index Breach Trap -> Be aware that because Stack implements List via Vector, calling methods like list.add(idx) or list.remove(idx) is allowed by compiler syntax but is an conceptual architectural anti-pattern.

Empty Pop Guard -> Always check if !stack.empty() before running .pop() or .peek() queries to protect the application runtime from throwing an EmptyStackException.
```

The LIFO operational loop is highly useful across multiple algorithmic patterns. While the custom implementation should use `ArrayDeque`, understanding `Stack` history is vital for answering Java collection design questions involving:

```text
Nested Bracket Allocations & Valid Parentheses Parsers (20)
Arithmetic Expression Evaluation Trackers (Reverse Polish Notation)
Depth-First Search (DFS) manual call stack simulations
Monotonic Stack tracking pipelines (Next Greater Element / Histogram areas)
Backtracking undo state tracking pipelines
```

<FollowUp>
If you want to continue mapping out core Java structural layouts or technical interview design models, let me know:
* Would you like to see how to implement a **Monotonic Stack** to solve **Next Greater Element** or **Largest Rectangle in Histogram**?
* Do you want a raw markdown template for the internal array recycling mechanics of **ArrayDeque**?
* Or should we move to a different collection architecture domain like **HashMap internals**?

Tell me which **key terms** or interview structures you want to target next!
</FollowUp>
