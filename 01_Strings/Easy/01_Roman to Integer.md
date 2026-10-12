# Roman to Integer

## Problem Statement

Given a roman numeral string `s`, convert it to an integer.

Roman numerals are represented by seven different symbols: `I`, `V`, `X`, `L`, `C`, `D` and `M`.

| Symbol | Value |
| :--- | :--- |
| **I** | 1 |
| **V** | 5 |
| **X** | 10 |
| **L** | 50 |
| **C** | 100 |
| **D** | 500 |
| **M** | 1000 |

Roman numerals are usually written largest to smallest from left to right. However, the numeral for four is not `IIII`. Instead, the number four is written as `IV`. Because the one is before the five we subtract it making four. The same principle applies to number nine, which is written as `IX`. 

There are six instances where subtraction is used:
* `I` can be placed before `V` (5) and `X` (10) to make 4 and 9. 
* `X` can be placed before `L` (50) and `C` (100) to make 40 and 90. 
* `C` can be placed before `D` (500) and `M` (1000) to make 400 and 900.

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
s = "III"
```

**Output**

```java
3
```

**Explanation**

`III` = 3.

---

### Example 2

**Input**

```java
s = "LVIII"
```

**Output**

```java
58
```

**Explanation**

`L` = 50, `V` = 5, `III` = 3. `50 + 5 + 3 = 58`.

---

### Example 3

**Input**

```java
s = "MCMXCIV"
```

**Output**

```java
1994
```

**Explanation**

`M` = 1000, `CM` = 900, `XC` = 90, `IV` = 4. `1000 + 900 + 90 + 4 = 1994`.

---

## Brute Force Approach

Scan the string from left to right, parsing every sub-token and using conditional branching to handle special two-character subtractive sequences explicitly.

### Steps

1. Maintain a pointer `i = 0`.
2. Check if the upcoming pair of characters matches one of the six subtractive forms (`"IV"`, `"IX"`, `"XL"`, `"XC"`, `"CD"`, `"CM"`).
3. If it matches a subtractive form, add the corresponding subtracted value to the running sum and increment `i` by 2.
4. Otherwise, map the single character at `i`, add its raw value to the sum, and increment `i` by 1.
5. Repeat until the entire string is processed.

### Complexity

```text
Time Complexity: O(n)
Space Complexity: O(1)
```

While this linear pass works, writing explicit string matching chains for all combinations results in bloated conditional logic. We can simplify this significantly by comparing the values of adjacent characters.

---

# Optimal Approach: Single-Pass Lookahead Comparison

## Key Idea

A simple rule governs Roman numeral encoding: **If a smaller numeral appears before a larger numeral, it signifies a subtraction operation.** Otherwise, the value is added directly.

Instead of hardcoding character pairs, we can process the string from left to right by looking ahead at the next adjacent character:
1. To implement this optimally without a heavy map container, we use a custom helper method `getVal(char c)` with a fast primitive `switch` block.
2. At any index `i`, retrieve the value of `s.charAt(i)` and the value of `s.charAt(i + 1)` (if a next character exists).
3. **Subtraction Condition:** If `value(s.charAt(i)) < value(s.charAt(i + 1))`, it means this character is part of a subtractive pair. We **subtract** its value from our running total.
4. **Addition Condition:** If `value(s.charAt(i)) >= value(s.charAt(i + 1))`, we **add** its value directly to our running total.

By mapping characters using a lookahead layout, the entire sequence reduces to a clean arithmetic loop.

---

## Visual Understanding

Suppose `s = "MCMXCIV"`:

Let's trace the running total across the characters from left to right:
- **'M' (1000) vs 'C' (100):** \(1000 \ge 100 \implies \text{Add } 1000\). Total = 1000.
- **'C' (100) vs 'M' (1000):** \(100 < 1000 \implies \text{Subtract } 100\). Total = 1000 - 100 = 900.
- **'M' (1000) vs 'X' (10):** \(1000 \ge 10 \implies \text{Add } 1000\). Total = 900 + 1000 = 1900.
- **'X' (10) vs 'C' (100):** \(10 < 100 \implies \text{Subtract } 10\). Total = 1900 - 10 = 1890.
- **'C' (100) vs 'I' (1):** \(100 \ge 1 \implies \text{Add } 100\). Total = 1890 + 100 = 1990.
- **'I' (1) vs 'V' (5):** \(1 < 5 \implies \text{Subtract } 1\). Total = 1990 - 1 = 1989.
- **'V' (5) vs null:** Last character. Always **Add** 5. Total = 1989 + 5 = 1994.

The total value `1994` is calculated smoothly in a single pass.

---

## Partition Variables

Let:

```java
int total = 0;
int n = s.length();
```

---

### Border Elements

The lookahead index is guarded continuously to ensure the trailing final character is processed without throwing an out-of-bounds error:

```java
if (i + 1 < n && currentVal < getVal(s.charAt(i + 1))) {
    total -= currentVal;
} else {
    total += currentVal;
}
```

---

## Correct Partition Condition

The mapping routine handles character evaluations using a lightweight helper method:

```java
private int getVal(char c) {
    switch (c) {
        case 'I': return 1;
        case 'V': return 5;
        case 'X': return 10;
        case 'L': return 50;
        case 'C': return 100;
        case 'D': return 500;
        case 'M': return 1000;
        default: return 0;
    }
}
```

---

## Java Solution

```java
class Solution {

    public int romanToInt(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }

        int total = 0;
        int n = s.length();

        // Step 1: Scan the string from left to right
        for (int i = 0; i < n; i++) {
            int currentVal = getVal(s.charAt(i));

            // Step 2: Look ahead at the next adjacent character
            // If current value is smaller than the next value, subtract it
            if (i + 1 < n && currentVal < getVal(s.charAt(i + 1))) {
                total -= currentVal;
            } 
            // Otherwise, add it directly to our running total
            else {
                total += currentVal;
            }
        }

        return total;
    }

    private int getVal(char c) {
        switch (c) {
            case 'I': return 1;
            case 'V': return 5;
            case 'X': return 10;
            case 'L': return 50;
            case 'C': return 100;
            case 'D': return 500;
            case 'M': return 1000;
            default: return 0;
        }
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
O(n)
```

Where `n` is the total number of characters in the string `s`. The algorithm loops exactly `n` times, performing O(1) constant-time lookups and comparisons at each step.

---

### Space Complexity

```text
O(1)
```

The algorithm evaluates the string entirely in-place. No extra lookup tables, maps, or string buffer clones are allocated in memory.

---

## Key Insight

Comparing adjacent values rather than grouping full tokens turns a complex substring routing problem into a simple single-pass arithmetic step.

```text
Time  : O(n) linear execution pass
Space : O(1) absolute constant memory footprint
```

---

## Similar Problems

1. Integer to Roman (12)
2. Valid Parentheses (20)
3. Count and Say (38)
4. String to Integer / atoi (8)
5. Integer to English Words (273)
