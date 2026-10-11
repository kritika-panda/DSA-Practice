# Comparator in Java

## What is Comparator?

`Comparator` is an interface in Java that is used to define a custom ordering of objects.

It belongs to:

```java
java.util.Comparator
```

Unlike `Comparable`, which defines the natural ordering inside the class itself, a `Comparator` provides external sorting logic.

---

# Why Do We Need Comparator?

Suppose we have:

```java
class Student {
    int id;
    String name;
    int marks;
}
```

A student can be sorted by:

- ID
- Name
- Marks

Since multiple sorting criteria exist, `Comparator` is preferred.

---

# Comparator vs Comparable

| Feature | Comparable | Comparator |
|----------|----------|----------|
| Package | java.lang | java.util |
| Method | compareTo() | compare() |
| Sorting Logic | Inside Class | Outside Class |
| Multiple Sorting Criteria | No | Yes |
| Modify Existing Class | Required | Not Required |
| Functional Interface | No | Yes |

---

# Comparator Syntax

```java
Comparator<T> comparator =
    (a, b) -> comparisonLogic;
```

Method:

```java
int compare(T a, T b);
```

Returns:

```text
Negative → a comes before b

Zero     → a equals b

Positive → a comes after b
```

---

# Sorting Integers

## Ascending Order

```java
List<Integer> nums =
        Arrays.asList(5, 2, 8, 1);

Collections.sort(nums,
        (a, b) -> a - b);

System.out.println(nums);
```

Output:

```java
[1, 2, 5, 8]
```

---

## Descending Order

```java
Collections.sort(nums,
        (a, b) -> b - a);
```

Output:

```java
[8, 5, 2, 1]
```

---

# Sorting Objects

## Student Class

```java
class Student {

    int id;
    String name;
    int marks;

    Student(int id,
            String name,
            int marks) {

        this.id = id;
        this.name = name;
        this.marks = marks;
    }

    @Override
    public String toString() {
        return name + " " + marks;
    }
}
```

---

## Sort By Marks

```java
Collections.sort(students,
        (a, b) -> a.marks - b.marks);
```

---

## Sort By Marks Descending

```java
Collections.sort(students,
        (a, b) -> b.marks - a.marks);
```

---

## Sort By Name

```java
Collections.sort(students,
        (a, b) -> a.name.compareTo(b.name));
```

---

# Using Comparator with List.sort()

```java
students.sort(
    (a, b) -> a.marks - b.marks
);
```

Preferred in modern Java.

---

# Using Anonymous Class

Before Java 8:

```java
Collections.sort(students,
    new Comparator<Student>() {

        @Override
        public int compare(
                Student a,
                Student b) {

            return a.marks - b.marks;
        }
    }
);
```

---

# Using Lambda Expression

Java 8+

```java
students.sort(
    (a, b) -> a.marks - b.marks
);
```

---

# Comparator Utility Methods

## comparing()

```java
students.sort(
    Comparator.comparing(
        s -> s.name
    )
);
```

---

## comparingInt()

```java
students.sort(
    Comparator.comparingInt(
        s -> s.marks
    )
);
```

---

## comparingLong()

```java
employees.sort(
    Comparator.comparingLong(
        e -> e.salary
    )
);
```

---

# Reverse Order

```java
students.sort(
    Comparator.comparingInt(
        s -> s.marks
    ).reversed()
);
```

---

# Multiple Level Sorting

Suppose:

```java
Student(id, name, marks)
```

Sort by:

1. Marks ascending
2. Name ascending

---

## thenComparing()

```java
students.sort(
    Comparator.comparingInt(
        (Student s) -> s.marks
    )
    .thenComparing(
        s -> s.name
    )
);
```

---

## Example

Input:

```java
Rohit 90
Aman 90
Virat 95
```

Output:

```java
Aman 90
Rohit 90
Virat 95
```

---

# Sorting Arrays

## Primitive Array

```java
int[] arr = {5,2,8,1};

Arrays.sort(arr);
```

---

## Integer Array Descending

```java
Integer[] arr =
        {5,2,8,1};

Arrays.sort(arr,
        (a,b) -> b - a);
```

Output:

```java
[8,5,2,1]
```

