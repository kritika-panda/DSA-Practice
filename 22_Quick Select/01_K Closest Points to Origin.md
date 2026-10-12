# K Closest Points to Origin

## Problem Statement

Given an array of `points` where `points[i] = [xi, yi]` represents a point on the **X-Y** plane and an integer `k`, return the `k` closest points to the origin `(0, 0)`.

The distance between two points on the **X-Y** plane is the Euclidean distance:

```text
√( (x1 - x2)^2 + (y1 - y2)^2 )
```

You may return the answer in **any order**. The answer is guaranteed to be unique (except for the order that it is in).

The overall run time complexity should be:

```text
O(n)
```

---

## Examples

### Example 1

**Input**

```java
points = [[1, 3], [-2, 2]]
k = 1
```

**Output**

```java
[[-2, 2]]
```

**Explanation**

The distance from (1, 3) to the origin is √(1^2 + 3^2) = √10.  
The distance from (-2, 2) to the origin is √((-2)^2 + 2^2) = √8.  
Since √8 < √10, (-2, 2) is closer to the origin.  
We only need the k = 1 closest points, so we return [[-2, 2]].

---

### Example 2

**Input**

```java
points = [[3, 3], [5, -1], [-2, 4]]
k = 2
```

**Output**

```java
[[3, 3], [-2, 4]]
```

**Explanation**

The answer [[-2, 4], [3, 3]] would also be accepted.

---

### Example 3

**Input**

```java
points = [[1, 1], [2, 2]]
k = 2
```

**Output**

```java
[[1, 1], [2, 2]]
```

---

## Brute Force Approach

Calculate the squared Euclidean distance for each point, store them in a list along with their source indices, and sort the list in ascending order.

### Steps

1. Traverse the points array and calculate the distance squared: `x^2 + y^2` (omitting the square root avoids precision loss).
2. Store the distance and point coordinate pair together.
3. Sort the collection using a custom comparator based on distance.
4. Extract the first `k` elements from the sorted collection and return them.

### Complexity

```text
Time Complexity: O(n * log(n))
Space Complexity: O(n)
```

Sorting all elements prevents this approach from achieving linear time execution. Alternatively, using a Max-Heap/Priority Queue scales to `O(n log k)`, which still falls short of strict average linear time.

---

# Optimal Approach: Quickselect Algorithm

## Key Idea

Instead of fully sorting the array, we can use the **Quickselect** algorithm (a variant of Quick Sort). 

Quickselect helps us find the k-th smallest element (or partition the array into the smallest k elements) without sorting the entire array. It selects an element as a **pivot** and rearranges the array into two parts:
1. All elements with a distance smaller than or equal to the pivot's distance move to the **left**.
2. All elements with a distance greater than the pivot's distance move to the **right**.

After partitioning, the pivot ends up at its exact sorted position index `p`.
- If `p == k`, the first `k` elements in the array are exactly the `k` closest points.
- If `p < k`, we recursively partition the **right** half.
- If `p > k`, we recursively partition the **left** half.

On average, this reduces the search space by half at each step, achieving a true linear average runtime.

---

## Visual Understanding

Suppose:

```java
points = [[3, 3], [5, -1], [-2, 4]]
k = 2
```

1. **Calculate Squared Distances:**
   - `[3, 3]   -> 3^2 + 3^2   = 18`
   - `[5, -1]  -> 5^2 + (-1)^2 = 26`
   - `[-2, 4]  -> (-2)^2 + 4^2 = 20`

2. **Quickselect Partition Step:**
   - Let's choose the last element `[-2, 4]` (Distance = 20) as the pivot.
   - Elements smaller/equal to 20 move left: `[3, 3]` (18) stays left.
   - Elements larger than 20 move right: `[5, -1]` (26) moves right.
   - The pivot `[-2, 4]` (20) settles at index `1`.

Array state becomes: `[[3, 3], [-2, 4], [5, -1]]`.  
The pivot index `p = 1`. Since we need `k = 2` elements, we continue running Quickselect on the remaining right side partition bounds to secure the next closest element index.

---

## Partition Variables

Let:

```java
int low = 0;
int high = points.length - 1;
```

During each Quickselect partitioning step:

```java
int pivotIdx = partition(points, low, high);
```

---

### Border Elements

