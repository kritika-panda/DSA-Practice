# Base 7

## Problem Statement

Given an integer `num`, return *its **base 7** string representation*.

The overall runtime complexity must be:

```text
O(log_7(num))
```

*(which is proportional to the number of digits in the final output base-7 string)*

---

## Examples

### Example 1

**Input**

```java
num = 100
```

**Output**

```java
"202"
```

**Explanation**

100 = 2 × 7² + 0 × 7¹ + 2 × 7⁰ = 98 + 0 + 2 = 100.

---

### Example 2

**Input**

```java
num = -7
```

**Output**

```java
"-10"
```

**Explanation**

-7 = -1 × 7¹ + 0 × 7⁰.

---

### Example 3

**Input**

```java
num = 0
```

**Output**

```java
"0"
```

---

## Brute Force Approach

Convert the integer into a string copy dynamically by calling language conversion shortcuts or built-in radix functions (such as `Integer.toString(num, 7)`).

### Steps

1. Call the built-in system parsing library: `Integer.toString(num, 7)`.
2. Return the parsed text value directly.

### Complexity

```text
Time Complexity: O(log_7(num))
Space Complexity: O(log_7(num)) to allocate the result string
```

While built-in radix utilities are excellent for production systems, technical interviews test your underlying understanding of **positional number system mathematics**. We should build this conversion layout from scratch using successive modulo division.

---

# Optimal Approach: Successive Division & Remainder Tracking

## Key Idea

Converting a number to any base B relies on a simple algorithmic division cycle: repeatedly dividing the number by B isolates the digit coefficients from right to left using the remainders.

To make the calculation elegant and robust against negative constraints, we isolate the absolute value upfront:
1. **Edge Case Guard:** If `num == 0`, immediately return `"0"`.
2. **Sign Isolation:** Track if the input is negative using a boolean flag `isNegative = num < 0`. Convert `num` to its absolute positive form: `num = Math.abs(num)`.
3. **Successive Base Reduction:** While `num > 0`:
   - Compute the trailing digit using the remainder: `remainder = num % 7`.
   - Append this digit directly to a mutable `StringBuilder`.
   - Scale down the number by dividing it by the base: `num = num / 7`.
4. **Sign Application:** If `isNegative` is true, append a trailing minus sign `'-'` to the buffer.
5. Since digits were extracted from the lowest place-value upwards (right to left), reverse the compiled `StringBuilder` before returning the final text.

---

## Visual Understanding

Suppose `num = 100`:

- **Initialization:** `isNegative = false`, `StringBuilder sb = ""`. Loop begins:
- **Iteration 1:**
  - Isolate digit: `100 % 7 = 2`. Append to builder \(\implies\) `sb = "2"`.
  - Base reduction: `100 / 7 = 14`.
- **Iteration 2:**
  - Isolate digit: `14 % 7 = 0`. Append to builder \(\implies\) `sb = "20"`.
  - Base reduction: `14 / 7 = 2`.
- **Iteration 3:**
  - Isolate digit: `2 % 7 = 2`. Append to builder \(\implies\) `sb = "202"`.
  - Base reduction: `2 / 7 = 0`.

**Termination:** `num` drops to 0. Loop ends. Reverse `sb` \(\implies\) `"202"`.

---

## Partition Variables

Let:

```java
boolean isNegative = num < 0;
long n = Math.abs((long) num); // Cast to long protects against Integer.MIN_VALUE negation overflow
StringBuilder sb = new StringBuilder();
```

---

### Border Elements

The algorithm handles zero instantly as a dedicated standalone statement to prevent empty loops:

```java
if (num == 0) return "0";
```

---

## Correct Partition Condition

The math loop continues stripping digits dynamically until the numerical spectrum runs down to zero:

```java
while (n > 0) {
    sb.append(n % 7);
    n /= 7;
}
```

---

## Java Solution

```java
class Solution {

    public String convertToBase7(int num) {
        // Step 1: Base Case - Zero is a unique single-digit element
        if (num == 0) {
            return "0";
        }

        boolean isNegative = num < 0;
        // Cast to long handles Integer.MIN_VALUE safely without negation errors
        long n = Math.abs((long) num); 
        StringBuilder sb = new StringBuilder();

        // Step 2: Successive division loop to extract base-7 digits
        while (n > 0) {
            sb.append(n % 7); // Append the current remainder digit
            n /= 7;           // Reduce the number by a factor of 7
        }

        // Step 3: Apply the negative sign if applicable
        if (isNegative) {
            sb.append('-');
        }

        // Step 4: Reverse the builder since digits were collected from right to left
        return sb.reverse().toString();
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Base-7 Reduction** | **O(log_7(num))** | **O(log_7(num))** | The value of the number drops by a factor of 7 in each loop cycle. The `StringBuilder` allocates memory cells matching the character length of the final converted string. |

---

## Key Insight

Isolating signs and utilizing successive modulo division transforms a number systems problem into a clean, single-pass digit extraction loop.

```text
Time  : O(log_7(num)) logarithmic tracking path
Space : O(log_7(num)) dynamic string output builder allocation
```

---

## Similar Problems

1. Excel Sheet Column Title (168)
2. Excel Sheet Column Number (171)
3. Add Binary (67)
4. Add Strings (415)
5. Base 2 / Convert to Binary
