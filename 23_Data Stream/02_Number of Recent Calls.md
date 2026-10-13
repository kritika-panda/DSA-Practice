# Number of Recent Calls

## Problem Statement

You have a `RecentCounter` class which counts the number of recent requests within a certain time window.

Implement the `RecentCounter` class:
*   `RecentCounter()` Initializes the counter with zero recent requests.
*   `int ping(int t)` Adds a new request at time `t`, where `t` represents milliseconds, and returns the number of requests that has happened in the past `3000` milliseconds (inclusive). Specifically, return the number of requests that have happened in the inclusive range `[t - 3000, t]`.

It is **guaranteed** that every call to `ping` uses a strictly larger value of `t` than the previous call.

The overall runtime complexity per `ping` operation must be:

```text
O(1) amortized
```

---

## Example

### Input

```java
RecentCounter recentCounter = new RecentCounter();
recentCounter.ping(1);    // Returns 1
recentCounter.ping(100);  // Returns 2
recentCounter.ping(3001); // Returns 3
recentCounter.ping(3002); // Returns 3
```

### Explanation

```text
1. recentCounter.ping(1) -> Window is [1-3000, 1] = [-2999, 1]. Requests:. Returns 1.
2. recentCounter.ping(100) -> Window is [100-3000, 100] = [-2900, 100]. Requests:. Returns 2.
3. recentCounter.ping(3001) -> Window is [3001-3000, 3001] =. Requests:. Returns 3.
4. recentCounter.ping(3002) -> Window is [3002-3000, 3002] =. Request at 1 falls out of bounds. Requests:. Returns 3.
```

---

# Intuition

Since the timestamps `t` are guaranteed to arrive in a strictly increasing chronological sequence, the elements we need to evict will always be the **oldest timestamps** that fall out of the trailing edge of the sliding time window. 

This behavior mirrors a **First-In, First-Out (FIFO)** structural pattern perfectly, which can be elegantly modeled using a **Queue**:
1. When a new ping arrives at time `t`, we immediately append it to the back of our queue.
2. The lower boundary limit of our active valid window is `t - 3000`. 
3. We examine the front of the queue. If the oldest timestamp is strictly less than `t - 3000`, it has expired. We eject it using `.poll()`.
4. We repeat this ejection step until the front of our queue holds a timestamp within the valid range `[t - 3000, t]`.
5. The remaining size of the queue represents exactly how many requests are alive inside the sliding window interval.

---

# State Definition

Let:

```text
Queue<Integer> requestQueue = new LinkedList<>(); // FIFO buffer tracking active pings
```

---

# Recurrence / Pointer Transition Condition

```text
While requestQueue.peek() < t - 3000:
    Eject requestQueue.poll()
```

---

# Optimal Structural Tabulation Solution

## Idea

By storing streaming inputs inside a FIFO Queue, addition operations take instant O(1) time. Even though the lookahead cleanup loop can pop multiple historical elements during a single massive gap jump, **every single timestamp is added once and removed at most once** across the entire operation stream. Thus, the runtime complexity maps cleanly to an optimized **amortized O(1)** performance footprint.

---

## Java Code

```java
import java.util.LinkedList;
import java.util.Queue;

class RecentCounter {

    // First-In, First-Out Queue to hold timestamps in chronological order
    private final Queue<Integer> requestQueue;

    public RecentCounter() {
        this.requestQueue = new LinkedList<>();
    }
    
    public int ping(int t) {
        // Step 1: Add the current incoming timestamp directly to the back of the queue
        requestQueue.offer(t);

        // Calculate the absolute lower bound cutoff for our 3000ms sliding window
        int minTimeBoundary = t - 3000;

        // Step 2: Evict all expired timestamps from the front of the queue
        while (!requestQueue.isEmpty() && requestQueue.peek() < minTimeBoundary) {
            requestQueue.poll();
        }

        // Step 3: The size of the queue corresponds exactly to the number of valid requests
        return requestQueue.size();
    }
}
```

---

## Complexity Analysis

```text
Time Complexity : O(1) Amortized -> Each timestamp is offered and polled from the queue at most once.
Space Complexity: O(W) -> Where W is the maximum number of requests that can arrive within a 3000ms window.
```

---

# Dry Run

## Input

```java
RecentCounter recentCounter = new RecentCounter();
```

---

### Initial State

```text
requestQueue = []
```

---

### recentCounter.ping(1)

```text
Add 1 -> requestQueue = [1]
Cutoff = 1 - 3000 = -2999
Front element 1 >= -2999 -> Loop skipped.
Returns: 1
```

---

### recentCounter.ping(3001)

```text
Add 3001 -> requestQueue = [1, 3001]
Cutoff = 3001 - 3000 = 1
Front element 1 >= 1 -> Loop skipped (1 is inclusive).
Returns: 2
```

---

### recentCounter.ping(3002)

```text
Add 3002 -> requestQueue = [1, 3002]
Cutoff = 3002 - 3000 = 2
Front element 1 < 2 -> Evict! requestQueue becomes [3001, 3002]
Front element 3001 >= 2 -> Loop breaks.
Returns: 2
```

---

# Visualization

## Chronological Sliding Window Frame

```text
       [ Evicted ]     ◄─── [ Active 3000ms Window ] ───►
      ┌───────────┐         ┌─────────────────────────┐
Time: │     1     │    -    │    100   ·   ·   ·   3001 │  ◄── New Ping (3001)
      └───────────┘         └─────────────────────────┘
        t < 1                 t - 3000 <= t
```

---

# Design Pattern

## Identify State

```text
A monotonic sliding window pipeline managed via a sequential FIFO Queue.
```

---

## Choices

```text
Append new metrics to the trailing edge. Prune expired coordinates from the front of the line.
```

---

## Key Takeaways

✅ Baseline architectural model for chronological time-series stream data pruning.

✅ Demonstrates a core real-world software design engine used for **Rate Limiting algorithms** (specifically the Sliding Window Log framework).

✅ Queue caching ensures old, out-of-date telemetry frames are discarded instantly, keeping active collections light.

✅ Common Systems Design Pattern:

```text
Sliding Window Metric Tracking via Monotonic Queue Eviction
```

### Similar Problems

- Design Hit Counter (LeetCode 362)
- Moving Average from Data Stream (LeetCode 346)
- Sliding Window Maximum (LeetCode 239)
- Design an Ordered Stream (LeetCode 1656)
