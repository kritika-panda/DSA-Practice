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
class Solution {
    public List<String> fizzBuzz(int n) {
        List<String> res=new ArrayList<>();
        for(int i=0;i<n;i++)
        {
            if((i+1) % 3 == 0 && (i+1) % 5 == 0)
                res.add("FizzBuzz");
            else if((i+1) % 3 == 0)
                res.add("Fizz");
            else if((i+1) % 5 == 0)
                res.add("Buzz");
            else
                res.add(String.valueOf(i+1));
        }
        return res;
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

# Auxiliary Space (Excluding Output)
 
Inside the loop, the only temporary object created is:
 
```java
String.valueOf(i + 1)
```
 
For the largest number `n`, the generated string contains:
 
```text
O(log n)
```
 
digits.
 
Since only one such temporary string exists at a time, the extra space required is:
 
```text
O(log n)
```
 
---
 
## Space Used by the Result List
 
```java
List<String> res = new ArrayList<>();
```
 
The list stores `n` string references.
 
### Constant-Size Strings
 
The following string literals are reused from the String Pool:
 
```java
"Fizz"
"Buzz"
"FizzBuzz"
```
 
They have constant size and do not create new string objects each time.
 
Contribution:
 
```text
O(1)
```
 
per entry.
 
### Numeric Strings
 
For numbers that are not divisible by 3 or 5:
 
```java
String.valueOf(i + 1)
```
 
creates strings such as:
 
```text
"1", "2", "3", ..., "n"
```
 
Each numeric string requires:
 
```text
O(log n)
```

# Why is `String.valueOf(i + 1)` O(log n) Space?

The reason is tied to the **number of digits required to represent a number**.

---

## Number of Digits in a Number

For a positive integer `n`, the number of digits is:

```text
⌊log₁₀(n)⌋ + 1
```

Examples:

| n | Digits | log₁₀(n) |
|---|---|---|
| 9 | 1 | 0.95 |
| 99 | 2 | 1.99 |
| 999 | 3 | 2.99 |
| 9999 | 4 | 3.99 |
| 99999 | 5 | 4.99 |

As `n` grows, the number of digits grows proportionally to:

```text
log₁₀(n)
```

In Big-O notation, the logarithm base is ignored:

```text
O(log n)
```

---

## Example

```java
String.valueOf(1234567)
```

Java creates a string containing:

```text
['1', '2', '3', '4', '5', '6', '7']
```

Number of characters:

```text
7
```

Since:

```text
1234567 ≈ 10⁷
```

the amount of memory required is proportional to:

```text
log n
```

Therefore:

```text
Space Complexity = O(log n)
```

---

## Why Not O(1)?

The string size is not constant.

Examples:

```java
String.valueOf(9);        // 1 character
String.valueOf(99);       // 2 characters
String.valueOf(999);      // 3 characters
String.valueOf(999999);   // 6 characters
```

As the value of `n` increases, more digits are needed.

Therefore:

```text
Space(String.valueOf(n)) = O(log n)
```

and

```text
Time(String.valueOf(n)) = O(log n)
```

because Java must process each digit.

---

## Applying This to FizzBuzz

Consider:

```java
for (int i = 0; i < n; i++) {
    res.add(String.valueOf(i + 1));
}
```

During any single iteration:

```java
String.valueOf(i + 1)
```

creates at most one temporary string.

The largest temporary string can contain:

```text
O(log n)
```

characters.

Since only one temporary string exists at a time:

```text
Auxiliary Space = O(log n)
```

---

## Important Observation

Many developers mistakenly think:

```text
n iterations × O(log n) space
= O(n log n) auxiliary space
```

This is incorrect because the temporary string created in one iteration is discarded before the next iteration.

Only one temporary string exists at a time.

Therefore:

```text
Auxiliary Space = O(log n)
```

not

```text
O(n log n)
```

---

## Interview Perspective

Most interviewers would simply write:

```text
Auxiliary Space = O(1)
```

because:

- The loop variable `i` uses constant space.
- Temporary strings are short-lived.
- Output space is typically excluded from complexity analysis.

However, a mathematically precise analysis is:

```text
Temporary numeric string = O(log n)
Auxiliary Space = O(log n)
```

---

## Final Takeaway

```text
Number of Digits in n = O(log n)
```

Therefore:

```text
String.valueOf(n)
```

requires:

```text
Time Complexity  = O(log n)
Space Complexity = O(log n)
```

And in the FizzBuzz solution:

```text
Auxiliary Space = O(log n)
```

because only one such temporary string exists at any moment.
 
characters.
 
Across all generated numeric strings, the total number of stored characters is:
 
```text
Θ(n log n)
```
 
---
 
## Interview / LeetCode Convention
 
When discussing space complexity, the returned output is usually excluded.
 
Therefore:
 
```text
Auxiliary Space = O(log n)
```
 
Some interviewers may simplify this to:
 
```text
O(1)
```
 
since the temporary string exists only for a single iteration.
 
---
 
## Total Space Complexity (Including Output)
 
The result list stores:
 
- `n` references → `O(n)`
- String contents → `Θ(n log n)`
 
Therefore:
 
```text
Total Space = O(n log n)
```
 
---
 
## Complexity Summary
 
| Metric | Complexity |
|----------|----------|
| Time Complexity | O(n log n) |
| Auxiliary Space (excluding output) | O(log n) |
| Space Complexity (including output) | O(n log n) |
 
---
 
## Final Answer
 
### Interviewer's Answer
 
```text
Time Complexity: O(n log n)
Auxiliary Space: O(1) (or O(log n) if counting the temporary numeric string)
```
 
### Strict Total Memory Usage
 
```text
Time Complexity: O(n log n)
Space Complexity: O(n log n)
```
 
The dominant memory usage comes from storing the generated strings in the returned `List<String>`.
Provide your feedback on BizChat
Make it more concise

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
