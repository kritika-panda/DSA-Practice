What is Selection Sort?
Selection Sort is a simple comparison-based sorting algorithm that repeatedly finds the smallest element from the unsorted portion and places it at the beginning.

Idea
Find the smallest element
↓
Place it at the beginning of the unsorted portion
↓
Repeat until the entire array is sorted
Real Life Analogy
Imagine you have numbers:

12 11 13 5 6
Find the smallest number:

5
Place it at the beginning:

5 11 13 12 6
Now ignore the sorted part:

5 | 11 13 12 6
Find the smallest element from the remaining portion:

6
Place it next:

5 6 13 12 11
Continue until everything is sorted.

Key Observation
After every iteration:

Left Part  -> Sorted
Right Part -> Unsorted
Example:

[5, 6, 11] | [13, 12]
The left side contains the elements that have already been placed in their final positions.

Given Code
public class SelectionSort {

    void sort(int arr[]) {

        int n = arr.length;

        for (int i = 0; i < n - 1; i++) {

            int minIndex = i;

            for (int j = i + 1; j < n; j++) {

                if (arr[j] < arr[minIndex]) {
                    minIndex = j;
                }
            }

            int temp = arr[minIndex];
            arr[minIndex] = arr[i];
            arr[i] = temp;
        }
    }
}
Understanding the Algorithm
Outer Loop
for (int i = 0; i < n - 1; i++)
The variable i represents the beginning of the unsorted portion.

Example:

[5, 11, 13, 12, 6]
 ↑
 i = 0
After one pass:

[5 | 11, 13, 12, 6]
      ↑
      i = 1
The sorted portion grows from left to right.

Minimum Index
int minIndex = i;
Initially, we assume that the first element of the unsorted portion is the smallest.

Example:

[12, 11, 13, 5, 6]
 ↑
minIndex = 0
Then we search the remaining elements.

Finding the Minimum
for (int j = i + 1; j < n; j++)
Start searching from:

i + 1
because arr[i] is already being considered as the minimum.

Example:

12 11 13 5 6
↑
i

   ↑
   j
Comparing Elements
if (arr[j] < arr[minIndex]) {
    minIndex = j;
}
If we find a smaller element, update minIndex.

Example:

12 11 13 5 6
↑        ↑
min      j
Since:

5 < 12
we update:

minIndex = 3
Swapping
After finding the smallest element:

int temp = arr[minIndex];
arr[minIndex] = arr[i];
arr[i] = temp;
We swap the smallest element with the first element of the unsorted portion.

Example:

12 11 13 5 6
↑        ↑
i     minIndex
After swapping:

5 11 13 12 6
Now 5 is in its final position.

Complete Dry Run
Input
[12, 11, 13, 5, 6]
Pass 1
i = 0
Initial:

[12, 11, 13, 5, 6]
 ↑
 i
Assume:

minIndex = 0
Search for the minimum:

12
11  → smaller
13
5   → smaller
6
Therefore:

minIndex = 3
Swap:

12 ↔ 5
Array becomes:

[5, 11, 13, 12, 6]
Sorted portion:

[5] | [11, 13, 12, 6]
Pass 2
i = 1
Array:

[5, 11, 13, 12, 6]
    ↑
    i
Assume:

minIndex = 1
Search:

11
13
12
6  → smaller
Therefore:

minIndex = 4
Swap:

11 ↔ 6
Array becomes:

[5, 6, 13, 12, 11]
Sorted portion:

[5, 6] | [13, 12, 11]
Pass 3
i = 2
Array:

[5, 6, 13, 12, 11]
       ↑
       i
Search:

13
12  → smaller
11  → smaller
Minimum:

11
Swap:

13 ↔ 11
Array becomes:

[5, 6, 11, 12, 13]
Sorted portion:

[5, 6, 11] | [12, 13]
Pass 4
i = 3
Array:

[5, 6, 11, 12, 13]
          ↑
          i
Search:

12
13
Minimum:

12
It is already in the correct position.

Array remains:

[5, 6, 11, 12, 13]
Final Output
[5, 6, 11, 12, 13]
Visualization
Initial
12 11 13 5 6
Pass 1
Find minimum:

12 11 13 5 6
         ↑
       minimum
Swap:

5 11 13 12 6
Pass 2
Find minimum:

5 | 11 13 12 6
            ↑
          minimum
Swap:

5 6 13 12 11
Pass 3
Find minimum:

5 6 | 13 12 11
            ↑
          minimum
Swap:

5 6 11 12 13
Pass 4
5 6 11 | 12 13
Already sorted.

Why Does Selection Sort Work?
At every iteration:

Find the smallest element
from the unsorted portion
Then:

Place it at the beginning
of the unsorted portion
Therefore, after every iteration:

One more element
is in its final position.
The sorted region keeps growing:

1 element sorted
↓
2 elements sorted
↓
3 elements sorted
↓
...
↓
Entire array sorted
Number of Comparisons
For every pass, we search the remaining unsorted elements.

Comparisons:

(n - 1) + (n - 2) + (n - 3) + ... + 1
Therefore:

= n(n - 1) / 2
Hence:

O(n²)
An important point is that Selection Sort performs O(n²) comparisons even when the array is already sorted.

Complexity Analysis
Best Case
Already Sorted:

[1, 2, 3, 4, 5]
Selection Sort still searches for the minimum in every remaining portion.

Time Complexity
O(n²)
Average Case
Random Array:

[4, 2, 5, 1, 3]
Time Complexity
O(n²)
Worst Case
Reverse Sorted:

[5, 4, 3, 2, 1]
Time Complexity
O(n²)
Space Complexity
Selection Sort sorts the array in-place.

Only a few variables are used:

i
j
minIndex
temp
Therefore:

O(1)
Stability
Standard Selection Sort is Not Stable.

Consider:

(5,A)
(3,B)
(5,C)
After selecting 3 and swapping it with the first element:

(3,B)
(5,C)
(5,A)
The two 5s changed their relative order:

Before:  (5,A) → (5,C)
After:   (5,C) → (5,A)
❌ Not Stable

In-Place Sorting
Selection Sort modifies the original array.

It does not require another array.

Therefore:

O(1) extra space
✅ In-place

Number of Swaps
One advantage of Selection Sort is that it performs at most n - 1 swaps.

For example:

[64, 25, 12, 22, 11]
Each pass performs at most one swap.

So:

Maximum swaps = n - 1
This can make Selection Sort useful when swaps are expensive, even though its overall running time is still O(n²).

Selection Sort vs Insertion Sort
Feature	Selection Sort	Insertion Sort
Best Case	O(n²)	O(n)
Average Case	O(n²)	O(n²)
Worst Case	O(n²)	O(n²)
Space	O(1)	O(1)
In-place	✅ Yes	✅ Yes
Stable	❌ No	✅ Yes
Adaptive	❌ No	✅ Yes
Maximum Swaps	O(n)	O(n²)
Basic Idea	Select minimum	Insert element
Selection Sort vs Bubble Sort
Feature	Selection Sort	Bubble Sort
Best Case	O(n²)	O(n)
Average Case	O(n²)	O(n²)
Worst Case	O(n²)	O(n²)
Space	O(1)	O(1)
In-place	✅ Yes	✅ Yes
Stable	❌ No	✅ Yes
Adaptive	❌ No	✅ Yes*
Swaps	At most O(n)	Up to O(n²)
*With the usual optimized Bubble Sort implementation.

Selection Sort in One Picture
                 Selection Sort
                       │
                       ↓
             Find minimum element
                       │
                       ↓
        Swap with first unsorted element
                       │
                       ↓
              Sorted portion grows
                       │
                       ↓
                 Repeat until done
Core Pattern to Remember
for (int i = 0; i < n - 1; i++) {

    int minIndex = i;

    for (int j = i + 1; j < n; j++) {

        if (arr[j] < arr[minIndex]) {
            minIndex = j;
        }
    }

    int temp = arr[minIndex];
    arr[minIndex] = arr[i];
    arr[i] = temp;
}
The easiest way to remember Selection Sort is:

SELECT → MINIMUM → SWAP
Find minimum
      ↓
Put minimum at i
      ↓
Move i forward
      ↓
Repeat
