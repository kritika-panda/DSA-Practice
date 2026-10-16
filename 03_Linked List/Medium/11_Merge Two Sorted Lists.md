# Merge Two Sorted Lists

## Problem Statement

You are given the heads of two sorted linked lists `list1` and `list2`.

Merge the two lists into one sorted linked list and return its head.

The merged list should be made by splicing together the nodes of the first two lists.

**LeetCode:** 21. Merge Two Sorted Lists

---

## Example 1

### Input

```text
list1 = [1,2,4]
list2 = [1,3,4]
```

### Output

```text
[1,1,2,3,4,4]
```

### Visualization

```text
List1: 1 -> 2 -> 4
List2: 1 -> 3 -> 4

Merged:
1 -> 1 -> 2 -> 3 -> 4 -> 4
```

---

## Example 2

### Input

```text
list1 = []
list2 = []
```

### Output

```text
[]
```

---

## Example 3

### Input

```text
list1 = []
list2 = [0]
```

### Output

```text
[0]
```

---

# Approach 1: Iterative (Optimal)

## Idea

Since both lists are already sorted:

- Compare the current nodes.
- Attach the smaller node to the answer.
- Move that list forward.
- Continue until one list becomes empty.
- Attach the remaining nodes.

---

## Algorithm

1. Create a dummy node.
2. Maintain a pointer `tail`.
3. Compare nodes of both lists.
4. Connect the smaller node to `tail`.
5. Move the corresponding pointer.
6. Move `tail`.
7. Attach remaining nodes.
8. Return `dummy.next`.

---

## Java Solution

```java
class Solution {

    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {

        ListNode dummy = new ListNode(-1);
        ListNode tail = dummy;

        while (list1 != null && list2 != null) {

            if (list1.val <= list2.val) {
                tail.next = list1;
                list1 = list1.next;
            } else {
                tail.next = list2;
                list2 = list2.next;
            }

            tail = tail.next;
        }

        if (list1 != null) {
            tail.next = list1;
        } else {
            tail.next = list2;
        }

        return dummy.next;
    }
}
```

---

## Dry Run

### Input

```text
list1 = 1 -> 2 -> 4
list2 = 1 -> 3 -> 4
```

### Initial

```text
dummy -> null
tail = dummy
```

### Compare 1 and 1

```text
Take list1

dummy -> 1
tail = 1
```

### Compare 2 and 1

```text
Take list2

dummy -> 1 -> 1
tail = second 1
```

### Compare 2 and 3

```text
Take 2

dummy -> 1 -> 1 -> 2
```

### Compare 4 and 3

```text
Take 3

dummy -> 1 -> 1 -> 2 -> 3
```

### Compare 4 and 4

```text
Take list1's 4
```

Remaining:

```text
4
```

Attach remaining node.

Final:

```text
1 -> 1 -> 2 -> 3 -> 4 -> 4
```

---

## Correctness Proof

At every step:

- The smaller current node is selected.
- All previously selected nodes remain sorted.
- Since each list is already sorted, the smallest available node must be among the current heads.

Thus:

- Nodes are appended in non-decreasing order.
- Every node is included exactly once.

Therefore, the resulting list is correctly sorted.

---

## Complexity Analysis

### Time Complexity

```text
O(n + m)
```

where:

- n = length of list1
- m = length of list2

---

### Space Complexity

```text
O(1)
```

Only pointers are used.

---

# Approach 2: Recursive

## Idea

Pick the smaller node as the head and recursively merge the remaining lists.

---

## Java Solution

```java
class Solution {

    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {

        if (list1 == null) {
            return list2;
        }

        if (list2 == null) {
            return list1;
        }

        if (list1.val <= list2.val) {
            list1.next = mergeTwoLists(list1.next, list2);
            return list1;
        }

        list2.next = mergeTwoLists(list1, list2.next);
        return list2;
    }
}
```

---

## Complexity Analysis

### Time Complexity

```text
O(n + m)
```

### Space Complexity

```text
O(n + m)
```

Recursive call stack.

---

# Interview Follow-Ups

### Why use a dummy node?

It avoids handling the special case of selecting the first node of the merged list.

---

### Can we create a new merged list?

Yes, but that would require:

```text
O(n + m)
```

extra space.

The optimal solution reuses existing nodes.

---

### Which approach is preferred?

✅ Iterative approach

Because:

- O(1) extra space
- No recursion stack overflow risk
- Easier to explain in interviews

---

## Key Interview Points

- Classic Linked List problem
- Two Pointer Technique
- Dummy Node Pattern
- In-place node manipulation
- O(n + m) Time, O(1) Space solution
