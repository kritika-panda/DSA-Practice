# Rearrange String k Distance Apart

## Problem Statement

Given a string `s` and an integer `k`, rearrange `s` such that the same characters are at least distance `k` from each other. If it is not possible to rearrange the string, return an empty string `""`.

The overall run time complexity should be:

```text
O(n * log(26)) -> O(n)
```

*(where n is the length of the string s)*

---

## Examples

### Example 1

**Input**

```java
s = "aabbcc"
k = 3
```

**Output**

```java
"abcabc"
```

**Explanation**

The same characters are exactly 3 positions away from each other, satisfying the minimum distance constraint `k = 3`. Another valid output is `"acbacb"`.

---

### Example 2

**Input**

```java
s = "aaabc"
k = 3
```

**Output**

```java
""
```

**Explanation**

It is impossible to separate the three `'a'` characters by a distance of 3 because there are only two other characters (`'b'` and `'c'`) available to act as fillers.

---

### Example 3

**Input**

```java
s = "aaadbbcc"
k = 2
```

**Output**

```java
"abacadbc"
```

**Explanation**

Every identical character pair maintains an index separation distance of at least 2.

---

## Brute Force Approach

Simulate character layout permutations recursively using a backtracking framework to verify every arrangement against the distance rule.

### Steps

1. Generate all unique permutations of the input string using backtracking.
2. For each permutation, iterate over its length to verify if any duplicate character pairs violate the index separation threshold `k`.
3. Return the first permutation that passes the distance verification step, or `""` if all combinations fail.

### Complexity

```text
Time Complexity: O(n!)
Space Complexity: O(n) for the recursive call stack
```

This combinatorial search scaling layer fails immediately for strings with many elements. The problem requires a more structured greedy management approach.

---

# Optimal Approach: Max-Heap with Cooldown Queue

## Key Idea

To satisfy the separation rule, we should always prioritizes scheduling the **most frequent characters** as early as possible. If we don't, they will bunch up at the end of the timeline and inevitably trigger adjacent collisions.

We achieve an optimized layout using a **Greedy Max-Heap** working alongside a **FIFO Cooldown Queue**:
1. Count the frequencies of all characters and offer them into a Max-Heap sorted strictly by count descending.
2. In each step, extract the highest frequency character from the Max-Heap, append it to a `StringBuilder`, and decrement its frequency count.
3. Instead of offering this character directly back into the Max-Heap, we push it into a **cooldown queue**. This holds the character temporary until the timeline moves forward by `k` steps.
4. When the cooldown queue reaches size `k`, the front element is released. If its remaining frequency is greater than 0, we push it back into the Max-Heap so it can be scheduled again.
5. If the Max-Heap becomes empty while the queue still holds valid, unscheduled characters, it means the timeline has broken down, making a valid arrangement impossible. We return `""`.

---

## Visual Understanding

Suppose `s = "aabbcc"` and `k = 3`:

1. **Build Frequency Track & Heap:**
   - Counts: `a -> 2`, `b -> 2`, `c -> 2`. Heap: `['a', 'b', 'c']` (tied counts).

2. **Simulation Timeline Progression:**
   - **Step 1:** Pop `'a'`. Result: `"a"`. Wait Queue: `[('a', count:1)]`.
   - **Step 2:** Pop `'b'`. Result: `"ab"`. Wait Queue: `[('a', 1), ('b', 1)]`.
   - **Step 3:** Pop `'c'`. Result: `"abc"`. Wait Queue hits size `k = 3`. Release front `'a'`. Remaining count is 1 \(\implies\) offer back to Heap. Heap becomes: `['a']`. Queue holds `[('b', 1), ('c', 1)]`.
   - **Step 4:** Pop `'a'`. Result: `"abca"`. Release front `'b'`. Re-offer to Heap. Heap: `['b']`. Queue holds `[('c', 1), ('a', 0)]`.
   - **Step 5:** Pop `'b'`. Result: `"abcab"`. Release front `'c'`. Re-offer to Heap. Heap: `['c']`. Queue holds `[('a', 0), ('b', 0)]`.
   - **Step 6:** Pop `'c'`. Result: `"abcabc"`. 

The total string completes successfully with no boundary collisions.

---

## Partition Variables

Let:

```java
int[] counts = new int;
PriorityQueue<Integer> maxHeap = new PriorityQueue<>((a, b) -> counts[b] - counts[a]);
Queue<Integer> cooldownQueue = new LinkedList<>();
```

---

### Border Elements

The cooldown queue functions as a strict sliding threshold fence, releasing characters only when they clear the window span `k`:

```java
if (cooldownQueue.size() >= k) {
    int releasedChar = cooldownQueue.poll();
    if (counts[releasedChar] > 0) maxHeap.offer(releasedChar);
}
```

