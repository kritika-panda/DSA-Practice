# Moving Average From Data Stream

**LeetCode 346 - Moving Average from Data Stream**

## Problem Statement

Given a stream of integers and a window size, calculate the moving average of all integers inside the sliding window.

The structure must support:

```java
MovingAverage(int size)
```

Initialize the object with the structural window `size`.

```java
double next(int val)
```

Returns the moving average of the elements inside the current sliding window after adding `val`.

---

## Example

```java
MovingAverage movingAverage = new MovingAverage(3);

movingAverage.next(1); // 1.0 -> [1] / 1
movingAverage.next(10); // 5.5 -> [1, 10] / 2
movingAverage.next(3); // 4.66667 -> [1, 10, 3] / 3
movingAverage.next(5); // 6.0 -> [10, 3, 5] / 3 (1 drops off)
```

---

# Intuition

A moving average depends entirely on tracking a rolling sequence of numbers up to a maximum capacity. 
- Elements follow a **FIFO** order: new values enter the window from the back, and old values leave from the front when the window fills up. This mapping mirrors the exact properties of a **Queue**.
- To avoid iterating through the window elements every time to calculate the sum, we can keep a running `windowSum` variable. When a new element arrives, we add it to our running sum. If the queue exceeds the window capacity, we pop the oldest element from the front and subtract its value from `windowSum`.

---

# Approach: Queue with Circular Rolling Sum

## Data Structures

```java
Queue<Integer> queue
int maxSize
double windowSum
```

---

## next(val)

1. Append `val` to the `queue`.
2. Add `val` to `windowSum`.
3. Check if `queue.size() > maxSize`.
4. If it is too large, remove the front element from the queue and subtract it from `windowSum`.
5. Return the moving average: `windowSum / queue.size()`.

---

# Dry Run

### Parameters
```java
maxSize = 3
```

### Execution Trace

| Operation | Queue State | windowSum | Queue Size | Return Calculation | Result |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `next(1)` | `[1]` | `1.0` | `1` | `1.0 / 1` | `1.0` |
| `next(10)` | `[1, 10]` | `11.0` | `2` | `11.0 / 2` | `5.5` |
| `next(3)` | `[1, 10, 3]` | `14.0` | `3` | `14.0 / 3` | `4.66667` |
| `next(5)` | `[10, 3, 5]` | `14.0 + 5 - 1 = 18.0` | `3` | `18.0 / 3` | `6.0` |

---

# Java Solution

```java
import java.util.*;

class MovingAverage {

    private final int maxSize;
    private final Queue<Integer> queue;
    private double windowSum;

    public MovingAverage(int size) {
        this.maxSize = size;
        this.queue = new LinkedList<>();
        this.windowSum = 0.0;
    }

    public double next(int val) {
        queue.add(val);
        windowSum += val;

        // If the window shifts out of capacity, evict the oldest element
        if (queue.size() > maxSize) {
            windowSum -= queue.poll();
        }

        return windowSum / queue.size();
    }
}
```

---

# Complexity Analysis

### Initialization

```java
O(1)
```

### next()

```java
O(1)
```
Enqueueing, dequeueing, and updating a numeric tracking sum takes fixed, constant time.

### Space Complexity

```java
O(W)
```
Where `W` is the maximum sliding window size. The queue will never store more than `W + 1` elements.

---

# Alternative Approach: Circular Array / Ring Buffer (Memory Optimized)

## Observation

Using a standard library `Queue` involves object allocation overhead for internal node pointers. We can optimize this by using a fixed-size primitive array (`int[]`) as a **Circular Array / Ring Buffer**, overriding old indices using a modulo operator (`%`).

### Java Solution (Alternative)

```java
class MovingAverageBuffer {

    private final int[] buffer;
    private final int maxSize;
    private int count;
    private int head;
    private double windowSum;

    public MovingAverageBuffer(int size) {
        this.maxSize = size;
        this.buffer = new int[size];
        this.count = 0;
        this.head = 0;
        this.windowSum = 0.0;
    }

    public double next(int val) {
        count++;
        
        // If buffer is full, remove the value currently sitting at the head pointer
        if (count > maxSize) {
            windowSum -= buffer[head];
        }

        // Place new value at head, then advance head circularly
        buffer[head] = val;
        head = (head + 1) % maxSize;
        windowSum += val;

        int activeElements = Math.min(count, maxSize);
        return windowSum / activeElements;
    }
}
```

---

# Comparison

| Strategy | Time Complexity | Memory Allocation Overhead | Scalability | Best Used For |
|---|---|---|---|---|
| **Standard Queue** | $O(1)$ | Moderate (Node pointer allocations) | High | Rapid implementation |
| **Circular Array** | $O(1)$ | Ultra Low (Fixed primitive array) | Exceptional | Low-latency or memory-constrained environments |

---

# Key Takeaways

- Track rolling data horizons efficiently by using a **FIFO** structural arrangement.
- Maintain a running summary field to calculate updates in $O(1)$ time instead of iterating through the window elements on every call.
- For high-throughput system design questions, replace link-based queues with a fixed-size **Ring Buffer** to avoid memory allocation costs.
- Core concepts:
  - Sliding Window Sum
  - FIFO Queue
  - Circular Array (Ring Buffer)
- Expected interview preference: **Circular Array Approach** for low-latency considerations, though **Standard Queue** is perfectly acceptable for normal algorithmic interviews.
