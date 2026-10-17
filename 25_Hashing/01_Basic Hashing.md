# Basic Hashing Theory

## What Is Hashing?

**Hashing** is a computational technique that transforms an arbitrary input key — an integer, a string, an object, or any data — into a fixed-size integer index called a **hash code**, using a deterministic function called a **hash function**. This hash code is then used as a direct address into a compact array structure called a **hash table**, enabling near-instantaneous storage and retrieval of associated values.

The central promise of hashing is breathtaking in its simplicity:

> Map any key to a slot in constant time. Look it up in constant time. Delete it in constant time.

Without hashing, finding a value by key inside an unsorted collection costs \(O(N)\) linear time per search — you must inspect every element. Sorting helps, bringing search down to \(O(\log N)\) via binary search — but insertions become expensive. Hashing shatters both barriers by achieving **\(O(1)\) average-case** performance for all three core operations: **insert**, **search**, and **delete**.

---

# Core Components of a Hashing System

A complete hashing system is composed of three interlocking parts:

| Component | Role |
|---|---|
| **Key Universe** | The set of all possible input keys (e.g., all integers, all strings) |
| **Hash Function** | Maps a key from the universe to a bounded integer index \([0, M)\) |
| **Hash Table** | A fixed-size array of \(M\) slots storing key-value pairs |

The quality of the hash function and the strategy used to resolve conflicts determine the real-world performance of the system.

---

# Hash Functions

## Definition

A **hash function** \(h\) maps a key \(k\) from a large key universe \(\mathcal{U}\) to a slot index in the range \([0, M)\) where \(M\) is the table size:

$$h : \mathcal{U} \rightarrow \{0, 1, 2, \ldots, M-1\}$$

## Properties of a Good Hash Function

A well-designed hash function must satisfy four structural properties:

1. **Determinism:** The same key always produces the same hash code. \(h(k)\) is a pure function with no side effects or randomness visible across calls.

2. **Uniformity:** Keys are distributed as evenly as possible across the \(M\) slots. Uneven distribution creates **clustering** — some slots overflow while others stay empty, degrading average-case performance toward \(O(N)\).

3. **Efficiency:** The hash function must compute in \(O(1)\) time (or \(O(L)\) for string keys of length \(L\), treated as a small constant relative to \(N\)).

4. **Avalanche Effect:** Small changes in the input key produce dramatically different hash outputs. A 1-bit flip in the key should flip approximately half the bits in the hash code — preventing nearby keys from clustering into the same slots.

---

## Common Hash Function Designs

### 1. Division Method (Integer Keys)

The simplest and most widely used hash function for integer keys:

$$h(k) = k \mod M$$

**Design Rule:** Choose \(M\) to be a **prime number** far from any power of 2. Prime table sizes reduce systematic clustering caused by keys that are multiples of common factors.

```java
int hash(int key, int M) {
    return key % M;
}
```

**Weakness:** If many keys share a common factor with \(M\), they all collide into the same small set of slots. Choosing a prime \(M\) mitigates but does not eliminate this risk.

---

### 2. Multiplication Method (Integer Keys)

A more robust alternative that decouples the hash output from the table size \(M\):

$$h(k) = \lfloor M \cdot (k \cdot A \mod 1) \rfloor$$

Where \(A\) is a carefully chosen constant in the range \((0, 1)\). Knuth recommends:

$$A \approx \frac{\sqrt{5} - 1}{2} \approx 0.6180339887 \quad \text{(golden ratio conjugate)}$$

```java
int hash(int key, int M) {
    double A = 0.6180339887;
    return (int) (M * ((key * A) % 1));
}
```

**Advantage:** The value of \(M\) does not critically affect output quality — non-prime table sizes work well. The golden ratio constant maximizes spread across the slot range.

---

### 3. Polynomial Rolling Hash (String Keys)

For string keys of length \(L\), treat each character as a coefficient in a polynomial evaluated at a prime base \(p\):

$$h(s) = \left( s[0] \cdot p^{L-1} + s[1] \cdot p^{L-2} + \cdots + s[L-1] \cdot p^{0} \right) \mod M$$

