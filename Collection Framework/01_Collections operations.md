# Java Collections Framework (JCF) Complete Guide

## Java Collections Hierarchy

```text
Iterable
│
└── Collection
    │
    ├── List
    │   ├── ArrayList
    │   ├── LinkedList
    │   ├── Vector
    │   ├── Stack
    │   └── CopyOnWriteArrayList
    │
    ├── Set
    │   ├── HashSet
    │   ├── LinkedHashSet
    │   ├── TreeSet
    │   ├── EnumSet
    │   ├── CopyOnWriteArraySet
    │   └── ConcurrentSkipListSet
    │
    └── Queue
        ├── PriorityQueue
        ├── ArrayDeque
        ├── LinkedList
        ├── ConcurrentLinkedQueue
        ├── ArrayBlockingQueue
        ├── LinkedBlockingQueue
        ├── PriorityBlockingQueue
        ├── DelayQueue
        └── SynchronousQueue

Map
│
├── HashMap
├── LinkedHashMap
├── TreeMap
├── Hashtable
├── WeakHashMap
├── IdentityHashMap
├── EnumMap
├── ConcurrentHashMap
├── ConcurrentSkipListMap
└── Properties
```

---

# List Implementations

## ArrayList

```text
Underlying DS : Dynamic Array
Order Maintained : Yes
Duplicates Allowed : Yes
Thread Safe : No
```

### Complexity

| Operation | Time Complexity |
|------------|------------|
| get(index) | O(1) |
| set(index) | O(1) |
| add(element) | O(1)* |
| add(index) | O(n) |
| remove(index) | O(n) |
| remove(last) | O(1) |
| contains() | O(n) |
| iteration | O(n) |

```text
* Amortized O(1)
```

---

## LinkedList

```text
Underlying DS : Doubly Linked List
Order Maintained : Yes
Duplicates Allowed : Yes
Thread Safe : No
```

### Complexity

| Operation | Time Complexity |
|------------|------------|
| get(index) | O(n) |
| addFirst() | O(1) |
| addLast() | O(1) |
| removeFirst() | O(1) |
| removeLast() | O(1) |
| contains() | O(n) |
| iteration | O(n) |

---

## Vector

```text
Legacy synchronized ArrayList
```

### Complexity

| Operation | Time Complexity |
|------------|------------|
| get() | O(1) |
| add() | O(1)* |
| remove() | O(n) |
| contains() | O(n) |

---

## Stack

```text
Extends Vector
LIFO Structure
```

### Complexity

| Operation | Time Complexity |
|------------|------------|
| push() | O(1) |
| pop() | O(1) |
| peek() | O(1) |
| search() | O(n) |

---

## CopyOnWriteArrayList

```text
Thread Safe
Best for Read Heavy Operations
```

### Complexity

| Operation | Time Complexity |
|------------|------------|
| get() | O(1) |
| add() | O(n) |
| remove() | O(n) |
| contains() | O(n) |
| iteration() | O(n) |

---

# Set Implementations

## HashSet

```text
Backed By : HashMap
Ordering : No
Duplicates : Not Allowed
```

### Complexity

| Operation | Time Complexity |
|------------|------------|
| add() | O(1) |
| remove() | O(1) |
| contains() | O(1) |
| iteration() | O(n) |

---

## LinkedHashSet

```text
Backed By : LinkedHashMap
Maintains Insertion Order
```

### Complexity

| Operation | Time Complexity |
|------------|------------|
| add() | O(1) |
| remove() | O(1) |
| contains() | O(1) |
| iteration() | O(n) |

---

## TreeSet

```text
Backed By : TreeMap
Implemented Using Red Black Tree
Sorted Order
```

### Complexity

| Operation | Time Complexity |
|------------|------------|
| add() | O(log n) |
| remove() | O(log n) |
| contains() | O(log n) |
| first() | O(log n) |
| last() | O(log n) |
| floor() | O(log n) |
| ceiling() | O(log n) |

---

## EnumSet

