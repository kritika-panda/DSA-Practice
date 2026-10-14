# Assign Cookies (LeetCode 455)

## Problem Statement

Assume you are an awesome parent and want to give your children some cookies.

Each child `i` has a greed factor `g[i]`, which is the minimum size of a cookie that will satisfy the child.

Each cookie `j` has a size `s[j]`.

If:

```text
s[j] >= g[i]
```

then the child is satisfied.

Each child can receive at most one cookie, and each cookie can be assigned to at most one child.

Return the maximum number of children that can be satisfied.

---

## Examples

### Example 1

```java
Input: g = [1,2,3], s = [1,1]
Output: 1
```

### Explanation

```text
Child greed : [1,2,3]
Cookie size : [1,1]

Cookie 1 → Child 1 ✅
Cookie 1 → Cannot satisfy Child 2 ❌

Satisfied children = 1
```

---

### Example 2

```java
Input: g = [1,2], s = [1,2,3]
Output: 2
```

### Explanation

```text
Cookie 1 → Child 1 ✅
Cookie 2 → Child 2 ✅

Satisfied children = 2
```

---

# Intuition

To maximize the number of satisfied children:

- Give the smallest cookie possible to the least greedy child.
- Save larger cookies for greedier children.
- If a cookie is too small for the current child, it cannot satisfy any larger greed factor either.

Therefore:

1. Sort greed factors.
2. Sort cookie sizes.
3. Use two pointers to greedily assign cookies.

---

# Greedy Observation

After sorting:

```text
Children : 1 2 3
Cookies  : 1 1 3
```

Assign cookies from smallest to largest.

```text
Child(1) ← Cookie(1) ✅
Child(2) ← Cookie(1) ❌
Child(2) ← Cookie(3) ✅
```

Total satisfied:

```text
2
```

Using larger cookies earlier would only waste resources.

---

# Approach: Sorting + Two Pointers

## Algorithm

1. Sort `g`.
2. Sort `s`.
3. Maintain:
   - `i` → current child
   - `j` → current cookie
4. If cookie can satisfy child:
   - Assign cookie.
   - Move both pointers.
5. Otherwise:
   - Cookie is too small.
   - Move cookie pointer only.
6. Number of satisfied children = `i`.

---

# Dry Run

```java
g = [1,2,3]
s = [1,1,3]
```

After sorting:

```text
g = [1,2,3]
s = [1,1,3]
```

### Step 1

```text
Child = 1
Cookie = 1

1 >= 1 ✅

Satisfied = 1
```

Move:

```text
i = 1
j = 1
```

---

### Step 2

```text
Child = 2
Cookie = 1

1 >= 2 ❌
```

Move cookie pointer:

```text
j = 2
```

---

### Step 3

```text
Child = 2
Cookie = 3

3 >= 2 ✅
```

Move both:

```text
i = 2
j = 3
```

Loop ends.

Answer:

```text
2
```

---

# Java Solution

```java
import java.util.Arrays;

class Solution {
    public int findContentChildren(int[] g, int[] s) {

        Arrays.sort(g);
        Arrays.sort(s);

        int i = 0; // child pointer
        int j = 0; // cookie pointer

        while (i < g.length && j < s.length) {

            if (s[j] >= g[i]) {
                i++;
            }

            j++;
        }

        return i;
    }
}
```

---

# Optimized Understanding

Whenever:

```text
s[j] >= g[i]
```

we successfully satisfy a child and move to the next child.

Whenever:

```text
s[j] < g[i]
```

the cookie is too small.

Since greed factors are sorted:

```text
Current child <= Future child
```

this cookie cannot satisfy any future child either.

Therefore we discard it.

This greedy decision is always optimal.

---

# Correctness Proof

### Greedy Choice

We always assign the smallest available cookie that can satisfy the current least greedy child.

Suppose we assign a larger cookie instead.

```text
Small Cookie -> Unused
Large Cookie -> Used
```

This can only reduce options for greedier children later.

Therefore using the smallest valid cookie is always optimal.

---

### Optimal Substructure

After satisfying one child optimally:

```text
Remaining children
Remaining cookies
```

form the same problem.

Thus the greedy solution repeatedly produces an optimal result.

---

# Complexity Analysis

Let:

```text
n = g.length
m = s.length
```

### Time Complexity

Sorting:

```text
O(n log n) + O(m log m)
```

Traversal:

```text
O(n + m)
```

Overall:

```text
O(n log n + m log m)
```

---

### Space Complexity

```text
O(1)
```

Ignoring the internal space used by sorting.

---

# Key Pattern

This problem belongs to:

```text
Greedy + Sorting + Two Pointers
```

Common indicators:

- Match two arrays optimally
- Maximize satisfied entities
- Assign resources efficiently
- Use smallest valid resource first

---

# Similar Problems

1. Boats to Save People
2. Activity Selection
3. Non-overlapping Intervals
4. Maximum Matching
5. Minimum Number of Arrows to Burst Balloons
6. Maximize Number of Tasks Assigned
7. Meeting Rooms Scheduling

---

# Takeaway

```text
Sort both arrays.

Always assign the smallest cookie that can satisfy
the least greedy child.

Use two pointers to process both arrays efficiently.
```

**Pattern:** Greedy + Sorting + Two Pointers
