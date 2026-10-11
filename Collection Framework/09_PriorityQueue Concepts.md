# PriorityQueue in Java

## What is PriorityQueue?

A `PriorityQueue` is a core collection class in Java that implements the `Queue` interface. It belongs to the package:

```java
java.util.PriorityQueue
```

Unlike a standard Queue which follows a strict First-In, First-Out (FIFO) processing timeline, a `PriorityQueue` schedules elements based on their **priority**. By default, it orders elements according to their **natural ordering** (ascending order, acting as a **Min-Heap**), but you can customize this sorting hierarchy by passing an external `Comparator` during instantiation.

Unlike some set implementations, a `PriorityQueue` **does not allow null elements** (attempting to offer a `null` value will throw a `NullPointerException`), and it permits duplicate values freely.

---

# Why Do We Need PriorityQueue?

While simple arrays or lists allow you to collect dynamic data streams, extracting elements based on continuous relative rank creates expensive processing bottlenecks:
- **The Sorting Overhead:** If you use an `ArrayList` and continuously need to extract the absolute smallest or largest element as new data streams in, you must repeatedly sort the array—taking a costly $O(n \log n)$ time penalty—or run an $O(n)$ linear scan loop.
- **Dynamic Priority Tracking:** If an algorithm requires real-time resource allocation, tracking the absolute highest priority item must happen instantly without paying full array shifting or re-sorting costs.

`PriorityQueue` completely resolves this. It maintains a partially ordered structure dynamically, allowing you to insert elements and continuously extract the highest-priority item in optimized **logarithmic $O(\log n)$ time**.

---

# Core Priority Queue Types

## 1. Min-Heap (Default)
The element with the **lowest value** is assigned the highest priority and sits right at the head of the queue.
```text
Head of Queue: [ 10 ]  <-- Pops out first
Internal elements: 20, 30, 40, 50
```

## 2. Max-Heap (Customized)
The element with the **highest value** is assigned the highest priority and sits right at the head of the queue. This is configured by passing a reverse comparator:
```java
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
```

---

# Internals & The Complete Binary Tree Heap Layout

A `PriorityQueue` is structurally implemented as a **Binary Heap** stored inside a dynamically resizable primitive `Object[] queue` array. 

It forms a **complete binary tree**, meaning every level of the tree is completely filled from left to right, except possibly the deepest leaf layer.

```text
Logical Complete Binary Tree Structure:
                 10 (Root / Minimum)
               /    \
             20      30
            /  \    /
          40   50  35
```

Mapped directly into a flat single-dimensional array using mathematical index relations:
```text
Index:  0   1   2   3   4   5
Value: [10][20][30][40][50][35]
```

For any element sitting at index `i`:
- **Parent Index:** `(i - 1) / 2`
- **Left Child Index:** `2 * i + 1`
- **Right Child Index:** `2 * i + 2`

### The Balancing Trick (Heapify)
- **`offer(element)` (Up-Heap / Sift-Up):** The new element is appended to the very end of the primitive array to preserve the complete tree shape. If it violates the priority rule, it bubbles up by swapping indices with its parent recursively until it settles into a stable spot.
- **`poll()` (Down-Heap / Sift-Down):** The root element at index `0` is removed. To fill the empty slot, the very last element in the array is copied directly to index `0`. It then trickles down by swapping indices with its smallest child recursively until the priority property is balanced across the tree branch.

---

# Core PriorityQueue Syntax & Basic Operations

## Initialization & Min-Heap Workflows

```java
// Default constructor initializes an ascending Min-Heap layout
Queue<Integer> minHeap = new PriorityQueue<>();

minHeap.offer(40);
minHeap.offer(10);
minHeap.offer(30);
minHeap.offer(20);

// peek() lets you read the highest priority root element without removing it
System.out.println(minHeap.peek()); // Output: 10

// poll() extracts and returns the highest priority root element
while (!minHeap.isEmpty()) {
    System.out.print(minHeap.poll() + " "); 
}
// Output is guaranteed to be: 10 20 30 40
```

---

## Max-Heap Construction using Custom Comparators

```java
// Passing a lambda expression comparator overrides natural ascending sorting
Queue<Integer> maxHeap = new PriorityQueue<>((a, b) -> Integer.compare(b, a));

maxHeap.offer(10);
maxHeap.offer(40);
maxHeap.offer(20);

System.out.println(maxHeap.poll()); // Output: 40 (Largest value rolls out first)
```

---

# Time Complexity Metrics

| Operation | Time Complexity | Space Complexity | Technical Reason |
| :--- | :--- | :--- | :--- |
| **`pq.peek()`** | **O(1)** | **O(1)** | Reads the absolute first slot `queue[0]` of the primitive array directly. |
| **`pq.offer(val)`** | **O(log n)** | **O(1)** | Bubbles up the single branch axis matching the tree height $\log_2(n)$. |
| **`pq.poll()`** | **O(log n)** | **O(1)** | Moves down the single branch axis matching the tree height $\log_2(n)$ to re-balance the root. |
| **`pq.remove(val)`** | **O(n)** | **O(1)** | Forces an un-indexed linear search loop to locate the target value before unlinking it. |
| **`pq.size()`** | **O(1)** | **O(1)** | Directly reads an internal primitive integer tracking field. |

---

# Key Interview Takeaways

```text
Constant Top Read -> Use PriorityQueue when your algorithm needs constant-time access O(1) to the minimum or maximum element while data streams dynamically.

The Chaotic Iterator Trap -> Never print or loop through a PriorityQueue using an Iterator or a for-each loop if you expect sorted order. The internal heap array is only partially ordered to guarantee branch limits. Sorted elements are only guaranteed when you extract them sequentially using .poll().

The Arbitrary Removal Penalty -> Avoid using pq.remove(element) inside core algorithmic loops. Finding and removing an element from the middle of a heap forces an O(n) linear array scan, erasing the performance benefits of the data structure.

The Immutable Element Contract -> If an object's properties change while it sits inside a PriorityQueue, the heap will not re-heapify automatically. This corrupts the data structure's priorities. Always poll an element out, mutate its fields, and offer it back to trigger re-balancing safely.
```

`PriorityQueue` is one of the most heavily tested data structures for optimizing greedy selections in tech evaluations. It is indispensable for writing fast, sub-linear lookup routines in problems involving:

```text
Top K Frequent Elements / K Closest Points to Origin (Capping heap size to k)
Merging k Sorted Lists / Streams simultaneously (Comparing heads)
Dijkstra's Shortest Path Algorithm (Greedily extracting the node with the minimum distance)
Huffman Coding Tree generation pipelines
Interval Scheduling Maxima & Greedy Timeline Simulation Blocks (e.g., Meeting Rooms II)
```

<FollowUp>
If you want to continue mapping out these priority data structures, let me know:
* Would you like to see how to solve **K Closest Points to Origin (973)** or **Merge k Sorted Lists (23)** using a `PriorityQueue`?
* Do you want a raw markdown template explaining **how to implement a custom Min-Heap from scratch using an array**?
* Or should we move to a different collection area like **HashMap internals**?

Tell me which **key terms** or design patterns you want to explore next!
</FollowUp>```
