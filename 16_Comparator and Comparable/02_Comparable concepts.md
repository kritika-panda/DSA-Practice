# Comparable Interface in Java

## What is Comparable?

`Comparable` is an interface in Java that is used to define the natural ordering of objects.

It belongs to:

```java
java.lang.Comparable
```

Unlike `Comparator`, which provides external sorting logic, a `Comparable` interface defines the default sorting strategy inside the class itself.

---

# Why Do We Need Comparable?

Suppose we have a collection of custom objects:

```java
class Employee {
    int id;
    String name;
    int salary;
}
```

If we try to sort a list of employees using `Collections.sort(employees)`, Java will throw a compilation error because it does not know whether to sort them by ID, Name, or Salary. 

By implementing `Comparable`, we define a single, default **natural ordering** strategy for that class.

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

# Comparable Syntax

```java
public class ClassName implements Comparable<T> {
    
    @Override
    public int compareTo(T o) {
        return comparisonLogic;
    }
}
```

Method:

```java
int compareTo(T o);
```

Returns:

```text
Negative → current object (this) comes before parameter object (o)

Zero     → current object (this) equals parameter object (o)

Positive → current object (this) comes after parameter object (o)
```

---

# Natural Ordering of Core Classes

Many built-in Java classes implement `Comparable` by default:

## String

```java
List<String> fruits = Arrays.asList("Orange", "Apple", "Banana");

Collections.sort(fruits);

System.out.println(fruits);
```

Output:

```java
[Apple, Banana, Orange]
```

---

## Integer

```java
List<Integer> nums = Arrays.asList(5, 2, 8, 1);

Collections.sort(nums);

System.out.println(nums);
```

Output:

```java
[1, 2, 5, 8]
```

---

# Implementing Comparable

## Student Class

```java
class Student implements Comparable<Student> {

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

    // Specifying natural ordering strategy by marks ascending
    @Override
    public int compareTo(Student other) {
        return Integer.compare(this.marks, other.marks);
    }

    @Override
    public String toString() {
        return name + " " + marks;
    }
}
```

---

## Sorting the Collection

```java
List<Student> students = new ArrayList<>();
students.add(new Student(1, "Rohit", 90));
students.add(new Student(2, "Aman", 85));
students.add(new Student(3, "Virat", 95));

Collections.sort(students);

System.out.println(students);
```

Output:

```java
[Aman 85, Rohit 90, Virat 95]
```

---

# Implementing Reverse Natural Ordering

If we want the default natural ordering to be descending (e.g., highest marks first):

```java
@Override
public int compareTo(Student other) {
    return Integer.compare(other.marks, this.marks);
}
```

Output:

```java
[Virat 95, Rohit 90, Aman 85]
```

---

# Implementing String Attribute Sorting

If we want the default natural sorting strategy of a class to be alphabetical by name:

```java
@Override
public int compareTo(Student other) {
    return this.name.compareTo(other.name);
}
```

Output:

```java
[Aman 85, Rohit 90, Virat 95]
```

---

# Multiple Level Sorting Natively

Suppose we want a multi-level natural sort order:

1. Marks ascending
2. Name ascending (Tie-Breaker)

---

## Multi-Level Logic

```java
@Override
public int compareTo(Student other) {
    if (this.marks != other.marks) {
        return Integer.compare(this.marks, other.marks);
    }
    return this.name.compareTo(other.name);
}
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

# Comparable in Sorted Collections

Implementing `Comparable` is mandatory if you plan to store custom objects inside Java's sorted collections without explicitly passing an external `Comparator`.

## TreeSet Natively

```java
TreeSet<Student> set = new TreeSet<>();
set.add(new Student(1, "Rohit", 90));
```

Maintains elements automatically using:

```text
Natural Order (via compareTo)
```

---

## TreeMap Natively

```java
TreeMap<Student, String> map = new TreeMap<>();
map.put(new Student(1, "Rohit", 90), "Class A");
```

Stores mapping keys automatically using:

```text
Natural Order (via compareTo)
```

---

## PriorityQueue Natively

```java
PriorityQueue<Student> pq = new PriorityQueue<>();
```

Functions inherently as a Min Heap using:

```text
Natural Order (via compareTo)
```

---

# Common Mistake

Avoid basic subtraction logic inside your object comparison structures:

```java
@Override
public int compareTo(Student other) {
    return this.marks - other.marks;
}
```

when values can be large or negative.

Because:

```text
Integer Overflow
```

may occur, flipping the sign unexpectedly.

Always use:

```java
Integer.compare(
    this.marks,
    other.marks
);
```

---

# Safer Comparisons Toolkit

## Integer

```java
Integer.compare(this.val, other.val)
```

---

## Long

```java
Long.compare(this.val, other.val)
```

---

## Double

```java
Double.compare(this.val, other.val)
```

---

# Time Complexity

Sorting collections of classes implementing `Comparable`:

```java
Collections.sort(list);
Arrays.sort(array);
```

Time Complexity:

```text
O(n log n)
```

---

# Interview Cheat Sheet

## Ascending Integer Field

```java
Integer.compare(this.val, other.val)
```

---

## Descending Integer Field

```java
Integer.compare(other.val, this.val)
```

---

## Ascending String Field

```java
this.str.compareTo(other.val)
```

---

## Descending String Field

```java
other.str.compareTo(this.val)
```

---

## String Length Ascending

```java
Integer.compare(
    this.str.length(),
    other.str.length()
);
```

---

# Key Takeaways

```text
Comparable → Natural Ordering (Inside Class)

Comparator → Custom Ordering (Outside Class)

Class implements Comparable
→ Eligible for Collections.sort(list)

Class implements Comparable
→ Eligible for Arrays.sort(array)

Class implements Comparable
→ Can be Key in default TreeMap

Class implements Comparable
→ Can be Element in default TreeSet

Class implements Comparable
→ Can be Element in default PriorityQueue
```

Implementing `Comparable` correctly is vital in tech interviews for creating clean data models, especially when your design relies on native collection sorting properties within:

```text
Priority Queues / Heaps
Sorted TreeMaps
Unique Sorted TreeSets
Custom Interval Objects
Graph Node Rank Algorithms
```
