# Insertion Sort

## What is Insertion Sort?

**Insertion Sort** is a simple comparison-based sorting algorithm that builds the final sorted array **one element at a time**.

It works similarly to how we sort playing cards in our hands.

### Idea

```text
Take one element at a time
↓
Insert it into its correct position
within the already sorted portion
```

---

# Real Life Analogy

Imagine you have cards:

```text
12 11 13 5 6
```

Start with:

```text
12
```

It is already sorted.

Take:

```text
11
```

Insert before 12:

```text
11 12
```

Take:

```text
13
```

Insert after 12:

```text
11 12 13
```

Take:

```text
5
```

Insert at beginning:

```text
5 11 12 13
```

Continue until all cards are processed.

---

# Key Observation

After every iteration:

```text
Left Part  -> Sorted
Right Part -> Unsorted
```

Example:

```text
[11,12,13] | [5,6]
```

The left side is always maintained in sorted order.

---

# Given Code

```java
public class InsertionSort {

    void sort(int arr[]) {

        int n = arr.length;

        for (int i = 1; i < n; ++i) {

            int key = arr[i];
            int j = i - 1;

            while (j >= 0 && arr[j] > key) {
                arr[j + 1] = arr[j];
                j = j - 1;
            }

            arr[j + 1] = key;
        }
    }
}
```

---

# Understanding the Algorithm

## Outer Loop

```java
for (int i = 1; i < n; i++)
```

Why start from:

```java
i = 1
```

Because:

```text
First element alone is already sorted.
```

Example:

```text
[12]
```

No work required.

---

# Key Variable

```java
int key = arr[i];
```

This represents the element we're trying to insert into the sorted portion.

Example:

```text
[12, 11, 13, 5, 6]
      ↑

key = 11
```

---

# Previous Index

```java
int j = i - 1;
```

Points to the last element of the sorted portion.

Example:

```text
[12, 11]

j = 0

12
↑
```

---

# Shifting Elements

```java
while (j >= 0 && arr[j] > key)
```

If elements are larger than the key:

```text
Shift them one position right
```

instead of swapping repeatedly.

---

# Insertion Step

```java
arr[j + 1] = key;
```

Insert the key into its correct location.

---

# Complete Dry Run

## Input

```text
[12, 11, 13, 5, 6]
```

---

# Pass 1

## i = 1

```text
key = 11
```

Array:

```text
[12, 11, 13, 5, 6]
```

Compare:

```text
12 > 11
```

Shift:

```text
[12, 12, 13, 5, 6]
```

Insert:

```text
[11, 12, 13, 5, 6]
```

---

# Pass 2

## i = 2

```text
key = 13
```

Array:

```text
[11, 12, 13, 5, 6]
```

Compare:

```text
12 > 13 ?
```

No.

Insert directly.

Array remains:

```text
[11, 12, 13, 5, 6]
```

---

# Pass 3

## i = 3

```text
key = 5
```

Array:

```text
[11, 12, 13, 5, 6]
```

Shift:

```text
13 → right
```

```text
[11,12,13,13,6]
```

Shift:

```text
12 → right
```

```text
[11,12,12,13,6]
```

Shift:

```text
11 → right
```

```text
[11,11,12,13,6]
```

Insert:

```text
[5,11,12,13,6]
```

---

# Pass 4

## i = 4

```text
key = 6
```

Array:

```text
[5,11,12,13,6]
```

Shift:

```text
13
12
11
```

Insert:

```text
[5,6,11,12,13]
```

---

# Final Output

```text
[5, 6, 11, 12, 13]
```

---

# Visualization

### Initial

```text
12 11 13 5 6
```

---

### Pass 1

```text
11 12 | 13 5 6
```

---

### Pass 2

```text
11 12 13 | 5 6
```

---

### Pass 3

```text
5 11 12 13 | 6
```

---

### Pass 4

```text
5 6 11 12 13
```

---

# Why Does Insertion Sort Work?

At every iteration:

```text
Elements from 0 to i-1
are already sorted.
```

We insert:

```text
arr[i]
```

into the correct position.

Thus the sorted region grows steadily.

```text
1 element sorted
↓
2 elements sorted
↓
3 elements sorted
↓
...
↓
Entire array sorted
```

---

# Recurrence Thinking

Each element:

```text
Moves left until
correct position is found
```

Example:

```text
[5,8,12,20,6]

Insert 6

↓

[5,6,8,12,20]
```

---

# Number of Comparisons

Worst Case:

```text
n-1
n-2
n-3
...
1
```

Total:

```text
(n-1) + (n-2) + ... + 1

= n(n-1)/2
```

Hence:

```text
O(n²)
```

---

# Complexity Analysis

## Best Case

Already Sorted:

```text
[1,2,3,4,5]
```

No shifting required.

### Time Complexity

```text
O(n)
```

Only one comparison per iteration.

---

## Average Case

Random Array

### Time Complexity

```text
O(n²)
```

---

## Worst Case

Reverse Sorted:

```text
[5,4,3,2,1]
```

Every element shifts all the way left.

### Time Complexity

```text
O(n²)
```

---

# Space Complexity

```text
O(1)
```

Only a few variables:

```java
key
i
j
```

No extra array used.

---

# Stability

Insertion Sort is **Stable**.

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

# In-Place Sorting

Works within the original array.

Extra memory:

```text
O(1)
```

✅ In-place

---

# Adaptive Nature

Insertion Sort is **Adaptive**.

If array is mostly sorted:

```text
Very few shifts occur.
```

Example:

```text
[1,2,3,5,4]
```

Only one insertion needed.

Performance remains close to:

```text
O(n)
```

---

# Insertion Sort vs Bubble Sort

| Feature | Insertion Sort | Bubble Sort |
|----------|---------------|-------------|
| Best Case | O(n) | O(n) |
| Average Case | O(n
