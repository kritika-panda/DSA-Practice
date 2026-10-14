# Lemonade Change (LeetCode 860)

## Problem Statement

At a lemonade stand, each lemonade costs `$5`. Customers are standing in a queue to buy from you and order one at a time. Each customer will only buy one lemonade and pay with either a `$5`, `$10`, or `$20` bill. You must provide the correct change to each customer so that the net transaction is exactly `$5`.

Note that you do not have any change in hand at first.

Given an integer array `bills` where `bills[i]` is the bill the `i`-th customer pays, return `true` *if you can provide every customer with the correct change, or `false` otherwise*.

---

## Examples

### Example 1

```java
Input: bills = [5,5,5,10,20]
Output: true
```

### Explanation

```text
Customer bills: [5,5,5,10,20]

Customer 1 pays $5  → No change needed. Register: [$5] ✅
Customer 2 pays $5  → No change needed. Register: [$5, $5] ✅
Customer 3 pays $5  → No change needed. Register: [$5, $5, $5] ✅
Customer 4 pays $10 → Give $5 change.   Register: [$5, $5, $10] ✅
Customer 5 pays $20 → Give $10 + $5.    Register: [$5, $20] ✅

All customers served = true
```

---

### Example 2

```java
Input: bills = [5,5,10,10,20]
Output: false
```

### Explanation

```text
Customer 1 pays $5  → Register: [$5] ✅
Customer 2 pays $5  → Register: [$5, $5] ✅
Customer 3 pays $10 → Give $5 change. Register: [$5, $10] ✅
Customer 4 pays $10 → Give $5 change. Register: [$10, $10] ✅
Customer 5 pays $20 → Need $15 change. We have two $10 bills but zero $5 bills. ❌

Served all customers = false
```

---

# Intuition

To maximize our ability to serve all subsequent customers:

- We never need to track `$20` bills because they can never be used to provide change.
- A `$5` bill is highly versatile because it can serve as change for both a `$10` customer and a `$20` customer.
- A `$10` bill is restrictive because it can only help with a `$20` customer.

Therefore:

1. Prioritize using a `$10` bill and a `$5` bill when a customer pays with a `$20` bill.
2. Save our highly flexible `$5` bills whenever possible.
3. If we don't have a `$10` bill to give for a `$20` bill, only then fall back to giving three `$5` bills.

---

# Greedy Observation

Maintain counts of `$5` and `$10` bills sequentially as transactions occur.

```text
Bill Paid : $5 -> Collect it ($5 count++)
Bill Paid : $10 -> Must give 1x $5 ($5 count--, $10 count++)
Bill Paid : $20 -> Must give $15 change
```

When giving change for `$20`:

```text
Choice A: 1x $10 + 1x $5 (Greedy Choice)
Choice B: 3x $5          (Fallback Choice)
```

Using a `$10` bill earlier saves two `$5` bills for the future, which maximizes flexibility.

---

# Approach: Cash Register Simulation

## Algorithm

1. Initialize `five = 0` and `ten = 0`.
2. Iterate through each `bill` in `bills`.
3. If `bill == 5`:
   - Increment `five`.
4. If `bill == 10`:
   - If `five == 0`, return `false`.
   - Otherwise, decrement `five` and increment `ten`.
5. If `bill == 20`:
   - If `ten > 0` and `five > 0`, give one `$10` and one `$5` (decrement both `ten` and `five`).
   - Otherwise, if `five >= 3`, give three `$5` bills (decrement `five` by 3).
   - Otherwise, return `false`.
6. If the loop completes successfully, return `true`.

---

# Dry Run

```java
bills = [5,5,5,10,20]
```

### Initial State

```text
five = 0
ten = 0
```

---

### Step 1

```text
Customer = 5
five++ -> five = 1
```

---

### Step 2

```text
Customer = 5
five++ -> five = 2
```

---

### Step 3

```text
Customer = 5
five++ -> five = 3
```

---

### Step 4

```text
Customer = 10
five > 0 ? ✅ (five = 3)

five-- -> five = 2
ten++  -> ten = 1
```

---

### Step 5

```text
Customer = 20
ten > 0 && five > 0 ? ✅ (ten = 1, five = 2)

ten--  -> ten = 0
five-- -> five = 1
```

Loop ends.

Answer:

```text
true
```

---

# Java Solution

```java
class Solution {
    public boolean lemonadeChange(int[] bills) {
        int five = 0;
        int ten = 0;

        for (int bill : bills) {
            if (bill == 5) {
                five++;
            } else if (bill == 10) {
                if (five == 0) {
                    return false;
                }
                five--;
                ten++;
            } else { // bill == 20
                // Greedy choice: use $10 bill first
                if (ten > 0 && five > 0) {
                    ten--;
                    five--;
                } else if (five >= 3) {
                    five -= 3;
                } else {
                    return false;
                }
            }
        }

        return true;
    }
}
```

---

# Optimized Understanding

Whenever:

```text
bill == 10
```

we have no choice but to use a `$5` bill. If we have none, the chain breaks immediately.

Whenever:

```text
bill == 20
```

we have a structural choice between `10 + 5` or `5 + 5 + 5`.

Since a `$5` bill can satisfy more future conditions than a `$10` bill:

```text
Utility ($5 bill) > Utility ($10 bill)
```

spending the `$10` bill first protects our more valuable, universally adaptive resource.

This greedy decision is always optimal.

---

# Correctness Proof

### Greedy Choice

We always prioritize discharging the largest possible denomination (`$10`) when returning change for a `$20` bill.

Suppose we used three `$5` bills instead while holding a `$10` bill.

```text
1x $10 -> Unused (Retained in cash register)
2x $5  -> Unused (Lost from cash register)
```

If the very next customer pays with a `$10` bill, we can no longer provide change because the retained `$10` bill is useless to a `$10` customer.

Therefore, using the `$10` bill for a `$20` transaction is always optimal.

---

### Optimal Substructure

After serving a customer successfully and greedily managing the change:

```text
Remaining cash register state (five, ten)
Remaining customer queue
```

form the same problem.

Thus the greedy solution repeatedly produces an optimal result.

---

# Complexity Analysis

Let:

```text
n = bills.length
```

### Time Complexity

Traversal:

```text
O(n)
```

Overall:

```text
O(n)
```

---

### Space Complexity

```text
O(1)
```

Only two scalar counter variables are maintained.

---

# Key Pattern

This problem belongs to:

```text
Greedy + Cash Register Simulation
```

Common indicators:

- Sequential transaction log processing
- Fixed, low-variety currency assets
- Choice between adaptive vs. restricted resource depletion
- Real-time safety validation

---

# Similar Problems

1. Task Scheduler
2. Gas Station
3. Assign Cookies
4. Walking Robot Simulation
5. Queue Reconstruction by Height
6. Minimize Cash Flow among Friends
7. Broken Calculator

---

# Takeaway

```text
Simulate register transactions linearly.

Always prioritize spending the restrictive \$10 bill
over the versatile \$5 bill when giving \$15 change.

Track counts using O(1) space variables without lookback.
```

**Pattern:** Greedy + Cash Register Simulation
