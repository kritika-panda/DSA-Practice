# The K Weakest Rows in a Matrix

## Problem Statement

You are given an `m x n` binary matrix `mat` of `0`s and `1`s, where the `1`s represent soldiers and the `0`s represent civilians. The soldiers are positioned to the **left** of the civilians; that is, all the `1`s will appear before all the `0`s in each row.

A row `i` is **weaker** than a row `j` if one of the following conditions is true:
1. The number of soldiers in row `i` is less than the number of soldiers in row `j`.
2. Both rows have the same number of soldiers and `i < j` (row `i` has a smaller row index).

Return the indices of the `k` **weakest rows** in the matrix ordered from weakest to strongest.

The overall run time complexity should be:

```text
O(m * log(n) + m * log(k))
```

*(where m is the number of rows, n is the number of columns, and k is the number of weakest rows requested)*

---

## Examples

### Example 1

**Input**

```java
mat = [,
 ,
 ,
 ,
  [1, 1, 1, 1, 1]
];
k = 3;
```

**Output**

```java
```

**Explanation**

The number of soldiers in each row is:
- Row 0: 2 
- Row 1: 4 
- Row 2: 1 
- Row 3: 2 
- Row 4: 5 

Ordered from weakest to strongest by soldier count (and index tie-breaker), the rows are: `2, 0, 3, 1, 4`.  
The `k = 3` weakest rows are `[2, 0, 3]`.

---

### Example 2

**Input**

```java
mat = [,
 ,
 ,
  [1, 0, 0, 0]
];
k = 2;
```

**Output**

```java
```

**Explanation**

The number of soldiers in each row is:
- Row 0: 1
- Row 1: 4
- Row 2: 1
- Row 3: 1

The rows ordered from weakest to strongest are `0, 2, 3, 1`. The `k = 2` weakest rows are `[0, 2]`.

---

## Brute Force Approach

Linearly count the number of `1`s in each row, pair the count with the row index, and sort all the rows entirely.

### Steps

1. Iterate through each row from `0` to `m - 1` and count the `1`s linearly.
2. Store pairs of `(soldier_count, row_index)` in a list.
3. Sort the list globally using a custom comparator (primary: soldier count ascending, secondary: index ascending).
4. Extract the first `k` row indices from the sorted list and return them.

### Complexity

```text
Time Complexity: O(m * n + m * log(m))
Space Complexity: O(m)
```

This approach is inefficient because it scans elements line-by-line and sorts all `m` rows when we only need the top `k` weakest rows. We can optimize this using binary search and a bounded Max-Heap.

---

# Optimal Approach: Binary Search + Bounded Max-Heap

## Key Idea

We can leverage the sorted structure of each row to optimize the row-counting step, and use a Max-Heap to track the `k` weakest rows efficiently without a global sort.

1. **Binary Search Counting:** Since all `1`s come before `0`s in a row, the number of soldiers is exactly the index of the first `0`. We can find this index in $O(\log n)$ time using binary search (`upper_bound` logic).
2. **Bounded Max-Heap Filtering:** We maintain a Max-Heap capped at a maximum size of `k`. A Max-Heap allows us to evict the *strongest* rows from our pool when capacity is exceeded. The "strongest" candidate inside our heap means:
   - Rows with a **higher soldier count**.
   - Rows with a **higher row index** if soldier counts tie.
3. **Reconstruct Output Array:** After processing all `m` rows, the heap contains exactly the `k` weakest rows. We unroll them from the heap into an array backward to arrange them from weakest to strongest.

---

## Visual Understanding

Suppose `mat` is from Example 1, and `k = 3`:

1. **Count Soldiers with Binary Search & push to Max-Heap:**
   - Row 0: 2 soldiers -> Heap: `[(2, idx:0)]`
   - Row 1: 4 soldiers -> Heap: `[(4, idx:1), (2, idx:0)]` (Strongest on top)
   - Row 2: 1 soldier  -> Heap: `[(4, idx:1), (2, idx:0), (1, idx:2)]`
   - Row 3: 2 soldiers -> Size becomes 4. Evict the strongest. Comparing `(4, 1)` and `(2, 3)`, `(4, 1)` is stronger and gets popped. Heap: `[(2, idx:3), (2, idx:0), (1, idx:2)]`
   - Row 4: 5 soldiers -> Size becomes 4. `(5, 4)` is stronger than `(2, 3)` and gets popped immediately.

2. **Unroll Heap to Result Array (Backward):**
   - Pop `(2, idx:3)` -> Place at `result = 3`
   - Pop `(2, idx:0)` -> Place at `result = 0`
   - Pop `(1, idx:2)` -> Place at `result = 2`

Final Output Array = ``.

---

## Partition Variables

Let:

