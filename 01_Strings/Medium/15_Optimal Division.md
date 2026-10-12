# Optimal Division

## Problem Statement

You are given an integer array `nums`. The adjacent integers in `nums` will perform a float division. For example, `[2, 3, 4]` will evaluate to `2 / 3 / 4`.

You can add any number of parentheses at any position to change the priority of operations. You want to add parentheses such that the final value of the expression after evaluation is **maximized**.

Return *the corresponding expression as a string format*. Your expression should not contain redundant parentheses.

The overall runtime complexity must be strictly bounded by:

```text
O(n)
```

*(where n is the total number of elements inside the nums array)*

---

## Examples

### Example 1

**Input**

```java
nums = [1000, 100, 10, 2]
```

**Output**

```java
"1000/(100/10/2)"
```

**Explanation**

Let's look at the evaluation paths:
- `1000 / 100 / 10 / 2 = 0.5`
- `1000 / (100 / 10 / 2) = 1000 / (5) = 200` (Maximized!)
- `1000 / (100 / 10) / 2 = 1000 / 10 / 2 = 50`
- `(1000 / 100) / (10 / 2) = 10 / 5 = 2`

---

### Example 2

**Input**

```java
nums = [2, 3, 4]
```

**Output**

```java
"2/(3/4)"
```

---

### Example 3

**Input**

```java
nums = [2]
```

**Output**

```java
"2"
```

---

## Brute Force Approach

Use recursive backtracking or top-down memoized interval dynamic programming to check every possible placement of parentheses across all sub-segments, tracking both the minimum and maximum float results for each segment.

### Steps

1. Launch a recursive helper function `solve(start, end)` that returns a custom object containing both the minimum and maximum possible float values along with their string representations.
2. For each split boundary index `i` from `start` to `end - 1`:
   - Recursively evaluate the left partition `solve(start, i)`.
   - Recursively evaluate the right partition `solve(i + 1, end)`.
   - To maximize the current division chunk, compute `left.max / right.min`.
   - To minimize the current division chunk, compute `left.min / right.max`.
3. Wrap operations with parentheses at each combined block interface.
4. Extract the absolute maximum string from the root call tracking block.

### Complexity

```text
Time Complexity: O(n^3) // due to matrix length loop cuts over combinations
Space Complexity: O(n^2) to hold string matrix frames
```

While functional, treating this as a traditional combinatorial matrix problem adds massive, unneeded overhead. Because all elements in the input array are positive integers greater than or equal to 2, we can bypass calculations completely using a **greedy mathematical simplification**.

---

# Optimal Approach: Greedy Mathematical Grouping

## Key Idea

Any general sequence of division operations can be rewritten as a single global fraction containing a numerator and a denominator:

\[\frac{A}{B / C / D / \dots} = \frac{A \times C \times D \times \dots}{B}\]

Let's look at the behavior of elements:
1. **The First Element (`nums[0]`):** This element **must always remain in the numerator** because no parentheses can ever be placed to the left of the expression to force a division shift over it.
2. **The Second Element (`nums[1]`):** This element **must always remain in the denominator** because it directly divides the first element.
3. **Subsequent Elements (`nums[2], nums[3], \dots`):** Since all elements are ≥ 2, to make the total value as large as possible, we want to make the numerator as **large** as possible and the denominator as **small** as possible.

We can achieve both goals simultaneously by wrapping **all elements starting from index 1 to the end inside a single giant parenthesis pair**:

\[\text{Result} = \text{nums}[0] / (\text{nums}[1] / \text{nums}[2] / \dots / \text{nums}[n-1])\]

By nesting the rest of the sequence, every element from index 2 onwards gets flipped from the denominator up into the numerator (via multiplying by the inverse), maximizing our product instantly in a single pass.

---

## Visual Understanding

Suppose `nums = [1000, 100, 10, 2]`:

- **Without Parentheses:**
  \[\frac{1000}{100 \times 10 \times 2} = 0.5\]
- **Greedy Parentheses Wrapping at Index 1:**
  \[1000 / (100 / 10 / 2) = \frac{1000 \times 10 \times 2}{100} = 200\]

Every subsequent positive integer multiplies the numerator directly, giving us the maximum mathematical value possible.

---

## Partition Variables

Let:

```java
StringBuilder sb = new StringBuilder();
int n = nums.length;
```

---

### Border Elements

The algorithm handles short arrays directly as isolated structural conditions to avoid generating empty or corrupt parenthesis blocks:

```java
if (n == 1) return Integer.toString(nums[0]);
if (n == 2) return nums[0] + "/" + nums[1];
```

---

## Correct Partition Condition

The linear assembly format hooks up opening and closing bracket marks precisely around index 1 and the trailing final cell:

```java
sb.append(nums[0]).append("/(").append(nums[1]);
for (int i = 2; i < n; i++) {
    sb.append("/").append(nums[i]);
}
sb.append(")");
```

---

## Java Solution

```java
class Solution {

    public String optimalDivision(int[] nums) {
        if (nums == null || nums.length == 0) {
            return "";
        }

        int n = nums.length;

        // Base Case 1: Single element requires no operations
        if (n == 1) {
            return Integer.toString(nums[0]);
        }

        // Base Case 2: Two elements have only one immutable division interface
        if (n == 2) {
            return nums[0] + "/" + nums[1];
        }

        // Step 1: For n > 2, maximize value by wrapping index 1 to the end in parentheses
        StringBuilder sb = new StringBuilder();
        sb.append(nums[0]).append("/(").append(nums[1]);

        // Step 2: Append the remaining elements linearly
        for (int i = 2; i < n; i++) {
            sb.append("/").append(nums[i]);
        }

        // Step 3: Close the giant denominator parenthesis block
        sb.append(")");

        return sb.toString();
    }
}
```

---

## Complexity Analysis

### Complexity Metrics

| Operation | Time Complexity | Space Complexity | Reason |
| :--- | :--- | :--- | :--- |
| **Greedy Concat Pass** | **O(n)** | **O(n)** | Traverses the primitive array exactly once. The `StringBuilder` allocates memory blocks matching the character length of the final output string sequence. |

---

## Key Insight

Recognizing that wrapping the trailing denominator sequence inside a single parenthesis block moves all remaining elements into the numerator eliminates the need for expensive combinatorial tree searches, solving the expression in optimal linear time.

```text
Time  : O(n) single pass forward execution
Space : O(n) dynamic string output builder allocation
```

---

## Similar Problems

1. Decode String (394)
2. Score of Parentheses (856)
3. Multiply Strings (43)
4. Basic Calculator (224)
5. Fraction to Recurring Decimal (166)
