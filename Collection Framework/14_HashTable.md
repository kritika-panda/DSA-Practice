# Hashtable in Java

## What is Hashtable?

`Hashtable` is a legacy class in Java that implements the `Map` interface and stores data in **key-value pairs**.

It belongs to:

```java
java.util.Hashtable
```

Hashtable is synchronized, which means it is **thread-safe**.

---

# Key Characteristics

```text
✓ Stores Key-Value pairs
✓ Thread Safe
✓ Synchronized
✓ No Duplicate Keys
✓ Allows Duplicate Values
✓ Null Key Not Allowed
✓ Null Value Not Allowed
✓ Unordered
```

---

# Class Hierarchy

```text
Object
   │
   └── Dictionary
          │
          └── Hashtable<K,V>
                  │
                  └── Map<K,V>
```

---

# Creating a Hashtable

```java
import java.util.Hashtable;

public class Main {

    public static void main(String[] args) {

        Hashtable<Integer, String> table =
                new Hashtable<>();

        table.put(1, "Google");
        table.put(2, "Amazon");
        table.put(3, "Meta");

        System.out.println(table);
    }
}
```

Output:

```java
{3=Meta, 2=Amazon, 1=Google}
```

---

# Internal Working

Hashtable uses:

```text
Array
+
Hash Function
+
Buckets
```

to store data.

---

## Step 1: Compute Hash

```java
hash = key.hashCode()
```

---

## Step 2: Find Bucket

```java
index = hash % capacity
```

---

## Step 3: Store Entry

```java
(key, value)
```

inside the bucket.

---

# Example

```java
Hashtable<Integer,String> table =
        new Hashtable<>();

table.put(10,"A");
table.put(20,"B");
table.put(30,"C");
```

Internally:

```text
Bucket 0 → (10,A)

Bucket 1 → (20,B)

Bucket 2 → (30,C)
```

---

# Basic Operations

## put()

Insert a key-value pair.

```java
table.put(1, "Java");
table.put(2, "Spring");
```

---

## get()

Retrieve value by key.

```java
System.out.println(
    table.get(2)
);
```

Output:

```java
Spring
```

---

## remove()

Remove entry.

```java
table.remove(2);
```

---

## containsKey()

```java
table.containsKey(1);
```

Output:

```java
true
```

---

## containsValue()

```java
table.containsValue("Java");
```

Output:

```java
true
```

---

## size()

```java
table.size();
```

Output:

```java
2
```

---

## isEmpty()

```java
table.isEmpty();
```

Output:

```java
false
```

---

# Null Handling

## Null Key Not Allowed

```java
Hashtable<Integer,String> table =
        new Hashtable<>();

table.put(null, "Java");
```

Throws:

```java
NullPointerException
```

---

## Null Value Not Allowed

```java
table.put(1, null);
```

Throws:

```java
NullPointerException
```

---

# Iterating Hashtable

## Using Entry Set

```java
for(Map.Entry<Integer,String> entry :
        table.entrySet()) {

    System.out.println(
        entry.getKey() +
        " " +
        entry.getValue()
    );
}
```

---

## Using Key Set

```java
for(Integer key : table.keySet()) {

    System.out.println(
        key + " " + table.get(key)
    );
}
```

---

## Using Enumeration

Legacy approach.

```java
Enumeration<Integer> keys =
        table.keys();

while(keys.hasMoreElements()) {

    Integer key =
            keys.nextElement();

    System.out.println(key);
}
```

---

# Hashtable vs HashMap

| Feature | Hashtable | HashMap |
|----------|----------|----------|
| Thread Safe | Yes | No |
| Synchronized | Yes | No |
| Null Key | Not Allowed | Allowed |
| Null Values | Not Allowed | Allowed |
| Performance | Slower | Faster |
| Introduced | Java 1.0 | Java 1.2 |
| Legacy Class | Yes | No |

---

# Hashtable vs ConcurrentHashMap

| Feature | Hashtable | ConcurrentHashMap |
|----------|----------|----------|
| Thread Safety | Yes | Yes |
| Locking | Whole Table | Segment/CAS Based |
| Performance | Slower | Faster |
| Null Key | Not Allowed | Not Allowed |
| Null Value | Not Allowed | Not Allowed |

---

# Time Complexity

| Operation | Average Case |
|------------|------------|
| put() | O(1) |
| get() | O(1) |
| remove() | O(1) |
| containsKey() | O(1) |
| containsValue() | O(n) |
| iteration | O(n) |

---

# Worst-Case Complexity

When all elements collide:

```text
put() = O(n)
get() = O(n)
remove() = O(n)
```

---

# Space Complexity

Let:

```java
n = number of entries
```

Space:

```text
O(n)
```

---

# Load Factor

Default load factor:

```java
0.75
```

Meaning:

```text
75% Full
→ Resize Triggered
```

---

## Default Capacity

```java
11
```

for Hashtable.

---

# Rehashing

When:

```text
Current Size >
Capacity × Load Factor
```

Hashtable creates a bigger table and reinserts all entries.

Example:

```text
Capacity = 11
Load Factor = 0.75

Threshold =
11 × 0.75
=
8
```

After inserting the 9th element:

```text
Rehash happens
```

---

# Synchronization Example

Every major method is synchronized.

Example:

```java
public synchronized V get(Object key)
```

and

```java
public synchronized V put(K key, V value)
```

This ensures thread safety.

---

# Why Hashtable Is Rarely Used Today

Problems:

```text
• Entire table lock
• Poor scalability
• Legacy API
• Slower performance
```

Modern alternatives:

```java
HashMap
ConcurrentHashMap
```

---

# Interview Questions

## Can Hashtable Store Null Keys?

```text
No
```

Throws:

```java
NullPointerException
```

---

## Can Hashtable Store Null Values?

```text
No
```

---

## Is Hashtable Thread Safe?

```text
Yes
```

All public methods are synchronized.

---

## Is Hashtable Ordered?

```text
No
```

Insertion order is not maintained.

---

## Difference Between HashMap and Hashtable?

```text
HashMap:
    Faster
    Not Synchronized
    Allows Nulls

Hashtable:
    Synchronized
    Thread Safe
    No Nulls
```

---

# Interview Cheat Sheet

## Create Hashtable

```java
Hashtable<Integer,String> table =
        new Hashtable<>();
```

---

## Insert

```java
table.put(key, value);
```

---

## Retrieve

```java
table.get(key);
```

---

## Delete

```java
table.remove(key);
```

---

## Search Key

```java
table.containsKey(key);
```

---

## Search Value

```java
table.containsValue(value);
```

---

# Key Takeaways

```text
Hashtable
=
Legacy Thread Safe HashMap

Uses:
    Hashing
    Buckets
    Synchronization

Pros:
    Thread Safe
    Simple API

Cons:
    Slower
    No Null Keys
    No Null Values
    Whole Table Locking

Modern Replacement:
    ConcurrentHashMap
```

### Complexity Summary

```text
put()          → O(1)
get()          → O(1)
remove()       → O(1)
containsKey()  → O(1)

Space          → O(n)
```
