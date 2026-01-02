# Java

## Overview
Java is a high-level, class-based, object-oriented programming language designed to have minimal implementation dependencies.

## Key Features
- **Platform Independent**: Write once, run anywhere (WORA)
- **Object-Oriented**: Everything is an object
- **Strongly Typed**: Type checking at compile time
- **Automatic Memory Management**: Garbage collection
- **Multi-threaded**: Built-in concurrency support

## Basic Syntax

### Data Types
```java
// Primitive types
int integer = 42;
double decimal = 3.14;
boolean flag = true;
char letter = 'A';
byte smallNum = 127;
short mediumNum = 32767;
long bigNum = 9223372036854775807L;
float floatNum = 3.14f;

// Reference types
String text = "Hello, World!";
Integer wrapper = 42;  // Wrapper class
```

### Arrays
```java
// Declaration and initialization
int[] numbers = {1, 2, 3, 4, 5};
int[] numbers2 = new int[5];

// 2D array
int[][] matrix = {{1, 2}, {3, 4}};

// ArrayList (dynamic)
import java.util.ArrayList;
ArrayList<Integer> list = new ArrayList<>();
list.add(1);
list.add(2);
list.get(0);  // 1
list.size(); // 2
```

## Classes and Objects

### Basic Class
```java
public class Person {
    // Fields
    private String name;
    private int age;
    
    // Constructor
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // Getters and Setters
    public String getName() {
        return name;
    }
    
    public void setName(String name) {
        this.name = name;
    }
    
    // Method
    public void greet() {
        System.out.println("Hello, I'm " + name);
    }
    
    // toString override
    @Override
    public String toString() {
        return "Person{name='" + name + "', age=" + age + "}";
    }
}
```

### Inheritance
```java
public class Student extends Person {
    private String studentId;
    
    public Student(String name, int age, String studentId) {
        super(name, age);  // Call parent constructor
        this.studentId = studentId;
    }
    
    public void study() {
        System.out.println(getName() + " is studying");
    }
    
    @Override
    public void greet() {
        super.greet();
        System.out.println("I'm a student");
    }
}
```

### Interfaces
```java
public interface Drawable {
    void draw();  // Abstract method
    
    // Default method (Java 8+)
    default void display() {
        System.out.println("Displaying...");
    }
    
    // Static method (Java 8+)
    static void info() {
        System.out.println("Drawable interface");
    }
}

public class Circle implements Drawable {
    @Override
    public void draw() {
        System.out.println("Drawing circle");
    }
}
```

### Abstract Classes
```java
public abstract class Shape {
    protected String color;
    
    public Shape(String color) {
        this.color = color;
    }
    
    // Abstract method
    public abstract double getArea();
    
    // Concrete method
    public void setColor(String color) {
        this.color = color;
    }
}

public class Rectangle extends Shape {
    private double width;
    private double height;
    
    public Rectangle(String color, double width, double height) {
        super(color);
        this.width = width;
        this.height = height;
    }
    
    @Override
    public double getArea() {
        return width * height;
    }
}
```

## Collections Framework

### List
```java
import java.util.*;

// ArrayList (dynamic array)
List<String> arrayList = new ArrayList<>();
arrayList.add("Apple");
arrayList.add("Banana");
arrayList.get(0);  // "Apple"
arrayList.remove(0);

// LinkedList (doubly linked list)
List<String> linkedList = new LinkedList<>();
linkedList.add("First");
linkedList.add("Last");
```

### Set
```java
// HashSet (no order, no duplicates)
Set<Integer> hashSet = new HashSet<>();
hashSet.add(1);
hashSet.add(2);
hashSet.contains(1);  // true

// TreeSet (sorted, no duplicates)
Set<Integer> treeSet = new TreeSet<>();
treeSet.add(3);
treeSet.add(1);
treeSet.add(2);
// Elements: 1, 2, 3 (sorted)

// LinkedHashSet (insertion order, no duplicates)
Set<Integer> linkedHashSet = new LinkedHashSet<>();
```

### Map
```java
// HashMap (key-value pairs)
Map<String, Integer> hashMap = new HashMap<>();
hashMap.put("Alice", 25);
hashMap.put("Bob", 30);
hashMap.get("Alice");  // 25
hashMap.containsKey("Alice");  // true

// TreeMap (sorted by keys)
Map<String, Integer> treeMap = new TreeMap<>();

// LinkedHashMap (insertion order)
Map<String, Integer> linkedHashMap = new LinkedHashMap<>();

// Iterate over map
for (Map.Entry<String, Integer> entry : hashMap.entrySet()) {
    System.out.println(entry.getKey() + ": " + entry.getValue());
}
```