```java
PriorityQueue<int[]> maxHeap = new PriorityQueue<>((a, b) -> a[0] == b[0] ? b[1] - a[1] : b[0] - a[0]);
int[] result = new int[k];
```

---

### Border Elements

The heap boundaries discard the strongest row candidate whenever the capacity limit crosses `k`:

```java
if (maxHeap.size() > k) {
    maxHeap.poll(); // Evicts the strongest row out of the tracking pool
}
```

---

## Correct Partition Condition

The binary search searches for the first occurrence of a civilian (`0`) to lock the soldier count boundary:

```java
while (low <= high) {
    int mid = low + (high - low) / 2;
    if (row[mid] == 1) low = mid + 1;
    else high = mid - 1;
}
return low; // Holds total count of 1s
```

---

## How to Move Binary Search

*(Note: This optimal strategy combines a 1D range Binary Search with a bounded Priority Queue heap filter to isolate row metrics in log-linear time).*

---

## Java Solution

```java
import java.util.PriorityQueue;

class Solution {

    public int[] kWeakestRows(int[][] mat, int k) {
        int m = mat.length;
        int n = mat[0].length;

        // Max-Heap tracking: stores array pairs of {soldier_count, row_index}
        // Custom comparator: Evicts larger soldier count first; ties broken by larger index
        PriorityQueue<int[]> maxHeap = new PriorityQueue<>((a, b) -> 
            a[0] == b[0] ? b[1] - a[1] : b[0] - a[0]
        );

        // Step 1: Count soldiers via Binary Search and filter through Max-Heap
        for (int i = 0; i < m; i++) {
            int soldiers = countSoldiers(mat[i], n);
            maxHeap.offer(new int[]{soldiers, i});
            
            if (maxHeap.size() > k) {
                maxHeap.poll(); // Evict the strongest row candidate
            }
        }

        // Step 2: Unroll the k weakest rows backward into the final array
        int[] result = new int[k];
        for (int i = k - 1; i >= 0; i--) {
            result[i] = maxHeap.poll()[1];
        }

        return result;
    }

    private int countSoldiers(int[] row, int n) {
        int low = 0;
        int high = n - 1;

        // Binary search for the first civilian '0' index
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (row[mid] == 1) {
                low = mid + 1; // Look right
            } else {
                high = mid - 1; // Look left
            }
        }
        return low; // 'low' index corresponds exactly to the count of 1s
    }
}
```

---

## Dry Run

### Input

```java
mat = [,
 ,
  [1, 1, 1]
];
k = 2;
```

---

### Step Execution Traversal

- **i = 0:** Binary Search counts `2` soldiers. `maxHeap.offer([2, 0])`. Heap: `[[2, 0]]`.
- **i = 1:** Binary Search counts `1` soldier. `maxHeap.offer([1, 1])`. Heap: `[[2, 0], [1, 1]]`.
- **i = 2:** Binary Search counts `3` soldiers. `maxHeap.offer([3, 2])`. Heap size = 3. 
  - `[3, 2]` is the strongest, sitting at the top of our Max-Heap. 
  - `maxHeap.poll()` removes `[3, 2]`. Heap stays: `[[2, 0], [1, 1]]`.
- **Result Unrolling:**
  - `i = 1`: polls `[2, 0]` -> `result[1] = 0`
  - `i = 0`: polls `[1, 1]` -> `result[0] = 1`

Final Array = ``.

---

## Why Is the Time Complexity Log-Linear?

By calculating soldier frequencies with Binary Search across the matrix rows (\(O(m \log n)\)) and maintaining a bounded tracking window size of `k` inside our tree framework (\(O(m \log k)\)), we completely bypass full array sorting overhead. 

This hybrid processing yields:

```text
O(m * log(n) + m * log(k))
```

which satisfies the strict optimization constraint.

---

## Complexity Analysis

### Time Complexity

```text
O(m * log(n) + m * log(k))
```

Where `m` is the row count, `n` is the column count, and `k` is the threshold size. The binary search on each row takes \(O(\log n)\) time, and updating a heap capped at size `k` takes \(O(\log k)\) time per row.

---

### Space Complexity

```text
O(k)
```

The active Max-Heap Priority Queue stores exactly `k` row pointer information blocks at any point during execution.

---

## Key Insight

Using Binary Search to identify soldier transitions removes row-scanning bottlenecks, while capping our Max-Heap at size `k` keeps our tracking window focused on the weakest rows in log-linear time.

```text
Time  : O(m * log(n) + m * log(k)) runtime profile
Space : O(k) auxiliary queue storage footprint
```

---

## Similar Problems

1. Kth Largest Element in an Array (215)
2. Top K Frequent Elements (347)
3. K Closest Points to Origin (973)
4. Find Row with Maximum 1's
5. Search a 2D Matrix (74)