Using **Horner's Method** to evaluate in \(O(L)\) without computing large powers:

$$h(s) = \bigl(\ldots\bigl((s[0] \cdot p + s[1]) \cdot p + s[2]\bigr) \cdot p + \cdots + s[L-1]\bigr) \mod M$$

```java
int hash(String s, int M) {
    int p = 31;       // Prime base (use 31 for lowercase letters, 37 for mixed-case)
    long h = 0;
    long power = 1;
    for (int i = 0; i < s.length(); i++) {
        h = (h + (s.charAt(i) - 'a' + 1) * power) % M;
        power = (power * p) % M;
    }
    return (int) h;
}
```

**Design Rule:** Choose both \(p\) and \(M\) as large primes. Common production choices: \(p = 31\) or \(p = 1000000007\), \(M = 10^9 + 9\).

---

### 4. Java's Built-in `hashCode()`

Java provides a `hashCode()` method on every object. For integers, `Integer.hashCode(x) = x`. For strings, Java uses a variant of polynomial rolling hash with base 31:

```java
// Java String.hashCode() — equivalent implementation
int javaStringHash(String s) {
    int h = 0;
    for (char c : s.toCharArray()) {
        h = 31 * h + c;
    }
    return h;
}
// Then apply: index = (hash & 0x7FFFFFFF) % M   (mask sign bit before mod)
```

**Critical Rule:** Always apply `Math.abs(key.hashCode()) % M` or use the bitmask `(hashCode & 0x7FFFFFFF) % M` when converting to a table index — raw `hashCode()` can return negative values in Java.

---

# Hash Tables

## Structure

A **hash table** is a fixed-size array \(T\) of \(M\) slots where each slot can store one or more key-value entries. Given a key \(k\), the target slot is computed as \(T[h(k)]\).

```text
Key Universe U:                Hash Table T (size M = 7):
┌────────────┐                 ┌──────────────────┐
│  "apple"   │──h("apple")=2──▶│ Slot 0:  (empty) │
│  "banana"  │──h("banana")=5─▶│ Slot 1:  (empty) │
│  "cherry"  │──h("cherry")=2─▶│ Slot 2:  "apple" │ ← COLLISION with "cherry"!
│  "date"    │──h("date")=0───▶│ Slot 3:  (empty) │
└────────────┘                 │ Slot 4:  (empty) │
                               │ Slot 5: "banana" │
                               │ Slot 6:  (empty) │
                               └──────────────────┘
```

---

## The Collision Problem

A **collision** occurs when two distinct keys \(k_1 \neq k_2\) map to the same slot:

$$h(k_1) = h(k_2)$$

Since the key universe \(\mathcal{U}\) is typically far larger than \(M\), collisions are **mathematically inevitable** by the **Pigeonhole Principle** — if \(|\mathcal{U}| > M\), at least two keys must share a slot.

Choosing \(M\) large and \(h\) uniform minimizes collision frequency, but a collision resolution strategy is always required for correctness.

---

# Collision Resolution Strategies

There are two primary families of collision resolution:

| Family | Strategy | Storage Location |
|---|---|---|
| **Separate Chaining** | Linked list (or BST) at each slot | Outside the table (external) |
| **Open Addressing** | Probe sequence inside the table | Inside the table (internal) |

---

## Strategy 1: Separate Chaining

### Concept

Each slot in the hash table holds the **head of a linked list**. All keys that hash to the same slot are stored as nodes in that slot's list. Collisions are resolved by simply appending to the chain.

```text
Hash Table T (Separate Chaining, M = 5):

Slot 0: → ["date", 40] → null
Slot 1: → null
Slot 2: → ["apple", 10] → ["cherry", 30] → null   ← both hash to 2
Slot 3: → null
Slot 4: → ["banana", 20] → null
```

### Operations

```text
Insert(k, v):
    i = h(k)
    Prepend (k, v) to T[i]'s linked list    // O(1)

Search(k):
    i = h(k)
    Traverse T[i]'s linked list for key k   // O(chain length)

Delete(k):
    i = h(k)
    Remove node with key k from T[i]'s list // O(chain length)
```

### Java Implementation

