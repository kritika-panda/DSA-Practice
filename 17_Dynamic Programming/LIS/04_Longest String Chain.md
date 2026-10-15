# Longest String Chain

## Problem Statement

You are given an array of words.

A word `A` is a predecessor of word `B` if:

```text
B can be formed by inserting exactly one character into A
without changing the order of the remaining characters.
```

Return the length of the longest possible word chain.

---

## Example 1

```text
Input:
["a","b","ba","bca","bda","bdca"]

Output:
4
```

### Chain

```text
a → ba → bda → bdca
```

Length = 4

---

## Example 2

```text
Input:
["xbc","pcxbcf","xb","cxbc","pcxbc"]

Output:
5
```

### Chain

```text
xb
↓
xbc
↓
cxbc
↓
pcxbc
↓
pcxbcf
```

Length = 5

---

# Intuition

This problem is a variation of **Longest Increasing Subsequence (LIS)**.

Instead of checking:

```text
nums[j] < nums[i]
```

we check:

```text
word[j] is predecessor of word[i]
```

Since every valid successor has exactly one extra character:

```text
Sort words by length
```

and build the chain from smaller words to larger words.

---

# Key Observation

For two words:

```text
abc
abac
```

`abc` is a predecessor of `abac` because:

```text
Insert 'a'
```

Similarly:

```text
bda
bdca
```

Insert:

```text
c
```

So:

```text
Length difference must be exactly 1
```

and all remaining characters should match.

---

# Predecessor Check Function

We check whether:

```text
shorter → longer
```

is valid.

### Example

```text
abc
abdc
```

Use two pointers.

```text
abc
|| |
abdc
```

One mismatch is allowed.

---

## Java Function

```java
private static boolean isPredecessor(String s1, String s2) {

    if (s2.length() != s1.length() + 1)
        return false;

    int i = 0;
    int j = 0;

    while (j < s2.length()) {

        if (i < s1.length() &&
            s1.charAt(i) == s2.charAt(j)) {

            i++;
            j++;
        } else {
            j++;
        }
    }

    return i == s1.length();
}
```

---

# DP Approach

Let:

```java
dp[i]
```

= longest chain ending at word `i`.

Transition:

```java
if(isPredecessor(words[j], words[i]))
{
    dp[i] = max(dp[i], dp[j] + 1);
}
```

Exactly similar to LIS.

---

# Java Code

```java
import java.util.*;

public class Main {

    privat* static boolean isPredecessor(Stri*g s1, String s2) {

        if (s2*length() != s1.length() + 1)
     *      return false;

        int i*= 0;
        int j = 0;

        w*ile (j < s2.length()) {

         *  if (i < s1.length() &&
         *      s1.charAt(i) == s2.charAt(j)* {

                i++;
         *      j++;
            } else {
  *             j++;
            }
        }

        return i == s1.length();
    }

    public static int longestStrChain(String[] words) {

        Arrays.sort(words,
                (a, b) -> a.length() - b.length());

        int n = words.length;

        int[] dp = new int[n];
        Arrays.fill(dp, 1);

        int ans = 1;

        for (int i = 0; i < n; i++) {

            for (int j = 0; j < i; j++) {

                if (isPredecessor(words[j], words[i]) &&
                    dp[j] + 1 > dp[i]) {

                    dp[i] = dp[j] + 1;
                }
            }

            ans = Math.max(ans, dp[i]);
        }

        return ans;
    }

    public static void main(String[] args) {

        String[] words =
            {"a","b","ba","bca","bda","bdca"};

        System.out.println(longestStrChain(words));
    }
}
```

---

# Dry Run

### Input

```text
["a","b","ba","bca","bda","bdca"]
```

### After Sorting by Length

```text
a
b
ba
bca
bda
bdca
```

---

### DP Values

| Word | Chain Length |
|--------|--------|
| a | 1 |
| b | 1 |
| ba | 2 |
| bca | 3 |
| bda | 3 |
| bdca | 4 |

```text
dp = [1,1,2,3,3,4]
```

Answer:

```text
4
```

---

# Visualization

```text
a
|
ba
|
bda
|
bdca
```

Longest chain length:

```text
4
```

---

# Time Complexity

Sorting:

```text
O(N log N)
```

DP:

```text
O(N²)
```

Predecessor Check:

```text
O(L)
```

where `L` = maximum word length.

Total:

```text
O(N² × L)
```

---

# Space Complexity

```text
O(N)
```

For the DP array.

---

# Pattern Recognition

This problem belongs to the **LIS Pattern** family.

```text
LIS
│
├── Longest Increasing Subsequence
├── Print LIS
├── Largest Divisible Subset
├── Longest String Chain ✅
└── Longest Bitonic Subsequence
```

### Interview Trick

Whenever you see:

```text
Find longest sequence/subset/chain
```

and

```text
Current state can be formed from previous state
```

think:

```text
Sort
+
LIS DP
+
Custom Comparison Function
```

This is exactly what Longest String Chain uses.