---

## Correct Partition Condition

If the greedy heap structure drops to an empty state while active character blocks are still locked in cooldown intervals, the layout constraint fails:

```java
if (maxHeap.isEmpty()) {
    return ""; // Search space exhausted due to separation gaps
}
```

---

## How to Move Binary Search

*(Note: This optimal strategy drops traditional binary range partition splits in favor of a Max-Heap Priority Queue coupled with a sliding FIFO cooldown queue to achieve stable linear runtimes).*

---

## Java Solution

```java
import java.util.LinkedList;
import java.util.PriorityQueue;
import java.util.Queue;

class Solution {

    public String rearrangeString(String s, int k) {
        if (s == null || s.length() == 0) {
            return "";
        }
        // If k is 0 or 1, the original order can never violate the distance rules
        if (k <= 1) {
            return s;
        }

        // Step 1: Record character frequencies
        int[] counts = new int;
        for (int i = 0; i < s.length(); i++) {
            counts[s.charAt(i) - 'a']++;
        }

        // Step 2: Push all active character indexes into a Max-Heap sorted by frequency
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>((a, b) -> 
            counts[b] - counts[a]
        );

        for (int i = 0; i < 26; i++) {
            if (counts[i] > 0) {
                maxHeap.offer(i);
            }
        }

        StringBuilder sb = new StringBuilder();
        Queue<Integer> cooldownQueue = new LinkedList<>();

        // Step 3: Greedily consume character tokens following a cooldown wait timeline
        while (!maxHeap.isEmpty()) {
            int currentCharIdx = maxHeap.poll();
            
            // Append selected character element
            sb.append((char) (currentCharIdx + 'a'));
            counts[currentCharIdx]--;

            // Buffer the current character into a temporary waiting queue
            cooldownQueue.offer(currentCharIdx);

            // Once the queue satisfies distance window k, release the oldest item back to the heap
            if (cooldownQueue.size() >= k) {
                int releasedCharIdx = cooldownQueue.poll();
                if (counts[releasedCharIdx] > 0) {
                    maxHeap.offer(releasedCharIdx);
                }
            }
        }

        // If the resulting text matches the source array length, the arrangement is valid
        return sb.length() == s.length() ? sb.toString() : "";
    }
}
```

---

## Dry Run

### Input

```java
s = "aaabc"; k = 3
```

---

### Step Execution Traversal

- **Initialization Map:** `counts['a'-'a'] = 3`, `counts['b'-'a'] = 1`, `counts['c'-'a'] = 1`. Heap = `['a', 'b', 'c']`.
- **Timeline Loop Progression:**
  - **Loop 1:** Pops `'a'`. `sb = "a"`. `counts['a'-'a'] = 2`. `cooldownQueue = ['a']`. Size \(1 < 3\).
  - **Loop 2:** Pops `'b'`. `sb = "ab"`. `counts['b'-'a'] = 0`. `cooldownQueue = ['a', 'b']`. Size \(2 < 3\).
  - **Loop 3:** Pops `'c'`. `sb = "abc"`. `counts['c'-'a'] = 0`. `cooldownQueue = ['a', 'b', 'c']`.
    - Size hits \(3 \ge 3\). `cooldownQueue.poll()` releases `'a'`. `counts['a'-'a'] = 2 > 0`, so `'a'` is re-offered. Heap = `['a']`.
  - **Loop 4:** Pops `'a'`. `sb = "abca"`. `counts['a'-'a'] = 1`. `cooldownQueue = ['b', 'c', 'a']`.
    - Size hits 3. `cooldownQueue.poll()` releases `'b'`. `counts['b'-'a'] = 0`, so it is not re-offered.
    - Heap is now completely empty.
- **Loop Terminates:** Main loop exits because `maxHeap.isEmpty()` is true.
- **Length Check:** `sb.length() (4) != s.length() (5)`. Returns `""`.

---

### Answer

```java
""
```

---

## Why Is the Time Complexity Bounded Linearly?

The algorithm parses the string input data once to configure tallies in \(O(n)\). The greedy selection pipeline executes exactly `n` times. During each step, insertion and deletion adjustments inside the Priority Queue scale relative to the finite uppercase alphabet size (\(\log 26\)), effectively running in constant time \(O(1)\).

This bounded pipeline processing yields:

```text
O(n)
```

which satisfies the optimal complexity constraint perfectly.

---

## Complexity Analysis

### Time Complexity

```text
O(n)
```

Where `n` is the length of string `s`. Populating character tracking elements runs in linear time. The structural heap adjustments do not exceed \(\log(26)\) operations per step, locking the timeline loop safely within an overall \(O(n)\) scale.

---

### Space Complexity

```text
O(1)
```

