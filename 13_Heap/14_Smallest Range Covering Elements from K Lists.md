# Smallest Range Covering Elements from K Lists

## Problem Statement

You are given `k` lists of sorted integers. Find the **smallest range** that includes at least one number from each of the `k` lists.

We define the range [a, b] is smaller than range [c, d] if b - a < d - c or a < c if b - a == d - c.

The overall run time complexity should be:

```text
O(n * log(k))
```

*(where n is the total number of elements across all k lists, and k is the number of lists)*

---

## Examples

### Example 1

**Input**

```java
nums = [
    [4, 10, 15, 24, 26],
    [0, 9, 12, 20],
    [5, 18, 22, 30]
]
```

**Output**

```java
[20, 24]
```

**Explanation**

- List 0: Contains `24` inside the range [20, 24].
- List 1: Contains `20` inside the range [20, 24].
- List 2: Contains `22` inside the range [20, 24].
This is the absolute smallest range possible containing an element from each list.

---

### Example 2

**Input**

```java
nums = [
    [1, 2, 3],
    [1, 2, 3],
    [1, 2, 3]
]
```

**Output**

```java
[1, 1]
```

---

## Brute Force Approach

Merge all lists into a single large array while tracking the list origin of each element, then use a sliding window over the combined array to find a valid window covering all k list IDs.

### Steps

1. Flatten all lists into a collection of pairs `(value, list_index)`.
2. Sort the entire collection by `value` in non-decreasing order.
3. Use a sliding window to find the smallest range [a, b] that captures at least one element from every list ID from 0 to k-1.
4. Track the absolute minimum range found.

### Complexity

```text
Time Complexity: O(n * log(n)) // where n is the total number of elements
Space Complexity: O(n)
```

Sorting the entire collection from scratch ignores the fact that each individual list is *already sorted*. We can optimize this using a Min-Heap pointer comparison strategy.

---

# Optimal Approach: Min-Heap with Maximum Pointer Tracking

## Key Idea

Since each list is already sorted, we can use a **Min-Heap (Priority Queue)** to inspect the smallest available elements from all k lists simultaneously. 

1. **Initialize the Window:** Push the first element of each of the k lists into the Min-Heap. Each entry stores a triplet: `{value, list_index, element_index}`. While pushing, track the absolute **maximum value** (`currentMax`) among these k initial elements.
2. **Evaluate the Range:** The root of the Min-Heap always represents the current **minimum value** (`currentMin`) of our active window. The difference between `currentMax` and `currentMin` forms a valid candidate range that spans at least one element from each list.
3. **Slide the Window Greedily:** Pop the minimum element from the heap. To try and narrow down our range, advance the pointer in that specific element's source list by pushing its next neighbor into the heap. Update `currentMax` if this new neighbor is larger.
4. **Terminate Early:** Repeat the process until any list runs out of elements. The moment a list is exhausted, it becomes impossible to form any subsequent valid window containing an element from all k lists.

---

## Visual Understanding

Suppose `nums = [[4, 10, 15], [0, 9], [5, 12]]`:

1. **Initial Heap State (k = 3):**
   - Offers: `4` (List 0), `0` (List 1), `5` (List 2).
   - `currentMin = 0`, `currentMax = 5`. Candidate Range = [0, 5] (Span = 5).

2. **Iteration 1:**
   - Pop `0` (List 1). Advance List 1 to next element `9`. Offer `9` to heap.
   - `currentMax` updates to `max(5, 9) = 9`.
   - New Heap Root `currentMin = 4`. Candidate Range = [4, 9] (Span = 5).

3. **Iteration 2:**
   - Pop `4` (List 0). Advance List 0 to next element `10`. Offer `10` to heap.
   - `currentMax` updates to `max(9, 10) = 10`.
   - New Heap Root `currentMin = 5`. Candidate Range = [5, 10] (Span = 5).

4. **Iteration 3:**
   - Pop `5` (List 2). Advance List 2 to next element `12`. Offer `12` to heap.
   - `currentMax` updates to `max(10, 12) = 12`.
   - New Heap Root `currentMin = 9`. Candidate Range = [9, 12] (Span = 3) -> Smaller! Update answer to [9, 12].

5. **Iteration 4:**
   - Pop `9` (List 1). List 1 is now empty. Loop terminates. Final Range = `[9, 12]`.

---

## Partition Variables

Let:

```java
PriorityQueue<int[]> minHeap = new PriorityQueue<>((a, b) -> a[0] - b[0]);
int currentMax = Integer.MIN_VALUE;
int rangeLeft = 0, rangeRight = Integer.MAX_VALUE;
```

---

### Border Elements

The sliding loop terminates instantly the moment any pointer reaches the boundary length limit of its corresponding list container:

```java
int nextElementIdx = minElement[2] + 1;
if (nextElementIdx == nums.get(listIdx).size()) {
    break; // A list is fully exhausted, no further global covers can be formed
}
```

---

## Correct Partition Condition

