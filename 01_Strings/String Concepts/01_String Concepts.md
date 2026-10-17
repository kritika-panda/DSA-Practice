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
# String Operation Complexity — Deep Dive Explanations

## The Complexity Table at a Glance

| Operation | Time Complexity | Space Complexity | Technical Reason |
|---|---|---|---|
| `s.charAt(i)` | O(1) | O(1) | Direct pointer offset into the underlying primitive array |
| `s.length()` | O(1) | O(1) | Reads an internally cached primitive integer count field |
| `s.substring(i, j)` | O(k) | O(k) | Where `k = j - i`; allocates memory and copies the slice to a new block |
| `s1.equals(s2)` | O(min(n, m)) | O(1) | Performs an initial length filter, then checks characters sequentially until a mismatch |
| `String + String` | O(n + m) | O(n + m) | Compiles a new `StringBuilder` instance, appends payloads, and extracts a fresh string copy |
| `sb.append()` | Amortized O(1) | O(1) | Appends to an expandable buffer, occasionally triggering an O(N) capacity resize step |

---

# Operation 1 — `s.charAt(i)`

## Verdict
- **Time Complexity:** `O(1)` — Constant
- **Space Complexity:** `O(1)` — Constant

---

## Internal Memory Layout

A Java `String` stores its characters in a backing `byte[]` (Java 9+ compact strings) or `char[]` (Java 8 and earlier). The array lives at a fixed memory address on the heap. Each character occupies a known, fixed number of bytes.

```
String s = "JAVA";

Heap Memory:
┌─────────────────────────────────────────────┐
│  String object                              │
│  ┌─────────────────────────────────────────┐│
│  │ char[] value = ['J','A','V','A']        ││
│  │ baseAddress  = 0x7F00                   ││
│  └─────────────────────────────────────────┘│
└─────────────────────────────────────────────┘

Memory layout of value[]:
Address:  0x7F00  0x7F02  0x7F04  0x7F06
Char:       'J'    'A'     'V'     'A'
Index:       0      1       2       3
```

---

## Why O(1)? — The Pointer Arithmetic Explanation

To access `s.charAt(i)`, the JVM performs a **single arithmetic operation**:

```
target_address = baseAddress + (i × charSize)
```

For `s.charAt(2)`:
```
target_address = 0x7F00 + (2 × 2 bytes) = 0x7F04 → reads 'V'
```

This is a **direct pointer offset calculation** — no iteration, no searching, no conditional branching. The CPU reaches the character in a single memory dereference, regardless of whether the string has 4 characters or 4 million characters.

```java
String s = "JAVA";

// All three execute in identical, constant time
char c0 = s.charAt(0); // 'J' — reads address: base + 0
char c2 = s.charAt(2); // 'V' — reads address: base + 4
char c3 = s.charAt(3); // 'A' — reads address: base + 6
```

## Analogy

Think of the string's character array as a **row of numbered mailboxes** in an apartment building. To reach mailbox #42, you don't walk past boxes 1 through 41 — you go directly to the 42nd slot. The index *is* the address. That's O(1).

---

# Operation 2 — `s.length()`

## Verdict
- **Time Complexity:** `O(1)` — Constant
- **Space Complexity:** `O(1)` — Constant

---

## Internal Memory Layout

The `String` class stores the character count as a **pre-computed, cached primitive `int` field** named `count` (or derived from `value.length` in modern JVMs). This field is set exactly once during construction and never changes (because strings are immutable).

```
String object on heap:
┌──────────────────────────────┐
│  char[] value  → [...]       │  ← reference to backing array
│  int    count  = 11          │  ← pre-stored length
│  int    hash   = 0           │  ← lazy hash cache
└──────────────────────────────┘
```

---

## Why O(1)? — The Cached Field Explanation

When you call `s.length()`, the JVM simply **reads a single integer field** from the String object's memory layout. There is no counting loop, no traversal of the character array, and no computation of any kind.

```java
// Equivalent to what the JVM does internally:
public int length() {
    return this.count; // Single field read — one CPU instruction
}
```

```java
String s = "Hello World";

// Calling length() 1 time or 1 million times — same cost
int n = s.length(); // 11 — reads the cached int, done
```

## The Danger of Calling `length()` in Loop Conditions

Because `length()` is O(1), this is safe — but it's important to understand *why*:

```java
// This is perfectly fine — length() is O(1), not O(N)
for (int i = 0; i < s.length(); i++) {
    // s.length() is re-evaluated each iteration but costs O(1) every time
}
```

## Analogy

