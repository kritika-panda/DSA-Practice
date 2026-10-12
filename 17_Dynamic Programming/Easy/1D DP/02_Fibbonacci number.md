# Fibonacci Number: Dynamic Programming Evolution

This guide breaks down the progression of solving the **Fibonacci Number** problem using Dynamic Programming, tracking the exact engineering evolution from **Top-Down Memoization** to **Bottom-Up Tabulation**, and finally down to the **Absolute Space-Optimized** pattern.

---

## 1. Top-Down Dynamic Programming (Memoization)

### Key Idea
The naive recursive solution for Fibonacci builds an exponential call tree because it continuously recomputes the same sub-Fibonacci numbers. We can intercept these redundant paths by introducing a linear memoization cache array. Before splitting into subproblems for any number `n`, we query our cache array; if a valid precomputed state is present, we return it instantly.

### Java Solution
```java
class Solution {
    public int fib(int n) {
        // Base case shortcut to prevent empty array generation
        if (n <= 1) {
            return n;
        }
        
        // Create an array cache initialized to 0 to store calculated states
        int[] memo = new int[n + 1];
        return memoize(n, memo);
    }

    private int memoize(int n, int[] memo) {
        // Base Cases
        if (n <= 1) {
            return n;
        }

        // Intercept Check: Return cached state instantly if previously solved
        if (memo[n] != 0) {
            return memo[n];
        }

        // Compute, log into cache memory, and return the state value
        memo[n] = memoize(n - 1, memo) + memoize(n - 2, memo);
        return memo[n];
    }
}
```

### Complexity Analysis
- **Time Complexity:** **O(n)** – Each discrete Fibonacci state from 0 to `n` is computed exactly once.
- **Space Complexity:** **O(n)** – Requires `O(n)` memory slots for the cache tracker array, plus `O(n)` call stack frames on the recursion engine.

---

## 2. Bottom-Up Dynamic Programming (Tabulation)

### Key Idea
We can transition to an iterative, stack-safe architecture by evaluating the problem from the bottom up. Instead of starting from `n` and working backward, we instantiate a flat 1D state array table, hardcode our known base values at indices 0 and 1, and run a forward loop to fill out each consecutive slot using the values calculated in the previous columns.

### Java Solution
```java
class Solution {
    public int fib(int n) {
        // Base Cases: Short inputs require no matrix evaluation loops
        if (n <= 1) {
            return n;
        }

        // Step 1: Pre-allocate a dynamic 1D state array table
        int[] dp = new int[n + 1];

        // Step 2: Establish known base case constants
        dp[0] = 0;
        dp[1] = 1;

        // Step 3: Run a forward loop to fill the table sequentially
        for (int i = 2; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }

        return dp[n];
    }
}
```

### Complexity Analysis
- **Time Complexity:** **O(n)** – A clear, single iterative loop pass from 2 to `n`.
- **Space Complexity:** **O(n)** – Eliminates recursive call stack overhead completely, but allocates an array of size `n + 1` on the heap memory space.

---

## 3. Absolute Space Optimization (Rolling Variables)

### Key Idea
By analyzing the recurrence relation `dp[i] = dp[i - 1] + dp[i - 2]`, we see that the state table only reads the values of the **immediate two preceding elements** to compute the current value. 

All historical columns computed further back are completely abandoned. We can discard the entire array structure and track the lookahead state using only two primitive integer variable registers (`prev2` and `prev1`), collapsing the auxiliary space footprint to a true zero-allocation bottleneck.

### Java Solution
```java
class Solution {
    public int fib(int n) {
        // Base Cases: Intercept short inputs before launching register shifts
        if (n <= 1) {
            return n;
        }

        // Step 1: Initialize local registers matching F(0) and F(1) states
        int prev2 = 0; // Represents state (i - 2) -> F(0)
        int prev1 = 1; // Represents state (i - 1) -> F(1)

        // Step 2: Roll variables forward inside a single linear tracking pass
        for (int i = 2; i <= n; i++) {
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
- **Space Complexity:** **O(1)** – Absolute constant memory footprint. The calculation executes entirely in-place within a fixed pair of primitive local registers.

---

## Architectural Summary

| Strategy | Time Complexity | Space Complexity | Risk / Tradeoff |
| :--- | :--- | :--- | :--- |
| **Memoization** | O(n) | O(n) | Heavy stack allocation overhead carries a risk of a `StackOverflowError` for deep values. |
| **Tabulation** | O(n) | O(n) | High stack safety, but consumes linear heap memory to store intermediate results. |
| **Optimized** | **O(n)** | **O(1)** | **Production-grade Best Practice**. Maximizes execution speed with near-zero memory footprint. |
