# First Unique Number

**LeetCode 1429 - First Unique Number**

## Problem Statement

Design a data structure that processes a stream of integers and retrieves the **first unique number** encountered so far.

The structure must support:

```java
FirstUnique(int[] nums)
```

Initialize the object with the given initial stream of numbers.

```java
int showFirstUnique()
```

Returns the value of the **first unique number** in the stream. If there is no such number, return `-1`.

```java
void add(int value)
```

Insert `value` into the stream.

---

## Example

```java
FirstUnique firstUnique = new FirstUnique(new int[]{2, 3, 5});

firstUnique.showFirstUnique(); // 2

firstUnique.add(5);            
firstUnique.showFirstUnique(); // 2

firstUnique.add(2);            
firstUnique.showFirstUnique(); // 3

firstUnique.add(3);            
firstUnique.showFirstUnique(); // -1
```

---

# Intuition

We need a system that preserves insertion order while instantly identifying duplicates. 
- A simple queue or list tracks the insertion order but takes linear `O(N)` time to search for uniqueness.
- A HashMap tracks uniqueness quickly, but lacks a clean way to remember who arrived first.

To get the best of both worlds, we can combine a **LinkedHashSet** (or a combination of a Doubly Linked List and a HashMap) with an auxiliary **HashSet**. The HashSet remembers everything we have ever seen, while the ordered set holds only the active unique items in the order they arrived.

---

# Approach: HashMap + Doubly Linked List (LinkedHashSet)

## Data Structures

```java
Set<Integer> uniqueElements
Set<Integer> allElements
```

- `allElements` tracking set: Records every value added to the stream.
- `uniqueElements` order set: A `LinkedHashSet` maintaining values that have appeared exactly once, preserving their arrival order.

---

## add(value)

1. Check if `value` exists in `allElements`.
2. **If it's brand new:** Add it to both `allElements` and `uniqueElements`.
3. **If it has been seen before:** It is no longer unique. Evict it from `uniqueElements`. It stays in `allElements` so future additions of the same number are instantly ignored.

---

## showFirstUnique()

```java
if (uniqueElements.isEmpty()) return -1;
return uniqueElements.iterator().next();
```
Since `LinkedHashSet` preserves order, the very first element returned by its iterator is our answer.

---

# Dry Run

### Operations
```java
FirstUnique([2, 3, 5])
add(5)
add(2)
```

### Initial State
```java
allElements:    [2, 3, 5]
uniqueElements: [2, 3, 5]
```
`showFirstUnique()` -> Iterator yields front item: `2`.

### State after add(5)
```java
5 is already in allElements -> Evict from uniqueElements
allElements:    [2, 3, 5]
uniqueElements: [2, 3]
```
`showFirstUnique()` -> Iterator yields front item: `2`.

### State after add(2)
```java
2 is already in allElements -> Evict from uniqueElements
allElements:    [2, 3, 5]
uniqueElements: [3]
```
`showFirstUnique()` -> Iterator yields front item: `3`.

---

# Java Solution

```java
import java.util.*;

class FirstUnique {

    private Set<Integer> uniqueElements;
    private Set<Integer> allElements;

    public FirstUnique(int[] nums) {
        uniqueElements = new LinkedHashSet<>();
        allElements = new HashSet<>();

        for (int num : nums) {
            add(num);
        }
    }

    public int showFirstUnique() {
        if (uniqueElements.isEmpty()) {
            return -1;
        }
        // O(1) retrieval of the first inserted unique element
        return uniqueElements.iterator().next();
    }

    public void add(int value) {
        if (!allElements.contains(value)) {
            allElements.add(value);
            uniqueElements.add(value);
        } else {
            uniqueElements.remove(value);
        }
    }
}
```

---

# Complexity Analysis

### Initialization

```java
O(K)
```
Where `K` is the initial size of the `nums` array.

### add()

```java
O(1)
```
Hashing lookups, additions, and double-linked sequence updates run in constant time.

### showFirstUnique()

```java
O(1)
```
Retrieving the head of a linked queue chain takes instant time.

---

# Alternative Approach: Standard Queue + HashMap (Lazy Deletion)

## Observation

Instead of aggressively removing duplicates from our storage during the `add` operation, we can use a standard `Queue` alongside a `HashMap<Integer, Boolean>` tracking uniqueness. 

When `showFirstUnique()` is invoked, we clear out invalid duplicates from the front of the queue dynamically until a valid unique element surfaces.

### Java Solution (Alternative)

```java
import java.util.*;

class FirstUniqueLazy {

    private Queue<Integer> queue;
    private Map<Integer, Integer> countMap;

    public FirstUniqueLazy(int[] nums) {
        queue = new LinkedList<>();
        countMap = new HashMap<>();

        for (int num : nums) {
            add(num);
        }
    }

    public int showFirstUnique() {
        // Discard elements from the front if they are no longer unique
        while (!queue.isEmpty() && countMap.get(queue.peek()) > 1) {
            queue.poll();
        }
        return queue.isEmpty() ? -1 : queue.peek();
    }

    public void add(int value) {
        countMap.put(value, countMap.getOrDefault(value, 0) + 1);
        if (countMap.get(value) == 1) {
            queue.add(value);
        }
    }
}
```

---

# Comparison

| Strategy | add Complexity | showFirstUnique Complexity | Space Footprint | Memory characteristic |
|---|---|---|---|---|
| **LinkedHashSet Approach** | O(1) | O(1) strict | O(N) | Maintains strict bounds immediately |
| **Queue Lazy Deletion** | O(1) | O(1) amortized | O(N) | Queue can hold stale elements until checked |

---

# Key Takeaways

- Track item arrivals sequentially via a `Queue` or `LinkedHashSet`.
- Maintain global item presence states across an independent `Set` or `Map` to check for repeating sequences.
- Avoid iterating through whole sets during lookups by reading elements directly from the structural header nodes.
- Core concepts:
  - LinkedHashSet
  - Queue with Lazy Deletion
  - Stream Data Design
- Expected interview preference: **LinkedHashSet Approach** due to strict `O(1)` tracking guarantees.