Think of `length()` as reading the **page count printed on the cover of a book**. You don't flip through every page and count them — the answer is already written there. The JVM pre-computes and stores this value at birth.

## Best Practice: The "Cache the Length" Habit

Because it is easy to accidentally transition a piece of code from a fast (O(1)) property to a slow (O(N)) property, many developers practice caching the length as a habit:

```java
// Safest habit: Evaluate once, store it, loop over the constant
int n = s.length(); 
for (int i = 0; i < n; i++) {
    // n never changes, completely safe regardless of data structure
}
```

---

# Operation 3 — `s.substring(i, j)`

## Verdict
- **Time Complexity:** `O(k)` where `k = j - i`
- **Space Complexity:** `O(k)` where `k = j - i`

---

## Internal Memory Layout

In **Java 7u6 and earlier**, `substring` was O(1) — it reused the original backing array and just stored a new offset + count. This caused a **memory leak** (the full original array stayed alive even when the substring was tiny), so this was removed.

From **Java 7u6 onwards**, `substring` always allocates a **brand-new array** and copies exactly the `k` requested characters into it.

```
Original String s = "Hello World"   (length = 11)
                     0123456789...

s.substring(6, 11)  →  "World"       (k = 5 characters)

Before (old Java ≤ 7u5 — shared array):         After (modern Java — new array):
┌──────────────────────────────┐                ┌──────────────┐
│ char[] value = "Hello World" │                │ char[] value │
│ offset = 6                   │                │  = "World"   │
│ count  = 5                   │                │ count = 5    │
└──────────────────────────────┘                └──────────────┘
  ↑ Entire original array kept alive               ↑ Only 5 chars, fresh copy
```

---

## Why O(k)? — The Memory Copy Explanation

`substring(i, j)` must perform two O(k) operations:

1. **Allocate** a new `char[]` of size `k = j - i` on the heap.
2. **Copy** exactly `k` characters from the original array starting at offset `i`.

```java
// Equivalent internal implementation:
public String substring(int beginIndex, int endIndex) {
    int length = endIndex - beginIndex;       // k = j - i
    char[] result = new char[length];         // O(k) — allocate
    System.arraycopy(value, beginIndex,       // O(k) — copy k characters
                     result, 0, length);
    return new String(result);
}
```

```java
String s = "Hello World";    // N = 11

String sub1 = s.substring(0, 1);   // k=1  — O(1) copy, 1 char
String sub2 = s.substring(0, 6);   // k=6  — O(6) copy, 6 chars
String sub3 = s.substring(0, 11);  // k=11 — O(11) copy, 11 chars (full copy)
```

The larger the slice you request, the more characters must be copied — cost scales linearly with `k`.

## Analogy

`substring` is like **photocopying a specific range of pages from a book**. You must physically touch and reproduce every page in the requested range. Copying 5 pages takes 5 units of work; copying 500 pages takes 500 units. The work is proportional to how much you copy — O(k).

---

---

# Operation 4 — `s1.equals(s2)`

## Verdict
- **Time Complexity:** `O(min(n, m))` where `n = s1.length()`, `m = s2.length()`
- **Space Complexity:** `O(1)` — Constant

---

## Why O(min(n, m))? — The Two-Phase Check Explanation

Java's `String.equals()` is carefully implemented with an **early exit length filter** before doing any character work:

### Phase 1 — Length Filter: O(1)

```java
// Step 1: Immediate rejection if lengths differ
if (this.length() != other.length()) return false;
```

If the lengths differ, no character needs to be inspected. The strings provably cannot be equal. This O(1) filter eliminates the most common inequality case instantly.

### Phase 2 — Sequential Character Scan: O(min(n, m))

Only if lengths match does Java scan characters one by one, stopping the moment a mismatch is found:

```java
// Step 2: Scan until mismatch or full match
for (int i = 0; i < length; i++) {
    if (this.charAt(i) != other.charAt(i)) return false; // Early exit
}
return true;
```

```java
// Case A: lengths differ — O(1), returns immediately at length check
"hello".equals("hi");         // false — 5 ≠ 2, no characters compared

// Case B: first char differs — O(1) effectively
"apple".equals("zebra");      // false — 'a' ≠ 'z', stops after 1 comparison

// Case C: strings are equal — O(N), must scan every character
"hello".equals("hello");      // true — all 5 characters verified

// Case D: differ only at last char — O(N), scans all the way to the end
"abcde".equals("abcdX");      // false — compares 4 chars before finding mismatch at index 4
```