```text
Optimized Set for Enums
Bit Vector Based
```

### Complexity

| Operation | Time Complexity |
|------------|------------|
| add() | O(1) |
| remove() | O(1) |
| contains() | O(1) |

---

## CopyOnWriteArraySet

```text
Thread Safe Set
Uses CopyOnWriteArrayList Internally
```

### Complexity

| Operation | Time Complexity |
|------------|------------|
| add() | O(n) |
| remove() | O(n) |
| contains() | O(n) |

---

## ConcurrentSkipListSet

```text
Sorted Concurrent Set
Skip List Based
```

### Complexity

| Operation | Time Complexity |
|------------|------------|
| add() | O(log n) |
| remove() | O(log n) |
| contains() | O(log n) |

---

# Map Implementations

## HashMap

```text
Backed By:
Array + LinkedList + Red Black Tree
```

### Complexity

| Operation | Time Complexity |
|------------|------------|
| put() | O(1) |
| get() | O(1) |
| remove() | O(1) |
| containsKey() | O(1) |

### Worst Case

```text
O(log n)
```

after treeification.

---

## LinkedHashMap

```text
Insertion Order Maintained
Used For LRU Cache
```

### Complexity

| Operation | Time Complexity |
|------------|------------|
| put() | O(1) |
| get() | O(1) |
| remove() | O(1) |

---

## TreeMap

```text
Red Black Tree
Sorted Keys
```

### Complexity

| Operation | Time Complexity |
|------------|------------|
| put() | O(log n) |
| get() | O(log n) |
| remove() | O(log n) |
| floorKey() | O(log n) |
| ceilingKey() | O(log n) |
| higherKey() | O(log n) |
| lowerKey() | O(log n) |

---

## Hashtable

```text
Legacy Thread Safe HashMap
```

### Complexity

| Operation | Time Complexity |
|------------|------------|
| put() | O(1) |
| get() | O(1) |
| remove() | O(1) |

---

## WeakHashMap

```text
Keys Can Be Garbage Collected
```

### Complexity

| Operation | Time Complexity |
|------------|------------|
| put() | O(1) |
| get() | O(1) |
| remove() | O(1) |

---

## IdentityHashMap

```text
Uses ==
Instead of equals()
```

### Complexity

| Operation | Time Complexity |
|------------|------------|
| put() | O(1) |
| get() | O(1) |
| remove() | O(1) |

---

## EnumMap

```text
Optimized Map for Enum Keys
```

### Complexity

| Operation | Time Complexity |
|------------|------------|
| put() | O(1) |
| get() | O(1) |
| remove() | O(1) |

---

## ConcurrentHashMap

```text
Thread Safe
Segment Based Locking (Older)
CAS + Fine Grained Locking (Java 8+)
```

### Complexity

| Operation | Time Complexity |
|------------|------------|
| put() | O(1) |
| get() | O(1) |
| remove() | O(1) |

---

## ConcurrentSkipListMap

```text
Sorted Concurrent Map
Skip List Based
```

### Complexity

| Operation | Time Complexity |
|------------|------------|
| put() | O(log n) |
| get() | O(log n) |
| remove() | O(log n) |

---

# Queue Implementations

## PriorityQueue

```text
Binary Heap
Min Heap By Default
```

### Complexity

| Operation | Time Complexity |
|------------|------------|
| offer() | O(log n) |
| poll() | O(log n) |
| peek() | O(1) |
| contains() | O(n) |

---

## ArrayDeque

```text
Resizable Circular Array
Recommended Over Stack
```

### Complexity

| Operation | Time Complexity |
|------------|------------|
| addFirst() | O(1) |
| addLast() | O(1) |
| removeFirst() | O(1) |
| removeLast() | O(1) |
| peekFirst() | O(1) |
| peekLast() | O(1) |

---

## LinkedList as Queue

### Complexity

| Operation | Time Complexity |
|------------|------------|
| offer() | O(1) |
| poll() | O(1) |
| peek() | O(1) |

