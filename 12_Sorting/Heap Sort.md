# Heap Sort

## What is Heap Sort?

**Heap Sort** is a comparison-based sorting algorithm that uses a **Binary Heap** data structure.

It works in two phases:

1. Build a **Max Heap** from the input array.
2. Repeatedly extract the maximum element and place it at the end of the array.

---

# Binary Heap Refresher

A **Max Heap** is a Complete Binary Tree where:

```text
Parent >= Left Child
Parent >= Right Child
```

### Example

```text
        10
       /  \
      8    5
     / \
    4   3
```

Every parent is greater than or equal to its children.

---

# Array Representation of Heap

Given:

```java
int[] arr = {9, 4, 3, 8, 10, 2, 5};
```

Indices:

```text
Index : 0  1  2  3  4  5  6
Value : 9  4  3  8 10  2  5
```

For any node at index `i`:

```text
Left Child  = 2 * i + 1
Right Child = 2 * i + 2
Parent      = (i - 1) / 2
```

---

# Complete Code

```java
import java.util.Arrays;

public class GFG {

    static void heapify(int[] arr, int n, int i) {

        int largest = i;

        int l = 2 * i + 1;
        int r = 2 * i + 2;

        if (l < n && arr[l] > arr[largest])
            largest = l;

        if (r < n && arr[r] > arr[largest])
            largest = r;

        if (largest != i) {

            int temp = arr[i];
            arr[i] = arr[largest];
            arr[largest] = temp;

            heapify(arr, n, largest);
        }
    }

    static void heapSort(int[] arr) {

        int n = arr.length;

        for (int i = n / 2 - 1; i >= 0; i--)
            heapify(arr, n, i);

        for (int i = n - 1; i > 0; i--) {

            int temp = arr[0];
            arr[0] = arr[i];
            arr[i] = temp;

            heapify(arr, i, 0);
        }
    }

    public static void main(String[] args) {

        int[] arr = {9, 4, 3, 8, 10, 2, 5};

        heapSort(arr);

        System.out.println(Arrays.toString(arr));
    }
}
```

---

# Understanding `heapify()`

## Purpose

Convert the subtree rooted at index `i` into a valid Max Heap.

```java
static void heapify(int[] arr, int n, int i)
```

Parameters:

```text
arr -> array
n   -> heap size
i   -> root index of subtree
```

---

## Step 1: Assume Current Node is Largest

```java
int largest = i;
```

Example:

```text
        4
       / \
      8   10
```

Initially:

```text
largest = 4
```

---

## Step 2: Find Child Indices

```java
int l = 2 * i + 1;
int r = 2 * i + 2;
```

Example:

```text
Index = 2

Left Child
= 2*2+1
= 5

Right Child
= 2*2+2
= 6
```

---

## Step 3: Compare Left Child

```java
if (l < n && arr[l] > arr[largest])
    largest = l;
```

If left child is larger:

```text
        4
       /
      8
```

Update:

```text
largest = 8
```

---

## Step 4: Compare Right Child

```java
if (r < n && arr[r] > arr[largest])
    largest = r;
```

Example:

```text
        4
       / \
      8  10
```

After comparison:

```text
largest = 10
```

---

## Step 5: Swap if Needed

```java
if (largest != i)
```

If root is not the largest:

```text
        4
       / \
      8  10
```

Swap:

```text
       10
       / \
      8   4
```

Code:

```java
int temp = arr[i];
arr[i] = arr[largest];
arr[largest] = temp;
```

---

## Step 6: Recursively Heapify

```java
heapify(arr, n, largest);
```

Why?

After swapping:

```text
       10
       / \
      8   4
         / \
        2   5
```

The subtree rooted at:

```text
4
```

may violate heap property.

So we recursively fix it.

---

# Building the Max Heap

Initial Array:

```text
[9, 4, 3, 8, 10, 2, 5]
```

Tree:

```text
            9
          /   \
         4     3
       /  \   / \
      8   10 2   5
```

---

## Why Start from `n/2 - 1`?

```java
for (int i = n / 2 - 1; i >= 0; i--)
```

For:

```text
n = 7
```

```text
7/2 - 1
= 2
```

Indices:

```text
0 1 2 3 4 5 6
```

Leaf Nodes:

```text
3 4 5 6
```

Only indices:

```text
0 1 2
```

can have children.

Hence start from:

```text
2
```

and move upward.

---

# Heap Construction Dry Run

Initial:

```text
[9, 4, 3, 8, 10, 2, 5]
```

---

## heapify(2)

```text
      3
     / \
    2   5
```

Largest:

```text
5
```

Swap:

```text
[9, 4, 5, 8, 10, 2, 3]
```

---

## heapify(1)

```text
       4
      / \
     8  10
```

Largest:

```text
10
```

Swap:

```text
[9, 10, 5, 8, 4, 2, 3]
```

---

## heapify(0)

```text
         9
       /   \
      10    5
```

Largest:

```text
10
```

Swap:

```text
[10, 9, 5, 8, 4, 2, 3]
```

---

Max Heap Built:

```text
        10
       /  \
      9    5
     / \  / \
    8  4 2  3
```

---

# Sorting Phase

Current Heap:

```text
[10, 9, 5, 8, 4, 2, 3]
```

---

## Iteration 1

Swap root with last element:

```text
[3, 9, 5, 8, 4, 2, 10]
```

Sorted Part:

```text
[10]
```

Heapify first 6 elements:

```text
[9, 8, 5, 3, 4, 2, 10]
```

---

## Iteration 2

Swap:

```text
[2, 8, 5, 3, 4, 9, 10]
```

Heapify:

```text
[8, 4, 5, 3, 2, 9, 10]
```

Sorted:

```text
[9,10]
```

---

Continue similarly...

Final Array:

```text
[2, 3, 4, 5, 8, 9, 10]
```

---

# Why Does Heap Sort Work?

A Max Heap guarantees:

```text
Largest Element = Root
```

Every iteration:

```text
1. Root (largest) moves to end.
2. Heap size reduces.
3. Heap property restored.
```

Thus elements are placed in sorted order from right to left.

---

# Complexity Analysis

## Heap Construction

```text
O(n)
```

Not O(n log n).

Building a heap bottom-up takes linear time.

---

## Sorting Phase

We perform:

```text
n extractions
```

Each extraction requires:

```text
heapify -> O(log n)
```

Total:

```text
O(n log n)
```

---

## Overall Time Complexity

```text
O(n log n)
```

Best Case:

```text
O(n log n)
```

Average Case:

```text
O(n log n)
```

Worst Case:

```text
O(n log n)
```

---

## Space Complexity

```text
O(1)
```

Heap Sort is an **in-place sorting algorithm**.

---

# Heap Sort vs Merge Sort

| Feature | Heap Sort | Merge Sort |
|----------|------------|------------|
| Time Complexity | O(n log n) | O(n log n) |
| Extra Space | O(1) | O(n) |
| Stable | ❌ No | ✅ Yes |
| In Place | ✅ Yes | ❌ No |

---

# Key Takeaways

- Heap Sort uses a **Max Heap**.
- `heapify()` ensures a subtree satisfies heap property.
- Build Heap:
  ```text
  Start from n/2 - 1 to 0
  ```
- Sorting:
  ```text
  Swap root with last element
  Reduce heap size
  Heapify root
  ```
- Time Complexity:
  ```text
  O(n log n)
  ```
- Space Complexity:
  ```text
  O(1)
  ```
- Heap Sort is an **in-place**, comparison-based sorting algorithm.
