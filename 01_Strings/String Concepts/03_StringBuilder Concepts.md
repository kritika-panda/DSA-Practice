# StringBuilder in Java

## What is StringBuilder?

`StringBuilder` is a companion class of `String` designed to represent a growable and writable sequence of characters. It belongs to the package:

```java
java.lang.StringBuilder
```

Unlike the immutable `String` class, `StringBuilder` can be modified in-place at runtime without allocating extensive intermediate string garbage objects. It is specifically optimized to be **non-thread-safe**, making it significantly faster than `StringBuffer` for single-threaded operations.

---

# Why Do We Need StringBuilder?

Because `String` is immutable, performing repetitive operations like concatenation inside a loop forces the JVM to continuously create fresh string objects, copy characters, and discard old allocations. This results in a highly inefficient quadratic time bottleneck:

```java
// Anti-Pattern: Triggers an expensive O(n²) time bottleneck
String s = "";
for (int i = 0; i < 1000; i++) {
    s += i; // Internally instantiates a temporary builder, copies characters, and discards old data
}
```

`StringBuilder` completely eliminates this performance penalty by modifying a single, expandable internal array buffer in-place, reducing sequential operations to an efficient linear runtime.

---

# StringBuilder vs StringBuffer vs String

| Feature | String | StringBuilder | StringBuffer |
| :--- | :--- | :--- | :--- |
| **Immutability** | **Immutable** | **Mutable** | **Mutable** |
| **Thread Safety** | **Thread-safe** (due to immutability) | **Non-thread-safe** | **Thread-safe** (methods are synchronized) |
| **Performance** | Fast for reading, slowest for modifications | **Fastest** for mutations (no synchronization lock overhead) | **Slower than StringBuilder** due to thread locking |
| **Memory Allocation** | Uses String Constant Pool (SCP) | Dynamically resizes a continuous internal array | Dynamically resizes a continuous internal array |
| **Introduced In** | Java 1.0 | Java 1.5 | Java 1.0 |

---

# Internals & Capacity Resizing Mechanics

`StringBuilder` maintains an internal primitive array buffer (a `byte[]` array combined with a `coder` flag since Java 9 to optimize space for Latin-1 characters) to store its text. When you instantiate a `StringBuilder`, it sets aside a baseline tracking capacity:

```java
StringBuilder sb = new StringBuilder(); // Default initial capacity is 16 characters
StringBuilder sbCustom = new StringBuilder(100); // Explicit initial capacity of 100 characters
```

## How Does Resizing Work?
When the size of the incoming character data exceeds the active capacity barrier, `StringBuilder` resizes its internal array buffer automatically under the hood using a predictable expansion formula:

```text
newCapacity = (oldCapacity * 2) + 2
```

It instantiates a brand-new, larger primitive array using this formula and efficiently copies the existing characters into the fresh memory block using `System.arraycopy()`. Pre-sizing the buffer using the integer constructor avoids these internal array reallocation overheads entirely.

---

# Core StringBuilder Syntax & Common Patterns

## Initialization and Appending

```java
StringBuilder sb = new StringBuilder("Leet");
sb.append("code");

System.out.println(sb.toString());
```

Output:

```java
Leetcode
```

---

## In-Place Inversion

```java
StringBuilder sb = new StringBuilder("stressed");
sb.reverse();

System.out.println(sb.toString());
```

Output:

```java
desserts
```

---

## Character Insertion and Deletion

```java
StringBuilder sb = new StringBuilder("Problem 79");
sb.insert(7, "s"); // Inserts text at the specified index offset boundary
System.out.println(sb); // Problems 79

sb.delete(8, 11); // Deletes characters from start index inclusive to end index exclusive
System.out.println(sb); // Problems
```

---

# Time Complexity Metrics

| Operation | Time Complexity | Space Complexity | Technical Reason |
| :--- | :--- | :--- | :--- |
| **`sb.append(str)`** | **Amortized O(k)** | **O(k)** | Where `k` is the length of `str`. Characters are appended directly to the end of the buffer; occasionally hits an O(n) array resizing step. |
| **`sb.insert(idx, str)`**| **O(n)** | **O(k)** | Requires shifting all elements after the specified index to make room for the new token. |
| **`sb.delete(i, j)`** | **O(n)** | **O(1)** | Requires shifting trailing elements forward to close the deleted gap. |
| **`sb.reverse()`** | **O(n)** | **O(1)** | Swaps elements in-place from both ends using a two-pointer linear strategy. |
| **`sb.toString()`** | **O(n)** | **O(n)** | Allocates and copies the internal array payload into a new immutable `String` object. |

---

# Key Interview Takeaways

```text
Single-Thread Optimization -> Always prefer StringBuilder over StringBuffer in technical interviews to eliminate thread locking penalties.

Loop String Modification -> Always append tokens to a StringBuilder inside loops to prevent O(n²) string allocation bottlenecks.

Method Chaining -> Mutating methods return a reference to the same StringBuilder instance, allowing clean fluent API invocation chains (e.g., sb.append("a").reverse()).

Pre-Sizing Advantage -> If the final output length is predictable, initialize the builder with an explicit starting capacity to completely bypass internal array resizing costs.
```

`StringBuilder` is one of the most heavily used structural classes in technical interviews to achieve optimal runtimes. It is indispensable for writing clean, optimized single-threaded pipelines in problems involving:

```text
Sliding Windows & Substring Accumulation Filters
Two-Pointer Axis Sequence Scans & Inversions
Palindromic Multi-Interval Verifications
String Compression and Decompression (Run-Length Encoding)
Parsing Stack Parsing Automata (Expression Solvers / Decode String)
```