```java
import java.util.LinkedList;

class SeparateChainingHashTable {
    private static final int M = 16; // Table size (use a prime in production)
    private LinkedList<int[]>[] table; // Each slot: list of [key, value] pairs

    @SuppressWarnings("unchecked")
    public SeparateChainingHashTable() {
        this.table = new LinkedList[M];
        for (int i = 0; i < M; i++) {
            this.table[i] = new LinkedList<>();
        }
    }

    // Hash function: division method
    private int hash(int key) {
        return Math.abs(key) % M;
    }

    // Insert or update key-value pair
    public void put(int key, int value) {
        int i = hash(key);
        for (int[] pair : table[i]) {
            if (pair[0] == key) {
                pair[1] = value; // Update existing key in chain
                return;
            }
        }
        table[i].addFirst(new int[]{key, value}); // Prepend new entry
    }

    // Search for a key — returns -1 if not found
    public int get(int key) {
        int i = hash(key);
        for (int[] pair : table[i]) {
            if (pair[0] == key) return pair[1];
        }
        return -1; // Key not present
    }

    // Delete a key from its chain
    public void remove(int key) {
        int i = hash(key);
        table[i].removeIf(pair -> pair[0] == key);
    }
}
```

### Performance

| Operation | Average Case | Worst Case |
|---|---|---|
| Insert | \(O(1)\) | \(O(N)\) |
| Search | \(O(1 + \alpha)\) | \(O(N)\) |
| Delete | \(O(1 + \alpha)\) | \(O(N)\) |

Where \(\alpha = \frac{N}{M}\) is the **load factor** (defined below). Worst case occurs when all \(N\) keys hash to the same slot, degenerating into a linear scan of a single chain.

---

## Strategy 2: Open Addressing — Linear Probing

### Concept

All key-value pairs are stored **directly inside the table array** — no external chains. When a collision occurs at slot \(i\), the algorithm probes consecutive slots \(i+1, i+2, \ldots\) (wrapping around modulo \(M\)) until an empty slot is found:

$$h(k, j) = (h(k) + j) \mod M \quad \text{for probe step } j = 0, 1, 2, \ldots$$

```text
Hash Table T (Linear Probing, M = 7):

Insert "apple" (h=2): place at slot 2.
Insert "cherry" (h=2): slot 2 occupied → probe slot 3 → place at slot 3.
Insert "fig" (h=3): slot 3 occupied → probe slot 4 → place at slot 4.

Slot 0: (empty)
Slot 1: (empty)
Slot 2: "apple"
Slot 3: "cherry"   ← displaced 1 step
Slot 4: "fig"      ← displaced 1 step (due to "cherry"'s displacement)
Slot 5: (empty)
Slot 6: (empty)
```

### Primary Clustering Problem

Linear probing suffers from **primary clustering** — long contiguous runs of occupied slots form and grow, because any key hashing into the middle of a cluster extends it by one more slot. Longer clusters attract more keys, making them grow faster in a self-reinforcing feedback loop that degrades performance.

### Java Implementation

```java
class LinearProbingHashTable {
    private static final int M = 16;
    private int[] keys;
    private int[] values;
    private boolean[] occupied;

    public LinearProbingHashTable() {
        keys = new int[M];
        values = new int[M];
        occupied = new boolean[M];
    }

    private int hash(int key) {
        return Math.abs(key) % M;
    }

    public void put(int key, int value) {
        int i = hash(key);
        // Probe forward until an empty slot or matching key is found
        while (occupied[i] && keys[i] != key) {
            i = (i + 1) % M; // Linear step: advance by 1
        }
        keys[i] = key;
        values[i] = value;
        occupied[i] = true;
    }

    public int get(int key) {
        int i = hash(key);
        while (occupied[i]) {
            if (keys[i] == key) return values[i];
            i = (i + 1) % M;
        }
        return -1; // Key not found
    }
}
```

---

## Strategy 3: Open Addressing — Quadratic Probing

### Concept

Instead of probing linearly by 1 each step, quadratic probing advances by **quadratically increasing offsets** to reduce primary clustering:

