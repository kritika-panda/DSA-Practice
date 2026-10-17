# Design Hit Counter

## Problem Statement

Design a hit counter that counts the number of hits received in the past 5 minutes (300 seconds).

Implement the `HitCounter` class:

```java
HitCounter()
```

Initializes the object.

```java
void hit(int timestamp)
```

Records a hit at the given timestamp (in seconds).

```java
int getHits(int timestamp)
```

Returns the number of hits in the past 300 seconds from the given timestamp.

### Assumptions

- Timestamps are provided in increasing order.
- Multiple hits can occur at the same timestamp.

---

## Example

### Input

```java
["HitCounter",
 "hit",
 "hit",
 "hit",
 "getHits",
 "hit",
 "getHits",
 "getHits"]

[[],[1],[2],[3],[4],[300],[300],[301]]
```

### Output

```java
[null,null,null,null,3,null,4,3]
```

### Explanation

```java
hit(1)
hit(2)
hit(3)

getHits(4)
```

Valid timestamps:

```java
[1,2,3]
```

Answer:

```java
3
```

---

```java
hit(300)
```

Stored hits:

```java
[1,2,3,300]
```

---

```java
getHits(300)
```

Window:

```java
[1...300]
```

Answer:

```java
4
```

---

```java
getHits(301)
```

Window:

```java
[2...301]
```

Timestamp `1` expires.

Answer:

```java
3
```

---

# Intuition

We only care about hits from the last:

```java
300 seconds
```

Whenever a query arrives:

- Remove expired hits.
- Count remaining hits.

A queue naturally supports:

```java
Insert new hit → Back
Remove old hit → Front
```

which perfectly matches a sliding window.

---

# Approach 1: Queue

## Algorithm

### hit(timestamp)

Store timestamp in queue.

### getHits(timestamp)

Remove all timestamps:

```java
<= timestamp - 300
```

Return current queue size.

---

## Dry Run

### Operations

```java
hit(1)
hit(2)
hit(3)
```

Queue:

```java
[1,2,3]
```

---

```java
getHits(4)
```

Nothing expires.

```java
size = 3
```

---

```java
hit(300)
```

Queue:

```java
[1,2,3,300]
```

---

```java
getHits(301)
```

Remove:

```java
1
```

Queue:

```java
[2,3,300]
```

Answer:

```java
3
```

---

## Java Solution

```java
import java.util.*;

class HitCounter {

    private Queue<Integer> queue;

    public HitCounter() {
        queue = new LinkedList<>();
    }

    public void hit(int timestamp) {
        queue.offer(timestamp);
    }

    public int getHits(int timestamp) {

        while (!queue.isEmpty() &&
               queue.peek() <= timestamp - 300) {
            queue.poll();
        }

        return queue.size();
    }
}
```

---

## Complexity Analysis

### hit()

```java
Time Complexity: O(1)
```

### getHits()

```java
Time Complexity: O(n) worst case
```

Each timestamp is removed only once over its lifetime.

### Space Complexity

```java
O(number of hits in last 300 seconds)
```

---

# Approach 2: Optimal Fixed-Size Circular Array

## Observation

We only care about:

```java
300 seconds
```

Therefore store exactly:

```java
300 slots
```

Each slot represents one second.

Maintain:

```java
timestamps[300]
hits[300]
```

---

## Idea

For a timestamp:

```java
timestamp
```

Use index:

```java
timestamp % 300
```

### If slot belongs to a different timestamp

Reset it.

### Otherwise

Increment hit count.

---

## Example

### hit(1)

```java
index = 1 % 300 = 1
```

Store:

```java
timestamps[1] = 1
hits[1] = 1
```

---

### hit(301)

```java
index = 1
```

Since:

```java
timestamps[1] != 301
```

Reset slot:

```java
hits[1] = 1
timestamps[1] = 301
```

Old data automatically expires.

---

## Java Solution (Optimal)

```java
class HitCounter {

    private int[] timestamps;
    private int[] hits;

    public HitCounter() {
        timestamps = new int[300];
        hits = new int[300];
    }

    public void hit(int timestamp) {

        int index = timestamp % 300;

        if (timestamps[index] != timestamp) {
            timestamps[index] = timestamp;
            hits[index] = 1;
        } else {
            hits[index]++;
        }
    }

    public int getHits(int timestamp) {

        int totalHits = 0;

        for (int i = 0; i < 300; i++) {

            if (timestamp - timestamps[i] < 300) {
                totalHits += hits[i];
            }
        }

        return totalHits;
    }
}
```

---

# Why Does the Circular Array Work?

Because:

```java
timestamp % 300
```

maps every second into one of the 300 buckets.

When a timestamp older than 300 seconds reuses the slot:

```java
timestamp - timestamps[index] >= 300
```

its data is no longer relevant and can be safely overwritten.

---

# Complexity Analysis

### hit()

```java
Time Complexity: O(1)
```

### getHits()

```java
Time Complexity: O(300)
```

Which is effectively:

```java
O(1)
```

### Space Complexity

```java
O(300)
```

```java
O(1)
```

constant space.

---

# Comparison of Approaches

| Approach | Hit | GetHits | Space |
|-----------|------|----------|--------|
| Queue | O(1) | O(n) amortized | O(n) |
| Circular Array | O(1) | O(1) | O(1) |

---

# Follow-Up: What If Hits Are Extremely Frequent?

Instead of storing every hit individually:

Use:

```java
(timestamp, count)
```

pairs.

Example:

```java
(100, 5000 hits)
```

This significantly reduces memory usage when many hits occur within the same second.

---

# Key Takeaways

- The problem is a **sliding window of 300 seconds**.
- Queue provides a straightforward solution.
- Circular array of size `300` is the optimal solution.
- Use:
  ```java
  timestamp % 300
  ```
  to recycle buckets.
- Each bucket stores:
  - Timestamp
  - Hit count
- Optimal complexity:
  - **hit() → O(1)**
  - **getHits() → O(1)**
  - **Space → O(1)**
- Classic interview problem testing **Queue**, **Sliding Window**, and **Circular Buffer** concepts.