---

# Comparator in PriorityQueue

## Min Heap

```java
PriorityQueue<Integer> pq =
        new PriorityQueue<>();
```

Default ordering:

```text
Ascending
```

---

## Max Heap

```java
PriorityQueue<Integer> pq =
    new PriorityQueue<>(
        (a,b) -> b - a
    );
```

---

# Comparator in TreeSet

```java
TreeSet<Integer> set =
    new TreeSet<>(
        (a,b) -> b - a
    );
```

Output:

```text
Descending Order
```

---

# Comparator in TreeMap

```java
TreeMap<Integer,String> map =
    new TreeMap<>(
        (a,b) -> b - a
    );
```

Stores keys in:

```text
Descending Order
```

---

# Important Comparator Patterns

## Pattern 1: Ascending

```java
(a, b) -> a - b
```

---

## Pattern 2: Descending

```java
(a, b) -> b - a
```

---

## Pattern 3: String Sorting

```java
(a, b) -> a.compareTo(b)
```

---

## Pattern 4: Descending String

```java
(a, b) -> b.compareTo(a)
```

---

## Pattern 5: Sort By Length

```java
(a, b) ->
    a.length() - b.length()
```

---

## Pattern 6: Sort 2D Array By First Column

```java
Arrays.sort(points,
    (a, b) ->
    a[0] - b[0]
);
```

---

## Pattern 7: Sort 2D Array By Second Column

```java
Arrays.sort(points,
    (a, b) ->
    a[1] - b[1]
);
```

---

## Pattern 8: Multiple Key Sorting

```java
Arrays.sort(arr, (a,b) -> {

    if (a[0] != b[0])
        return a[0] - b[0];

    return a[1] - b[1];
});
```

---

# Comparator in FAANG Interviews

## Meeting Rooms

```java
Arrays.sort(intervals,
    (a,b) -> a[0] - b[0]);
```

---

## Merge Intervals

```java
Arrays.sort(intervals,
    (a,b) -> a[0] - b[0]);
```

---

## Job Scheduling

```java
Arrays.sort(jobs,
    (a,b) -> a.end - b.end);
```

---

## Top K Problems

```java
PriorityQueue<Pair>
```

with custom comparator.

---

## K Closest Points

```java
PriorityQueue<int[]>
```

sorted by distance.

---

# Common Mistake

Avoid:

```java
(a, b) -> a.age - b.age
```

when values can be large.

Because:

```text
Integer Overflow
```

may occur.

Use:

```java
Integer.compare(
    a.age,
    b.age
);
```

---

# Safer Comparisons

## Integer

```java
Integer.compare(a, b)
```

---

## Long

```java
Long.compare(a, b)
```

---

## Double

```java
Double.compare(a, b)
```

---

# Time Complexity

Sorting using Comparator:

```java
Collections.sort()
Arrays.sort()
List.sort()
```

Time Complexity:

```text
O(n log n)
```

---

# Interview Cheat Sheet

## Ascending

```java
(a,b) -> a - b
```

---

## Descending

```java
(a,b) -> b - a
```

---

## Max Heap

```java
(a,b) -> b - a
```

---

## Min Heap

```java
(a,b) -> a - b
```

---

## By String Length

```java
(a,b) ->
a.length() - b.length()
```

---

## By First Element of Pair

```java
(a,b) ->
a[0] - b[0]
```

---

## By Second Element of Pair

```java
(a,b) ->
a[1] - b[1]
```

---

## Multi-Level Sorting

```java
Comparator
.comparingInt(Student::getMarks)
.thenComparing(Student::getName)
```

---

# Key Takeaways

```text
Comparable → Natural Ordering

Comparator → Custom Ordering

PriorityQueue + Comparator
→ Custom Heap

TreeSet + Comparator
→ Custom Sorted Set

TreeMap + Comparator
→ Custom Sorted Map

Collections.sort()
Arrays.sort()
List.sort()
→ Accept Comparator
```

Comparator is one of the most frequently used Java concepts in FAANG interviews, especially in problems involving:

```text
Intervals
Priority Queues
Greedy Algorithms
Custom Sorting
Top-K Problems
Sweep Line Algorithms
```
