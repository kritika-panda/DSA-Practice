# Quick Sort

## What is Quick Sort?

**Quick Sort** is a Divide and Conquer sorting algorithm.

It works by:

1. Selecting a **Pivot** element.
2. Partitioning the array around the pivot.
3. Recursively sorting the left part.
4. Recursively sorting the right part.

---

# Divide and Conquer Strategy

```text
Choose Pivot
      ↓
Partition Array
      ↓
Left Side < Pivot < Right Side
      ↓
Recursively Sort Left
      ↓
Recursively Sort Right
```

---

# Example

Input:

```text
[10, 7, 8, 9, 1, 5]
```

Choose Pivot:

```text
5 (last element)
```

Partition:

```text
[1] 5 [10,7,8,9]
```

Now:

```text
Left Side  -> [1]
Right Side -> [10,7,8,9]
```

Recursively sort both sides.

Final Result:

```text
[1,5,7,8,9,10]
```

---

# Why is it Called Quick Sort?

Because in practice it is one of the fastest comparison-based sorting algorithms.

Average Complexity:

```text
O(n log n)
```

and usually performs better than Merge Sort due to lower constant factors.

---

# Given Code

```java
class GfG {

    static int partition(int[] arr, int low, int high) {

        int pivot = arr[high];

        int i = low - 1;

        for (int j = low; j <= high - 1; j++) {

            if (arr[j] < pivot) {
                i++;
                swap(arr, i, j);
            }
        }

        swap(arr, i + 1, high);

        return i + 1;
    }

    static void swap(int[] arr, int i, int j) {

        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }

    static void quickSort(int[] arr, int low, int high) {

        if (low < high) {

            int pi = partition(arr, low, high);

            quickSort(arr, low, pi - 1);
            quickSort(arr, pi + 1, high);
        }
    }
}
```

---

# Understanding Partition()

## Purpose

Partition the array such that:

```text
All elements < Pivot
appear on left side

All elements > Pivot
appear on right side
```

---

## Step 1: Choose Pivot

```java
int pivot = arr[high];
```

Given:

```text
[10, 7, 8, 9, 1, 5]
```

Pivot:

```text
5
```

---

## Step 2: Initialize Boundary

```java
int i = low - 1;
```

This keeps track of:

```text
Last position of smaller elements
```

Initially:

```text
i = -1
```

---

## Step 3: Traverse Array

```java
for (j = low; j <= high - 1; j++)
```

Traverse all elements excluding pivot.

---

## Step 4: Compare with Pivot

```java
if(arr[j] < pivot)
```

If current element is smaller than pivot:

```text
Move it to left section
```

---

## Step 5: Expand Smaller Section

```java
i++;
```

This creates space for a smaller element.

---

## Step 6: Swap

```java
swap(arr, i, j);
```

Place smaller element into left partition.

---

# Complete Partition Dry Run

Input:

```text
[10, 7, 8, 9, 1, 5]
```

Pivot:

```text
5
```

Initially:

```text
i = -1
```

---

## j = 0

```text
10 < 5 ?
```

No.

Array:

```text
[10,7,8,9,1,5]
```

---

## j = 1

```text
7 < 5 ?
```

No.

---

## j = 2

```text
8 < 5 ?
```

No.

---

## j = 3

```text
9 < 5 ?
```

No.

---

## j = 4

```text
1 < 5 ?
```

Yes.

```text
i = 0
```

Swap:

```text
10 ↔ 1
```

Array:

```text
[1,7,8,9,10,5]
```

---

## Place Pivot

After loop:

```java
swap(arr, i + 1, high)
```

Swap:

```text
7 ↔ 5
```

Array:

```text
[1,5,8,9,10,7]
```

Pivot Position:

```text
1
```

---

# Result after Partition

```text
[1] 5 [8,9,10,7]
```

Everything left of pivot:

```text
< 5
```

Everything right of pivot:

```text
> 5
```

---

# Understanding QuickSort()

## Function Signature

```java
quickSort(arr, low, high)
```

Parameters:

```text
arr  -> array
low  -> start index
high -> end index
```

---

## Base Condition

```java
if(low < high)
```

When:

```text
low >= high
```

Subarray has:

```text
0 or 1 element
```

Already sorted.

Stop recursion.

---

## Find Pivot Position

```java
int pi = partition(arr, low, high);
```

Example:

```text
[1,5,8,9,10,7]

pi = 1
```

---

## Recur Left Side

```java
quickSort(arr, low, pi - 1);
```

Sort:

```text
Elements less than pivot
```

---

## Recur Right Side

```java
quickSort(arr, pi + 1, high);
```

Sort:

```text
Elements greater than pivot
```

---

# Complete Recursive Dry Run

Input:

```text
[10,7,8,9,1,5]
```

---

## First Partition

```text
Pivot = 5

[1] 5 [8,9,10,7]
```

---

## Left Side

```text
[1]
```

Already sorted.

---

## Right Side