$$h(k, j) = \bigl(h(k) + c_1 \cdot j + c_2 \cdot j^2\bigr) \mod M \quad \text{for probe step } j = 0, 1, 2, \ldots$$

A common choice is \(c_1 = 0\), \(c_2 = 1\), giving probe positions: \(h(k), h(k)+1, h(k)+4, h(k)+9, h(k)+16, \ldots\)

```text
Insert "cherry" (h=2, M=7):
  j=0: slot 2 occupied
  j=1: slot (2 + 1) % 7 = 3 → place at slot 3
```

### Secondary Clustering Problem

Quadratic probing eliminates primary clustering but introduces **secondary clustering** — all keys that hash to the same initial slot \(i\) follow the exact same probe sequence. They do not interfere with each other during insertion, but during search they share the same collision chain.

**Guarantee:** For a table of prime size \(M\) with load factor \(\alpha < 0.5\), quadratic probing is guaranteed to find an empty slot within \(\lceil M/2 \rceil\) probes.

### Java Implementation

```java
class QuadraticProbingHashTable {
    private static final int M = 17; // Must be prime for guaranteed slot coverage
    private int[] keys;
    private int[] values;
    private boolean[] occupied;

    public QuadraticProbingHashTable() {
        keys = new int[M];
        values = new int[M];
        occupied = new boolean[M];
    }

    private int hash(int key) {
        return Math.abs(key) % M;
    }

    public void put(int key, int value) {
        int i = hash(key);
        int j = 0;
        // Quadratic probe: advance by j^2 offsets at each step
        while (occupied[(i + j * j) % M] && keys[(i + j * j) % M] != key) {
            j++;
        }
        int slot = (i + j * j) % M;
        keys[slot] = key;
        values[slot] = value;
        occupied[slot] = true;
    }

    public int get(int key) {
        int i = hash(key);
        int j = 0;
        while (occupied[(i + j * j) % M]) {
            if (keys[(i + j * j) % M] == key) return values[(i + j * j) % M];
            j++;
        }
        return -1;
    }
}
```

---

## Strategy 4: Open Addressing — Double Hashing

### Concept

Double hashing eliminates both primary and secondary clustering by computing the **probe step size itself using a second independent hash function** \(h_2(k)\):

$$h(k, j) = \bigl(h_1(k) + j \cdot h_2(k)\bigr) \mod M \quad \text{for probe step } j = 0, 1, 2, \ldots$$

Since \(h_2(k)\) varies by key, two keys colliding at \(h_1(k)\) follow completely different probe sequences — eliminating clustering at the structural level.

**Design Rules:**
- \(h_2(k)\) must **never return 0** (otherwise the probe sequence degenerates to a single slot).
- \(h_2(k)\) and \(M\) must be **coprime** so the probe sequence visits all \(M\) slots before repeating.
- A common safe choice: \(h_2(k) = R - (k \mod R)\) where \(R\) is a prime smaller than \(M\).

```text
h1(k) = k % 7
h2(k) = 5 - (k % 5)     // R = 5

Insert key=22 (h1=1, h2=3):  probes → slot 1, slot 4, slot 0, slot 3, ...
Insert key=8  (h1=1, h2=2):  probes → slot 1, slot 3, slot 5, slot 0, ...
                                       ↑ same start, completely different sequence
```

### Java Implementation

```java
class DoubleHashingHashTable {
    private static final int M = 17; // Prime table size
    private static final int R = 13; // Secondary prime (R < M)
    private int[] keys;
    private int[] values;
    private boolean[] occupied;

    public DoubleHashingHashTable() {
        keys = new int[M];
        values = new int[M];
        occupied = new boolean[M];
    }

    // Primary hash function
    private int h1(int key) {
        return Math.abs(key) % M;
    }

    // Secondary hash function — determines probe step size, never returns 0
    private int h2(int key) {
        return R - (Math.abs(key) % R);
    }

    public void put(int key, int value) {
        int i = h1(key);
        int step = h2(key);
        int j = 0;
        while (occupied[(i + j * step) % M] && keys[(i + j * step) % M] != key) {
            j++;
        }
        int slot = (i + j * step) % M;
        keys[slot] = key;
        values[slot] = value;
        occupied[slot] = true;
    }

    public int get(int key) {
        int i = h1(key);
        int step = h2(key);
        int j = 0;
        while (occupied[(i + j * step) % M]) {
            if (keys[(i + j * step) % M] == key) return values[(i + j * step) % M];
            j++;
        }
        return -1;
    }
}
```