---

## ConcurrentLinkedQueue

```text
Lock Free Queue
```

### Complexity

| Operation | Time Complexity |
|------------|------------|
| offer() | O(1) |
| poll() | O(1) |
| peek() | O(1) |

---

# Blocking Queues

## ArrayBlockingQueue

```text
Fixed Size
Thread Safe
```

| Operation | Time Complexity |
|------------|------------|
| put() | O(1) |
| take() | O(1) |

---

## LinkedBlockingQueue

```text
Linked List Based
```

| Operation | Time Complexity |
|------------|------------|
| put() | O(1) |
| take() | O(1) |

---

## PriorityBlockingQueue

```text
Thread Safe Heap
```

| Operation | Time Complexity |
|------------|------------|
| offer() | O(log n) |
| poll() | O(log n) |
| peek() | O(1) |

---

## DelayQueue

```text
Processes Delayed Objects
```

| Operation | Time Complexity |
|------------|------------|
| offer() | O(log n) |
| poll() | O(log n) |

---

## SynchronousQueue

```text
No Internal Storage
```

| Operation | Time Complexity |
|------------|------------|
| put() | O(1) |
| take() | O(1) |

---

# Special Collections

## Properties

```text
Configuration Storage
Extends Hashtable
```

### Complexity

| Operation | Time Complexity |
|------------|------------|
| put() | O(1) |
| get() | O(1) |

---

# Space Complexity Summary

| Collection | Space Complexity |
|------------|------------|
| ArrayList | O(n) |
| LinkedList | O(n) |
| Vector | O(n) |
| Stack | O(n) |
| HashMap | O(n) |
| HashSet | O(n) |
| TreeMap | O(n) |
| TreeSet | O(n) |
| LinkedHashMap | O(n) |
| LinkedHashSet | O(n) |
| PriorityQueue | O(n) |
| ArrayDeque | O(n) |
| ConcurrentHashMap | O(n) |
| CopyOnWriteArrayList | O(n) |
| ConcurrentSkipListMap | O(n) |
| ConcurrentSkipListSet | O(n) |

---

# FAANG Interview Cheat Sheet

| Requirement | Best Collection |
|------------|------------|
| Fast Lookup | HashMap |
| Unique Elements | HashSet |
| Sorted Keys | TreeMap |
| Sorted Unique Elements | TreeSet |
| LRU Cache | LinkedHashMap |
| Min Heap | PriorityQueue |
| Max Heap | PriorityQueue + Comparator |
| Monotonic Queue | ArrayDeque |
| Thread Safe Map | ConcurrentHashMap |
| Read Heavy List | CopyOnWriteArrayList |
| Producer Consumer | BlockingQueue |
| Sorted Concurrent Map | ConcurrentSkipListMap |
| Enum Keys | EnumMap |
| Enum Set | EnumSet |

---

# Most Important Collections for FAANG

## Tier 1

```java
ArrayList
HashMap
HashSet
PriorityQueue
ArrayDeque
LinkedHashMap
TreeMap
TreeSet
```

---

## Tier 2

```java
ConcurrentHashMap
BlockingQueue
CopyOnWriteArrayList
ConcurrentLinkedQueue
```

---

## Tier 3

```java
ConcurrentSkipListMap
ConcurrentSkipListSet
WeakHashMap
IdentityHashMap
EnumMap
EnumSet
```

---

# Key Takeaways

```text
ArrayList        → Fast Random Access
LinkedList       → Fast Insert/Delete at Ends
HashMap          → O(1) Lookup
HashSet          → O(1) Membership Check
TreeMap          → Sorted Keys
TreeSet          → Sorted Unique Elements
LinkedHashMap    → LRU Cache Implementation
PriorityQueue    → Heap Operations
ArrayDeque       → Stack + Queue + Monotonic Queue
ConcurrentHashMap→ Thread-Safe O(1) Map
BlockingQueue    → Producer Consumer Pattern
EnumMap          → Fastest Enum Key Storage
```
