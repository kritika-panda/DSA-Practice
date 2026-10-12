# Merge Sort

## What is Merge Sort?

**Merge Sort** is a Divide and Conquer algorithm.

It works in three steps:

1. Divide the array into two halves.
2. Recursively sort both halves.
3. Merge the two sorted halves.

---

# Divide and Conquer Strategy

```text
Divide
  ↓
Conquer
  ↓
Combine (Merge)
```

Unlike Bubble Sort and Selection Sort, Merge Sort doesn't sort in-place by repeatedly swapping elements.

Instead, it:

- Breaks the problem into smaller subproblems.
- Solves them recursively.
- Combines the solutions.

---

# Example

### Input

```text
[38, 27, 43, 10]
```

### Divide

```text
[38, 27, 43, 10]

           |
           v

      [38,27] [43,10]

           |
           v

      [38] [27] [43] [10]
```

Now every subarray contains only one element.

A single element array is already sorted.

---

### Merge

```text
[38] + [27]
       ↓
[27, 38]
```

```text
[43] + [10]
       ↓
[10, 43]
```

```text
[27,38] + [10,43]
            ↓
[10,27,38,43]
```

Final Sorted Array:

```text
[10,27,38,43]
```

---

# Complete Code

```java
class GfG {

    static void merge(int arr[], int l, int m, int r) {

        int n1 = m - l + 1;
        int n2 = r - m;

        int L[] = new int[n1];
        int R[] = new int[n2];

        for (int i = 0; i < n1; i++)
            L[i] = arr[l + i];

        for (int j = 0; j < n2; j++)
            R[j] = arr[m + 1 + j];

        int i = 0;
        int j = 0;
        int k = l;

        while (i < n1 && j < n2) {

            if (L[i] <= R[j]) {
                arr[k] = L[i];
                i++;
            } else {
                arr[k] = R[j];
                j++;
            }

            k++;
        }

        while (i < n1) {
            arr[k] = L[i];
            i++;
            k++;
        }

        while (j < n2) {
            arr[k] = R[j];
            j++;
            k++;
        }
    }

    static void mergeSort(int arr[], int l, int r) {

        if (l < r) {

            int m = l + (r - l) / 2;

            mergeSort(arr, l, m);
            mergeSort(arr, m + 1, r);

            merge(arr, l, m, r);
        }
    }
}
```

---

# Understanding mergeSort()

## Function Signature

```java
mergeSort(arr, l, r)
```

Parameters:

```text
arr -> original array
l   -> left index
r   -> right index
```

---

## Base Condition

```java
if (l < r)
```

When:

```text
l == r
```

Example:

```text
[38]
```

Only one element exists.

Such an array is already sorted.

Recursion stops.

---

## Finding Mid

```java
int m = l + (r - l) / 2;
```

Why not?

```java
(l + r) / 2
```

Because:

```text
l + r may overflow for very large values
```

Safer formula:

```java
l + (r - l) / 2
```

---

## Recursive Division

```java
mergeSort(arr, l, m);
```

Sort left half.

---

```java
mergeSort(arr, m + 1, r);
```

Sort right half.

---

## Merge Step

```java
merge(arr, l, m, r);
```

Combine the two sorted halves.

---

# Complete Recursive Breakdown

Input:

```text
[38, 27, 43, 10]
```

---

## First Call

```text
mergeSort(0,3)

Mid = 1
```

Split:

```text
[38,27] | [43,10]
```

---

## Left Subarray

```text
mergeSort(0,1)

Mid = 0
```

Split:

```text
[38] | [27]
```

Both single elements.

Merge:

```text
[38] + [27]

↓

[27,38]
```

---

## Right Subarray

```text
mergeSort(2,3)

Mid = 2
```

Split:

```text
[43] | [10]
```

Merge:

```text
[43] + [10]

↓

[10,43]
```

---

## Final Merge

```text
[27,38]
[10,43]
```

Merge:

```text
10
27
38
43
```

Result:

```text
[10,27,38,43]
```

---

# Understanding merge()

## Purpose

Merge two already sorted subarrays into one sorted array.

---

# Step 1: Compute Sizes

```java
int n1 = m - l + 1;
int n2 = r - m;
```

Example:

```text
[27,38] [10,43]

l = 0
m = 1
r = 3
```

Left size:

```text
1 - 0 + 1 = 2
```

Right size:

```text
3 - 1 = 2
```

---

# Step 2: Create Temporary Arrays

```java
int[] L = new int[n1];
int[] R = new int[n2];
```

Result:

```text
L = [27,38]
R = [10,43]
```

---

# Step 3: Copy Data

```java
for(...)
    L[i] = arr[l+i];

for(...)
    R[j] = arr[m+1+j];
```