---

# Collision Resolution Strategy Comparison

| Strategy | Clustering | Memory | Cache Efficiency | Deletion Ease |
|---|---|---|---|---|
| Separate Chaining | None | Extra (linked nodes) | Low (pointer chasing) | Easy (unlink node) |
| Linear Probing | Primary | In-table | **High** (sequential memory) | Complex (tombstones) |
| Quadratic Probing | Secondary | In-table | Medium | Complex (tombstones) |
| Double Hashing | **None** | In-table | Medium | Complex (tombstones) |

> **Tombstone Problem:** Deleting from open-addressing tables requires marking slots as "deleted" (tombstones) rather than "empty" — otherwise, future searches terminate prematurely at the deleted slot and fail to find keys that probed past it.

---

# Load Factor

## Definition

The **load factor** \(\alpha\) quantifies how full the hash table currently is:

$$\alpha = \frac{N}{M}$$

Where \(N\) = number of stored key-value pairs, \(M\) = total number of table slots.

## Impact on Performance

The load factor is the single most important parameter governing hash table performance:

| Strategy | Recommended \(\alpha\) | Average Search Cost |
|---|---|---|
| Separate Chaining | \(\alpha \leq 1.0\) (can exceed 1) | \(1 + \frac{\alpha}{2}\) |
| Linear Probing | \(\alpha \leq 0.7\) | \(\frac{1}{2}\left(1 + \frac{1}{(1-\alpha)^2}\right)\) |
| Quadratic Probing | \(\alpha < 0.5\) | \(1 - \frac{\alpha}{2} - \frac{1}{2}\log(1-\alpha)\) |
| Double Hashing | \(\alpha \leq 0.8\) | \(\frac{1}{1-\alpha}\) |

As \(\alpha \rightarrow 1\) for open-addressing strategies, average probe lengths explode toward \(O(N)\) — the table effectively degenerates into a linear scan.

## Dynamic Resizing (Rehashing)

Production hash tables **dynamically resize** when \(\alpha\) exceeds a threshold (typically 0.7 for open addressing, 1.0 for chaining). The resize protocol:

```text
Rehash():
    oldTable = current table
    M_new = next prime > 2 * M_old
    Create new empty table of size M_new
    For each (key, value) in oldTable:
        Insert (key, value) into new table using h_new(key)
    Replace old table reference with new table
```

**Cost:** Rehashing costs \(O(N)\) time and triggers amortized \(O(1)\) insertions on average — because rehashing events become exponentially rarer as the table grows (doubling strategy).

---

# The Birthday Paradox and Expected Collisions

A counterintuitive result from probability theory governs collision frequency:

> In a hash table of size \(M\), after inserting only \(\approx \sqrt{M}\) keys, the probability of at least one collision exceeds **50%**.

This is the **Birthday Paradox** applied to hashing. For \(M = 365\) (days in a year), only 23 people need to share a room before a birthday collision becomes more likely than not.

**Implication for hash table design:** Even with a perfect uniform hash function, collisions appear far sooner than intuition suggests. Collision resolution is not an edge case — it is a **routine, expected event** that must be handled efficiently on every insertion-heavy workload.

$$P(\text{at least one collision after } n \text{ insertions}) \approx 1 - e^{-n(n-1)/(2M)}$$

---

# Complexity Summary

Let \(N\) = number of stored entries, \(M\) = table size, \(\alpha = N/M\) = load factor, \(L\) = string key length.

## Time Complexity

| Operation | Average Case | Worst Case | Condition |
|---|---|---|---|
| `put` (insert/update) | \(O(1)\) | \(O(N)\) | All keys collide |
| `get` (search) | \(O(1)\) | \(O(N)\) | All keys collide |
| `remove` (delete) | \(O(1)\) | \(O(N)\) | All keys collide |
| Rehash | \(O(N)\) | \(O(N)\) | Triggered at threshold |
| String key hashing | \(O(L)\) | \(O(L)\) | Per-character scan |

