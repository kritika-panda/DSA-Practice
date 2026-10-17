# String Concepts in Java

## What is a String?

In Java, a **String** is an object that represents a sequence of characters. Unlike other programming languages where strings are simply null-terminated character arrays, Java implements strings natively as objects of the final `java.lang.String` class. 

Because the `String` class is **final**, it cannot be extended or subclassed. This absolute restriction prevents developers from overriding core methods or compromising underlying memory vulnerabilities.

---

# Key String Memory Layout

## The String Constant Pool (SCP)

To optimize memory allocation, reuse identical character streams, and reduce the runtime memory footprint, Java implements a specialized storage layout within the standard **Heap** area called the **String Constant Pool (SCP)**.

```text
    +-------------------------------------------------------+

    |                     HEAP MEMORY                       |
    |                                                       |
    |   +-----------------------------------------------+   |
    |   |         STRING CONSTANT POOL (SCP)            |   |
    |   |                                               |   |
    |   |   s1 ---->  "hello"  <---- s2                 |   |
    |   +-----------------------------------------------+   |
    |                                                       |
    |   s3 ---------> [ "hello" (Heap Object) ]             |
    +-------------------------------------------------------+
```

When you instantiate a string literal, the JVM executes a deduplication check across the pool:
- **Literal Syntax (`String s1 = "hello";`):** The JVM looks up the characters inside the SCP. If the text block exists, it assigns the reference straight to the pre-existing instance. If it is missing, a fresh entry is instantiated directly into the pool.
- **Explicit Constructor Syntax (`String s3 = new String("hello");`):** This forces the JVM to allocate a brand-new distinct object in the standard Heap space *outside* the pool, even if an identical text sequence is already sitting inside the SCP.

### The `intern()` Method Deep Dive
The `.intern()` method allows developers to dynamically interact with the SCP at runtime. When you invoke `s3.intern()`, the JVM looks up the character sequence of `s3` inside the pool:
- If an equal string is already in the SCP, it returns that pool reference directly.
- If it isn't in the pool, the character stream is copied into the SCP, and that pool reference is returned.

```java
String s1 = "hello";
String s3 = new String("hello");
String s4 = s3.intern(); 

System.out.println(s1 == s3); // false (Different memory locations)
System.out.println(s1 == s4); // true  (s4 references the pool instance)
System.out.println(s1 == s3); // false
```

The final print statement `System.out.println(s1 == s3);` will output **false**.

### 💡 Why is it false?

Calling `s3.intern()` finds the canonical string in the pool and returns its reference (which is assigned to `s4`), but it **does not alter the original reference** stored in the `s3` variable.
* `s1` still points to the literal "hello" inside the **String Constant Pool**.
* `s3` still points to the distinct object created on the **Heap** via the `new` keyword.


---

# String Immutability

In Java, `String` objects are completely **immutable**. The private underlying storage mechanism (historically a primitive `char[] value`, optimized to a `byte[] value` combined with a `coder` byte flag since Java 9 to save space for Latin-1 characters) is marked `final`. 

No API methods expose routes to modify this internal layout. Any modification operation (such as `.concat()`, `.replace()`, or `.toUpperCase()`) allocates a completely new `String` object rather than editing the original memory block.

## Why is String Immutable?

- **String Constant Pool Caching:** If strings were mutable, changing the content via one reference descriptor would instantly corrupt the data state for all other references pointing to that same pool slot.
- **Security & Network Protocols:** Strings are the default containers used to feed parameters into file configurations, network sockets, OS ports, and database username/password properties. If strings were mutable, an untrusted component could bypass validation checks by altering the string array characters mid-execution.
- **Thread Safety:** Immutability removes race conditions. Multiple execution threads can access, parse, or evaluate a single string reference concurrently without requiring external explicit synchronization locks.
- **Cached Hash Code (`hashCode`):** The `String` class computes its `hashCode` field lazily and stores the calculated integer value inside a local cache variable. Because the string contents can never alter, the hash code is guaranteed to stay uniform, providing an exceptional performance optimization for hash-based collections (like `HashMap` or `HashSet` keys).

---

# Core String Comparison Contracts

| Method / Operator | Behavior Contract | Evaluation Strategy |
| :--- | :--- | :--- |
| **`==`** | Compares **memory address references** | Referential Identity Check |
| **`.equals()`** | Compares the **actual character sequence contents** | Structural Value Verification |
| **`.equalsIgnoreCase()`** | Compares character data **ignoring case sensitivity** | Case-Insensitive Verification |

