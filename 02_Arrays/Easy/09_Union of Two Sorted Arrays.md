# Union of Two Sorted Arrays

## Problem Statement

Given two sorted arrays `a[]` and `b[]`, return the union of the arrays in sorted order.

The union of two arrays is a collection of all distinct elements present in either of the arrays.

### Example 1

**Input:**
```java
a = [1, 2, 3, 4, 5]
b = [1, 2, 3, 6, 7]
```

**Output:**
```java
[1, 2, 3, 4, 5, 6, 7]
```

### Example 2

**Input:**
```java
a = [1, 1, 2, 3, 4]
b = [2, 2, 4, 5]
```

**Output:**
```java
[1, 2, 3, 4, 5]
```

---

## Intuition

Since both arrays are already sorted, we can use the Merge Process of Merge Sort.

- Compare elements from both arrays.
- Add the smaller element to the result if it is not already present.
- Move the corresponding pointer forward.
- Handle duplicates by ensuring the last inserted element is different.

This gives a sorted union efficiently.

---

## Approach (Two Pointers)

1. Initialize pointers:
   - `i = 0` for array `a`
   - `j = 0` for array `b`
2. Compare `a[i]` and `b[j]`.
3. Insert the smaller element into the answer if it is not already present.
4. If both elements are equal:
   - Insert only once.
   - Move both pointers.
5. Process remaining elements of both arrays.
6. Return the result.

---

## Dry Run

### Input

```java
a = [1, 1, 2, 3, 4]
b = [2, 2, 4, 5]
```

| i | j | a[i] | b[j] | Result |
|---|---|------|------|---------|
| 0 | 0 | 1 | 2 | [1] |
| 1 | 0 | 1 | 2 | [1] |
| 2 | 0 | 2 | 2 | [1,2] |
| 3 | 1 | 3 | 2 | [1,2] |
| 3 | 2 | 3 | 4 | [1,2,3] |
| 4 | 2 | 4 | 4 | [1,2,3,4] |
| - | 3 | - | 5 | [1,2,3,4,5] |

### Final Output

```java
[1,2,3,4,5]
```

---

## Java Solution (Optimal)

```java
import java.util.*;

class Solution {
    public static ArrayList<Integer> findUnion(int a[], int b[]) {
        int n = a.length;
        int m = b.length;

        int i = 0, j = 0;

        ArrayList<Integer> union = new ArrayList<>();

        while (i < n && j < m) {

            if (a[i] <= b[j]) {
                if (union.isEmpty() || union.get(union.size() - 1) != a[i]) {
                    union.add(a[i]);
                }
                i++;
            } else {
                if (union.isEmpty() || union.get(union.size() - 1) != b[j]) {
                    union.add(b[j]);
                }
                j++;
            }
        }

        while (i < n) {
            if (union.isEmpty() || union.get(union.size() - 1) != a[i]) {
                union.add(a[i]);
            }
            i++;
        }

        while (j < m) {
            if (union.isEmpty() || union.get(union.size() - 1) != b[j]) {
                union.add(b[j]);
            }
            j++;
        }

        return union;
    }
}
```

---

## Alternative Approach (Using HashSet)

### Idea

- Add elements from both arrays into a `HashSet`.
- Convert the set into a list.
- Sort the list.

### Java Code

```java
import java.util.*;

class Solution {
    public static ArrayList<Integer> findUnion(int a[], int b[]) {

        HashSet<Integer> set = new HashSet<>();

        for (int num : a) {
            set.add(num);
        }

        for (int num : b) {
            set.add(num);
        }

        ArrayList<Integer> result = new ArrayList<>(set);
        Collections.sort(result);

        return result;
    }
}
```

---

## Complexity Analysis

### Two Pointer Approach

- **Time Complexity:** `O(n + m)`
- **Space Complexity:** `O(n + m)` (union array)

### HashSet Approach

- **Time Complexity:** `O((n + m) + k log k)`
- **Space Complexity:** `O(n + m)`

where `k` is the number of unique elements.

---

## Why Two Pointer Approach is Optimal?

Since the arrays are already sorted:

- No extra sorting is required.
- Each element is processed exactly once.
- Better than the HashSet approach due to the absence of sorting overhead.

---

## Key Takeaways

- Sorted arrays suggest using the two-pointer technique.
- Similar to the merge step of Merge Sort.
- Avoid duplicates by checking the last inserted element.
- Optimal solution runs in `O(n + m)` time.
- Frequently asked in coding interviews and is a foundation for array merging problems.
