# C++

## Overview
C++ is a general-purpose programming language that supports procedural, object-oriented, and generic programming.

## Key Features
- **Compiled Language**: High performance
- **Object-Oriented**: Classes, inheritance, polymorphism
- **Low-Level Memory Management**: Pointers, manual memory management
- **Templates**: Generic programming
- **STL**: Standard Template Library

## Basic Syntax
```cpp
#include <iostream>
using namespace std;

int main() {
    cout << "Hello, World!" << endl;
    return 0;
}
```

## Data Types
```cpp
// Primitive types
int integer = 42;
double decimal = 3.14;
float floatNum = 3.14f;
char letter = 'A';
bool flag = true;

// Pointers
int* ptr = &integer;
*ptr = 100;  // Dereference

// References
int& ref = integer;
ref = 200;  // Changes integer

// Arrays
int arr[5] = {1, 2, 3, 4, 5};

// Vectors (dynamic arrays)
#include <vector>
vector<int> vec = {1, 2, 3};
vec.push_back(4);
```

## Classes and Objects
```cpp
class Person {
private:
    string name;
    int age;

public:
    // Constructor
    Person(string n, int a) : name(n), age(a) {}
    
    // Getter
    string getName() { return name; }
    
    // Method
    void greet() {
        cout << "Hello, I'm " << name << endl;
    }
    
    // Destructor
    ~Person() {
        cout << "Destructor called" << endl;
    }
};
```

## Memory Management
```cpp
// Stack allocation
int x = 10;

// Heap allocation
int* ptr = new int(10);
delete ptr;  // Must free memory

// Array
int* arr = new int[10];
delete[] arr;  // Must use delete[]

// Smart pointers (C++11)
#include <memory>
unique_ptr<int> ptr1 = make_unique<int>(10);
shared_ptr<int> ptr2 = make_shared<int>(20);
// Automatically deallocated
```

## STL Containers
```cpp
#include <vector>
#include <map>
#include <set>
#include <queue>
#include <stack>

// Vector
vector<int> vec = {1, 2, 3};
vec.push_back(4);
vec.size();

// Map
map<string, int> ages;
ages["Alice"] = 25;
ages["Bob"] = 30;

// Set
set<int> nums = {1, 2, 3};
nums.insert(4);

// Queue
queue<int> q;
q.push(1);
q.pop();

// Priority Queue (max heap by default)
priority_queue<int> pq;
pq.push(3);
pq.push(1);
pq.top();  // 3
```

## Templates
```cpp
template <typename T>
T max(T a, T b) {
    return (a > b) ? a : b;
}

template <typename T>
class Box {
private:
    T content;
public:
    void set(T c) { content = c; }
    T get() { return content; }
};
```

## STL Algorithms
```cpp
#include <algorithm>

vector<int> vec = {3, 1, 4, 1, 5};

// Sort
sort(vec.begin(), vec.end());

// Find
auto it = find(vec.begin(), vec.end(), 4);

// Count
int count = count(vec.begin(), vec.end(), 1);

// For each
for_each(vec.begin(), vec.end(), [](int x) {
    cout << x << " ";
});
```

## Best Practices
1. Use RAII (Resource Acquisition Is Initialization)
2. Prefer smart pointers over raw pointers
3. Use const correctness
4. Initialize variables
5. Use STL containers instead of raw arrays
6. Avoid manual memory management when possible
