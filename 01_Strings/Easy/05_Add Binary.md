# Add Binary

## Problem Statement

Given two binary strings `a` and `b`, return *their sum as a binary string*.

The input strings are non-empty and contain only characters `'1'` or `'0'`. They do not contain any leading zeros except for the number zero itself.

The overall runtime complexity must be:

```text
O(max(n, m))
```

*(where n is the length of string a and m is the length of string b)*

---

## Examples

### Example 1

**Input**

```java
a = "11"
b = "1"
```

**Output**

```java
"100"
```

**Explanation**

Binary `11` (3 in decimal) + binary `1` (1 in decimal) equals binary `100` (4 in decimal).

---

### Example 2

**Input**

```java
a = "1010"
b = "1011"
```

**Output**

```java
"10101"
```

**Explanation**

Binary `1010` (10 in decimal) + binary `1011` (11 in decimal) equals binary `10101` (21 in decimal).

---

## Brute Force Approach

Convert the binary strings into base-10 integers using language built-in conversion utilities, add them mathematically, and format the sum back into a binary string representation.

### Steps

1. Parse strings to base-10 integers: `int numA = Integer.parseInt(a, 2)`.
2. Sum the values: `int sum = numA + numB`.
3. Re-convert the result to binary format: `Integer.toBinaryString(sum)`.

### Complexity

```text
Time Complexity: O(n + m)
Space Complexity: O(n + m) to hold converted string buffers
```

While this works for short values, it triggers a catastrophic runtime crash with a `NumberFormatException` when the string lengths exceed standard primitive variable constraints (e.g., bits span longer than 32 or 64 positions). We must process the addition dynamically using a bit-by-bit lookahead loop.

---

# Optimal Approach: Two-Pointer Bit-by-Bit Simulation

## Key Idea

We can compute the exact binary sum for arbitrary string lengths in **linear time** by simulating basic long addition from right to left.

1. Initialize two pointers, `i` at the end of string `a` and `j` at the end of string `b`.
2. Maintain a running integer `carry` variable to hold overflow bits across iterations.
3. In each iteration, compute the sum of the bits at `i` and `j` along with the current `carry`:
   - If `i >= 0`, extract the bit numerical value and decrement `i`.
   - If `j >= 0`, extract the bit numerical value and decrement `j`.
4. Calculate the new bit to append and update the carry:
   - **New Bit:** `sum % 2`
   - **New Carry:** `sum / 2`
5. Append the calculated bit to a mutable `StringBuilder`. 
6. Repeat until both pointers are exhausted and the `carry` drops to 0. Finally, reverse the compiled string to restore the correct left-to-right order.

---

## Visual Understanding

Suppose `a = "11"`, `b = "1"`:

- **Initialization:** `i = 1`, `j = 0`, `carry = 0`, `StringBuilder sb = ""`.
- **Iteration 1:**
  - `i = 1` \(\implies\) Bit is `1`. `j = 0` \(\implies\) Bit is `1`.
  - `sum = 1 + 1 + carry (0) = 2`.
  - Append `sum % 2 = 0` \(\implies\) `sb = "0"`. Update `carry = 2 / 2 = 1`.
  - Decrement pointers: `i = 0, j = -1`.
- **Iteration 2:**
  - `i = 0` \(\implies\) Bit is `1`. `j = -1` \(\implies\) Out of bounds, use `0`.
  - `sum = 1 + 0 + carry (1) = 2`.
  - Append `sum % 2 = 0` \(\implies\) `sb = "00"`. Update `carry = 2 / 2 = 1`.
  - Decrement pointers: `i = -1, j = -2`.
- **Iteration 3:**
  - Both pointers out of bounds. `carry = 1`.
  - `sum = 0 + 0 + 1 = 1`.
  - Append `sum % 2 = 1` \(\implies\) `sb = "001"`. Update `carry = 1 / 2 = 0`.
- **Termination:** Pointers exhausted and `carry == 0`. Reverse `sb`: `"100"`.

The calculated binary string `"100"` is built correctly without arithmetic overflow risks.

---

## Partition Variables

Let:

```java
StringBuilder sb = new StringBuilder();
int i = a.length() - 1;
int j = b.length() - 1;
int carry = 0;
```

---

### Border Elements

The scanning loop continues running as long as at least one string pointer is active or a trailing carry bit remains to be flushed into the builder:

```java
while (i >= 0 || j >= 0 || carry != 0)
```

---

## Correct Partition Condition

The mathematical digit extraction maps character unicode values into primitive integers using a clean subtraction index line:

```java
if (i >= 0) {
    sum += a.charAt(i) - '0';
    i--;
}
```

---

## Java Solution

```java
class Solution {

    public String addBinary(String a, String b) {
        StringBuilder sb = new StringBuilder();
        
        int i = a.length() - 1;
        int j = b.length() - 1;
        int carry = 0;

        // Step 1: Traverse both strings from right to left
        while (i >= 0 || j >= 0 || carry != 0) {
            int sum = carry;

            // Extract numerical bit from string a if within bounds
            if (i >= 0) {
                sum += a.charAt(i) - '0';
                i--;
            }

            // Extract numerical bit from string b if within bounds
            if (j >= 0) {
                sum += b.charAt(j) - '0';
                j--;
            }

            // Step 2: Append the calculated binary digit remainder
            sb.append(sum % 2);

            // Step 3: Compute the carry forward bit for the next column
            carry = sum / 2;
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

Simulating manual column-addition from right to left with a primitive carry variable avoids system conversion overflow traps completely, handling infinite bit lengths inside safe linear boundaries.

```text
Time  : O(max(n, m)) linear tracking pass
Space : O(max(n, m)) dynamic string output builder allocation
```

---

## Similar Problems

1. Multiply Strings (43)
2. Add Strings (415)
3. Plus One (66)
4. Add Two Numbers (2)
5. Sum of Two Integers (371)
