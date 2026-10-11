# Interval List Intersections

## Problem Statement

You are given two lists of closed intervals, `firstList` and `secondList`, where `firstList[i] = [starti, endi]` and `secondList[j] = [startj, endj]`. Each list of intervals is pairwise disjoint and sorted in ascending order.

Return the **intersection** of these two interval lists.

A closed interval `[a, b]` (with a ≤ b) denotes the set of real numbers x with a ≤ x ≤ b. The intersection of two closed intervals is a set of real numbers that is either empty or forms another closed interval. For example, the intersection of `[1, 3]` and `[2, 4]` is `[2, 3]`.

The overall run time complexity should be:

```text
O(m + n)
```

*(where m is the length of firstList and n is the length of secondList)*

---

## Examples

### Example 1

**Input**

```java
firstList = [[0, 2], [5, 10], [13, 23], [24, 25]]
secondList = [[1, 5], [8, 12], [15, 24], [25, 26]]
```

**Output**

```java
[[1, 2], [5, 5], [8, 10], [15, 23], [24, 24], [25, 25]]
```

**Explanation**

The intersecting boundaries between pairs are extracted sequentially:
- `[0, 2]` and `[1, 5]` overlap from `[1, 2]`.
- `[5, 10]` and `[1, 5]` touch at `[5, 5]`.
- `[5, 10]` and `[8, 12]` overlap from `[8, 10]`.
- `[13, 23]` and `[15, 24]` overlap from `[15, 23]`.
- `[24, 25]` and `[15, 24]` touch at `[24, 24]`.
- `[24, 25]` and `[25, 26]` touch at `[25, 25]`.

---

### Example 2

**Input**

```java
firstList = [[1, 3], [5, 9]]
secondList = []
```

**Output**

```java
[]
```

---

## Brute Force Approach

Use two nested loops to compare every interval in `firstList` with every interval in `secondList`, checking for any boundary overlap.

### Steps

1. Run an outer loop with index `i` from `0` to `m - 1` for `firstList`.
2. Run an inner loop with index `j` from `0` to `n - 1` for `secondList`.
3. For each pair, check if they overlap by finding the maximum start time and minimum end time:
   - `start = max(firstList[i][0], secondList[j][0])`
   - `end = min(firstList[i][1], secondList[j][1])`
4. If `start <= end`, a valid intersection exists. Save `[start, end]`.

### Complexity

```text
Time Complexity: O(m * n)
Space Complexity: O(1) auxiliary space
```

This brute-force approach ignores the fact that both lists are **already sorted** and disjoint. Comparing every single pair leads to an unnecessary quadratic time bottleneck. We can achieve linear time using a two-pointer approach.

---

# Optimal Approach: Two-Pointer Sliding Pass

## Key Idea

Since both lists are sorted and disjoint, we can use a **two-pointer approach** (index pointer `i` for `firstList` and index pointer `j` for `secondList`) to find intersections in a single parallel sweep.

For any pair of intervals `firstList[i]` and `secondList[j]`:
1. **Find Intersection:** Calculate the potential overlap window:
   - `start = Math.max(firstList[i][0], secondList[j][0])`
   - `end = Math.min(firstList[i][1], secondList[j][1])`
   If `start <= end`, the intervals overlap, and we add `[start, end]` to our results.
2. **Advance Pointers:** To move forward, we compare the end times of the two current intervals. The interval that ends **earlier** can never intersect with any subsequent interval in the opposite list because the remaining intervals start even later. Therefore, we advance the pointer belonging to the interval with the **smaller end time**:
   - If `firstList[i][1] < secondList[j][1]`, increment `i`.
   - Otherwise, increment `j`.

---

## Visual Understanding

Suppose `firstList = [[0, 2]]` and `secondList = [[1, 5]]`:

- **Step 1 (`i = 0, j = 0`):** Compare `[0, 2]` and `[1, 5]`.
  - `start = max(0, 1) = 1`, `end = min(2, 5) = 2`.
  - Since `1 <= 2`, add `[1, 2]` to the results.
  - End times comparison: `firstList[0][1] (2) < secondList[0][1] (5)` \(\implies\) Advance `i` (`i = 1`).

