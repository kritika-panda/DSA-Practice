# Stock Price Fluctuation

**LeetCode 2034 - Stock Price Fluctuation**

## Problem Statement

Design a stock price fluctuation tracker that supports:

```java
StockPrice()
```

Initialize the tracker.

```java
void update(int timestamp, int price)
```

Updates the price of the stock at the given `timestamp`. 

If the timestamp already exists, the price is corrected to the new `price`.

```java
int current()
```

Returns the latest price of the stock based on the maximum `timestamp` recorded.

```java
int maximum()
```

Returns the maximum price of the stock.

```java
int minimum()
```

Returns the minimum price of the stock.

---

## Example

```java
StockPrice stockPrice = new StockPrice();

stockPrice.update(1, 10); 
stockPrice.update(2, 5);  

stockPrice.current(); // 5

stockPrice.maximum(); // 10

stockPrice.update(1, 3);  

stockPrice.maximum(); // 5

stockPrice.update(4, 2);  

stockPrice.minimum(); // 2
```

---

# Intuition

We need to track:

```java
1. Timestamp to Price mapping (to check for corrections)
2. The latest timestamp processed
3. The overall maximum and minimum prices
```

A simple HashMap can store the historical data:

```java
timestamp -> price
```

For tracking `maximum()` and `minimum()`, we need a system that keeps prices sorted but handles price corrections efficiently. If an old price gets overwritten, the outdated price might still exist in our min/max tracking structures.

---

# Approach 1: HashMap + Two Heaps (Lazy Deletion)

## Data Structure

```java
Map<Integer, Integer> timestampPriceMap
PriorityQueue<int[]> maxHeap
PriorityQueue<int[]> minHeap
```

The heaps store arrays of `[price, timestamp]`.

---

## update()

1. Update the `timestampPriceMap`.
2. Update the `latestTimestamp` if the current one is larger.
3. Push the `[price, timestamp]` pair into both the max heap and min heap.

---

## current()

```java
return timestampPriceMap.get(latestTimestamp)
```

---

## maximum() and minimum()

1. Peek the top of the heap.
2. Check if the price at the top matches the current valid price for that timestamp in the `timestampPriceMap`.
3. If it doesn't match (outdated data), poll it out (lazy deletion).
4. Repeat until a valid entry is found at the top, then return its price.

---

# Dry Run

### Operations

```java
update(1, 10)
update(2, 5)
update(1, 3)
```

### Map & Heaps after update(1, 3)

Map:

```java
1 -> 3
2 -> 5
```

Max Heap elements:

```java, [5, 2], [3, 1]
```

### maximum()

1. Peek max heap: `[10, 1]`.
2. Check Map for timestamp `1`: Valid price is `3`, not `10`.
3. Poll `[10, 1]` out.
4. Peek next: `[5, 2]`.
5. Check Map for timestamp `2`: Valid price is `5`. Matches!
6. Return `5`.

---

# Java Solution

```java
import java.util.*;

class StockPrice {

    private int latestTimestamp;
    private Map<Integer, Integer> timestampPriceMap;
    private PriorityQueue<int[]> maxHeap;
    private PriorityQueue<int[]> minHeap;

    public StockPrice() {
        latestTimestamp = 0;
        timestampPriceMap = new HashMap<>();
        maxHeap = new PriorityQueue<>((a, b) -> b[0] - a[0]);
        minHeap = new PriorityQueue<>((a, b) -> a[0] - b[0]);
    }

    public void update(int timestamp, int price) {
        latestTimestamp = Math.max(latestTimestamp, timestamp);
        timestampPriceMap.put(timestamp, price);
        
        maxHeap.add(new int[]{price, timestamp});
        minHeap.add(new int[]{price, timestamp});
    }

    public int current() {
        return timestampPriceMap.get(latestTimestamp);
    }

    public int maximum() {
        while (!maxHeap.isEmpty()) {
            int[] top = maxHeap.peek();
            int price = top[0];
            int timestamp = top[1];
            
            if (timestampPriceMap.get(timestamp) == price) {
                return price;
            }
            maxHeap.poll();
        }
        return -1;
    }

    public int minimum() {
        while (!minHeap.isEmpty()) {
            int[] top = minHeap.peek();
            int price = top[0];
            int timestamp = top[1];
            
            if (timestampPriceMap.get(timestamp) == price) {
                return price;
            }
            minHeap.poll();
        }
        return -1;
    }
}
```

