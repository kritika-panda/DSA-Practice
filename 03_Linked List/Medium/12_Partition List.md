# Partition List

## Problem Statement

Given the head of a linked list and an integer `x`, partition the list such that:

- All nodes with values **less than x** come before nodes with values **greater than or equal to x**.
- The **relative order** of nodes in each partition must be preserved.

Return the head of the partitioned linked list.

**LeetCode:** 86. Partition List

---

## Example 1

### Input

```text
head = [1,4,3,2,5,2]
x = 3
```

### Output

```text
[1,2,2,4,3,5]
```

### Visualization

```text
Original:
1 -> 4 -> 3 -> 2 -> 5 -> 2

Less than 3:
1 -> 2 -> 2

Greater than or equal to 3:
4 -> 3 -> 5

Merged:
1 -> 2 -> 2 -> 4 -> 3 -> 5
```

---

## Example 2

### Input

```text
head = [2,1]
x = 2
```

### Output

```text
[1,2]
```

---

# Approach: Two Separate Lists

## Idea

Create two linked lists:

1. Nodes with values `< x`
2. Nodes with values `>= x`

While traversing:

- Append nodes to the appropriate list.
- Preserve original ordering.
- Connect both lists at the end.

---

## Algorithm

1. Create dummy nodes:
   - `beforeDummy`
   - `afterDummy`
2. Traverse the original list.
3. If node value is less than `x`:
   - Add to `before` list.
4. Otherwise:
   - Add to `after` list.
5. Connect:
   - `before` list → `after` list.
6. Return `beforeDummy.next`.

---

# Java Solution

```java
class Solution {

    public ListNode partition(ListNode head, int x) {

        ListNode beforeDummy = new ListNode(0);
        ListNode afterDummy = new ListNode(0);

        ListNode before = beforeDummy;
        ListNode after = afterDummy;

        while (head != null) {

            if (head.val < x) {
                before.next = head;
                before = before.next;
            } else {
                after.next = head;
                after = after.next;
            }

            head = head.next;
        }

        after.next = null;

        before.next = afterDummy.next;

        return beforeDummy.next;
    }
}
```

---

# Dry Run

### Input

```text
head = 1 -> 4 -> 3 -> 2 -> 5 -> 2
x = 3
```

### Step 1

```text
before: 1
after :
4 -> 3
```

### Step 2

```text
before: 1 -> 2
after :
4 -> 3
```

### Step 3

```text
before: 1 -> 2
after :
4 -> 3 -> 5
```

### Step 4

```text
before: 1 -> 2 -> 2
after :
4 -> 3 -> 5
```

### Connect

```text
1 -> 2 -> 2 -> 4 -> 3 -> 5
```

---

# Why Does This Preserve Order?

Consider:

```text
1 -> 4 -> 3 -> 2 -> 5 -> 2
```

Nodes less than 3 appear as:

```text
1, 2, 2
```

They are appended to the `before` list in the same order they are encountered.

Similarly:

```text
4, 3, 5
```

remain in the same relative order.

Thus stability is maintained.

---

# Correctness Proof

Let:

```text
L = nodes having value < x
R = nodes having value >= x
```

During traversal:

- Every node is placed into exactly one of the two lists.
- Nodes are appended in encounter order.
- Therefore relative order inside each partition is preserved.

Finally:

```text
L + R
```

creates a list where:

- Every node in `L` precedes every node in `R`.
- Internal ordering remains unchanged.

Hence the algorithm is correct.

---

# Complexity Analysis

### Time Complexity

```text
O(N)
```

Each node is visited exactly once.

---

### Space Complexity

```text
O(1)
```

Only a few pointers are used.

---

# Alternative Approach

Using an array:

1. Store nodes/values in a list.
2. Separate values into two arrays.
3. Rebuild the linked list.

### Complexity

```text
Time  : O(N)
Space : O(N)
```

Not preferred because extra memory is required.

---

# Edge Cases

### Single Node

```text
[1], x = 2

Output:
[1]
```

### All Nodes Less Than x

```text
1 -> 1 -> 2
x = 5

Output:
1 -> 1 -> 2
```

### All Nodes Greater Than or Equal to x

```text
5 -> 6 -> 7
x = 3

Output:
5 -> 6 -> 7
```

### Duplicate Values

```text
1 -> 4 -> 3 -> 2 -> 3 -> 2
x = 3

Output:
1 -> 2 -> 2 -> 4 -> 3 -> 3
```

Order remains preserved.

---

# Key Interview Points

- Stable Partitioning
- Linked List Manipulation
- Dummy Node Technique
- Preserve Relative Order
- O(N) Time
- O(1) Extra Space
- Two-List Construction Pattern