The worst case is **O(min(n, m))** — when strings match all the way through or differ only at the very last character. Since Phase 1 guarantees `n == m` before Phase 2 begins, the bound simplifies to O(n) in the equal-length case.

## Space: O(1)

`equals` allocates **no extra memory**. It reads both strings' backing arrays in-place, comparing one character at a time using only a loop counter. No temporary arrays, no copies, no intermediate strings.

## Analogy

`equals` is like comparing two submitted exam papers word by word. First, you check the page count — if they differ, you immediately know they are different papers (O(1) rejection). If they have the same page count, you read through both simultaneously and stop the moment you spot the first difference. In the best case you stop on page 1; in the worst case you read every page of both papers.

---

---

# Operation 5 — `String + String` (Concatenation)

## Verdict
- **Time Complexity:** `O(n + m)` where `n = s1.length()`, `m = s2.length()`
- **Space Complexity:** `O(n + m)`

---

## What the `+` Operator Actually Does Internally

The `+` operator on strings is **syntactic sugar**. The Java compiler rewrites it under the hood into a `StringBuilder` construction sequence at compile time:

```java
// What you write:
String result = s1 + s2;

// What the compiler generates (Java 8 and earlier):
String result = new StringBuilder()   // Step 1: Create builder
    .append(s1)                        // Step 2: Copy s1's n characters
    .append(s2)                        // Step 3: Copy s2's m characters
    .toString();                       // Step 4: Copy (n+m) characters into new String
```

> **Java 9+ Note:** The compiler uses `invokedynamic` with `StringConcatFactory` instead of explicit `StringBuilder`, but the fundamental O(n + m) cost remains identical — characters must still be read and written.

---

## Step-by-Step Cost Breakdown

```
s1 = "Hello "    (n = 6 characters)
s2 = "World"     (m = 5 characters)

Step 1 — Create StringBuilder:           O(1)
  Internal buffer: [_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ ]  (16 default slots)

Step 2 — append(s1):                     O(n) = O(6)
  Copy 6 chars from s1 into buffer:
  [ H  e  l  l  o     _  _  _  _  _  _  _  _  _  _ ]

Step 3 — append(s2):                     O(m) = O(5)
  Copy 5 chars from s2 into buffer:
  [ H  e  l  l  o     W  o  r  l  d  _  _  _  _  _ ]

Step 4 — toString():                     O(n + m) = O(11)
  Allocate new char[11], copy 11 chars:
  New String: "Hello World"

Total work: O(1) + O(n) + O(m) + O(n+m) = O(n + m)
Total new memory: O(n + m) for the final String object
```

---

## The Loop Anti-Pattern — Why `+` in Loops is O(N²)

This is the most critical practical consequence of understanding `+` concatenation:

```java
String result = "";
String[] words = {"one", "two", "three", ..., "thousand"}; // N words

// BAD — O(N²) time
for (String word : words) {
    result = result + word;
    // Each iteration: copies ALL previous characters + new word
    // Iteration 1: copy 3 chars
    // Iteration 2: copy 6 chars
    // Iteration 3: copy 9 chars
    // ...
    // Iteration N: copy ~N*avg_len chars
    // Total: 3 + 6 + 9 + ... ≈ O(N²)
}

// GOOD — O(N) time using StringBuilder
StringBuilder sb = new StringBuilder();
for (String word : words) {
    sb.append(word); // O(1) amortized per append
}
String result = sb.toString(); // O(N) one-time copy
```

## Analogy

`String + String` is like **merging two printed documents into one**. You must photocopy every page of Document 1 (O(n) pages) and every page of Document 2 (O(m) pages) into a new combined document (O(n+m) pages total). Doing this once is fine. Doing it inside a loop of N iterations means re-photocopying a growing document on every step — quadratic work.

---

---

# Operation 6 — `sb.append()` (StringBuilder)

## Verdict
- **Time Complexity:** Amortized `O(1)` per call
- **Space Complexity:** `O(1)` per call (amortized)

---

## Internal Memory Layout — The Expandable Buffer

`StringBuilder` maintains an internal **resizable `char[]` buffer** with two key properties:

- **`count`** — number of characters currently stored (logical size)
- **`capacity`** — total allocated slots in the buffer (physical size)

```
StringBuilder sb = new StringBuilder(); // Default capacity = 16

Internal state:
┌──────────────────────────────────────────────────────────┐
│ char[] buffer = [_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _]       │
│                  0 1 2 3 4 5 6 7 8 9 ...            15   │
│ count    = 0   (characters stored)                       │
│ capacity = 16  (buffer slots available)                  │
└──────────────────────────────────────────────────────────┘
```