---

# Complexity Analysis

### update()

```java
O(log N)
```

### current()

```java
O(1)
```

### maximum() / minimum()

Amortized time per valid operation:

```java
O(log N)
```

In the worst case, we might discard multiple stale elements.

---

# Approach 2: HashMap + TreeMap (Optimal)

## Observation

Instead of dealing with stale elements inside two separate heaps, we can actively manage price frequencies using a self-balancing binary search tree.

### Data Structures

```java
HashMap<timestamp, price>
TreeMap<price, frequency>
```

The TreeMap automatically keeps the prices sorted in real-time.

---

## Example

```java
timestampPriceMap:

1 -> 10
2 -> 5
```

```java
priceFrequency:

5 -> 1
10 -> 1
```

If `update(1, 3)` occurs, we decrement the frequency of `10`, remove it if it hits `0`, and add `3`.

---

## maximum() and minimum()

```java
maximum() -> priceFrequency.lastKey()
minimum() -> priceFrequency.firstKey()
```

---

# Java Solution (Optimal)

```java
import java.util.*;

class StockPrice {

    private int latestTimestamp;
    private Map<Integer, Integer> timestampPriceMap;
    private TreeMap<Integer, Integer> priceFrequency;

    public StockPrice() {
        latestTimestamp = 0;
        timestampPriceMap = new HashMap<>();
        priceFrequency = new TreeMap<>();
    }

    public void update(int timestamp, int price) {
        latestTimestamp = Math.max(latestTimestamp, timestamp);

        if (timestampPriceMap.containsKey(timestamp)) {
            int oldPrice = timestampPriceMap.get(timestamp);
            priceFrequency.put(oldPrice, priceFrequency.get(oldPrice) - 1);
            
            if (priceFrequency.get(oldPrice) == 0) {
                priceFrequency.remove(oldPrice);
            }
        }

        timestampPriceMap.put(timestamp, price);
        priceFrequency.put(price, priceFrequency.getOrDefault(price, 0) + 1);
    }

    public int current() {
        return timestampPriceMap.get(latestTimestamp);
    }

    public int maximum() {
        return priceFrequency.lastKey();
    }

    public int minimum() {
        return priceFrequency.firstKey();
    }
}
```

---

# Why TreeMap is Better?

Suppose you need strict predictability without waiting on unbounded loop cycles to clear heaps:

**Heap Approach**

```java
Cleans up stale elements lazily
```

Worst-case per call:

```java
O(N log N)
```

---

**TreeMap Approach**

Keeps the minimum and maximum boundaries explicitly correct at all times.

Cost:

```java
O(log N)
```

for all min/max requests consistently.

---

# Complexity Analysis (Optimal)

### update()

```java
O(log N)
```

### current()

```java
O(1)
```

### maximum() / minimum()

```java
O(log N)
```

---

# Comparison

| Approach | update | current | maximum / minimum |
|-----------|----------|--------|---------|
| HashMap + Heaps | O(log N) | O(1) | O(log N) amortized |
| HashMap + TreeMap | O(log N) | O(1) | O(log N) strict |

---

# Key Takeaways

- Match timestamps to tracking fields using a `HashMap`.
- Implement a heap approach using lazy deletion to discard overwritten configurations.
- For the cleanest LeetCode configuration, track value limits via:
  ```java
  HashMap<Timestamp, Price>
  TreeMap<Price, Frequency>
  ```
- `TreeMap` maintains a continually balanced window across valid values.
- Core concepts:
  - HashMap
  - Min/Max Heap
  - TreeMap (Ordered Map)
  - Lazy Deletion
- Expected interview preference: **HashMap + TreeMap**.
