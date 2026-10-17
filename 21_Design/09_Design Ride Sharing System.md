# Design Ride Sharing System

**LeetCode 3829 - Design Ride Sharing System**

## Problem Statement

A ride-sharing system manages:

- Waiting riders
- Available drivers

The system should always match:

```java
Earliest Driver  ↔  Earliest Rider
```

and support rider cancellation before matching.

---

# Key Observation

This is essentially a **FIFO matching problem**.

For example:

```java
Riders  : [3, 1, 7]
Drivers : [2, 5]
```

First match should be:

```java
[2, 3]
```

Then:

```java
[5, 1]
```

which naturally suggests using **queues**.

---

# Challenge: Rider Cancellation

Suppose:

```java
Riders = [3, 1, 7]
```

Then:

```java
cancelRider(1)
```

Removing a rider directly from the middle of a queue would cost:

```java
O(n)
```

Instead, use **lazy deletion**:

```java
cancelled = {1}
```

Keep rider `1` in the queue and skip him when matching.

---

# Data Structures

```java
Queue<Integer> riders
Queue<Integer> drivers
Set<Integer> cancelledRiders
```

### Riders Queue

Stores waiting riders in arrival order.

```java
[3,1,7]
```

### Drivers Queue

Stores available drivers in arrival order.

```java
[2,5]
```

### Cancelled Set

Stores riders that canceled before being matched.

```java
{1}
```

---

# Approach

## addRider(riderId)

Add rider to waiting queue.

```java
riders.offer(riderId);
```

---

## addDriver(driverId)

Add driver to available queue.

```java
drivers.offer(driverId);
```

---

## cancelRider(riderId)

Mark rider as cancelled.

```java
cancelled.add(riderId);
```

No immediate removal.

---

## matchDriverWithRider()

### Step 1

Remove invalid riders from front.

```java
while front rider is cancelled
    remove rider
```

### Step 2

If either queue is empty:

```java
return [-1, -1]
```

### Step 3

Match:

```java
driver = drivers.poll()
rider  = riders.poll()
```

Return:

```java
[driver, rider]
```

---

# Dry Run

## Example 1

```java
addRider(3)
```

```java
Riders = [3]
```

---

```java
addDriver(2)
```

```java
Drivers = [2]
```

---

```java
addRider(1)
```

```java
Riders = [3,1]
```

---

```java
matchDriverWithRider()
```

Match:

```java
Driver = 2
Rider  = 3
```

Return:

```java
[2,3]
```

---

```java
addDriver(5)
```

```java
Drivers = [5]
```

---

```java
cancelRider(3)
```

Rider 3 already matched.

No effect.

---

```java
matchDriverWithRider()
```

Return:

```java
[5,1]
```

---

## Example 2

```java
addRider(8)
addDriver(8)
addDriver(6)
```

Queues:

```java
Riders  = [8]
Drivers = [8,6]
```

---

```java
matchDriverWithRider()
```

Return:

```java
[8,8]
```

---

```java
addRider(2)
cancelRider(2)
```

```java
cancelled = {2}
```

---

```java
matchDriverWithRider()
```

Before matching:

```java
remove cancelled rider 2
```

No rider remains.

Return:

```java
[-1,-1]
```

---

# Java Solution

```java
import java.util.*;

class RideSharingSystem {

    private Queue<Integer> riders;
    private Queue<Integer> drivers;
    private Set<Integer> cancelled;

    public RideSharingSystem() {
        riders = new LinkedList<>();
        drivers = new LinkedList<>();
        cancelled = new HashSet<>();
    }

    public void addRider(int riderId) {
        riders.offer(riderId);
    }

    public void addDriver(int driverId) {
        drivers.offer(driverId);
    }

    public int[] matchDriverWithRider() {

        while (!riders.isEmpty()
                && cancelled.contains(riders.peek())) {

            cancelled.remove(riders.peek());
            riders.poll();
        }

        if (riders.isEmpty() || drivers.isEmpty()) {
            return new int[]{-1, -1};
        }

        int driver = drivers.poll();
        int rider = riders.poll();

        return new int[]{driver, rider};
    }

    public void cancelRider(int riderId) {
        cancelled.add(riderId);
    }
}
```

---

# Complexity Analysis

## addRider()

```java
Time Complexity: O(1)
Space Complexity: O(1)
```

---

## addDriver()

```java
Time Complexity: O(1)
Space Complexity: O(1)
```

---

## cancelRider()

```java
Time Complexity: O(1)
Space Complexity: O(1)
```

---

## matchDriverWithRider()

```java
Time Complexity: O(1) amortized
```

Why?

- Each rider enters queue once.
- Each rider leaves queue once.
- Cancelled riders are skipped only once.

---

## Overall Space

```java
O(R + D + C)
```

where:

- `R` = waiting riders
- `D` = available drivers
- `C` = cancelled riders

---

# Why Lazy Deletion Is the Key Idea

Without lazy deletion:

```java
cancelRider(id)
```

would require searching the queue:

```java
O(n)
```

With lazy deletion:

```java
cancelled.add(id)
```

becomes:

```java
O(1)
```

and invalid riders are cleaned up only when they reach the front.

---

# Key Takeaways

- Use **Queue** for riders and drivers because matching is FIFO.
- Use a **HashSet** to track cancelled riders.
- Apply **lazy deletion** to avoid expensive removals from the middle of the queue.
- Skip cancelled riders before every match.
- All operations are **O(1)** amortized.
- Core concepts:
  - Queue
  - HashSet
  - Lazy Deletion
  - FIFO Matching
