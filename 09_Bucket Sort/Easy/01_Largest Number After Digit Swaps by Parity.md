# Largest Number After Digit Swaps by Parity

## Problem Statement

Given a positive integer `num`, you can swap any two digits of `num` that have the same parity (i.e., both are odd or both are even).

Return the largest possible value of `num` after any number of swaps.

The overall run time complexity should be:

```text
O(d * log(d))
```

*(where d is the number of digits in num)*

---

## Examples

### Example 1

**Input**

```java
num = 1234
```

**Output**

```java
3412
```

**Explanation**

- Swap the digit `1` with the digit `3` (both are odd) -> `3214`
- Swap the digit `2` with the digit `4` (both are even) -> `3412`

No other unique parity swaps can make the number larger than `3412`.

---

### Example 2

**Input**

```java
num = 65875
```

**Output**

```java
87655
```

**Explanation**

- Swap the digit `6` with the digit `8` (both are even) -> `85675`
- Swap the first digit `5` with the digit `7` (both are odd) -> `87655`

The value `87655` is the largest possible outcome.

---

### Example 3

**Input**

```java
num = 7
```

**Output**

```java
7
```

---

## Brute Force Approach

Generate all possible combinations of valid parity swaps recursively or using a backtracking mechanism, tracking the maximum number encountered.

### Steps

1. Convert the number into a mutable array of digits.
2. Run nested loops to find any pair of indices `(i, j)` where `digits[i]` and `digits[j]` have the same parity and `digits[i] < digits[j]`.
3. Perform the swap, record the new number value, and recurse to check further swap chains.
4. Backtrack by reversing the swap, and return the absolute maximum integer tracked.

### Complexity

```text
Time Complexity: O(d!)
Space Complexity: O(d)
```

This factorial scaling behaves inefficiently for numbers with many digits. The problem can be resolved with a more optimal greedy strategy.

---

# Optimal Approach: Parity Separation & Sorting

## Key Idea

Since we can make any number of swaps between digits of the same parity, this problem essentially means we can **reorder the even digits among themselves** and **reorder the odd digits among themselves** in any way we want.

To create the largest possible number, we should greedily place the largest available digits as early as possible. Thus:

1. Extract all the even digits and odd digits into two separate collections.
2. Sort both collections in descending order.
3. Reconstruct the number by iterating through the original digit positions:
   - If the original digit was even, pick the largest remaining even digit.
   - If the original digit was odd, pick the largest remaining odd digit.

---

## Visual Understanding

Suppose:

```java
num = 1234
```

Original array mapping of parities:

```text
Index 0: 1 (Odd)
Index 1: 2 (Even)
Index 2: 3 (Odd)
Index 3: 4 (Even)
```

Separate and sort parities descending:

```text
Evens: [4, 2]
Odds:  [3, 1]
```

Reconstruct step-by-step:

```text
Index 0 (was Odd)  -> Take largest Odd  (3) -> Result: 3
Index 1 (was Even) -> Take largest Even (4) -> Result: 34
Index 2 (was Odd)  -> Take largest Odd  (1) -> Result: 341
Index 3 (was Even) -> Take largest Even (2) -> Result: 3412
```

Final Largest Value = `3412`.

---

## Partition Variables

Let:

```java
List<Integer> evens = new ArrayList<>()
List<Integer> odds = new ArrayList<>()
char[] digits = String.valueOf(num).toCharArray()
```

---

### Border Elements

The arrays are sorted using built-in sorting mechanisms:

```java
Collections.sort(evens, Collections.reverseOrder());
Collections.sort(odds, Collections.reverseOrder());
```

Two independent pointer indexes track the next available largest element from each list during reconstruction.

---

## Correct Partition Condition

```java
if ((digits[i] - '0') % 2 == 0) {
    ans = ans * 10 + evens.get(evenPtr++);
} else {
    ans = ans * 10 + odds.get(oddPtr++);
}
```

This maps the correctly sorted largest digit back into its structural parity slot position.

---

## How to Move Binary Search

*(Note: This optimal approach swaps standard Binary Search for a Greedy Sorting framework because the choices do not follow a binary range split structure but instead require a full relative sorting mapping).*

---

## Java Solution

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

class Solution {

    public int largestInteger(int num) {

        char[] digits = String.valueOf(num).toCharArray();
        List<Integer> evens = new ArrayList<>();
        List<Integer> odds = new ArrayList<>();

        // Step 1: Separate digits by parity
        for (char c : digits) {
            int digit = c - '0';
            if (digit % 2 == 0) {
                evens.add(digit);
            } else {
                odds.add(digit);
            }
        }

        // Step 2: Sort both lists in descending order
        Collections.sort(evens, Collections.reverseOrder());
        Collections.sort(odds, Collections.reverseOrder());

        int evenPtr = 0;
        int oddPtr = 0;
        int result = 0;

        // Step 3: Reconstruct the maximum number
        for (char c : digits) {
            int digit = c - '0';
            if (digit % 2 == 0) {
                result = result * 10 + evens.get(evenPtr++);
            } else {
                result = result * 10 + odds.get(oddPtr++);
            }
        }

        return result;
    }
}
```

---

## Dry Run

### Input

```java
num = 1234
```

---

### Initial Values

```java
digits = ['1', '2', '3', '4']
evens = [2, 4]
odds = [1, 3]
```

---

### Sorting Phase

```java
evens = [4, 2]
odds = [3, 1]
evenPtr = 0
oddPtr = 0
result = 0
```

---

### Reconstruction Loops

- **i = 0:** `digits[0] = '1'` (Odd). `result = 0 * 10 + 3 = 3`. `oddPtr = 1`.
- **i = 1:** `digits[1] = '2'` (Even). `result = 3 * 10 + 4 = 34`. `evenPtr = 1`.
- **i = 2:** `digits[2] = '3'` (Odd). `result = 34 * 10 + 1 = 341`. `oddPtr = 2`.
- **i = 3:** `digits[3] = '4'` (Even). `result = 341 * 10 + 2 = 3412`. `evenPtr = 2`.

---

### Answer

```java
3412
```

---

## Why Do We Separate and Sort Parities?

Since elements of the same parity can be swapped fluidly with each other across any distance, any subset of identical parities behaves like an isolated array that can be completely sorted. A greedy placement ensures the global maximum value configuration is realized.

This reordering layout yields:

```text
O(d * log(d))
```

which satisfies the optimal constraint perfectly.

---

## Complexity Analysis

### Time Complexity

```text
O(d * log(d))
```

Where `d` is the number of digits in `num`. Converting and traversing the number takes linear time `O(d)`, while the sorting step dominates the runtime at an `O(d log d)` scaling factor.

---

### Space Complexity

```text
O(d)
```

Additional lists (`evens` and `odds`) along with the string character array are allocated to track the digits.

---

## Key Insight

Recognizing that unrestricted adjacent or long-distance swaps within a specific property group allow for full sorting capability shifts the challenge from a complex permutation search into an efficient greedy sorting task.

```text
Time  : O(d * log(d))
Space : O(d)
```

---

## Similar Problems

1. Largest Number
2. Sort Array By Parity
3. Sort Array By Parity II
4. Rearrange Array Elements by Sign
5. Wiggle Sort