The loop terminates because `i` goes out of bounds. The process completes efficiently in linear time.

---

## Partition Variables

Let:

```java
List<int[]> result = new ArrayList<>();
int i = 0; // Pointer for firstList
int j = 0; // Pointer for secondList
```

---

### Border Elements

The parallel scanning loops guard the limits of both data sets continuously to prevent out-of-bounds errors:

```java
while (i < firstList.length && j < secondList.length)
```

---

## Correct Partition Condition

The tracking pointer that belongs to the interval with the smaller trailing edge advances forward to test subsequent matches:

```java
if (firstList[i][1] < secondList[j][1]) {
    i++;
} else {
    j++;
}
```

---

## How to Move Binary Search

*(Note: This optimal strategy replaces standard binary range division with a parallel two-pointer pass over pre-sorted coordinate sets to guarantee true linear runtimes).*

---

## Java Solution

```java
import java.util.ArrayList;
import java.util.List;

class Solution {

    public int[][] intervalIntersection(int[][] firstList, int[][] secondList) {
        if (firstList == null || secondList == null || firstList.length == 0 || secondList.length == 0) {
            return new int[0][0];
        }

        List<int[]> result = new ArrayList<>();
        int i = 0;
        int j = 0;

        // Traverse both lists in a single parallel sweep
        while (i < firstList.length && j < secondList.length) {
            
            // Find the maximum start point and minimum end point
            int start = Math.max(firstList[i][0], secondList[j][0]);
            int end = Math.min(firstList[i][1], secondList[j][1]);

            // If start <= end, the two intervals overlap
            if (start <= end) {
                result.add(new int[]{start, end});
            }

            // Move the pointer that has the smaller end time forward
            if (firstList[i][1] < secondList[j][1]) {
                i++;
            } else {
                j++;
            }
        }

        // Convert the list back into a primitive 2D array
        return result.toArray(new int[result.size()][]);
    }
}
```

---

## Dry Run

### Input

```java
firstList = [[1, 3], [5, 9]];
secondList = [[2, 5]];
```

---

### Step Execution Traversal

- **Iteration 1 (`i = 0, j = 0`):** Compare `[1, 3]` and `[2, 5]`.
  - `start = max(1, 2) = 2`, `end = min(3, 5) = 3`.
  - `2 <= 3` (True) \(\implies\) Add `[2, 3]` to results. `result = [[2, 3]]`.
  - End times check: `firstList[0][1] (3) < secondList[0][1] (5)` \(\implies\) `i` increments to `1`.
- **Iteration 2 (`i = 1, j = 0`):** Compare `[5, 9]` and `[2, 5]`.
  - `start = max(5, 2) = 5`, `end = min(9, 5) = 5`.
  - `5 <= 5` (True) \(\implies\) Add `[5, 5]` to results. `result = [[2, 3], [5, 5]]`.
  - End times check: `firstList[1][1] (9) >= secondList[0][1] (5)` \(\implies\) `j` increments to `1`.

Loop terminates because `j` reaches the end of `secondList`.

---

### Answer

```java
[[2, 3], [5, 5]]
```

---

## Why Do We Use Two Pointers?

By comparing end times, we can immediately discard the interval that ends earlier, as it cannot intersect with any subsequent intervals. This greedy choice avoids the need for nested comparisons and allows the problem to be solved in a single pass.

This single-pass workflow yields:

```text
O(m + n)
```

which satisfies the optimal time constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(m + n)
```

Where `m` is the total number of intervals in `firstList` and `n` is the total number of intervals in `secondList`. The pointer indices increment a maximum total of `m + n` times.

---

### Space Complexity

```text
O(m + n)
```

An auxiliary array list is used to temporarily collect the intersected segments before converting them into the final output structure.

```text
Time  : O(m + n) execution pass
Space : O(m + n) dynamic array allocation
```

---

## Similar Problems

1. Merge Intervals (56)
2. Insert Interval (57)
3. Non-overlapping Intervals (435)
4. Meeting Rooms (252)
5. Summary Ranges (228)
