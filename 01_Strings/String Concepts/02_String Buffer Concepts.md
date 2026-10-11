# StringBuffer in Java

## What is StringBuffer?

`StringBuffer` is a peer class of `String` that provides much of the functionality of strings but represents growable and writable character sequences. It belongs to the:

```java
java.lang.StringBuffer
```

Unlike the immutable `String` class, `StringBuffer` can be modified safely at runtime without generating extensive intermediate garbage allocations, and it does so while being completely **thread-safe**.

---

# Why Do We Need StringBuffer?

If you try to modify a standard `String` repeatedly inside a concurrent loop, the JVM is forced to allocate a massive number of temporary string objects because strings are immutable. This creates a severe memory and performance bottleneck.

While `StringBuilder` solves this in single-threaded environments, if multiple threads attempt to append characters to a shared sequence concurrently, a non-thread-safe buffer will suffer from data corruption and race conditions. `StringBuffer` completely resolves this by ensuring all modification operations are synchronized.

---

# StringBuffer vs StringBuilder vs String

| Feature | String | StringBuilder | StringBuffer |
| :--- | :--- | :--- | :--- |
| **Immutability** | **Immutable** | **Mutable** | **Mutable** |
| **Thread Safety** | **Thread-safe** (due to immutability) | **Non-thread-safe** | **Thread-safe** (methods are synchronized) |
| **Performance** | Fast for reading, very slow for modifications | **Fastest** for mutations (no synchronization lock overhead) | **Slower than StringBuilder** due to thread locking |
| **Memory Allocation** | Uses String Constant Pool (SCP) | Dynamically resizes a continuous internal array | Dynamically resizes a continuous internal array |
| **Introduced In** | Java 1.0 | Java 1.5 | Java 1.0 |

---

# Internals & Capacity Resizing Mechanics

`StringBuffer` maintains an internal primitive array buffer to store its characters. When you instantiate a `StringBuffer`, it sets aside a baseline helper layout capacity:

```java
StringBuffer sb = new StringBuffer(); // Default initial capacity is 16 characters
StringBuffer sbCustom = new StringBuffer(50); // Explicit initial capacity of 50 characters
```

## How Does Resizing Work?
When the count of characters appended crosses the active capacity barrier, `StringBuffer` resizes its internal array automatically under the hood using a predictable expansion formula:

```text
newCapacity = (oldCapacity * 2) + 2
```

It instantiates a brand-new, larger primitive array using this formula and efficiently copies the existing data into the fresh memory block using `System.arraycopy()`.

---

# Core StringBuffer Syntax & Common Patterns

## Initialization and Appending

```java
StringBuffer sb = new StringBuffer("Hello");
sb.append(" ");
sb.append("World");

System.out.println(sb.toString());
```

Output:

```java
Hello World
```

---

## In-Place Inversion

```java
StringBuffer sb = new StringBuffer("leetcode");
sb.reverse();

System.out.println(sb.toString());
```

Output:

```java
edocteel
```

---

## Character Insertion and Deletion

```java
StringBuffer sb = new StringBuffer("Java");
sb.insert(4, " 23"); // Inserts text at the specified index offset boundary
System.out.println(sb); // Java 23

sb.delete(4, 7); // Deletes characters from start index inclusive to end index exclusive
System.out.println(sb); // Java
```

---

# Thread Safety Mechanics

The thread safety of `StringBuffer` is achieved by applying the `synchronized` keyword directly to almost all of its mutating methods. This ensures that only a single thread can execute an operation on a `StringBuffer` instance at any given point in time.

```java
// Conceptual snippet of StringBuffer's internal implementation
@Override
public synchronized StringBuffer append(String str) {
    toStringCache = null;
    super.append(str);
    return this;
}
```

Because of this runtime lock tracking, if your algorithmic solution or technical interview code is strictly single-threaded, you should avoid `StringBuffer` and use `StringBuilder` to eliminate the unnecessary locking overhead.

---

# Time Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **`sb.append(str)`** | **Amortized O(1)** | **O(1)** | Characters are appended directly to the end of the buffer; occasionally hits an O(n) array resizing step. |
| **`sb.insert(idx, str)`**| **O(n)** | **O(1)** | Requires shifting all elements after the specified index to make room for the new token. |
| **`sb.delete(i, j)`** | **O(n)** | **O(1)** | Requires shifting trailing elements forward to close the deleted gap. |
| **`sb.reverse()`** | **O(n)** | **O(1)** | Swaps elements in-place from both ends using a two-pointer linear strategy. |
| **`sb.toString()`** | **O(n)** | **O(n)** | Allocates and copies the internal array payload into a new immutable `String` object. |

---

# Key Interview Takeaways

```text
Thread-Safe Modifications -> Always prefer StringBuffer over StringBuilder if multiple concurrent threads share the buffer sequence.

Single-Thread Optimization -> Avoid StringBuffer in standard single-thread string manipulation problems to bypass execution locking penalties.

Capacity Growth -> The buffer grows dynamically via (oldCapacity * 2) + 2; pre-sizing the buffer prevents costly array copies.

In-Place Mutations -> Methods like append(), insert(), delete(), and reverse() modify the sequence in-place and return a reference to the same object, enabling method chaining.
```

`StringBuffer` is highly useful in system design interviews and multithreaded architecture evaluations where thread safety across shared data streams is paramount. It ensures robust concurrent tracking in scenarios involving:

```text
Multi-Threaded Producer-Consumer Logging Pipelines
Concurrent Network Socket Buffer Management
Shared Dynamic Thread-Safe String Builders
Asynchronous Sweep-Line Event Parsers
```