Average \(O(1)\) holds when \(\alpha\) stays below the recommended threshold and the hash function distributes keys uniformly.

## Space Complexity

| Strategy | Auxiliary Space |
|---|---|
| Separate Chaining | \(O(N + M)\) — \(M\) slot pointers + \(N\) linked nodes |
| Open Addressing | \(O(M)\) — all entries stored inside the table array |

---

# Real-World Applications of Hashing

| Domain | Application | Hash Use |
|---|---|---|
| **Programming Languages** | `HashMap`, `HashSet`, `dict`, `unordered_map` | Key-value store, set membership |
| **Database Systems** | Hash joins, index structures | Equi-join acceleration, index lookup |
| **Cryptography** | SHA-256, MD5, bcrypt | One-way fingerprinting, password storage |
| **Networking** | IP routing tables, ARP caches | Fast packet forwarding, address resolution |
| **Compilers** | Symbol tables for variable names | Identifier resolution in \(O(1)\) |
| **Caching** | CDN content routing, CPU L1/L2 caches | Content-addressable memory lookup |
| **Distributed Systems** | Consistent hashing (Dynamo, Cassandra) | Load-balanced partition routing |
| **Plagiarism Detection** | Rabin-Karp rolling hash | Substring fingerprint matching |
| **Blockchain** | Merkle trees, block headers | Tamper-evident chain verification |

---

# Key Takeaways

✅ Core fundamental paradigm powering nearly every modern data structure that requires fast lookups — `HashMap`, `HashSet`, database indexes, caches, compilers, and distributed key-value stores all rest on hashing foundations.

✅ **The Hash Function is the Critical Bottleneck:** A poorly designed hash function — one that clusters keys into a small subset of slots — collapses \(O(1)\) average performance to \(O(N)\) worst-case behavior. Uniform distribution is not optional; it is the entire premise on which the \(O(1)\) promise is built.

✅ **Load Factor is the Runtime Dial:** Keeping \(\alpha\) below the strategy-specific threshold (0.7 for linear probing, 0.5 for quadratic probing, 1.0 for chaining) is the single most impactful runtime optimization available after choosing a good hash function. Dynamic resizing enforces this threshold automatically.

✅ **Open Addressing vs. Chaining Trade-off:** Open addressing is **cache-friendly** (all data lives in a contiguous array — sequential memory access patterns align with CPU cache-line prefetching) but requires tombstone deletion and degrades sharply near \(\alpha = 1\). Separate chaining is more **deletion-friendly** and handles high load gracefully, but pointer-chasing across heap-allocated linked nodes defeats cache prefetching.

✅ **Double Hashing gives the best clustering behavior** among all open-addressing strategies and is the preferred choice when deletions are rare and cache performance is secondary to throughput uniformity.

✅ Directly drives real-world microarchitectures like **database query engine hash-join operators** (partitioning and probing tuples in \(O(N)\) instead of \(O(N^2)\) nested-loop joins), CPU branch predictor tables (hashing instruction addresses to stored branch outcomes for speculative execution), and content delivery network edge-router forwarding tables (resolving domain-to-server mappings in sub-millisecond constant time under billion-request traffic loads).

---

### Similar Problems and Applications

- Two Sum ([LeetCode 1](https://leetcode.com))
- Group Anagrams ([LeetCode 49](https://leetcode.com))
- Longest Consecutive Sequence ([LeetCode 128](https://leetcode.com))
- Subarray Sum Equals K ([LeetCode 560](https://leetcode.com))
- Find Duplicate Subtrees ([LeetCode 652](https://leetcode.com))
- Design HashMap ([LeetCode 706](https://leetcode.com))
- Design HashSet ([LeetCode 705](https://leetcode.com))
- Implement Rabin-Karp String Matching ([LeetCode 28](https://leetcode.com))
- Max Points on a Line ([LeetCode 149](https://leetcode.com))
- Detect Squares ([LeetCode 2013](https://leetcode.com))
