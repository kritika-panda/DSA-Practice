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

# 1. `sb.append(str)`

## Complexity

- **Time:** Amortized **O(k)**
- **Space:** **O(k)**

Where `k = str.length()`.

---

## Normal Case

```java
StringBuilder sb = new StringBuilder("abc");
sb.append("xyz");
```

### Before

```text
[a][b][c][_][_][_]
length = 3
```

### After

```text
[a][b][c][x][y][z]
length = 6
```

Appending simply copies `k` characters to the end.

### Copy Cost

```text
x
y
z
```

Total work = `k`

Therefore:

```text
Time = O(k)
```

---

## Why Not O(1)?

Because Java must copy every character of the appended string.

Example:

```java
sb.append("hello");
```

Needs 5 writes.

```text
h e l l o
```

So:

```text
O(5)
```

Generalized:

```text
O(k)
```

---

## Capacity Expansion Case

Suppose the buffer is full:

```text
[a][b][c]
capacity = 3
```

Append:

```java
sb.append("d");
```

Java creates a larger array.

```text
Old:
[a][b][c]

↓

New:
[a][b][c][d][_][_][_]
```

### Steps

1. Allocate bigger array
2. Copy old characters (`n`)
3. Copy new string (`k`)

### Cost

```text
O(n + k)
```

---

## Why "Amortized O(k)"?

Resizing doesn't happen every append.

Example capacities:

```text
16 → 34 → 70 → 142 ...
```

Many appends occur between resizes.

The occasional expensive copy is spread across many cheap operations.

Therefore:

```text
Amortized Time = O(k)
```

---

## Space Complexity

Appending `"hello"`:

```text
5 new characters added
```

Additional storage:

```text
O(k)
```

---

# 2. `sb.insert(idx, str)`

## Complexity

```text
Time = O(n)
Space = O(k)
```

Where:

```text
n = current StringBuilder length
k = inserted string length
```

---

## Example

```java
StringBuilder sb = new StringBuilder("abcdef");
sb.insert(2, "XYZ");
```

### Before

```text
0 1 2 3 4 5
a b c d e f
```

---

### Need Space at Index 2

Characters from index 2 onward move right.

```text
c d e f
```

Shift:

```text
a b _ _ _ c d e f
```

Then insert:

```text
a b X Y Z c d e f
```

---

## How Much Work?

### Shift Elements

```text
c d e f
```

4 elements moved.

Worst case:

```java
sb.insert(0, str);
```

Everything shifts.

```text
n elements move
```

Cost:

```text
O(n)
```

---

### Insert New String

Need to write:

```text
k characters
```

Cost:

```text
O(k)
```

Total:

```text
O(n + k)
```

Since usually:

```text
k ≤ n
```

We simplify to:

```text
O(n)
```

---

## Space Complexity

Need space for inserted characters:

```text
XYZ
```

Extra storage:

```text
O(k)
```

---

# 3. `sb.delete(i, j)`

## Complexity

```text
Time = O(n)
Space = O(1)
```

---

## Example

```java
StringBuilder sb = new StringBuilder("abcdefgh");
sb.delete(2, 5);
```

Delete:

```text
c d e
```

---

### Before

```text
a b c d e f g h
```

### After Removal

Need to close the gap.

Move:

```text
f g h
```

Left:

```text
a b f g h
```

---

## Work Done

Trailing characters are shifted.

Worst case:

```java
sb.delete(0, 1);
```

Move:

```text
n - 1 characters
```

Cost:

```text
O(n)
```

---

## Why O(1) Space?

No extra array is created.

Java merely overwrites positions and updates length.

```text
same char[]
```

Thus:

```text
Space = O(1)
```

---

# 4. `sb.reverse()`

## Complexity

```text
Time = O(n)
Space = O(1)
```

---

## Example

```java
abcdef
```

---

### Two-Pointer Approach

```text
L       R
a b c d e f
```

Swap:

```text
f b c d e a
```

Move pointers:

```text
  L   R
f b c d e a
```

Swap:

```text
f e c d b a
```

Move:

```text
    L R
f e c d b a
```

Swap:

```text
f e d c b a
```

Done.

---

## Number of Swaps

Only:

```text
n / 2 swaps
```

Which equals:

```text
O(n)
```

---

## Why O(1) Space?

Only two indexes:

```java
int left;
int right;
```

No new array.

Everything happens inside the same buffer.

Therefore:

```text
Space = O(1)
```

---

# 5. `sb.toString()`

## Complexity

```text
Time = O(n)
Space = O(n)
```

---

## Example

```java
StringBuilder sb = new StringBuilder("hello");
String s = sb.toString();
```

---

## Important Point

`StringBuilder` is mutable.

```text
Can change
```

`String` is immutable.

```text
Cannot change
```

Therefore Java **cannot share the same buffer**.

A brand-new `String` object must be created.

---

### Step 1: Allocate New Memory

```text
StringBuilder:
[h][e][l][l][o]
```

Create:

```text
String:
[_][_][_][_][_]
```

Cost:

```text
O(n) space
```

---

### Step 2: Copy Characters

```text
h -> h
e -> e
l -> l
l -> l
o -> o
```

Need to copy all `n` characters.

Cost:

```text
O(n)
```

---

# Quick Interview Summary

| Operation | Time | Space | Why? |
|------------|--------|--------|--------|
| `append(str)` | Amortized O(k) | O(k) | Copy k characters; occasional resize copies old array |
| `insert(idx, str)` | O(n) | O(k) | Shift suffix right + copy inserted string |
| `delete(i, j)` | O(n) | O(1) | Shift trailing characters left |
| `reverse()` | O(n) | O(1) | In-place two-pointer swaps |
| `toString()` | O(n) | O(n) | Create new immutable `String` and copy all characters |

# Easy Memory Trick

Think of `StringBuilder` as an **array**:

- **Append at end** → usually cheap → **Amortized O(k)**
- **Insert in middle** → shift right → **O(n)**
- **Delete in middle** → shift left → **O(n)**
- **Reverse** → swap ends → **O(n)**
- **Convert to String** → deep copy → **O(n)** time and space.
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