A distance helper function evaluates standard squared coordinates:

```java
private int getDistance(int[] point) {
    return point[0] * point[0] + point[1] * point[1];
}
```

---

## Correct Partition Condition

```java
if (pivotIdx == k) {
    break;
}
```

When the pivot index matches `k`, the first `k` spaces contain the closest points (unordered).

---

## How to Move Binary Search

### Case 1

```java
pivotIdx < k
```

The split point index did not capture enough elements on the left side.

Move right to expand the target pool:

```java
low = pivotIdx + 1;
```

---

### Case 2

```java
pivotIdx > k
```

The split point index captured too many elements on the left side.

Move left to shrink the target pool:

```java
high = pivotIdx - 1;
```

---

## Java Solution

```java
import java.util.Arrays;

class Solution {

    public int[][] kClosest(int[][] points, int k) {

        int low = 0;
        int high = points.length - 1;

        // Perform Quickselect to partition the array around index k
        while (low < high) {
            int pivotIdx = partition(points, low, high);
            
            if (pivotIdx == k) {
                break;
            } else if (pivotIdx < k) {
                low = pivotIdx + 1;
            } else {
                high = pivotIdx - 1;
            }
        }

        // Return the first k elements from the partitioned array
        return Arrays.copyOfRange(points, 0, k);
    }

    private int partition(int[][] points, int low, int high) {
        
        int[] pivot = points[high];
        int pivotDist = getDistance(pivot);
        int i = low;

        for (int j = low; j < high; j++) {
            if (getDistance(points[j]) <= pivotDist) {
                swap(points, i, j);
                i++;
            }
        }
        
        swap(points, i, high);
        return i;
    }

    private int getDistance(int[] point) {
        return point[0] * point[0] + point[1] * point[1];
    }

    private void swap(int[][] points, int i, int j) {
        int[] temp = points[i];
        points[i] = points[j];
        points[j] = temp;
    }
}
```

---

## Dry Run

### Input

```java
points = [[3, 3], [5, -1], [-2, 4]]
k = 2
```

---

### Initial Values

```java
low = 0
high = 2
```

---

### Iteration 1

```java
pivot = points[2] = [-2, 4] -> distance = 20
i = 0
```

- **j = 0:** `points[0] = [3, 3]` (dist = 18). `18 <= 20` -> swap `points[0]` with `points[0]`. `i = 1`.
- **j = 1:** `points[1] = [5, -1]` (dist = 26). `26 > 20` -> no swap.
- Loop ends. Swap `points[1]` with `points[2]`.
- Array becomes: `[[3, 3], [-2, 4], [5, -1]]`.
- Returns `pivotIdx = 1`.

Since `pivotIdx (1) < k (2)`:

```java
low = 1 + 1 = 2
```

---

### Iteration 2

```java
low = 2
high = 2
```

Loop terminates because `low < high` condition fails (`2 < 2` is false). The element arrays are correctly partitioned.

---

### Answer

```java
[[3, 3], [-2, 4]]
```

---

## Why Do We Use Quickselect?

Quickselect avoids sorting the entire array by isolating operations only to the half section that contains our goal target rank index `k`. By bypassing ordering calculations for irrelevant elements, the full time complexity reduces significantly.

This selective search yields:

```text
O(n) average time complexity
```

which satisfies the optimal time constraint.

---

## Complexity Analysis

### Time Complexity

```text
Average Time: O(n)
Worst Case Time: O(n^2)
```

On average, the partitioning step cuts the problem size in half: n + n/2 + n/4 + ... = 2n, resulting in O(n) time. The worst case occurs if the array splits poorly every single time (e.g., already sorted with bad pivot selections), which scales to O(n²).

---

### Space Complexity

```text
O(1)
```

No supplemental heap priority queues or duplicate tracker matrices are created in memory; all operations happen in-place via swaps.

---

## Key Insight

When a problem asks for the top k items without requiring the output itself to be perfectly sorted, utilizing Quickselect partitions the border boundaries in-place without paying full comparison sorting costs.

```text
Time  : O(n) average
Space : O(1)
```

---

## Similar Problems

1. Kth Largest Element in an Array (215)
2. Top K Frequent Elements (347)
3. Sort Characters By Frequency (451)
4. High Five (1086)
5. K Closest Elements (658)
