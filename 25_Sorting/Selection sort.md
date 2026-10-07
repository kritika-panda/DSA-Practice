# Selection Sort

## What is Selection Sort?

**Selection Sort** is a simple comparison-based sorting algorithm.

It repeatedly finds the smallest element from the unsorted portion of the array and places it at the beginning of that portion.

After every pass, one element reaches its correct position.

---

# Intuition

Consider:

[64, 25, 12, 22, 11]


Find the smallest element:

11


Swap it with the first element:

[11, 25, 12, 22, 64]


Notice:

11 has reached its final position.


Now ignore the sorted portion:

[11] | [25, 12, 22, 64]


Find the smallest element from the remaining portion:

12


Swap:

[11, 12, 25, 22, 64]


Continue until the entire array is sorted.

---

# Why is it Called Selection Sort?

Because during every pass, we:

Select the smallest element


from the unsorted portion and move it to its correct position.

The process looks like:

Select Minimum ↓ Swap with first unsorted element ↓ Sorted portion grows ↓ Repeat


---

# Given Code

class GFG {

static void selectionSort(int[] arr) {

int n = arr.length;

for (int i = 0; i < n - 1; i++) {

int minIndex = i;

for (int j = i + 1; j < n; j++) {

if (arr[j] < arr[minIndex]) { minIndex = j; } }

int temp = arr[minIndex]; arr[minIndex] = arr[i]; arr[i] = temp; } } }


---

# Understanding the Algorithm

## Outer Loop

for (int i = 0; i < n - 1; i++)


Purpose:

Number of passes.


Why `n - 1`?

Because:

After first pass, smallest element is fixed. After second pass, second smallest is fixed. ...


For:

n elements


Maximum passes needed:

n - 1


---

## Minimum Index

int minIndex = i;


Initially, we assume:

Current first unsorted element is the minimum.


Example:

[64, 25, 12, 22, 11] ↑ i


Initially:

minIndex = 0


Then we search the remaining elements.

---

## Inner Loop

for (int j = i + 1; j < n; j++)


Purpose:

Search for the smallest element.


Why:

i + 1


Because:

arr[i]


is already considered as the current minimum.

Example:

64 25 12 22 11 ↑ ↑ i j


---

# Comparison Logic

if (arr[j] < arr[minIndex])


Example:

64 25


Since:

25 < 64


Update:

minIndex = j;


Now:

25


is considered the smallest element found so far.

---

# Finding the Minimum

Consider:

[64, 25, 12, 22, 11]


Start:

minIndex = 0


Compare:

25 < 64 → Yes


Update:

minIndex = 1


Compare:

12 < 25 → Yes


Update:

minIndex = 2


Compare:

22 < 12 → No


Compare:

11 < 12 → Yes


Update:

minIndex = 4


Minimum element:

11


---

# Swap Logic

After finding the minimum element:

int temp = arr[minIndex];

arr[minIndex] = arr[i];

arr[i] = temp;


Before:

[64, 25, 12, 22, 11] ↑ ↑ i minIndex


After:

[11, 25, 12, 22, 64]


Now:

11


is in its final position.

---

# Complete Dry Run

## Input

[64, 25, 12, 22, 11]


---

# Pass 1

Start:

[64, 25, 12, 22, 11] ↑ i


Assume:

minIndex = 0


Compare:

25 < 64 → Yes


minIndex = 1


Compare:

12 < 25 → Yes


minIndex = 2


Compare:

22 < 12 → No


Compare:

11 < 12 → Yes


minIndex = 4


Swap:

64 ↔ 11


Array becomes:

[11, 25, 12, 22, 64]


Smallest element fixed:

11


---

# Pass 2

Starting:

[11, 25, 12, 22, 64]


Sorted portion:

[11] | [25, 12, 22, 64]


Find minimum:

12


Swap:

25 ↔ 12


Array becomes:

[11, 12, 25, 22, 64]


Fixed:

11, 12


---

# Pass 3

Starting:

[11, 12, 25, 22, 64]


Sorted portion:

[11, 12] | [25, 22, 64]


Find minimum:

22


Swap:

25 ↔ 22


Array becomes:

[11, 12, 22, 25, 64]


Fixed:

11, 12, 22


---

# Pass 4

Starting:

[11, 12, 22, 25, 64]


Remaining portion:

[25, 64]


Minimum:

25


It is already in the correct position.

Array remains:

[11, 12, 22, 25, 64]


Sorted.

---

# Visualization

Initial:

64 25 12 22 11


Pass 1:

11 25 12 22 64


Pass 2:

11 12 25 22 64


Pass 3:

11 12 22 25 64


Pass 4:

11 12 22 25 64


---

