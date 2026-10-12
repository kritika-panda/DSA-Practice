# Hashing Collisions

## What is a Hash Collision?

A **hash collision** occurs when two different keys generate the same hash bucket/index in a hash table.

In other words:

```text
Different Keys
      ↓
 Same Bucket Index
```

Since a hash table has a finite number of buckets and an infinite number of possible keys, collisions are unavoidable.

---

# Example

Suppose:

```java
index = key % 10
```

and we insert:

```java
10
20
30
```

Then:

```java
10 % 10 = 0
20 % 10 = 0
30 % 10 = 0
```

All three keys map to:

```text
Bucket 0
```

Result:

```text
Bucket 0
│
├── 10
├── 20
└── 30
```

This is called a collision.

---

# Why Do Collisions Occur?

A hash function maps:

```text
Large Key Space
        ↓
Small Bucket Space
```

Example:

```text
1 Million Keys
100 Buckets
```

Many keys must share buckets.

According to the:

```text
Pigeonhole Principle
```

collisions are guaranteed.

---

# Visual Example

Capacity:

```text
5 Buckets
```

Keys:

```java
5, 10, 15, 20
```

Hash Function:

```java
hash = key % 5
```

Result:

```text
5  → Bucket 0
10 → Bucket 0
15 → Bucket 0
20 → Bucket 0
```

Table:

```text
Bucket 0 → [5,10,15,20]
Bucket 1 → []
Bucket 2 → []
Bucket 3 → []
Bucket 4 → []
```

---

# Collision Resolution Techniques

There are two major approaches:

```text
1. Separate Chaining
2. Open Addressing
```

---

# 1. Separate Chaining

Most popular approach.

Each bucket stores:

```text
Linked List
or
Tree
```

of entries.

Example:

```text
Bucket 0
│
├── (10, A)
├── (20, B)
└── (30, C)
```

Insertion:

```java
put(10, A)
put(20, B)
put(30, C)
```

---

## Time Complexity

Average Case:

```text
O(1)
```

Worst Case:

```text
O(n)
```

if all keys collide.

---

# Java HashMap Approach

### Java 7

```text
Bucket
    ↓
Linked List
```

```text
Bucket 0
    ↓
10 → 20 → 30
```

---

### Java 8+

When bucket size exceeds:

```java
TREEIFY_THRESHOLD = 8
```

the linked list becomes:

```text
Red Black Tree
```

```text
Bucket
    ↓
      20
     /  \
   10   30
```

---

### Benefits

Before Java 8:

```text
Worst Case = O(n)
```

After treeification:

```text
Worst Case = O(log n)
```

---

# Treeification Conditions

Java converts a linked list to a tree when:

```java
Bucket Size >= 8
```

and

```java
Table Capacity >= 64
```

---

# 2. Open Addressing

Instead of storing multiple elements in the same bucket:

```text
Find Another Empty Bucket
```

---

## Linear Probing

If bucket is occupied:

```text
i
i+1
i+2
...
```

until an empty bucket is found.

---

### Example

```text
Capacity = 10
```

Insert:

```java
10
20
30
```

Hash:

```java
key % 10
```

All map to:

```text
0
```

Placement:

```text
10 → Bucket 0
20 → Bucket 1
30 → Bucket 2
```

---

## Quadratic Probing

Use:

```text
i + 1²
i + 2²
i + 3²
```

instead of:

```text
i+1
i+2
```

to reduce clustering.

---

## Double Hashing

Use another hash function.

```java
index =
hash1(key)
+
i * hash2(key)
```

Provides better distribution.

---

# Load Factor

## Definition

```text
Load Factor
=
Number of Elements
/
Number of Buckets
```

Formula:

```java
loadFactor = size / capacity
```

---

## Example

```java
size = 12
capacity = 16
```

```java
loadFactor = 12/16
           = 0.75
```

---

# Why is Load Factor Important?

Higher load factor:

```text
More Collisions
```

Lower load factor:

```text
More Memory
Less Collisions
```

Trade-off:

```text
Space vs Performance
```

---

# HashMap Default Load Factor

```java
0.75
```

Meaning:

```text
75% full
```

before resizing.

---

# Rehashing

When load factor exceeds threshold:

```java
capacity * loadFactor
```

HashMap expands.

---

## Example

Initial capacity:

```java
16
```

Load factor:

```java
0.75
```

Threshold:

```java
16 × 0.75
=
12
```

After inserting:

```java
13th element
```

HashMap resizes.

---

# What Happens During Rehashing?

Old Table

```text
16 Buckets
```

New Table

```text
32 Buckets
```

Every key is rehashed.

```java
newIndex =
hash % newCapacity
```

---

# HashMap Collision Example

```java
HashMap<Integer, String> map =
        new HashMap<>();

map.put(1, "A");
map.put(17, "B");
```

Capacity:

```java
16
```

Indexes:

```java
1  % 16 = 1
17 % 16 = 1
```

Collision occurs.

Stored as:

```text
Bucket 1

(1,A)
  ↓
(17,B)
```

---

# Good Hash Function Properties

A good hash function should:

```text
1. Be Fast
2. Be Deterministic
3. Distribute Keys Uniformly
4. Minimize Collisions
```

---

# Worst Case Scenario

Suppose every key gets mapped to:

```text
Bucket 0
```

```text
Bucket 0
│
├── key1
├── key2
├── key3
├── key4
└── ...
```

Operations become:

```text
Search  → O(n)
Insert  → O(n)
Delete  → O(n)
```

which defeats the purpose of hashing.

---

# Complexity Analysis

## Separate Chaining

| Operation | Average | Worst |
|------------|------------|------------|
| Search | O(1) | O(n) |
| Insert | O(1) | O(n) |
| Delete | O(1) | O(n) |

---

## Java 8 HashMap

| Operation | Average | Worst |
|------------|------------|------------|
| Search | O(1) | O(log n) |
| Insert | O(1) | O(log n) |
| Delete | O(1) | O(log n) |

---

# Collision Resolution Comparison

| Technique | Search | Insert | Delete |
|------------|------------|------------|------------|
| Separate Chaining | O(1) Avg | O(1) Avg | O(1) Avg |
| Linear Probing | O(1) Avg | O(1) Avg | Difficult |
| Quadratic Probing | O(1) Avg | O(1) Avg | Difficult |
| Double Hashing | O(1) Avg | O(1) Avg | Difficult |

---

# Interview Questions

## What is Collision?

```text
Two different keys mapping to the same bucket.
```

---

## Why Are Collisions Inevitable?

```text
Finite buckets
Infinite possible keys
```

Pigeonhole Principle.

---

## How Does Java HashMap Handle Collisions?

### Java 7

```text
Linked List
```

### Java 8+

```text
Linked List
      ↓
Red Black Tree
```

when bucket size exceeds 8.

---

## What is Treeification?

Conversion of:

```text
Linked List
      ↓
Red Black Tree
```

inside a bucket.

---

## What is Rehashing?

```text
Resize Hash Table
+
Recompute Bucket Indices
```

---

# Key Takeaways

```text
Collision
=
Multiple Keys → Same Bucket

Handling Techniques:
1. Separate Chaining
2. Open Addressing

Java HashMap:
Java 7 → Linked List
Java 8 → Linked List + Red Black Tree

Load Factor:
0.75

Treeification:
Bucket Size ≥ 8

Average Complexity:
O(1)

Worst Complexity:
O(log n) in Java 8+
```
