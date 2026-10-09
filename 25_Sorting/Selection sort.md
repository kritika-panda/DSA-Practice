# Selection Sort in Java

## Problem Statement
Given an unsorted array, sort the elements in ascending order using the **Selection Sort** algorithm.

---

## What is Selection Sort?

Selection Sort is a simple comparison-based sorting algorithm.

### How it works:
1. Divide the array into two parts:
   - Sorted portion (initially empty)
   - Unsorted portion (initially the entire array)

2. Find the smallest element from the unsorted portion.

3. Swap it with the first element of the unsorted portion.

4. Move the boundary of the sorted portion one step forward.

5. Repeat until the array becomes sorted.

---

## Java Implementation

```java
import java.util.Arrays;

class GfG {

    static void selectionSort(int[] arr) {
        int n = arr.length;

        for (int i = 0; i < n - 1; i++) {

            // Assume current index contains minimum element
            int min_idx = i;

            // Find actual minimum element
            for (int j = i + 1; j < n; j++) {
                if (arr[j] < arr[min_idx]) {
                    min_idx = j;
                }
            }

            // Swap minimum element with current position
            int temp = arr[i];
            arr[i] = arr[min_idx];
            arr[min_idx] = temp;
        }
    }

    static void printArray(int[] arr) {
        for (int val : arr) {
            System.out.print(val + " ");
        }
        System.out.println();
    }

    public static void main(String[] args) {

        int[] arr = {64, 25, 12, 22, 11};

        System.out.print("Original array: ");
        printArray(arr);

        selectionSort(arr);

        System.out.print("Sorted array: ");
        printArray(arr);
    }
}
```

---

## Example

### Input

```text
[64, 25, 12, 22, 11]
```

### Output

```text
[11, 12, 22, 25, 64]
```

---

## Dry Run

### Initial Array

```text
[64, 25, 12, 22, 11]
```

### Pass 1 (i = 0)

Find minimum element from:

```text
[64, 25, 12, 22, 11]
```

Minimum = `11`

Swap `64` and `11`

```text
[11, 25, 12, 22, 64]
```

---

### Pass 2 (i = 1)

Find minimum element from:

```text
[25, 12, 22, 64]
```

Minimum = `12`

Swap `25` and `12`

```text
[11, 12, 25, 22, 64]
```

---

### Pass 3 (i = 2)

Find minimum element from:

```text
[25, 22, 64]
```

Minimum = `22`

Swap `25` and `22`

```text
[11, 12, 22, 25, 64]
```

---

### Pass 4 (i = 3)

Find minimum element from:

```text
[25, 64]
```

Minimum already at correct position.

```text
[11, 12, 22, 25, 64]
```

---

## Visualization

```text
Pass 1:
[64, 25, 12, 22, 11]
 ↑
Find min = 11
Swap
[11, 25, 12, 22, 64]

Pass 2:
[11, 25, 12, 22, 64]
     ↑
Find min = 12
Swap
[11, 12, 25, 22, 64]

Pass 3:
[11, 12, 25, 22, 64]
         ↑
Find min = 22
Swap
[11, 12, 22, 25, 64]

Pass 4:
[11, 12, 22, 25, 64]
             ↑
Already smallest
```

---

## Algorithm

```text
selectionSort(arr)

for i = 0 to n-2

    minIndex = i

    for j = i+1 to n-1
        if arr[j] < arr[minIndex]
            minIndex = j

    swap(arr[i], arr[minIndex])

return arr
```

---

## Why Does It Work?

At each iteration:

- The smallest element from the unsorted part is identified.
- It is placed into its final sorted position.
- Therefore, after the first pass, the first element is correctly positioned.
- After the second pass, the first two elements are correctly positioned.
- This continues until the entire array becomes sorted.

---

## Complexity Analysis

### Time Complexity

| Case | Complexity |
|--------|-----------|
| Best Case | O(n²) |
| Average Case | O(n²) |
| Worst Case | O(n²) |

### Why?

For every element:

```text
(n-1) + (n-2) + (n-3) + ... + 1
```

comparisons are performed.

This sum equals:

```text
n(n-1)/2
```

Therefore:

```text
O(n²)
```

---

## Space Complexity

```text
O(1)
```

Selection Sort is an **in-place sorting algorithm** because it uses only a few extra variables regardless of input size.

---

## Number of Swaps

One major advantage of Selection Sort is that it performs at most:

```text
n - 1
```

swaps.

This makes it useful when:

- Swapping is expensive
- Write operations should be minimized

---

## Characteristics of Selection Sort

| Property | Value |
|-----------|--------|
| Sorting Type | Comparison-based |
| In-place | ✅ Yes |
| Stable | ❌ No |
| Adaptive | ❌ No |
| Extra Space | O(1) |
| Recursive | No |
| Best for | Small datasets |

---

## Is Selection Sort Stable?

**No.**

Selection Sort may change the relative order of equal elements because of swapping.

### Example

```text
[(5,A), (5,B), (1,C)]
```

After sorting:

```text
[(1,C), (5,B), (5,A)]
```

The order of equal elements (`5,A` and `5,B`) changed.

Therefore, Selection Sort is **not stable**.

---

## Advantages

- Very easy to understand and implement.
- Requires only O(1) extra space.
- Performs a minimal number of swaps.
- Works well for small datasets.

---

## Disadvantages

- O(n²) time complexity.
- Inefficient for large datasets.
- Not stable.
- Does not adapt to partially sorted arrays.

---

## Interview Points

### Q1. Why is Selection Sort called "Selection" Sort?

Because in every iteration, it **selects the minimum element** from the unsorted portion and places it in its correct position.

---

### Q2. Is Selection Sort in-place?

✅ Yes

It requires only constant extra memory.

---

### Q3. Is Selection Sort stable?

❌ No

Swapping may change the relative order of equal elements.

---

### Q4. How many swaps does Selection Sort perform?

At most:

```text
n - 1
```

swaps.

---

### Q5. When should Selection Sort be preferred?

When:
- Dataset size is small.
- Memory usage must be minimal.
- Swaps are more expensive than comparisons.

---

## Key Takeaway

Selection Sort repeatedly finds the smallest element from the unsorted portion and places it at its correct position. It is easy to implement, uses **O(1)** extra space, and performs only **O(n)** swaps, but its **O(n²)** time complexity makes it inefficient for large datasets.