# Sorted Portion Visualization

Initial:

[64 25 12 22 11]


After Pass 1:

[11] | [25 12 22 64]


After Pass 2:

[11 12] | [25 22 64]


After Pass 3:

[11 12 22] | [25 64]


After Pass 4:

[11 12 22 25] | [64]


Final:

[11 12 22 25 64]


---

# Why Does Selection Sort Work?

After every pass:

Smallest remaining element moves to its correct position.


Therefore:

Pass 1 → Smallest element fixed Pass 2 → Second smallest fixed Pass 3 → Third smallest fixed ...


Eventually all elements become sorted.

---

# Important Observation

Unlike Bubble Sort:

Selection Sort does not repeatedly swap adjacent elements.


Instead:

Find minimum ↓ Remember its index ↓ Perform one swap


Therefore, Selection Sort performs at most:

n - 1 swaps


---

# Best Case

Already sorted:

[1, 2, 3, 4, 5]


Selection Sort still searches for the minimum in every unsorted portion.

### Time Complexity

O(n²)


Unlike optimized Bubble Sort, Selection Sort does not become `O(n)` for an already sorted array.

---

# Average Case

Random array:

[4, 2, 5, 1, 3]


### Time Complexity

O(n²)


---

# Worst Case

Reverse sorted:

[5, 4, 3, 2, 1]


### Time Complexity

O(n²)


---

# Complexity Analysis

## Time Complexity

Case	Complexity
Best	O(n²)
Average	O(n²)
Worst	O(n²)
Space Complexity
O(1)
Uses only a few extra variables:

i
j
minIndex
temp
Number of Comparisons
For n elements:

(n - 1) + (n - 2) + (n - 3) + ... + 1
Sum:

n(n - 1) / 2
Therefore:

O(n²)
The number of comparisons is essentially fixed regardless of whether the array is sorted or unsorted.

Number of Swaps
Selection Sort performs at most:

n - 1 swaps
For example:

[64, 25, 12, 22, 11]
There are at most:

4 swaps
for:

5 elements
This is one of the main advantages of Selection Sort.

Stability
Standard Selection Sort is Not Stable.

Consider:

(5,A) (3,B) (5,C)
The minimum element is:

(3,B)
Swap it with the first element:

(3,B) (5,C) (5,A)
Notice:

Before:
(5,A) → (5,C)

After:
(5,C) → (5,A)
The relative order of equal elements changed.

❌ Not Stable


In-Place Sorting
Selection Sort sorts the array within the same memory.

It does not require another array.

Extra space:

O(1)
✅ In-place

Adaptive Nature
Selection Sort is not adaptive.

Even if the array is already sorted:

[1, 2, 3, 4, 5]
it still performs all the comparisons.

Therefore:

Best Case = O(n²)
Selection Sort vs Bubble Sort
Feature	Selection Sort	Bubble Sort
Stable	❌ No	✅ Yes
Adaptive	❌ No	✅ Yes
Best Case	O(n²)	O(n)
Average Case	O(n²)	O(n²)
Worst Case	O(n²)	O(n²)
Swaps	Fewer	More
In-place	✅ Yes	✅ Yes
Selection Sort vs Insertion Sort
Feature	Selection Sort	Insertion Sort
Stable	❌ No	✅ Yes
Adaptive	❌ No	✅ Yes
Best Case	O(n²)	O(n)
Average Case	O(n²)	O(n²)
Worst Case	O(n²)	O(n²)
Space	O(1)	O(1)
In-place	✅ Yes	✅ Yes
Advantages
✅ Very simple to understand
✅ Easy to implement
✅ In-place sorting
✅ Requires only O(1) extra space
✅ Performs at most n - 1 swaps
✅ Useful when swaps are expensive
Disadvantages
❌ O(n²) time complexity in all cases
❌ Not stable
❌ Not adaptive
❌ Performs many comparisons
❌ Not suitable for large datasets
When to Use Selection Sort?
Suitable for:

Learning sorting fundamentals
Small datasets
Situations where memory usage must be minimal
Situations where the number of swaps should be minimized
Not suitable for:

Large datasets
Nearly sorted arrays
Applications requiring stable sorting
Performance-critical production systems
Key Takeaways
Selection Sort repeatedly searches for the minimum element.
The minimum element is placed at the beginning of the unsorted portion.
After every pass, one element reaches its final position.
Best Case Time Complexity = O(n²).
Average Case Time Complexity = O(n²).
Worst Case Time Complexity = O(n²).
Space Complexity = O(1).
Selection Sort performs at most n - 1 swaps.
Selection Sort is:
❌ Not Stable
✅ In-place
❌ Not Adaptive
❌ Inefficient for large inputs