---

## The Two-Path Execution of `append()`

Every call to `sb.append(str)` follows one of two paths:

### Path A — Buffer Has Space: O(1)

If `count + str.length() <= capacity`, the new characters are written directly into the unused buffer slots:

```
sb.append("Hello");   // str.length() = 5, count=0, capacity=16  → 5 ≤ 16 ✅

Before:  [_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _]   count=0
After:   [H e l l o _ _ _ _ _ _ _ _ _ _ _]   count=5

Cost: O(5) to copy 5 characters — O(k) where k = length of appended string.
For single-char appends (the common case): O(1).
```

### Path B — Buffer is Full: O(N) resize, then O(1) amortized

If the buffer is too small, `StringBuilder` **doubles its capacity**, copies all existing content into the new larger array, then writes the new characters:

```
// Buffer is full at capacity=16, count=16
sb.append("!"); // Triggers resize

Step 1 — Allocate new buffer of size 2 × 16 = 32:
  new char[32]

Step 2 — Copy all 16 existing characters into new buffer: O(N)
  [H e l l o   W o r l d   J a v a _ _ _ ...]

Step 3 — Write new character '!' at index 16: O(1)
  count = 17, capacity = 32

Total cost this call: O(N)
```

---

## Why "Amortized O(1)"? — The Doubling Strategy Proof

The doubling strategy ensures resize events become **exponentially rarer** as the buffer grows:

```
Capacity history:  16 → 32 → 64 → 128 → 256 → ...

Resize costs:
  At capacity 16:   copy 16  chars  (resize #1)
  At capacity 32:   copy 32  chars  (resize #2)
  At capacity 64:   copy 64  chars  (resize #3)
  At capacity 128:  copy 128 chars  (resize #4)
  At capacity 256:  copy 256 chars  (resize #5)

Total copy work after N appends:
  16 + 32 + 64 + ... + N  =  2N  (geometric series)

Average cost per append = 2N total work / N appends = O(1) amortized
```

Although individual resize calls cost O(N), they are rare enough that **the average cost per append across the entire sequence remains constant**. This is the formal definition of amortized O(1).

```java
StringBuilder sb = new StringBuilder(); // capacity=16

// Appends 1–16: Path A, O(1) each, no resize
for (int i = 0; i < 16; i++) sb.append('x');

// Append 17: Path B, O(16) resize → copies 16 chars, new capacity=32
sb.append('x');

// Appends 18–32: Path A, O(1) each, no resize
for (int i = 0; i < 15; i++) sb.append('x');

// Append 33: Path B, O(32) resize → copies 32 chars, new capacity=64
sb.append('x');

// Total: ~3N work for N appends → O(N) total → O(1) amortized per append
```

---

## Visual: Cost Per Append Over Time

```
Cost
│
O(N)│         *               *               *
    │
O(1)│  * * * * * * * * * * * * * * * * * * * *
    └────────────────────────────────────────────→ Append #
         1   2   3   ...  16  17  ...  32  33

*  = rare O(N) resize spike
•  = common O(1) normal append

Average across all appends = O(1) amortized
```

## Analogy

`sb.append()` is like building a **city that doubles its land area** every time it runs out of space. Most days, new houses are built instantly on existing free land (O(1)). Occasionally, the city must relocate everyone to a new, twice-as-large territory (O(N) move). But because each relocation doubles the available land, these expensive moves become rarer and rarer. Averaged across all building days, the per-house cost remains constant.

---

---

# Summary — The Unified Mental Model

| Operation | The Core Action | Why That Complexity |
|---|---|---|
| `s.charAt(i)` | Single memory address calculation: `base + i × size` | No iteration needed — index *is* the address |
| `s.length()` | Read one pre-stored `int` field | Cached at construction, never recomputed |
| `s.substring(i,j)` | Allocate + copy `k = j - i` characters | Must physically reproduce every character in the slice |
| `s1.equals(s2)` | O(1) length filter → O(N) character scan (stops early) | Length filter eliminates fast; characters must be verified one by one |
| `String + String` | Create StringBuilder → append n chars → append m chars → copy n+m chars | Characters of both strings must be read and written into a new block |
| `sb.append()` | Write into buffer (O(1)) or resize-then-write (O(N) rare) | Doubling strategy amortizes expensive resizes across many cheap appends |

> **The common thread:** Operations that read a pre-stored scalar are O(1). Operations that must touch every character are O(N) or O(k). Operations that occasionally do O(N) work but rarely enough to average out are amortized O(1).


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
