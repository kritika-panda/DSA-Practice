# The Celebrity Problem ([GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

A **celebrity** is a person who is known to everyone but does not know anyone at the party. You are given a square 2D matrix `M[][]` of size `N x n`, where `M[i][j] = 1` indicates that the `i`-th person knows the `j`-th person, and `M[i][j] = 0` indicates they do not know them. 

Find if there is a celebrity at the party. If a celebrity is present, return their **0-indexed ID**. If there is no celebrity, return `-1`.

**Notes:**
- A celebrity cannot know themselves, so `M[i][i]` will always be `0`.
- There can be at most **one** celebrity present at the party.

The overall runtime complexity must be strictly bounded by:

```text
O(N)
```

*(where N is the number of people at the party)*

---

## Examples

### Example 1

```java
Input: M = [
  [0, 1, 0],
  [0, 0, 0],
  [0, 1, 0]
]
Output: 1
```

### Explanation

```text
- Person 0 knows Person 1, but doesn't know Person 2.
- Person 1 knows no one (row 1 contains all 0s).
- Person 2 knows Person 1, but doesn't know Person 0.
Person 1 is known by everyone (0 and 2) and knows no one. Thus, Person 1 is the celebrity.
```

---

### Example 2

```java
Input: M = [
  [0, 1],
  [1, 0]
]
Output: -1
```

### Explanation

```text
Person 0 knows Person 1, and Person 1 knows Person 0. 
There is no person who knows no one. Hence, no celebrity exists.
```

---

# Intuition

A naive approach checks every person against everyone else using a nested loop, costing an inefficient O(N²) runtime.

To solve this problem in optimal **linear time**, we can use a **Two-Pointer** elimination strategy or a **Stack**:
- **The Elimination Rule:** If Person `A` knows Person `B` (`M[A][B] == 1`), then `A` **cannot** be the celebrity (since celebrities know no one). However, `B` *might* be a celebrity. Conversely, if `A` does not know `B` (`M[A][B] == 0`), then `B` **cannot** be the celebrity (since everyone must know the celebrity).
- This means every time we compare two people, we can decisively eliminate one of them from being the celebrity candidate!
- By maintaining a `left` pointer at index `0` and a `right` pointer at index `N - 1`, we can slide them inward until they meet at a single remaining index. This index is our **celebrity candidate**.
- **The Validation Step:** Since the elimination loop only tells us who *could* be a celebrity, we must run a quick linear verification pass to confirm that the candidate actually knows no one and is known by everyone.

---

# Brute Force Solution

## Idea

For each person `i` from `0` to `N - 1`, we scan their row to verify they know no one, and scan their column to verify everyone else knows them. If a person satisfies both conditions perfectly, we return their ID.

---

## Java Code

```java
class Solution {
    public int celebrity(int[][] M) {
        int n = M.length;

        for (int i = 0; i < n; i++) {
            boolean isCelebrity = true;

            for (int j = 0; j < n; j++) {
                if (i != j) {
                    // Condition 1: If i knows j, i cannot be a celebrity
                    // Condition 2: If j does not know i, i cannot be a celebrity
                    if (M[i][j] == 1 || M[j][i] == 0) {
                        isCelebrity = false;
                        break;
                    }
                }
            }

            if (isCelebrity) {
                return i; // Celebrity found
            }
        }

        return -1; // No celebrity exists
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** O(N²) due to the nested loops verifying column and row conditions for all nodes.
- **Space Complexity:** O(1) auxiliary memory footprint.

---

# State Definition (Optimal Approach)

To optimize our performance to an O(N) linear track, we track bounding boundaries using two elimination pointers:

```java
int left = 0;          // Pointer tracking the head of the candidate pool
int right = n - 1;     // Pointer tracking the tail of the candidate pool
```

---

# Recurrence / Structural State Condition

```java
while (left < right) {
    if (M[left][right] == 1) {
        // left knows right -> left is eliminated, move left forward
        left++;
    } else {
        // left does not know right -> right is eliminated, move right backward
        right--;
    }
}
```

---

# Optimal Two-Pointer Elimination Solution

## Idea

We set up `left = 0` and `right = n - 1`. If `M[left][right] == 1`, then `left` cannot be a celebrity, so we increment `left`. Otherwise, `right` cannot be a celebrity, so we decrement `right`. When the pointers meet, the remaining element `left` is our final candidate. We run a quick loop to verify that this candidate's row consists of all `0`s and their column consists of all `1`s (except for the self-loop index).

---

## Java Code

```java
class Solution {
    public int celebrity(int[][] M) {
        if (M == null || M.length == 0) {
            return -1;
        }

        int n = M.length;
        int left = 0;
        int right = n - 1;

        // Step 1: Eliminate candidates lineally until only one remains
        while (left < right) {
            if (M[left][right] == 1) {
                // 'left' knows 'right' -> 'left' cannot be a celebrity
                left++;
            } else {
                // 'left' does not know 'right' -> 'right' cannot be a celebrity
                right--;
            }
        }

        int candidate = left;

        // Step 2: Validate the remaining celebrity candidate
        for (int i = 0; i < n; i++) {
            if (i != candidate) {
                // Celebrity must know NO ONE (M[candidate][i] must be 0)
                // EVERYONE must know the celebrity (M[i][candidate] must be 1)
                if (M[candidate][i] == 1 || M[i][candidate] == 0) {
                    return -1; // Candidate failed verification checks
                }
            }
        }

        return candidate;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N)
```
*Explanation:* The candidate elimination phase runs in O(N) operations since every single comparison increments `left` or decrements `right`, discarding exactly one element. The subsequent verification loop executes a single pass over N items, keeping total execution runtime bounded strictly linearly.

---

### Space Complexity

```text
Space Complexity: O(1)
```
*Explanation:* Highly optimized. It replaces explicit matrix or stack collections with two primitive tracking index pointers, ensuring an absolute constant space complexity.

---

# Key Takeaways

✅ Core fundamental paradigm for matrix elimination tracking using structural graph dependencies.

✅ **The Stack De-escalation Alternative:** This problem can also be solved inside identical O(N) time and space boundaries using a **LIFO Stack**. You push all N people onto the stack, pop the top two elements to compare them, discard the invalid one, and push the surviving candidate back onto the stack. This cycle repeats until only one candidate remains. However, the Two-Pointer method is structurally superior as it drops the memory cost down to a constant O(1) tier.

✅ Directly models real-world configurations like **trusted authority router identification layers** inside distributed networks, leader election sorting pools, graph dependency trackers, and anomaly index filters.

✅ Common Architectural Paradigm:

```text
Bilateral Property Elimination for Singular Invariant Lookups
```

### Similar Problems

- Find the Town Judge ([LeetCode 997](https://leetcode.com))
- Majority Element ([LeetCode 169](https://leetcode.com))
- Container With Most Water ([LeetCode 11](https://leetcode.com))
- Next Greater Element I ([LeetCode 496](https://leetcode.com))