Copies elements into temporary arrays.

---

# Step 4: Merge Arrays

Pointers:

```java
i -> L
j -> R
k -> Original Array
```

Initially:

```text
L = [27,38]
R = [10,43]

i=0
j=0
k=0
```

---

## Comparison 1

```text
27 vs 10
```

Smaller:

```text
10
```

Put into array.

```text
arr = [10, ?, ?, ?]
```

Move:

```text
j++
k++
```

---

## Comparison 2

```text
27 vs 43
```

Smaller:

```text
27
```

```text
arr = [10,27,?,?]
```

Move:

```text
i++
k++
```

---

## Comparison 3

```text
38 vs 43
```

Smaller:

```text
38
```

```text
arr = [10,27,38,?]
```

---

## Copy Remaining Elements

```text
43
```

Final:

```text
[10,27,38,43]
```

---

# Visualization

```text
                [38,27,43,10]

                     |
          -------------------------
          |                       |
      [38,27]                 [43,10]

          |                       |
      --------                 --------
      |      |                 |      |
    [38]   [27]             [43]   [10]

      |      |                 |      |
      --------                 --------
          |                       |
      [27,38]                 [10,43]

          \                       /
           \                     /
            \                   /
             [10,27,38,43]
```

---

# Dry Run

Input:

```text
[38,27,43,10]
```

---

After Division:

```text
[38] [27] [43] [10]
```

---

After First Merge:

```text
[27,38]
[10,43]
```

---

Final Merge:

```text
[10,27,38,43]
```

---

# Why Merge Sort Works?

At every level:

```text
Problem Size
      ↓
Split into Smaller Problems
      ↓
Each Piece Gets Sorted
      ↓
Merge Sorted Pieces
```

Since merged arrays are always sorted:

```text
Final Result is Sorted
```

---

# Recursion Tree

For:

```text
n = 8
```

```text
Level 0 -> 8

Level 1 -> 4 + 4

Level 2 -> 2 + 2 + 2 + 2

Level 3 -> 1 + 1 + 1 + 1 + 1 + 1 + 1 + 1
```

Height:

```text
log₂(n)
```

---

# Time Complexity Derivation

At every level:

```text
Total Work = O(n)
```

Number of levels:

```text
log₂(n)
```

Therefore:

```text
O(n) × O(log n)

= O(n log n)
```

---

# Complexity Analysis

## Best Case

```text
O(n log n)
```

---

## Average Case

```text
O(n log n)
```

---

## Worst Case

```text
O(n log n)
```

Unlike Quick Sort, Merge Sort always maintains the same complexity.

---

## Space Complexity

Temporary arrays:

```text
L[]
R[]
```

Extra memory required:

```text
O(n)
```

---

# Stability

Merge Sort is a Stable Sorting Algorithm.

Example:

```text
(5,A)
(5,B)
```

After sorting:

```text
(5,A)
(5,B)
```

Relative order remains unchanged.

✅ Stable

---

# Merge Sort vs Quick Sort

| Feature | Merge Sort | Quick Sort |
|----------|-----------|-----------|
| Best Case | O(n log n) | O(n log n) |
| Average Case | O(n log n) | O(n log n) |
| Worst Case | O(n log n) | O(n²) |
| Stable | ✅ Yes | ❌ No |
| Extra Space | O(n) | O(log n) |
| Linked List Performance | Excellent | Poor |

---

# Merge Sort vs Heap Sort

| Feature | Merge Sort | Heap Sort |
|----------|-----------|-----------|
| Stable | ✅ Yes | ❌ No |
| Extra Space | O(n) | O(1) |
| Worst Case | O(n log n) | O(n log n) |
| In-place | ❌ No | ✅ Yes |

---

# Advantages

✅ Guaranteed O(n log n)

✅ Stable sorting

✅ Works well for Linked Lists

✅ Good for large datasets

✅ Predictable performance

---

# Disadvantages

❌ Requires extra memory

❌ Recursive overhead

❌ Not in-place

---

# Applications

### 1. External Sorting

Used when data is too large for memory.

---

### 2. Linked List Sorting

Merge Sort works extremely well on Linked Lists.

---

### 3. Divide and Conquer Problems

Foundation for many advanced algorithms.

---

### 4. Parallel Processing

Left and right halves can be sorted independently.

---

# Key Takeaways

- Merge Sort follows **Divide and Conquer**.
- Divide array into two halves recursively.
- Merge sorted halves using the `merge()` function.
- Time Complexity:
  ```text
  O(n log n)
  ```
- Space Complexity:
  ```text
  O(n)
  ```
- Merge Sort is:
  - ✅ Stable
  - ✅ Predictable
  - ❌ Not In-place
- It is one of the most important sorting algorithms for interviews and real-world systems.