The running tracking range updates dynamically only if a strictly narrower window boundary interval is discovered:

```java
if (currentMax - currentMin < rangeRight - rangeLeft) {
    rangeLeft = currentMin;
    rangeRight = currentMax;
}
```

---

## How to Move Binary Search

*(Note: This optimal strategy replaces standard binary range partitioning with a multi-pointer Min-Heap sliding window to scale range validations in log-linear time).*

---

## Java Solution

```java
import java.util.List;
import java.util.PriorityQueue;

class Solution {

    public int[] findSmallestRange(List<List<Integer>> nums) {
        int k = nums.size();
        
        // Min-Heap stores array triplets: {element_value, list_index, element_index}
        PriorityQueue<int[]> minHeap = new PriorityQueue<>((a, b) -> a[0] - b[0]);
        
        int currentMax = Integer.MIN_VALUE;

        // Step 1: Initialize the heap with the first element of each list
        for (int i = 0; i < k; i++) {
            int val = nums.get(i).get(0);
            minHeap.offer(new int[]{val, i, 0});
            currentMax = Math.max(currentMax, val);
        }

        int rangeLeft = 0;
        int rangeRight = Integer.MAX_VALUE;

        // Step 2: Slide the tracking window greedily across elements
        while (minHeap.size() == k) {
            int[] minElement = minHeap.poll();
            int currentMin = minElement[0];
            int listIdx = minElement[1];
            int elementIdx = minElement[2];

            // Check if the current window forms a smaller range interval
            if (currentMax - currentMin < rangeRight - rangeLeft) {
                rangeLeft = currentMin;
                rangeRight = currentMax;
            }

            // Move pointer to the next element in the extracted list
            int nextElementIdx = elementIdx + 1;
            if (nextElementIdx < nums.get(listIdx).size()) {
                int nextVal = nums.get(listIdx).get(nextElementIdx);
                minHeap.offer(new int[]{nextVal, listIdx, nextElementIdx});
                currentMax = Math.max(currentMax, nextVal); // Update maximum window threshold
            } else {
                // If any list runs out of elements, we can no longer form a complete cover
                break;
            }
        }

        return new int[]{rangeLeft, rangeRight};
    }
}
```

---

## Dry Run

### Input

```java
nums = [[4, 10], [0, 9], [5, 12]]; // k = 3
```

---

### Step Execution Traversal

- **Initialization:**
  - Heap offered: `(4, 0, 0)`, `(0, 1, 0)`, `(5, 2, 0)`.
  - `currentMax = max(4, 0, 5) = 5`.
- **Iteration 1:**
  - `minElement = (0, 1, 0)`. `currentMin = 0`.
  - Range update: `5 - 0 < Integer.MAX` \(\implies\) `range = [0, 5]`.
  - Next item from List 1 is `9` \(\implies\) heap offers `(9, 1, 1)`. `currentMax = max(5, 9) = 9`.
- **Iteration 2:**
  - `minElement = (4, 0, 0)`. `currentMin = 4`.
  - Range check: `9 - 4 (5) == 5 - 0 (5)` -> Range stays `[0, 5]`.
  - Next item from List 0 is `10` \(\implies\) heap offers `(10, 0, 1)`. `currentMax = max(9, 10) = 10`.
- **Iteration 3:**
  - `minElement = (5, 2, 0)`. `currentMin = 5`.
  - Range check: `10 - 5 (5) == 5` -> Range stays `[0, 5]`.
  - Next item from List 2 is `12` \(\implies\) heap offers `(12, 2, 1)`. `currentMax = max(10, 12) = 12`.
- **Iteration 4:**
  - `minElement = (9, 1, 1)`. `currentMin = 9`.
  - Range check: `12 - 9 = 3 < 5` \(\implies\) **Range updates to `[9, 12]`**.
  - Next pointer from List 1 is out of bounds. Loop terminates.

---

### Answer

```java
[9, 12]
```

---

## Why Is the Runtime Complexity Log-Linear?

Instead of fully merging and sorting the multi-dimensional dataset from scratch—which introduces a high runtime barrier—the algorithms caps heap data tracking strictly to a capacity of k. At each step, pointer shifts execute in O(\log k) rebalancing steps over a maximum of n total elements.

This dynamic multi-pointer filtering yields:

```text
O(n * log(k))
```

which satisfies the optimal tracking complexity constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(n * log(k))
```

Where `n` is the total number of integers across all lists and `k` is the number of lists. Every item is inserted and removed from the heap at most once, and each priority rebalancing call takes O(\log k) steps.

---

### Space Complexity

```text
O(k)
```

The Priority Queue stores exactly one active tracking element per source list, consuming at most O(k) auxiliary space.

## Key Insight

Using a bounded Min-Heap to track the smallest element from each sorted list handles the window lower bound in logarithmic time, while an inline tracker monitors the upper bound to minimize the range in a single pass.
```text 
Time : O(n * log(k)) execution path Space : O(k) auxiliary heap storage capacity
```
