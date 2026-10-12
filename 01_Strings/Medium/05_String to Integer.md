# String to Integer (atoi)

## Problem Statement

Implement the `myAtoi(string s)` function, which converts a string to a 32-bit signed integer (similar to C/C++'s `atoi` function).

The algorithm for `myAtoi(string s)` is as follows:
1. **Whitespace:** Read in and ignore any leading whitespace.
2. **Signedness:** Check if the next character (if not already at the end of the string) is `'-'` or `'+'`. Read this character in if it is either. This determines if the final result is negative or positive respectively. Assume the result is positive if neither is present.
3. **Conversion:** Read in next the characters until the next non-digit character or the end of the input is reached. The rest of the string is ignored. Convert these digits into an integer (i.e. `"123"` → `123`, `"0032"` → `32`). If no digits were read, then the integer is `0`.
4. **Rounding:** If the integer is out of the 32-bit signed integer range \([-2^{31}, 2^{31} - 1]\), then clamp the integer so that it remains in the range. Specifically, integers less than -2³¹ should be clamped to -2³¹, and integers greater than 2³¹ - 1 should be clamped to 2³¹ - 1.

The overall runtime complexity must be:

```text
O(n)
```

*(where n is the length of the string s)*

---

## Examples

### Example 1

**Input**

```java
s = "42"
```

**Output**

```java
42
```

---

### Example 2

**Input**

```java
s = "   -042"
```

**Output**

```java
-42
```

**Explanation**

Leading spaces are skipped. Then `'-'` is read making the result negative. `"042"` is parsed into `42`.

---

### Example 3

**Input**

```java
s = "1337c0d3"
```

**Output**

```java
1337
```

**Explanation**

Parsing stops at character `'c'` because it is a non-digit.

---

### Example 4

**Input**

```java
s = "words and 987"
```

**Output**

```java
0
```

**Explanation**

Parsing stops immediately at character `'w'` because it is not a space, sign, or digit.

---

### Example 5

**Input**

```java
s = "-91283472332"
```

**Output**

```java
-2147483648
```

**Explanation**

The number -91283472332 is less than -2³¹, so it is clamped to Integer.MIN\_VALUE (-2147483648).

---

## Brute Force Approach

Clean the string using high-level string trimming and regex splitting utilities, and convert the remaining segment to a numeric type while catching format exceptions.

### Steps

1. Trim leading whitespaces from the string.
2. Use regular expressions to extract the valid prefix block matching an optional sign followed by digits.
3. If no match is found, return `0`.
4. Parse the matched substring using `Double.parseDouble()` or `BigInteger`.
5. Clamp the numeric value explicitly to 32-bit signed integer boundaries and cast to an `int`.

### Complexity

```text
Time Complexity: O(n)
Space Complexity: O(n) // due to allocating dynamic substring copies and regex buffers
```

Relying on regular expressions and heavy string allocations incurs high memory performance penalties. In technical evaluations, this problem is designed to test your ability to handle state machine flows and overflow detections **in-place** with constant extra memory.

---

# Optimal Approach: Single-Pass In-Place State Machine

## Key Idea

We can implement the `atoi` specifications smoothly in a single left-to-right pass using a state-tracking algorithm with zero extra allocations.

1. **Phase 1 (Skip Whitespace):** Advance a pointer `i` forward through the string while `s.charAt(i) == ' '`.
2. **Phase 2 (Sign Check):** If the current character is `'-'` or `'+'`, set a `sign` variable accordingly (`-1` or `1`) and increment `i`.
3. **Phase 3 (Digit Accumulation & Overflow Protection):** Loop through the remaining characters as long as they are valid digits (`'0'` to `'9'`). For each digit, before accumulating it into a running `result` integer variable, we must verify if the incoming digit will push our value beyond 32-bit signed integer bounds:
   - If `result > Integer.MAX_VALUE / 10`, an overflow is guaranteed. 
   - If `result == Integer.MAX_VALUE / 10`, an overflow occurs if the next digit is strictly greater than `7` (since `Integer.MAX_VALUE` ends in `7`, and `Integer.MIN_VALUE` ends in `8` under absolute value properties).
   - If an overflow condition is tripped, instantly return the clamped boundary boundary value (`Integer.MAX_VALUE` or `Integer.MIN_VALUE`).

---

## Visual Understanding

Suppose `s = "   -2147483649"`:

- **Phase 1:** Index `i` advances to `3` to clear leading spaces.
- **Phase 2:** `s.charAt(3) == '-'` \(\implies\) `sign = -1`, `i = 4`.
- **Phase 3 (Accumulation Loops):**
  - Digits accumulate normally up to the 9th digit: `result = 214748364`.
  - Next digit at `i` is `'9'`. Before appending it, we check our overflow protection gate:
    - `result (214748364) == Integer.MAX_VALUE / 10 (214748364)`.
    - Incoming digit is `9`. Since `9 > 7`, an overflow event is caught instantly!
  - Because `sign == -1`, the overflow is clamped to the lower floor threshold. Return `Integer.MIN_VALUE` (-2147483648) immediately.

---

## Partition Variables

Let:

```java
int i = 0;
int sign = 1;
int result = 0;
int n = s.length();
```

---

### Border Elements

The lookahead boundary protection expression isolates value trends precisely before the numeric registry state overflows:

```java
if (result > Integer.MAX_VALUE / 10 || (result == Integer.MAX_VALUE / 10 && digit > 7)) {
    return sign == 1 ? Integer.MAX_VALUE : Integer.MIN_VALUE;
}
```

---

## Correct Partition Condition

The compilation tracking terminates the instant the pointer strikes an out-of-bounds index or any non-digit character layout:

```java
while (i < n) {
    int digit = s.charAt(i) - '0';
    if (digit < 0 || digit > 9) break; // Non-digit breaks the string parsing conversion loop
    // ... overflow verification and accumulation step ...
    i++;
}
```

---

## Java Solution

```java
class Solution {

    public int myAtoi(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }

        int n = s.length();
        int i = 0;
        int sign = 1;
        int result = 0;

        // Step 1: Skip leading whitespaces safely
        while (i < n && s.charAt(i) == ' ') {
            i++;
        }

        // Handle edge case where the string consists entirely of spaces
        if (i == n) {
            return 0;
        }

        // Step 2: Check for an optional sign character
        if (s.charAt(i) == '-') {
            sign = -1;
            i++;
        } else if (s.charAt(i) == '+') {
            i++;
        }

        // Step 3: Process consecutive digits and handle clamp boundaries
        while (i < n) {
            int digit = s.charAt(i) - '0';

            // Terminate immediately if a non-digit character is encountered
            if (digit < 0 || digit > 9) {
                break;
            }

            // Step 4: Overflow Check - Guard against primitive register boundary breaks
            if (result > Integer.MAX_VALUE / 10 || 
               (result == Integer.MAX_VALUE / 10 && digit > 7)) {
                return sign == 1 ? Integer.MAX_VALUE : Integer.MIN_VALUE;
            }

            // Accumulate digit into the running total
            result = result * 10 + digit;
            i++;
        }

        return result * sign;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **In-Place State Scan** | **O(n)** | **O(1)** | Traverses characters sequentially at most once. Runs entirely in-place inside local registers with absolute zero auxiliary memory tracking allocations. |

---

## Key Insight

Using an in-place math overflow lookahead gate (`Integer.MAX_VALUE / 10`) stops execution before register caps can naturally wrap or error out, securing safe, linear string-to-integer parsing.

```text
Time  : O(n) single pass forward execution
Space : O(1) absolute constant auxiliary space footprint
```

---

## Similar Problems

1. Reverse Integer (7)
2. Palindrome Number (9)
3. Divide Two Integers Without Multiplication and Division (29)
4. Multiply Strings (43)
5. Valid Number (65)
