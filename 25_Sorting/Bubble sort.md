# Bubble Sort

## What is Bubble Sort?

**Bubble Sort** is the simplest comparison-based sorting algorithm.

It repeatedly compares adjacent elements and swaps them if they are in the wrong order.

After every pass, the largest unsorted element "bubbles up" to its correct position at the end of the array.

---

# Intuition

Consider:

```text
[5, 1, 4, 2, 8]
```

Compare adjacent elements:

```text
5 > 1 → Swap
```

```text
[1, 5, 4, 2, 8]
```

```text
5 > 4 → Swap
```

```text
[1, 4, 5, 2, 8]
```

```text
5 > 2 → Swap
```

```text
[1, 4, 2, 5, 8]
```

```text
5 < 8 → No Swap
```

```text
[1, 4, 2, 5, 8]
```

Notice:

```text
8 has reached its final position.
```

Largest element bubbled to the end.

---

# Why is it Called Bubble Sort?

Because larger elements gradually move toward the end just like air bubbles rise to the surface.

```text
Small Elements ↓
Large Elements ↑
```

---

# Given Code

```java
class GFG {

    static void bubbleSort(int[] arr) {

        int n = arr.length;
        int i, j, temp;
        boolean swapped;

        for (i = 0; i < n - 1; i++) {

            swapped = false;

            for (j = 0; j < n - i - 1; j++) {

                if (arr[j] > arr[j + 1]) {

                    temp = arr[j];
                    arr[j] = arr[j + 1];
                    arr[j + 1] = temp;

                    swapped = true;
                }
            }

            if (swapped == false)
                break;
        }
    }
}
```

---

# Understanding the Algorithm

## Outer Loop

```java
for (i = 0; i < n - 1; i++)
```

Purpose:

```text
Number of passes.
```

Why `n - 1`?

Because:

```text
After first pass, largest element is fixed.
After second pass, second largest is fixed.
...
```

For:

```text
n elements
```

Maximum passes needed:

```text
n - 1
```

---

## Optimization Flag

```java
boolean swapped;
```

Used to detect:

```text
Did we make any swap?
```

If no swaps occur:

```text
Array is already sorted.
```

Then we stop early.

---

## Inner Loop

```java
for (j = 0; j < n - i - 1; j++)
```

Purpose:

```text
Compare adjacent elements.
```

Why:

```java
n - i - 1
```

Because after each pass:

```text
One element reaches its final position.
```

No need to revisit it.

---

# Comparison Logic

```java
if (arr[j] > arr[j + 1])
```

Example:

```text
5 2
```

Since:

```text
5 > 2
```

Swap them.

---

# Swap Logic

```java
temp = arr[j];
arr[j] = arr[j + 1];
arr[j + 1] = temp;
```

Before:

```text
5 2
```

After:

```text
2 5
```

---

# Why Set Swapped = true?

```java
swapped = true;
```

Indicates that:

```text
Array was not sorted.
```

At least one swap occurred.

---

# Early Termination

```java
if (swapped == false)
    break;
```

Meaning:

```text
No swaps occurred in entire pass.
```

Therefore:

```text
Array is already sorted.
```

Stop immediately.

---

# Complete Dry Run

## Input

```text
[64, 34, 25, 12, 22, 11, 90]
```

---

# Pass 1

Compare:

```text
64 > 34 ✔ Swap
```

```text
[34, 64, 25, 12, 22, 11, 90]
```

```text
64 > 25 ✔ Swap
```

```text
[34, 25, 64, 12, 22, 11, 90]
```

```text
64 > 12 ✔ Swap
```

```text
[34, 25, 12, 64, 22, 11, 90]
```

```text
64 > 22 ✔ Swap
```

```text
[34, 25, 12, 22, 64, 11, 90]
```

```text
64 > 11 ✔ Swap
```

```text
[34, 25, 12, 22, 11, 64, 90]
```

```text
64 < 90
```

End of pass:

```text
[34, 25, 12, 22, 11, 64, 90]
```

Largest element fixed:

```text
90
```

---

# Pass 2

Starting:

```text
[34, 25, 12, 22, 11, 64, 90]
```

After pass:

```text
[25, 12, 22, 11, 34, 64, 90]
```

Fixed:

```text
64, 90
```

---

# Pass 3

After pass:

```text
[12, 22, 11, 25, 34, 64, 90]
```

---

# Pass 4

After pass:

```text
[12, 11, 22, 25, 34, 64, 90]
```

---

# Pass 5

After pass:

```text
[11, 12, 22, 25, 34, 64, 90]
```

Sorted.

---

# Visualization

Initial:

```text
64 34 25 12 22 11 90
```

Pass 1:

```text
34 25 12 22 11 64 90
```

Pass 2:

```text
25 12 22 11 34 64 90
```

Pass 3:

```text
12 22 11 25 34 64 90
```

Pass 4:

```text
12 11 22 25 34 64 90
```

Pass 5:

```text
11 12 22 25 34 64 90
```

---

# Why Does Bubble Sort Work?

After every pass:

```text
Largest remaining element
moves to its correct position.
```

Therefore:

```text
Pass 1 → Largest fixed
Pass 2 → Second Largest fixed
Pass 3 → Third Largest fixed
...
```

Eventually all elements become sorted.

---

# Optimized Bubble Sort

Without optimization:

```text
Always perform n-1 passes.
```

Even if array is already sorted.

Example:

```text
[1,2,3,4,5]
```

Still executes all passes.

---

With optimization:

```java
if(swapped == false)
    break;
```

First pass:

```text
No swaps
```

Stop immediately.

---

# Best Case

Already sorted:

```text
[1,2,3,4,5]
```

Only one pass required.

### Time Complexity

```text
O(n)
```

---

# Average Case

Random array.

### Time Complexity

```text
O(n²)
```

---

# Worst Case

Reverse sorted:

```text
[5,4,3,2,1]
```

Every comparison causes a swap.

### Time Complexity

```text
O(n²)
```

---

# Complexity Analysis

## Time Complexity

| Case | Complexity |
|--------|---------|
| Best | O(n) |
| Average | O(n²) |
| Worst | O(n²) |

---

## Space Complexity

```text
O(1)
```

Uses only a few extra variables.

---

# Stability

Bubble Sort is a **Stable Sorting Algorithm**.

Example:

```text
(2,A) (2,B)
```

After sorting:

```text
(2,A) (2,B)
```

Relative order remains same.

✅ Stable

---

# In-Place Sorting

Bubble Sort sorts the array within the same memory.

Extra space:

```text
O(1)
```

✅ In-place

---

# Number of Comparisons

For n elements:

```text
(n-1) + (n-2) + (n-3) + ... + 1
```

Sum:

```text
n(n-1)/2
```

Thus:

```text
O(n²)
```

---

# Bubble Sort vs Selection Sort

| Feature | Bubble Sort | Selection Sort |
|----------|------------|--------------|
| Stable | ✅ Yes | ❌ No |
| Adaptive | ✅ Yes | ❌ No |
| Best Case | O(n) | O(n²) |
| Worst Case | O(n²) | O(n²) |
| Swaps | More | Fewer |

---

# Bubble Sort vs Insertion Sort

| Feature | Bubble Sort | Insertion Sort |
|----------|------------|---------------|
| Best Case | O(n) | O(n) |
| Worst Case | O(n²) | O(n²) |
| Practical Performance | Poor | Better |
| Adaptive | Yes | Yes |
| Stable | Yes | Yes |

---

# Advantages

✅ Very simple to understand

✅ Easy to implement

✅ Stable sorting algorithm

✅ Detects already sorted arrays

✅ In-place sorting

---

# Disadvantages

❌ Very slow for large datasets

❌ O(n²) complexity

❌ Large number of swaps

❌ Rarely used in production systems

---

# When to Use Bubble Sort?

Suitable for:

- Learning sorting fundamentals
- Small datasets
- Nearly sorted arrays
- Interview introductions to sorting

Not suitable for:

- Large datasets
- Production-grade applications

---

# Key Takeaways

- Bubble Sort repeatedly swaps adjacent elements.
- Largest element reaches the end after every pass.
- Optimized Bubble Sort uses a `swapped` flag.
- Best Case Time Complexity = **O(n)**.
- Average/Worst Case Time Complexity = **O(n²)**.
- Space Complexity = **O(1)**.
- Bubble Sort is:
  - ✅ Stable
  - ✅ In-place
  - ✅ Adaptive (with optimization)
  - ❌ Inefficient for large inputs
