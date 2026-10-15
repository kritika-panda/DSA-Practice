# Print Longest Increasing Subsequence (LIS)

## Problem Statement

Given an integer array `nums`, return the actual **Longest Increasing Subsequence (LIS)**.

A subsequence is derived by deleting some or no elements without changing the order of the remaining elements.

---

## Approach (DP + Parent Tracking)

To print the LIS:

1. Use `dp[i]` to store the length of the LIS ending at index `i`.
2. Use `parent[i]` to store the previous index contributing to the LIS.
3. Track the index where the maximum LIS ends.
4. Backtrack using the parent array.
5. Reverse the result to obtain the correct order.

---

## Java Code

```java
import java.util.*;

public class Main {

    p*blic static List<Integer> printLIS*int[] nums) {
        int n = nums*length;

        int[] dp = new in*[n];
        int[] parent = new in*[n];

        Arrays.fill(dp, 1);
*        int maxLen = 1;
        in* lastIndex = 0;

        for (int * = 0; i < n; i++) {
            pa*ent[i] = i;

            for (int * = 0; j < i; j++) {

             *  if (nums[j] < nums[i] &&
       *            dp[j] + 1 > dp[i]) {

*                   dp[i] = dp[j] +*1;
                    parent[i] =*j;
                }
            }*
            if (dp[i] > maxLen) {*                maxLen = dp[i];
  *             lastIndex = i;
      *     }
        }

        List<Int*ger> lis = new ArrayList<>();

   *    while (parent[lastIndex] != la*tIndex) {
            lis.add(nums*lastIndex]);
            lastIndex*= parent[lastIndex];
        }

  *     lis.add(nums[lastIndex]);

  *     Collections.reverse(lis);

  *     return lis;
    }

    public*static void main(String[] args) {
*       int[] nums = {10, 9, 2, 5, *, 7, 101, 18};

        System.out*println(printLIS(nums));
    }
}
`*`

---

## Dry Run

### Input

```*ext
[10, 9, 2, 5, 3, 7, 101, 18]
`*`

### DP Array After Processing

*``text
Index:  0 1 2 3 4 5 6 7
Val*e: 10 9 2 5 3 7 101 18

DP:     1 * 1 2 2 3 4 4
```

### Parent Array*
```text
Index : 0 1 2 3 4 5 6 7
P*rent: 0 1 2 2 2 3 5 5
```

### Bac*tracking

```text
101 ← 7 ← 5 ← 2
*``

Reverse it:

```text
[2, 5, 7,*101]
```

---

## Output

```text
*2, 5, 7, 101]
```

---

## Why Par*nt Array?

The DP array only tells*us the length of the LIS.

Example*

```text
DP = [1, 1, 1, 2, 2, 3, *, 4]
```

From this we know:

```t*xt
LIS Length = 4
```

But we do n*t know which elements form the LIS*

The `parent[]` array helps recon*truct the actual sequence.

---

#* Time Complexity

```text
O(N²)
``*

Nested loop over all pairs of in*ices.

---

## Space Complexity

`*`text
O(N)
```

For:

- `dp[]`
- `*arent[]`
- Result list

---

# Opt*mal O(N log N) Follow-Up

Interviewers often ask:

> Can we print the LIS in O(N log N)?

Yes.

Maintain:

- `tails[]` → stores indices of smallest possible tail values.
- `parent[]` → stores predecessor indices.
- Use Binary Search to determine where the current element should be placed.

### Complexities

```text
Time Complexity: O(N log N)
Space Complexity: O(N)
```

This is the most optimal solution for printing the actual LIS.
