# Excel Sheet Column Number

## Problem Statement

Given a string `columnTitle` that represents the column title as appears in an Excel sheet, return *its corresponding column number*.

For example:
- `"A"` -> `1`
- `"B"` -> `2`
- `"C"` -> `3`
- `"Z"` -> `26`
- `"AA"` -> `27`
- `"AB"` -> `28` 

The overall runtime complexity must be:

```text
O(n)
```

*(where n is the length of the string columnTitle)*

---

## Examples

### Example 1

**Input**

```java
columnTitle = "A"
```

**Output**

```java
1
```

---

### Example 2

**Input**

```java
columnTitle = "AB"
```

**Output**

```java
28
```

**Explanation**

`"A"` maps to 1, and `"B"` maps to 2. Calculated as: 1 × 26¹ + 2 × 26⁰ = 26 + 2 = 28.

---

### Example 3

**Input**

```java
columnTitle = "ZY"
```

**Output**

```java
701
```

---

## Brute Force Approach

Parse the string characters from right to left, manually tracking and multiplying exponentially scaling power weights of 26 (\(26^0, 26^1, 26^2 \dots\)) using explicit loops or standard power functions.

### Steps

1. Initialize a `total = 0`.
2. Iterate backward from the end of the string using a loop variable `i` from `n - 1` down to `0`.
3. Extract the 1-based alphabetic value of the character: `val = columnTitle.charAt(i) - 'A' + 1`.
4. Calculate the place value weight using `Math.pow(26, power)` and increment `power` by 1.
5. Multiply the value by the weight, accumulate it into `total`, and return it.

### Complexity

```text
Time Complexity: O(n) // though floating-point Math.pow calls add overhead
Space Complexity: O(1)
```

While backward parsing is functionally correct, calling floating-point exponentiation math functions inside performance loops adds unneeded structural complexity. We can simplify this significantly into a single forward loop using Horner's rule for polynomial evaluation.

---

# Optimal Approach: Left-to-Right Polynomial Accumulation

## Key Idea

Converting an Excel column title to a number is exactly like parsing a standard base-10 numerical string (like `"123"`), but using a **base-26 number system** where `"A"` maps to 1 and `"Z"` maps to 26.

We can accumulate the value cleanly in a single left-to-right pass using **Horner's Method**:
1. Initialize an integer accumulator variable `result = 0`.
2. Iterate forward through the characters of the string from index `0` up to `n - 1`.
3. In each step, before processing the current character, multiply our existing accumulated total by the base value `26`: `result = result * 26`. This effectively shifts all previously calculated characters up by one column place value.
4. Extract the 1-based numerical value of the current character: `currentVal = c - 'A' + 1`.
5. Add `currentVal` straight into `result` and repeat.

---

## Visual Understanding

Suppose `columnTitle = "CAB"`:

- **Initialize:** `result = 0`.
- **Character 0 (`'C'`):**
  - Shift existing total: `result = 0 * 26 = 0`.
  - Extract value: `'C' - 'A' + 1 = 3`.
  - Accumulate: `result = 0 + 3 = 3`.
- **Character 1 (`'A'`):**
  - Shift existing total: `result = 3 * 26 = 78`.
  - Extract value: `'A' - 'A' + 1 = 1`.
  - Accumulate: `result = 78 + 1 = 79`.
- **Character 2 (`'B'`):**
  - Shift existing total: `result = 79 * 26 = 2054`.
  - Extract value: `'B' - 'A' + 1 = 2`.
  - Accumulate: `result = 2054 + 2 = 2056`.

The total integer value `2056` is constructed efficiently without executing expensive external power calculations.

---

## Partition Variables

Let:

```java
int result = 0;
```

---

### Border Elements

The algorithm handles max length limits safely up to standard Excel boundaries (7-character column ranges like `"FXSHRXW"`), fitting comfortably within 32-bit signed integer limits.

---

## Correct Partition Condition

The forward shift multiplication must precede the current character accumulation in each step:

```java
result = result * 26;
result += (columnTitle.charAt(i) - 'A' + 1);
```

---

## Java Solution

```java
class Solution {

    public int titleToNumber(String columnTitle) {
        if (columnTitle == null || columnTitle.length() == 0) {
            return 0;
        }

        int result = 0;
        int n = columnTitle.length();

        // Step 1: Scan the column title string from left to right
        for (int i = 0; i < n; i++) {
            // Shift the previously accumulated total up by a base of 26
            result *= 26;
            
            // Extract the 1-based index value ('A' -> 1, 'B' -> 2, etc.) and add it
            result += (columnTitle.charAt(i) - 'A' + 1);
        }

        return result;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Polynomial Sweep** | **O(n)** | **O(1)** | Processes each character in the string exactly once. Runs entirely in-place with zero memory allocation. |

---

## Key Insight

Multiplying the running total by 26 at each step simulates natural column shifts dynamically, removing the need for manual tracking or expensive exponentiation logic.

```text
Time  : O(n) single pass forward execution
Space : O(1) absolute constant memory footprint
```

---

## Similar Problems

1. Excel Sheet Column Title (168)
2. Base 7 (504)
3. String to Integer / atoi (8)
4. Multiply Strings (43)
5. Roman to Integer (13)