```text
[8,9,10,7]
```

Pivot:

```text
7
```

After partition:

```text
[7] [9,10,8]
```

Array becomes:

```text
[1,5,7,9,10,8]
```

---

## Continue Right Side

Pivot:

```text
8
```

After partition:

```text
[8] [10,9]
```

Array:

```text
[1,5,7,8,10,9]
```

---

## Final Partition

Pivot:

```text
9
```

Result:

```text
[9] [10]
```

Final:

```text
[1,5,7,8,9,10]
```

---

# Recursion Tree

```text
                    [10,7,8,9,1,5]
                           |
                         Pivot=5
                           |
              ---------------------------
              |                         |
            [1]                   [8,9,10,7]
                                      |
                                    Pivot=7
                                      |
                           -----------------------
                           |                     |
                         []                [9,10,8]
                                               |
                                             Pivot=8
                                               |
                                         -------------
                                         |           |
                                        []       [10,9]
                                                  |
                                                Pivot=9
                                                  |
                                              [10]
```

---

# Why Quick Sort Works?

Partition guarantees:

```text
Pivot reaches its correct position.
```

Example:

```text
[1,5,8,9,10,7]
```

The pivot:

```text
5
```

will never move again.

Then we recursively sort:

```text
Left Side
Right Side
```

until every pivot reaches its final position.

---

# Lomuto Partition Scheme

Your code uses:

```text
Lomuto Partition
```

Characteristics:

```text
Pivot = Last Element
```

```text
i = Boundary of smaller elements
```

```text
Single traversal
```

Very common in interviews.

---

# Complexity Analysis

## Best Case

Perfect partition every time.

```text
n/2 and n/2
```

Recursion Tree:

```text
log n levels
```

Time Complexity:

```text
O(n log n)
```

---

## Average Case

Random pivot distribution.

Time Complexity:

```text
O(n log n)
```

---

## Worst Case

Already sorted array:

```text
[1,2,3,4,5]
```

Pivot:

```text
Always largest element
```

Partition:

```text
0 and n-1
```

Recursion:

```text
n levels
```

Time Complexity:

```text
O(n²)
```

---

# Complexity Derivation

At each level:

```text
Partition Cost = O(n)
```

Balanced Tree:

```text
Height = log n
```

Thus:

```text
O(n) × O(log n)

= O(n log n)
```

---

# Space Complexity

Recursive stack only.

### Best/Average Case

```text
O(log n)
```

### Worst Case

```text
O(n)
```

---

# Stability

Quick Sort is **Not Stable**.

Example:

```text
(5,A) (5,B)
```

After partitioning:

```text
(5,B) (5,A)
```

Relative order may change.

❌ Not Stable

---

# In-Place Sorting

Quick Sort sorts within the same array.

Extra arrays:

```text
None
```

✅ In-place

---

# Quick Sort vs Merge Sort

| Feature | Quick Sort | Merge Sort |
|----------|------------|------------|
| Best | O(n log n) | O(n log n) |
| Average | O(n log n) | O(n log n) |
| Worst | O(n²) | O(n log n) |
| Space | O(log n) | O(n) |
| Stable | ❌ No | ✅ Yes |
| In-place | ✅ Yes | ❌ No |

---

# Quick Sort vs Heap Sort

| Feature | Quick Sort | Heap Sort |
|----------|------------|------------|
| Average Performance | Better | Good |
| Worst Case | O(n²) | O(n log n) |
| Cache Friendly | ✅ Yes | ❌ No |
| Space | O(log n) | O(1) |

---

# Advantages

✅ Extremely fast in practice

✅ In-place sorting

✅ Cache friendly

✅ Low memory usage

✅ Widely used in real systems

---

# Disadvantages

❌ Worst-case O(n²)

❌ Recursive algorithm

❌ Not stable

❌ Performance depends on pivot choice

---

# Common Pivot Selection Techniques

## 1. First Element

```text
pivot = arr[low]
```

---

## 2. Last Element

```text
pivot = arr[high]
```

(Used in your code)

---

## 3. Random Pivot

```text
Random index
```

Helps avoid worst cases.

---

## 4. Median of Three

Choose median of:

```text
First
Middle
Last
```

Popular optimization.

---

# Applications

- Internal Sorting
- Standard Library Implementations
- Large In-Memory Datasets
- Competitive Programming
- Database Query Optimization

---

# Key Takeaways

- Quick Sort follows **Divide and Conquer**.
- Uses a **Pivot** to partition the array.
- Your implementation uses:
  ```text
  Lomuto Partition Scheme
  ```
- Partition places pivot in its correct position.
- Time Complexity:
  ```text
  Best/Average = O(n log n)
  Worst = O(n²)
  ```
- Space Complexity:
  ```text
  O(log n) average
  ```
- Quick Sort is:
  - ✅ In-place
  - ✅ Very Fast
  - ❌ Not Stable
- It is one of the most important sorting algorithms asked in interviews.
