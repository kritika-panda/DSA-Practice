# Climbing Stairs: Dynamic Programming Evolution

This guide breaks down the progression of solving the **Climbing Stairs** problem using Dynamic Programming, tracking the exact engineering evolution from **Top-Down Memoization** to **Bottom-Up Tabulation**, and finally down to the **Absolute Space-Optimized** pattern.

---

## 1. Top-Down Dynamic Programming (Memoization)

### Key Idea
Instead of calculating identical step states repeatedly over an expensive recursive tree, we keep the intuitive structure of top-down recursion intact but wrap it with an array cache (lookup dictionary). Before executing any child branches for a given step, we check our cache. If a cache hit occurs, we return the stored result instantly, avoiding redundant branches.

### Java Solution
```java
class Solution {
    public int climbStairs(int n) {
        // Create an array cache initialized to 0 to store calculated steps
        int[] memo = new int[n + 1];
        return memoize(n, memo);
    }

    private int memoize(int step, int[] memo) {
        // Base Cases: 1 way to stay on step 1, 2 ways to reach step 2
        if (step <= 2) {
            return step;
        }

        // Intercept Check: Return cached state instantly if previously solved
        if (memo[step] != 0) {
            return memo[step];
        }

        // Compute, log into cache memory, and return the state value
        memo[step] = memoize(step - 1, memo) + memoize(step - 2, memo);
        return memo[step];
    }
}
```

### Complexity Analysis
- **Time Complexity:** **O(n)** – Each step value from 1 to `n` is calculated exactly once.
- **Space Complexity:** **O(n)** – Requires `O(n)` memory slots for the tracking cache array, plus `O(n)` memory overhead for the recursive execution call stack frames.

---

## 2. Bottom-Up Dynamic Programming (Tabulation)

### Key Idea
We can optimize this further by completely eliminating recursion and its associated call stack overhead. Since the state transitions build forward chronologically, we change the direction: we instantiate a flat 1D state array table, pre-populate our hardcoded base case constants at indices 1 and 2, and use a standard iterative loop to push calculations forward up to the target index `n`.

### Java Solution
```java
class Solution {
    public int climbStairs(int n) {
        // Base Case: Short staircases require no iteration checks
        if (n <= 2) {
            return n;
        }

        // Step 1: Pre-allocate a dynamic 1D state array table
        int[] dp = new int[n + 1];

        // Step 2: Establish known base case constants
        dp[1] = 1;
        dp[2] = 2;

        // Step 3: Run a forward loop to fill the table sequentially
        for (int i = 3; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }

        return dp[n];
    }
}
```

### Complexity Analysis
- **Time Complexity:** **O(n)** – A clean, single iterative loop pass from 3 to `n`.
- **Space Complexity:** **O(n)** – Allocates an array of size `n + 1` on the heap space, but reduces recursive stack frames down to absolute constant safety.

---

## 3. Absolute Space Optimization (Rolling Variables)

### Key Idea
If you analyze the tabulation recurrence relation equation `dp[i] = dp[i - 1] + dp[i - 2]`, an essential optimization pattern reveals itself: to calculate the unique ways for the current step, the state machine only looks at the **immediate two preceding values**. 

All deeper historical elements stored further back in the array are dead weight. Therefore, we can discard the array structure completely and track the sliding lookahead window using just two local primitive variable registers (`prev2` and `prev1`), collapsing the auxiliary space requirement down to an absolute constant footprint.

### Java Solution
```java
class Solution {
    public int climbStairs(int n) {
        // Base Case: Clamps execution on short stairs before starting shifts
        if (n <= 2) {
            return n;
        }

        // Step 1: Initialize local registers matching step 1 and step 2 states
        int prev2 = 1; // Represents ways to reach step (i - 2)
        int prev1 = 2; // Represents ways to reach step (i - 1)

        // Step 2: Roll variables forward inside a single linear tracking pass
        for (int i = 3; i <= n; i++) {
            int curr = prev1 + prev2;
            
            // Shift the lookahead window values forward for the next column cycle
            prev2 = prev1;
            prev1 = curr;
        }

        return prev1;
    }
}
```

### Complexity Analysis
- **Time Complexity:** **O(n)** – Runs a single iterative sequence track up to `n`.
- **Space Complexity:** **O(1)** – Uses absolute constant memory space. The execution runs entirely in-place within a few highly optimized primitive processor registers.

---

## Architectural Summary

| Strategy | Time Complexity | Space Complexity | Risk / Tradeoff |
| :--- | :--- | :--- | :--- |
| **Memoization** | O(n) | O(n) | Incurs a theoretical threat of a `StackOverflowError` if `n` scales extremely deep. |
| **Tabulation** | O(n) | O(n) | Stack-safe, but consumes a linear amount of heap memory for the lookup array. |
| **Optimized** | **O(n)** | **O(1)** | **Production-grade Best Practice**. Maximizes execution speed while maintaining near-zero memory footprint. |
