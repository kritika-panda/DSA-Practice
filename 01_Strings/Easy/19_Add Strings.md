# Add Strings

## Problem Statement

Given two non-negative integers, `num1` and `num2`, represented as string, return *the sum of `num1` and `num2` as a string*.

You must solve the problem without using any built-in BigInteger library or converting the inputs to integers directly.

The overall runtime complexity must be:

```text
O(max(n, m))
```

*(where n is the length of string num1 and m is the length of string num2)*

---

## Examples

### Example 1

**Input**

```java
num1 = "11"
num2 = "123"
```

**Output**

```java
"134"
```

---

### Example 2

**Input**

```java
num1 = "456"
num2 = "77"
```

**Output**

```java
"533"
```

---

### Example 3

**Input**

```java
num1 = "0"
num2 = "0"
```

**Output**

```java
"0"
```

---

## Brute Force Approach

Convert the strings to primitive long integers or `BigInteger` types using built-in parsing functions, add them together mathematically, and convert the sum back to a string object.

### Steps

1. Parse the strings to values: `long n1 = Long.parseLong(num1)`.
2. Sum the extracted numbers: `long sum = n1 + n2`.
3. Format the result back into text format: `String.valueOf(sum)`.

### Complexity

```text
Time Complexity: O(n + m)
Space Complexity: O(n + m) to hold intermediate string buffers
```

This strategy crashes immediately with a `NumberFormatException` when the string lengths exceed standard primitive variable data thresholds (e.g., numbers with hundreds of digits). We must process the addition dynamically using a column-by-column simulation loop.

---

# Optimal Approach: Two-Pointer Digit long Addition Simulation

## Key Idea

We can compute the exact sum for arbitrary string lengths in **linear time** by simulating basic long addition from right to left, just like doing addition on paper.

1. Initialize two pointers, `i` at the end of string `num1` and `j` at the end of string `num2`.
2. Maintain a running integer `carry` variable to hold overflow digits across columns.
3. In each iteration, compute the sum of the digits at `i` and `j` along with the current `carry`:
   - If `i >= 0`, extract the numerical digit value (`num1.charAt(i) - '0'`) and decrement `i`.
   - If `j >= 0`, extract the numerical digit value (`num2.charAt(j) - '0'`) and decrement `j`.
4. Calculate the new digit to append and update the carry:
   - **New Digit:** `sum % 10`
   - **New Carry:** `sum / 10`
5. Append the calculated digit to a mutable `StringBuilder`. 
6. Repeat until both pointers are completely exhausted and the `carry` drops to 0. Finally, reverse the compiled string to restore the correct left-to-right order.

---

## Visual Understanding

Suppose `num1 = "456"`, `num2 = "77"`:

- **Initialization:** `i = 2`, `j = 1`, `carry = 0`, `StringBuilder sb = ""`.
- **Iteration 1:**
  - `i = 2` \(\implies\) Digit is `6`. `j = 1` \(\implies\) Digit is `7`.
  - `sum = 6 + 7 + carry (0) = 13`.
  - Append `sum % 10 = 3` \(\implies\) `sb = "3"`. Update `carry = 13 / 10 = 1`.
  - Decrement pointers: `i = 1, j = 0`.
- **Iteration 2:**
  - `i = 1` \(\implies\) Digit is `5`. `j = 0` \(\implies\) Digit is `7`.
  - `sum = 5 + 7 + carry (1) = 13`.
  - Append `sum % 10 = 3` \(\implies\) `sb = "33"`. Update `carry = 13 / 10 = 1`.
  - Decrement pointers: `i = 0, j = -1`.
- **Iteration 3:**
  - `i = 0` \(\implies\) Digit is `4`. `j = -1` \(\implies\) Out of bounds, use `0`.
  - `sum = 4 + 0 + carry (1) = 5`.
  - Append `sum % 10 = 5` \(\implies\) `sb = "335"`. Update `carry = 5 / 10 = 0`.
- **Termination:** Pointers exhausted and `carry == 0`. Reverse `sb`: `"533"`.

The calculated sum string `"533"` is assembled correctly without any type size overflow risks.

---

## Partition Variables

Let:

```java
StringBuilder sb = new StringBuilder();
int i = num1.length() - 1;
int j = num2.length() - 1;
int carry = 0;
```

---

### Border Elements

The scanning loop continues running as long as at least one string pointer remains active or a trailing carry digit needs to be flushed out:

```java
while (i >= 0 || j >= 0 || carry != 0)
```

---

## Correct Partition Condition

The mathematical digit extraction maps character unicode values into primitive integers using a clean subtraction index line:

```java
if (i >= 0) {
    sum += num1.charAt(i) - '0';
    i--;
}
```

---

## Java Solution

```java
class Solution {

    public String addStrings(String num1, String num2) {
        StringBuilder sb = new StringBuilder();
        
        int i = num1.length() - 1;
        int j = num2.length() - 1;
        int carry = 0;

        // Step 1: Traverse both strings from right to left
        while (i >= 0 || j >= 0 || carry != 0) {
            int sum = carry;

            // Extract numerical digit from string num1 if within bounds
            if (i >= 0) {
                sum += num1.charAt(i) - '0';
                i--;
            }

            // Extract numerical digit from string num2 if within bounds
            if (j >= 0) {
                sum += num2.charAt(j) - '0';
                j--;
            }

            // Step 2: Append the calculated digit remainder
            sb.append(sum % 10);

            // Step 3: Compute the carry forward digit for the next column
            carry = sum / 10;
        }

        // Step 4: Reverse the builder to restore the left-to-right order
        return sb.reverse().toString();
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Bit addition Pass** | **O(max(n, m))** | **O(max(n, m))** | Processes each column coordinate exactly once. The auxiliary string builder allocates character slots proportional to the maximum input string length. |

---

## Key Insight

Simulating manual column-addition from right to left with a primitive carry variable avoids system conversion overflow traps completely, handling infinite digit lengths inside safe linear boundaries.

```text
Time  : O(max(n, m)) linear tracking pass
Space : O(max(n, m)) dynamic string output builder allocation
```

---

## Similar Problems

1. Add Binary (67)
2. Multiply Strings (43)
3. Plus One (66)
4. Add Two Numbers (2)
5. Add to Array-Form of Integer (989)
