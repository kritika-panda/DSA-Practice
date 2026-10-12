# Minimum Cost to Connect Sticks

## Problem Statement

You have some number of sticks with positive integer lengths. These lengths are given as an array `sticks`, where `sticks[i]` represents the length of the `i`-th stick.

You can connect any two sticks of lengths `x` and `y` into one stick by paying a cost of `x + y`. You must connect all the sticks together until there is only one stick remaining.

Return *the **minimum cost** to connect all the given sticks into one stick*.

The overall runtime complexity must be strictly bounded by:

```text
O(n * log(n))
```

*(where n is the total number of sticks in the array)*

---

## Examples

### Example 1

**Input**

```java
sticks = [2, 4, 3]
```

**Output**

```java
14
```

**Explanation**

We can optimize the connection steps as follows:
1. Connect stick `2` and stick `3` for a cost of `2 + 3 = 5`. The remaining sticks are `[5, 4]`. Total cost = `5`.
2. Connect stick `4` and stick `5` for a cost of `4 + 5 = 9`. The remaining stick is `[9]`. Total cost = `5 + 9 = 14`.

If we connected `2` and `4` first (`cost = 6`, remaining `[6, 3]`), then connected `6` and `3` (`cost = 9`), the total cost would be `6 + 9 = 15`, which is sub-optimal.

---

### Example 2

**Input**

```java
sticks = [1, 8, 3, 5]
```

**Output**

```java
30
```

---

### Example 3

**Input**

```java
sticks = [5]
```

**Output**

```java
0
```

**Explanation**

There is only one stick, so no connections are necessary. The cost is `0`.

---

## Brute Force Approach

Sort the array and repeatedly combine the first two elements. Insert the combined sum back into the collection, re-sorting the entire dataset after every single connection pass.

### Steps

1. Sort the `sticks` array in ascending order.
2. While the array has more than one element:
   - Extract the first two smallest elements: `x = sticks[0]` and `y = sticks[1]`.
   - Add their combined value `x + y` to a running `totalCost` accumulator.
   - Delete `x` and `y` from the collection and append the sum `x + y` back to the array.
   - Re-sort the entire array completely to ensure the next two elements extracted are the smallest.

### Complexity

```text
Time Complexity: O(n^2 * log(n)) // due to re-sorting the array after every connection cycle
Space Complexity: O(n) to manage dynamic array mutations
```

Re-sorting the entire array after every merge step introduces a massive quadratic bottleneck. We can optimize this process down to **log-linear time** by using a PriorityQueue.

---

# Optimal Approach: Greedy Huffman Coding via Min-Heap

## Key Idea

This problem is a classic application of a greedy strategy known as **Huffman Coding**. To minimize the global cost, we should **always merge the two shortest sticks available** at any given moment. 

When we combine two sticks, their sum is added to our total cost and then becomes a *new* stick that must be merged with others downstream. By merging the smallest sticks first, we ensure that smaller values are added to the cost multiple times, while larger values stay near the roots of the connection tree and are added fewer times.

We can maintain this dynamic pool of minimums efficiently using a **Min-Heap (PriorityQueue)**:
1. **Edge Case Guard:** If `sticks.length <= 1`, no connections can be made. Return `0` instantly.
2. **Heap Initialization:** Push all elements from the `sticks` array onto a `PriorityQueue`. The queue organizes items such that the absolute minimum element floats to the root.
3. **Greedy Collection Loop:** While the heap contains more than 1 element:
   - Extract the two absolute smallest elements from the root of the heap via `.poll()`: `first = minHeap.poll()` and `second = minHeap.poll()`.
   - Calculate their combined merge cost: `cost = first + second`.
   - Accumulate this value into our global counter: `totalCost += cost`.
   - Push the newly formed combined stick back onto the heap: `minHeap.offer(cost)`.
4. When only one single tracking item remains in the heap, return `totalCost`.

---

## Visual Understanding

Suppose `sticks = [1, 8, 3, 5]`:

- **Step 1 (Populate Heap):** `minHeap = [1, 3, 5, 8]`. Initialize `totalCost = 0`.
- **Step 2 (Merge 1):** 
  - Poll the two smallest items: `1` and `3`. 
  - Combined cost = `1 + 3 = 4`. Update `totalCost = 0 + 4 = 4`.
  - Re-offer `4`. Heap state re-balances to: `[4, 5, 8]`.
- **Step 3 (Merge 2):**
  - Poll the two smallest items: `4` and `5`.
  - Combined cost = `4 + 5 = 9`. Update `totalCost = 4 + 9 = 13`.
  - Re-offer `9`. Heap state re-balances to: `[8, 9]`.
- **Step 4 (Merge 3):**
  - Poll the final two items: `8` and `9`.
  - Combined cost = `8 + 9 = 17`. Update `totalCost = 13 + 17 = 30`.
  - Re-offer `17`. Heap state matches `[17]`. Loop terminates.

The total minimum cost required to stitch the sticks together evaluates to `30`.

---

## Partition Variables

Let:

```java
PriorityQueue<Integer> minHeap = new PriorityQueue<>();
int totalCost = 0;
```

---

### Border Elements

The method provides an immediate guard gate if the input length constraints fall below viable merging thresholds:

```java
if (sticks == null || sticks.length <= 1) {
    return 0;
}
```

---

## Correct Partition Condition

The greedy reduction loop pops element pairs and updates the collection dynamically until a single root value remains:

```java
while (minHeap.size() > 1) {
    int currentCost = minHeap.poll() + minHeap.poll();
    totalCost += currentCost;
    minHeap.offer(currentCost);
}
return totalCost;
```

---

## Java Solution

```java
import java.util.PriorityQueue;

class Solution {

    public int connectSticks(int[] sticks) {
        // Fast-fail: No cost is incurred if there are 1 or fewer sticks
        if (sticks == null || sticks.length <= 1) {
            return 0;
        }

        // Initialize a Min-Heap to dynamically track the smallest available stick lengths
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        for (int stick : sticks) {
            minHeap.offer(stick);
        }

        int totalCost = 0;

        // Step 1: Greedily merge the two shortest sticks until only one remains
        while (minHeap.size() > 1) {
            int first = minHeap.poll();
            int second = minHeap.poll();

            // Calculate the cost to connect these two specific sticks
            int currentMergeCost = first + second;
            totalCost += currentMergeCost;

            // Push the newly formed combined stick back onto the heap
            minHeap.offer(currentMergeCost);
        }

        return totalCost;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Greedy Heap Reduction** | **O(n * log(n))** | **O(n)** | Inserting `n` elements into the heap takes \(O(n \log n)\). The loop runs exactly `n - 1` times. Each iteration pops twice and pushes once, costing \(O(\log n)\) heap balancing steps. The priority queue stores up to `n` elements. |

---

## Key Insight

Using a priority queue to greedily pop and merge the two absolute smallest values dynamically prevents unnecessary element lookups, optimizing the combination path in stable log-linear time.

```text
Time  : O(n * log(n)) optimal log-linear performance track
Space : O(n) auxiliary heap layout tracking allocation
```

---

## Similar Problems

1. Merge k Sorted Lists (23)
2. Kth Largest Element in an Array (215)
3. Top K Frequent Elements (347)
4. Hand of Straights (846)
5. Huffman Coding Implementation
