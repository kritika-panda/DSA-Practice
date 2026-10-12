# Fizz Buzz

## Problem Statement

Given an integer `n`, return *a string array `answer` (**1-indexed**) where*:
- `answer[i] == "FizzBuzz"` if `i` is divisible by `3` and `5`.
- `answer[i] == "Fizz"` if `i` is divisible by `3`.
- `answer[i] == "Buzz"` if `i` is divisible by `5`.
- `answer[i] == String.valueOf(i)` if none of the above conditions are true.

The overall runtime complexity must be:

```text
O(n)
```

---

## Examples

### Example 1

**Input**

```java
n = 3
```

**Output**

```java
["1", "2", "Fizz"]
```

---

### Example 2

**Input**

```java
n = 5
```

**Output**

```java
["1", "2", "Fizz", "4", "Buzz"]
```

---

### Example 3

**Input**

```java
n = 15
```

**Output**

```java
["1", "2", "Fizz", "4", "Buzz", "Fizz", "7", "8", "Fizz", "Buzz", "11", "Fizz", "13", "14", "FizzBuzz"]
```

---

## Brute Force Approach

Iterate from `1` to `n` and use a standard conditional sequence to check each divisibility rule sequentially using modulo arithmetic.

### Steps

1. Initialize an empty list of strings.
2. Run a loop from `i = 1` up to `n`.
3. Check conditions in a specific order:
   - If `i % 3 == 0 && i % 5 == 0`, add `"FizzBuzz"`.
   - Else if `i % 3 == 0`, add `"Fizz"`.
   - Else if `i % 5 == 0`, add `"Buzz"`.
   - Else, add `String.valueOf(i)`.
4. Return the list.

### Complexity

```text
Time Complexity: O(n)
Space Complexity: O(1) // excluding the output list memory
```

While this linear pass runs in optimal time, explicitly writing out composite modulo gates (`i % 3 == 0 && i % 5 == 0`) forces redundant calculations per cycle. We can clean this structural dependency up by using **string concatenation**.

---

# Optimal Approach: String Concatenation Pattern

## Key Idea

Instead of performing separate modulo operations for individual conditions and combined cases, we can construct the output strings dynamically by **concatenating words**. 

This pattern scales gracefully if extra divisibility parameters are added later (e.g., Jazz for 7, Fuzz for 11):
1. Create an expandable `ArrayList<String>`.
2. Loop through numbers `i` from `1` up to `n`.
3. Initialize an empty temporary string `currentStr = ""` for the current iteration loop.
4. **Divisible by 3 Check:** If `i % 3 == 0`, append `"Fizz"` to `currentStr`.
5. **Divisible by 5 Check:** If `i % 5 == 0`, append `"Buzz"` to `currentStr`.
6. **The Default Check:** If `currentStr` remains completely empty after both checks, it means the number is divisible by neither. Convert the integer `i` to a string directly.
7. Append the completed `currentStr` token to the results list.

---

## Visual Understanding

Suppose `n = 15` and we evaluate the iteration `i = 15`:

- Initialize: `currentStr = ""`.
- **Condition 1:** `15 % 3 == 0` is true \(\implies\) `currentStr += "Fizz"` \(\implies\) `currentStr = "Fizz"`.
- **Condition 2:** `15 % 5 == 0` is true \(\implies\) `currentStr += "Buzz"` \(\implies\) `currentStr = "FizzBuzz"`.
- **Condition 3:** `currentStr` is not empty, so the default number assignment is bypassed.

The output token is evaluated as `"FizzBuzz"` smoothly without evaluating a dedicated combined conditional block.

---

## Partition Variables

Let:

```java
List<String> ans = new ArrayList<>(n); // Pre-sizing saves dynamic array reallocation costs
```

---

### Border Elements

The range loop initializes directly from index `1` to eliminate 0-index offset checks:

```java
for (int i = 1; i <= n; i++)
```

---

## Correct Partition Condition

The concatenation framework appends text fragments dynamically:

```java
String numStr = "";
if (i % 3 == 0) numStr += "Fizz";
if (i % 5 == 0) numStr += "Buzz";
if (numStr.isEmpty()) numStr += i;
ans.add(numStr);
```

---

## Java Solution

```java
import java.util.ArrayList;
import java.util.List;

class Solution {

    public List<String> fizzBuzz(int n) {
        // Pre-size the array list to avoid intermediate internal array reallocations
        List<String> ans = new ArrayList<>(n);

        // Linear sweep from 1 up to n
        for (int i = 1; i <= n; i++) {
            StringBuilder sb = new StringBuilder();

            // Step 1: If divisible by 3, append Fizz
            if (i % 3 == 0) {
                sb.append("Fizz");
            }
            
            // Step 2: If divisible by 5, append Buzz
            if (i % 5 == 0) {
                sb.append("Buzz");
            }

            // Step 3: If no conditions match, fall back to the integer string value
            if (sb.length() == 0) {
                sb.append(i);
            }

            ans.add(sb.toString());
        }

        return ans;
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Linear Evaluation Pass** | **O(n)** | **O(1)** | Loops through `n` numbers exactly once. The auxiliary space complexity is constant O(1) excluding the required memory to allocate the output string collection list. |

---

## Key Insight

Constructing the token stream via additive appending avoids redundant logical checks, streamlining sequential modulo tracking paths cleanly.

```text
Time  : O(n) linear execution pass
Space : O(1) absolute constant extra memory footprint
```

---

## Similar Problems

1. Fizz Buzz Multithreaded (1195)
2. Add Binary (67)
3. Count and Say (38)
4. Length of Last Word (58)
5. Divisible and Non-divisible Sums Difference (2894)
