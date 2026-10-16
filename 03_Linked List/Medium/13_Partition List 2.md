# Partition List II

## Problem Statement

Given the head of a linked list and a value `x`, rearrange the list such that:

- All nodes with values less than `x` come first.
- Followed by nodes with values equal to `x`.
- Followed by nodes with values greater than `x`.

The relative order of nodes within each partition should remain unchanged.

Return the head of the modified linked list.

---

## Example

### Input

```text
head = [3,5,8,5,10,2,1]
x = 5
```

### Output

```text
[3,2,1,5,5,8,10]
```

### Explanation

```text
Original:
3 -> 5 -> 8 -> 5 -> 10 -> 2 -> 1

Less than 5:
3 -> 2 -> 1

Equal to 5:
5 -> 5

Greater than 5:
8 -> 10

Combined:
3 -> 2 -> 1 -> 5 -> 5 -> 8 -> 10
```

---

# Approach: Three Separate Lists

## Idea

Maintain three linked lists:

1. Less than x
2. Equal to x
3. Greater than x

Traverse the original list once and place each node into the appropriate list.

Finally connect:

```text
Less -> Equal -> Greater
```

This preserves relative ordering within each partition.

---

## Algorithm

1. Create three dummy nodes:
   - lessDummy
   - equalDummy
   - greaterDummy

2. Traverse the list.
   - If value < x → add to less list.
   - If value == x → add to equal list.
   - If value > x → add to greater list.

3. Terminate greater list.

4. Connect:
   - less → equal
   - equal → greater

5. Return the appropriate head.

---

# Java Solution

```java
class Solution {

    public ListNode partitionListII(ListNode head, int x) {

        ListNode lessDummy = new ListNode(0);
        ListNode equalDummy = new ListNode(0);
        ListNode greaterDummy = new ListNode(0);

        ListNode less = lessDummy;
        ListNode equal = equalDummy;
        ListNode greater = greaterDummy;

        while (head != null) {

            if (head.val < x) {
                less.next = head;
                less = less.next;
            } else if (head.val == x) {
                equal.next = head;
                equal = equal.next;
            } else {
                greater.next = head;
                greater = greater.next;
            }

            head = head.next;
        }

        greater.next = null;

        equal.next = greaterDummy.next;
        less.next = equalDummy.next;

        if (lessDummy.next != null) {
            return lessDummy.next;
        }

        if (equalDummy.next != null) {
            return equalDummy.next;
        }

        return greaterDummy.next;
    }
}
```

---

# Cleaner Version

```java
class Solution {

    public ListNode partitionListII(ListNode head, int x) {

        ListNode lessDummy = new ListNode(-1);
        ListNode equalDummy = new ListNode(-1);
        ListNode greaterDummy = new ListNode(-1);

        ListNode less = lessDummy;
        ListNode equal = equalDummy;
        ListNode greater = greaterDummy;

        while (head != null) {

            if (head.val < x) {
                less.next = head;
                less = less.next;
            } else if (head.val == x) {
                equal.next = head;
                equal = equal.next;
            } else {
                greater.next = head;
                greater = greater.next;
            }

            head = head.next;
        }

        greater.next = null;
        equal.next = greaterDummy.next;
        less.next = equalDummy.next;

        return lessDummy.next;
    }
}
```

---

# Dry Run

### Input

```text
3 -> 5 -> 8 -> 5 -> 10 -> 2 -> 1
x = 5
```

### After Traversal

```text
Less:
3 -> 2 -> 1

Equal:
5 -> 5

Greater:
8 -> 10
```

### Connect Lists

```text
3 -> 2 -> 1
       |
       v
5 -> 5
       |
       v
8 -> 10
```

### Result

```text
3 -> 2 -> 1 -> 5 -> 5 -> 8 -> 10
```

---

# Correctness Proof

For every node:

- If value < x, it is placed in the less list.
- If value = x, it is placed in the equal list.
- If value > x, it is placed in the greater list.

Every node is placed exactly once.

Since nodes are appended in traversal order:

- Relative ordering inside each partition is preserved.

After concatenating:

```text
Less + Equal + Greater
```

all nodes satisfy the required ordering.

Therefore the algorithm is correct.

---

# Complexity Analysis

### Time Complexity

```text
O(N)
```

Single traversal of the list.

---

### Space Complexity

```text
O(1)
```

Only a few pointers are used.

---

# Edge Cases

### Case 1

```text
head = [5,5,5]
x = 5

Output:
5 -> 5 -> 5
```

---

### Case 2

```text
head = [1,2,3]
x = 5

Output:
1 -> 2 -> 3
```

---

### Case 3

```text
head = [6,7,8]
x = 5

Output:
6 -> 7 -> 8
```

---

### Case 4

```text
head = [5,1,5,2,5,3]
x = 5

Output:
1 -> 2 -> 3 -> 5 -> 5 -> 5
```

---

# Interview Follow-Up

### Generalization

This is a linked-list version of the Dutch National Flag problem:

```text
< pivot
= pivot
> pivot
```

Using three lists gives:

```text
Time  : O(N)
Space : O(1)
Stable Partitioning
```

---

# Key Interview Points

- Three-way partitioning
- Dutch National Flag concept
- Stable reordering
- Dummy node technique
- Linked list manipulation
- O(N) time
- O(1) extra space
