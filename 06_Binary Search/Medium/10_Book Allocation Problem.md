# Book Allocation Problem

## Problem Statement

You are given:

- An array `books[]`, where `books[i]` represents the number of pages in the `i-th` book.
- An integer `m` representing the number of students.

The task is to allocate books to students such that:

1. Each student receives at least one book.
2. A book can be allocated to only one student.
3. Books must be allocated in contiguous order.
4. The goal is to minimize the maximum number of pages assigned to any student.

Return the minimum possible value of the maximum pages assigned to a student.

If it is not possible to allocate books to all students, return `-1`.

---

## Examples

### Example 1

**Input**

```java
books = [12, 34, 67, 90]
m = 2
```

**Output**

```java
113
```

**Explanation**

Possible allocation:

```java
Student 1 -> [12, 34, 67]
Student 2 -> [90]
```

Pages assigned:

```text
113, 90
```

Maximum pages:

```text
113
```

No other valid allocation gives a smaller maximum.

---

### Example 2

**Input**

```java
books = [15, 17, 20]
m = 2
```

**Output**

```java
32
```

**Explanation**

Optimal allocation:

```java
Student 1 -> [15, 17]
Student 2 -> [20]
```

Maximum pages:

```text
32
```

---

### Example 3

**Input**

```java
books = [10, 20, 30]
m = 4
```

**Output**

```java
-1
```

**Explanation**

There are more students than books.

Since each student must receive at least one book, allocation is impossible.

---

# Intuition

We need to:

```text
Minimize the maximum pages assigned to any student.
```

This is a classic:

```text
Minimize the Maximum
```

problem.

Whenever a problem asks for:

```text
Minimum possible maximum
or
Maximum possible minimum
```

Binary Search on Answer is often the solution.

---

## Search Space

### Minimum Possible Answer

A student must read at least the largest book.

```java
low = max(books)
```

---

### Maximum Possible Answer

One student reads all books.

```java
high = sum(books)
```

---

## Key Observation

Suppose we guess:

```java
mid
```

as the maximum pages a student can receive.

Can we allocate all books using at most `m` students such that no student receives more than `mid` pages?

### If Yes

```text
Try a smaller answer.
```

---

### If No

```text
Need a larger answer.
```

This monotonic property allows Binary Search.

---

# Feasibility Check

For a given limit:

```java
mid
```

Assign books greedily.

Keep adding books to the current student.

If adding a book exceeds:

```java
mid
```

allocate that book to the next student.

Count how many students are needed.

---

## Example

```java
books = [12,34,67,90]
mid = 113
```

### Student 1

```java
12 + 34 + 67 = 113
```

### Student 2

```java
90
```

Students needed:

```java
2
```

Valid allocation.

---

# Binary Search Solution

```java
class Solution {

    public int findPages(int[] books, int m) {

        int n = books.length;

        if (m > n) {
            return -1;
        }

        int low = 0;
        int high = 0;

        for (int pages : books) {
            low = Math.max(low, pages);
            high += pages;
        }

        int answer = high;

        while (low <= high) {

            int mid = low + (high - low) / 2;

            if (canAllocate(books, m, mid)) {
                answer = mid;
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }

        return answer;
    }

    private boolean canAllocate(int[] books, int students, int maxPages) {

        int countStudents = 1;
        int currentPages = 0;

        for (int pages : books) {

            if (currentPages + pages <= maxPages) {

                currentPages += pages;

            } else {

                countStudents++;
                currentPages = pages;
            }
        }

        return countStudents <= students;
    }
}
```

---

# Dry Run

### Input

```java
books = [12, 34, 67, 90]
m = 2
```

### Search Space

```java
low  = 90
high = 203
```

---

### mid = 146

Allocation:

```java
[12,34,67] = 113
[90] = 90
```

Students needed:

```text
2
```

Valid.

```java
answer = 146
high = 145
```

---

### mid = 117

Allocation:

```java
[12,34,67] = 113
[90] = 90
```

Students:

```text
2
```

Valid.

```java
answer = 117
high = 116
```

---

### mid = 103

Allocation:

```java
[12,34] = 46
[67]
[90]
```

Students:

```text
3
```

Invalid.

```java
low = 104
```

---

Continue Binary Search until:

```java
answer = 113
```

---

# Why Greedy Works?

For a fixed limit:

```java
maxPages = mid
```

The best strategy is to keep assigning consecutive books to the current student until adding another book would exceed the limit.

This guarantees the minimum number of students required for that limit.

---

# Complexity Analysis

Let:

```java
n = number of books
S = sum of all pages
```

### Time Complexity

Binary Search:

```text
O(log S)
```

Feasibility Check:

```text
O(n)
```

Overall:

```text
O(n log S)
```

---

### Space Complexity

```text
O(1)
```

Only a few variables are used.

---

# Difference Between Book Allocation and Painter's Partition

| Feature | Book Allocation | Painter's Partition |
|----------|----------|----------|
| Items | Books | Boards |
| Workers | Students | Painters |
| Contiguous Allocation | Yes | Yes |
| Goal | Minimize maximum pages | Minimize maximum painting time |
| Technique | Binary Search on Answer | Binary Search on Answer |

In fact, both problems are solved using the exact same pattern.

---

# Pattern Recognition

Problems similar to Book Allocation:

1. Allocate Minimum Number of Pages
2. Painter's Partition Problem
3. Split Array Largest Sum (LC 410)
4. Capacity To Ship Packages Within D Days (LC 1011)
5. Divide Chocolate
6. Minimum Limit of Balls in a Bag

---

## Key Insight

```text
Answer = Minimum Possible Maximum Pages Assigned
```

Search Space:

```java
[max(books), sum(books)]
```

Use Binary Search to find the smallest page limit for which allocation is possible.

```text
Time  : O(n log(sum))
Space : O(1)
```