```java
String s1 = "hello";
String s2 = "hello";
String s3 = new String("hello");

System.out.println(s1 == s2);      // true  (References match inside the SCP)
System.out.println(s1 == s3);      // false (s3 points to a distinct Heap allocation address)
System.out.println(s1.equals(s3)); // true  (The underlying character sequences are identical)
```

---

# Efficient Mutation Tools

Because `String` is immutable, modifying strings inside heavy loops creates extensive temporary object garbage. Java provides two mutable alternatives:

## StringBuilder vs StringBuffer

| Feature | `StringBuilder` | `StringBuffer` |
| :--- | :--- | :--- |
| **Thread Safety** | **Non-thread-safe** (Methods lack synchronization) | **Thread-safe** (Methods contain explicit synchronization locks) |
| **Performance Execution**| **Faster** (Highly preferred for single-thread algorithmic loops) | **Slower** due to lock tracking thread-overhead |
| **Introduced In** | Java 1.5 | Java 1.0 |
| **Internal Storage** | Expandable `byte[]` array buffer | Expandable `byte[]` array buffer |

```java
// Preferred loop optimization pattern in technical interviews
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++) {
    sb.append(i); // Appends characters in-place without generating extra object allocations
}
String result = sb.toString();
```

---

# Indispensable String Methods Cheat Sheet

## Substring Extraction
```java
String s = "leetcode";
String sub1 = s.substring(4);     // "code" (From index 4 inclusive straight to the end)
String sub2 = s.substring(0, 4);  // "leet" (From index 0 inclusive up to 4 exclusive)
```

## Character & Index Lookups
```java
char c = s.charAt(0);          // 'l' (Constant-time direct element index lookup)
int first = s.indexOf('e');    // 1   (Returns the index of the first occurrence of 'e')
int last = s.lastIndexOf('e'); // 7   (Returns the index of the last occurrence of 'e')
int strIdx = s.indexOf("code");// 4   (Returns starting index or -1 if the token is missing)
```

## Parsing & Cleaning
```java
String dirty = "  leet code  ";
String clean = dirty.trim();    // "leet code" (Trims leading and trailing whitespace characters)
String[] parts = s.split("");   // Splits text into a flat array using delimiter match lines
```

## Conversions & Transformations
```java
char[] arr = s.toCharArray();     // Copies characters out into a primitive mutable array
String rev = new StringBuilder(s).reverse().toString(); // Best practice routine for sequence inversion
```

---

# Complexity Analysis

| Operation | Time Complexity | Space Complexity | Technical Reason |
| :--- | :--- | :--- | :--- |
| **`s.charAt(i)`** | **O(1)** | **O(1)** | Direct pointer offset references calculation into the underlying primitive array. |
| **`s.length()`** | **O(1)** | **O(1)** | Reads an internally cached local primitive integer count property. |
| **`s.substring(i, j)`** | **O(k)** | **O(k)** | Where `k = j - i`; allocates memory and copies the slice to a new block. |
| **`s1.equals(s2)`** | **O(min(n, m))** | **O(1)** | Performs an initial length filter, then checks characters sequentially until a mismatch occurs. |
| **`String + String`** | **O(n + m)** | **O(n + m)** | Compiles a new `StringBuilder` instance, appends payloads, and extracts a fresh string copy. |
| **`sb.append()`** | **Amortized O(1)**| **O(1)** | Appends to an expandable buffer, occasionally triggering an O(N) capacity resize step. |

---

# Key Interview Takeaways

```text
String Literals -> Cached in the String Constant Pool (SCP) to preserve memory allocations

new String() -> Forces duplicate allocation bypass, creating an object in the standard Heap zone

Loop Transformations -> Always use StringBuilder inside loops to prevent O(n²) string creation bottlenecks

HashMap Keys -> Strings are the ideal key type due to immutability and highly optimized cached hash codes

Substrings -> Always manage end boundary ranges carefully, as the upper index parameter is strictly exclusive
```

String manipulation logic forms the core of many technical evaluations. Mastering these concepts prevents common allocation traps in problems involving:

```text
Sliding Windows & Substring Slicing Filters
Two-Pointer Axis Sequence Scans
Palindromic Multi-Interval Verifications
Polynomial Rolling Hash Patterns (Rabin-Karp)
Linear Prefix Failures Lookups (KMP / LPS Array Parsing)
Nested Bracket Trackers (Stack Parsing Automata)
```