### Queue and Deque
```java
// Queue (FIFO)
Queue<Integer> queue = new LinkedList<>();
queue.offer(1);
queue.offer(2);
queue.poll();  // Remove and return 1

// PriorityQueue (min heap by default)
PriorityQueue<Integer> pq = new PriorityQueue<>();
pq.offer(3);
pq.offer(1);
pq.offer(2);
pq.poll();  // 1

// Deque (double-ended queue)
Deque<Integer> deque = new ArrayDeque<>();
deque.offerFirst(1);
deque.offerLast(2);
deque.pollFirst();  // 1
```

## Generics
```java
// Generic class
public class Box<T> {
    private T content;
    
    public void set(T content) {
        this.content = content;
    }
    
    public T get() {
        return content;
    }
}

// Generic method
public <T> void printArray(T[] array) {
    for (T element : array) {
        System.out.println(element);
    }
}

// Bounded type parameters
public <T extends Comparable<T>> T findMax(T[] array) {
    T max = array[0];
    for (T element : array) {
        if (element.compareTo(max) > 0) {
            max = element;
        }
    }
    return max;
}
```

## Exception Handling
```java
try {
    int result = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Error: " + e.getMessage());
} catch (Exception e) {
    System.out.println("General error");
} finally {
    System.out.println("Always executed");
}

// Try-with-resources (Java 7+)
try (BufferedReader br = new BufferedReader(new FileReader("file.txt"))) {
    String line = br.readLine();
} catch (IOException e) {
    e.printStackTrace();
}

// Custom exception
public class InvalidAgeException extends Exception {
    public InvalidAgeException(String message) {
        super(message);
    }
}
```

## Streams API (Java 8+)
```java
import java.util.stream.*;

List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

// Map
List<Integer> squared = numbers.stream()
    .map(x -> x * x)
    .collect(Collectors.toList());

// Filter
List<Integer> evens = numbers.stream()
    .filter(x -> x % 2 == 0)
    .collect(Collectors.toList());

// Reduce
int sum = numbers.stream()
    .reduce(0, (a, b) -> a + b);

// More operations
numbers.stream()
    .filter(x -> x % 2 == 0)
    .map(x -> x * 2)
    .sorted()
    .forEach(System.out::println);
```

## Lambda Expressions (Java 8+)
```java
// Functional interface
@FunctionalInterface
interface Calculator {
    int calculate(int a, int b);
}

// Lambda expression
Calculator add = (a, b) -> a + b;
Calculator multiply = (a, b) -> a * b;

System.out.println(add.calculate(5, 3));  // 8

// With collections
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
names.forEach(name -> System.out.println(name));

// Method reference
names.forEach(System.out::println);
```

## Multithreading

### Thread Creation
```java
// Extending Thread class
class MyThread extends Thread {
    public void run() {
        System.out.println("Thread running");
    }
}

MyThread thread = new MyThread();
thread.start();

// Implementing Runnable
class MyRunnable implements Runnable {
    public void run() {
        System.out.println("Runnable running");
    }
}

Thread thread2 = new Thread(new MyRunnable());
thread2.start();

// Lambda (Java 8+)
Thread thread3 = new Thread(() -> System.out.println("Lambda thread"));
thread3.start();
```

### Synchronization
```java
public class Counter {
    private int count = 0;
    
    // Synchronized method
    public synchronized void increment() {
        count++;
    }
    
    // Synchronized block
    public void decrement() {
        synchronized(this) {
            count--;
        }
    }
    
    public int getCount() {
        return count;
    }
}
```

## String Operations
```java
String str = "Hello, World!";

// Common methods
str.length();              // 13
str.charAt(0);             // 'H'
str.substring(0, 5);       // "Hello"
str.toLowerCase();         // "hello, world!"
str.toUpperCase();         // "HELLO, WORLD!"
str.contains("World");     // true
str.startsWith("Hello");   // true
str.endsWith("!");         // true
str.replace("World", "Java"); // "Hello, Java!"
str.split(",");            // ["Hello", " World!"]
str.trim();                // Remove whitespace

// StringBuilder (mutable, not thread-safe)
StringBuilder sb = new StringBuilder();
sb.append("Hello");
sb.append(" World");
String result = sb.toString();

// StringBuffer (mutable, thread-safe)
StringBuffer sbuf = new StringBuffer();
```

## Best Practices

1. **Follow naming conventions**: camelCase for variables, PascalCase for classes
2. **Use interfaces**: Program to interfaces, not implementations
3. **Encapsulation**: Make fields private, provide public methods
4. **Override equals() and hashCode()**: When using objects as keys
5. **Use StringBuilder**: For string concatenation in loops
6. **Close resources**: Use try-with-resources
7. **Avoid null**: Use Optional<T> (Java 8+)
8. **Use generics**: For type safety
9. **Handle exceptions**: Don't swallow exceptions
10. **Write clean code**: Single responsibility, small methods

## Common Interview Questions

- Explain OOP concepts
- What's the difference between abstract class and interface?
- Explain method overloading vs overriding
- What is the difference between == and equals()?
- Explain the Collections Framework
- What are generics?
- Explain exception handling
- What is multithreading?
- What's the difference between ArrayList and LinkedList?
- Explain garbage collection
