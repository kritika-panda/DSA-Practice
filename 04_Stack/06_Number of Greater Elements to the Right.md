# Number of Greater Elements to the Right ([LeetCode 496 Variant / GeeksforGeeks](https://geeksforgeeks.com))

## Problem Statement

Given an array of integers `arr` of size `N` and `Q` queries given in an array `indices`, for each query, determine the **total count of elements strictly greater than** `arr[indices[i]]` that appear **to its right** in the array.

Return *an array of answers representing the count for each respective query.*

---

## Examples

### Example 1

```text
Input: arr = [3, 4, 2, 7, 5, 8, 10, 6], queries = [0, 5, 1]
Output: [6, 1, 4]
```

### Explanation

```text
- Query 0 (index 0 -> value 3): Looking right of 3, the greater elements are 4, 7, 5, 8, 10, 6. Total count = 6.
- Query 1 (index 5 -> value 8): Looking right of 8, the only greater element is 10. Total count = 1.
- Query 2 (index 1 -> value 4): Looking right of 4, the greater elements are 7, 5, 8, 10, 6. Total count = 4.
```

---

### Example 2

```text
Input: arr = [1, 2, 3, 4], queries = [2, 3]
Output: [1, 0]
```

### Explanation

```text
- Query 0 (index 2 -> value 3): Looking right, only 4 is greater. Total count = 1.
- Query 1 (index 3 -> value 4): No elements exist to the right. Total count = 0.
```

---

# Intuition

Unlike the classic "Next Greater Element" problem which only seeks the *immediate first* larger element, this problem asks for the **total count of all greater elements** to the right. 

Because we need to collect cumulative frequency metrics rather than just tracking the nearest bottleneck obstacle, a standard Monotonic Stack cannot solve this in a single pass. Instead, this problem structurally maps to **Order Statistics / Range Queries**:
- If we process the array **backward (from right to left)**, we want to dynamically maintain a collection of all visited elements.
- When we reach an index `i`, we need to query our collection to count how many items are strictly greater than `arr[i]`.
- To achieve this efficiently, we can use a **Fenwick Tree (Binary Indexed Tree / BIT)** or a **Segment Tree** indexed by element values (after coordinate compression if values are large). 
- Alternatively, if the array sizes are bounded within moderate constraints (e.g., N, Q ≤ 10⁴), an optimized suffix scanning array pass or a Merge Sort tree handles the counts directly.

---

# Brute Force Solution

## Idea

For each query, extract the target index from the `queries` array. Run a nested forward loop starting exactly from `index + 1` up to `N - 1`, incrementing a counter variable every time an element `arr[j] > arr[index]` is encountered.

---

## Java Code

```java
class Solution {
    public static int[] countGreater(int[] arr, int[] queries) {
        int q = queries.length;
        int n = arr.length;
        int[] result = new int[q];

        // Process each query independently
        for (int i = 0; i < q; i++) {
            int targetIdx = queries[i];
            int targetValue = arr[targetIdx];
            int count = 0;

            // Scan everything to the right of targetIdx
            for (int j = targetIdx + 1; j < n; j++) {
                if (arr[j] > targetValue) {
                    count++;
                }
            }
            result[i] = count;
        }

        return result;
    }
}
```

---

## Complexity Analysis

- **Time Complexity:** \(O(Q \times N)\) where Q is the number of queries and N is the length of the array. This triggers TLE (Time Limit Exceeded) for large test environments.
- **Space Complexity:** O(1) auxiliary memory footprint.

---

# State Definition (Optimal Offline Approach)

If we process the queries **offline** (sorting queries based on their target indices from right to left), we can maintain a **Fenwick Tree (Binary Indexed Tree)** mapping element values to their frequencies as we sweep backward:

```text
int[] BIT = new int[MAX_VAL + 1]; // Fenwick tree to track frequency counts of elements seen so far
```

---

# Recurrence / Structural State Condition

```text
As we move index i from N-1 down to 0:
    1. Insert arr[i] into the Fenwick Tree: update(arr[i], 1)
    2. For any query anchored at index i:
       Total Greater Elements = total_elements_seen - query_prefix_sum(arr[i])
```

---

# Optimal Offline Fenwick Tree / Offline Solution

## Idea

By processing queries offline or using a precomputed suffix coordinate framework, we reduce the problem to counting frequencies. For a standard platform constraint where N ≤ 10⁴, we can precalculate an `ans` array where `ans[i]` stores the total number of elements greater than `arr[i]` to its right using a value-indexed array loop pass, allowing O(1) lookup per query.

---

## Java Code (Optimized Precomputation)

```java
class Solution {
    public static int[] countGreater(int[] arr, int[] queries) {
        int n = arr.length;
        int q = queries.length;
        
        // ans[i] will store the number of greater elements to the right of arr[i]
        int[] ans = new int[n];
        
        // Find the maximum value in the array to set up a value-frequency tracker
        // If values are up to 10^5, an array works; if larger, coordinate compression is required.
        int maxVal = 0;
        for (int num : arr) {
            maxVal = Math.max(maxVal, num);
        }
        
        // Frequency array to keep track of elements processed from right to left
        int[] freq = new int[maxVal + 2];
        
        // Process the array backward from right to left
        for (int i = n - 1; i >= 0; i--) {
            int currentVal = arr[i];
            
            // Count how many elements seen so far are strictly greater than currentVal
            int greaterCount = 0;
            for (int val = currentVal + 1; val <= maxVal; val++) {
                greaterCount += freq[val];
            }
            
            ans[i] = greaterCount;
            
            // Record the current element into our frequency tracker
            freq[currentVal]++;
        }
        
        // Map the precomputed index answers to the query slots
        int[] result = new int[q];
        for (int i = 0; i < q; i++) {
            result[i] = ans[queries[i]];
        }
        
        return result;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
Time Complexity: O(N × maxVal + Q) or O(N log(maxVal) + Q) if using a true Fenwick Tree
```
*Explanation:* The backward sweep runs N times. In each step, it checks the frequency thresholds. For standard interview constraints where element magnitudes are bounded, this executes significantly faster than the \(O(Q \times N)\) brute force loop. If a Fenwick Tree is paired with coordinate compression, the lookup becomes \(O(N \log N + Q)\), which is completely optimal.

---

### Space Complexity

```text
Space Complexity: O(N + maxVal)
```
*Explanation:* Auxiliary space scales linearly to allocate the `ans` array mapping table along with the value-indexed `freq` tracking collection bucket.

---

# Key Takeaways

✅ Advanced query optimization pattern merging backward array sweeps with value indexing.

✅ **Monotonic Stack vs. Range Query Identification:** Always look closely at the question metrics. If the question asks for the *nearest* boundary, a **Monotonic Stack** handles it in O(1). If it asks for the *total aggregate count/sum* of all matching criteria across an open horizon, pivot immediately to a **Fenwick Tree**, **Segment Tree**, or **Frequency Array precomputation**.

✅ Natively forms the foundation for algorithms tracking **inversion counts**, scheduling pipeline bottleneck metrics, and database analytical indexing optimization tasks.

✅ Common Architectural Paradigm:

```text
Retroactive Suffix Value Tracking for Open-Horizon Frequency Lookups
```

### Similar Problems

- Count of Smaller Numbers After Self ([LeetCode 315](https://leetcode.com))
- Next Greater Element I ([LeetCode 496](https://leetcode.com))
- Sum of Subarray Minimums ([LeetCode 907](https://leetcode.com))
- Online Stock Span ([LeetCode 901](https://leetcode.com))
