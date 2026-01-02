# Python

## Overview
Python is a high-level, interpreted, general-purpose programming language known for its simplicity and readability.

## Key Features
- **Interpreted**: No compilation needed
- **Dynamically Typed**: Types determined at runtime
- **Object-Oriented**: Everything is an object
- **Rich Standard Library**: "Batteries included"
- **Cross-Platform**: Works on Windows, Mac, Linux

## Data Types

### Basic Types
```python
# Numbers
integer = 42
floating = 3.14
complex_num = 3 + 4j

# Strings
text = "Hello, World!"
multiline = """Multiple
lines"""

# Boolean
is_true = True
is_false = False

# None
nothing = None
```

### Collections
```python
# List (mutable, ordered)
numbers = [1, 2, 3, 4, 5]
mixed = [1, "two", 3.0, True]

# Tuple (immutable, ordered)
coordinates = (10, 20)
single = (1,)  # Note the comma

# Set (mutable, unordered, unique)
unique_numbers = {1, 2, 3, 4, 5}

# Dictionary (key-value pairs)
person = {
    "name": "John",
    "age": 30,
    "city": "New York"
}
```

## List Comprehensions
```python
# Basic
squares = [x**2 for x in range(10)]

# With condition
evens = [x for x in range(10) if x % 2 == 0]

# Nested
matrix = [[i+j for j in range(3)] for i in range(3)]

# Dictionary comprehension
square_dict = {x: x**2 for x in range(5)}

# Set comprehension
unique_chars = {c for c in "hello"}
```

## Functions
```python
# Basic function
def greet(name):
    return f"Hello, {name}!"

# Default arguments
def power(base, exponent=2):
    return base ** exponent

# Variable arguments
def sum_all(*args):
    return sum(args)

# Keyword arguments
def create_profile(**kwargs):
    return kwargs

# Lambda functions
square = lambda x: x**2
add = lambda a, b: a + b
```

## Classes and OOP
```python
class Person:
    # Class variable
    species = "Homo sapiens"
    
    # Constructor
    def __init__(self, name, age):
        self.name = name  # Instance variable
        self.age = age
    
    # Instance method
    def greet(self):
        return f"Hello, I'm {self.name}"
    
    # String representation
    def __str__(self):
        return f"Person(name={self.name}, age={self.age})"
    
    # Class method
    @classmethod
    def from_birth_year(cls, name, birth_year):
        age = 2024 - birth_year
        return cls(name, age)
    
    # Static method
    @staticmethod
    def is_adult(age):
        return age >= 18

# Inheritance
class Student(Person):
    def __init__(self, name, age, student_id):
        super().__init__(name, age)
        self.student_id = student_id
    
    def study(self):
        return f"{self.name} is studying"
```

## Decorators
```python
def timing_decorator(func):
    import time
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(f"{func.__name__} took {end - start:.4f} seconds")
        return result
    return wrapper

@timing_decorator
def slow_function():
    time.sleep(1)
    return "Done"
```

## Generators
```python
# Generator function
def countdown(n):
    while n > 0:
        yield n
        n -= 1

# Generator expression
squares_gen = (x**2 for x in range(10))

# Example: Fibonacci
def fibonacci():
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b
```

## Context Managers
```python
# Using 'with' statement
with open('file.txt', 'r') as f:
    content = f.read()

# Custom context manager
class DatabaseConnection:
    def __enter__(self):
        print("Opening connection")
        return self
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        print("Closing connection")

with DatabaseConnection() as db:
    # Use database
    pass
```

## Exception Handling
```python
try:
    result = 10 / 0
except ZeroDivisionError as e:
    print(f"Error: {e}")
except Exception as e:
    print(f"Unexpected error: {e}")
else:
    print("No exceptions occurred")
finally:
    print("Always executed")

# Raising exceptions
def validate_age(age):
    if age < 0:
        raise ValueError("Age cannot be negative")
```

## Important Built-in Functions
```python
# map, filter, reduce
numbers = [1, 2, 3, 4, 5]
squared = list(map(lambda x: x**2, numbers))
evens = list(filter(lambda x: x % 2 == 0, numbers))

from functools import reduce
sum_all = reduce(lambda a, b: a + b, numbers)

# zip
names = ['Alice', 'Bob', 'Charlie']
ages = [25, 30, 35]
combined = list(zip(names, ages))

# enumerate
for i, value in enumerate(['a', 'b', 'c']):
    print(f"{i}: {value}")

# any, all
has_even = any(x % 2 == 0 for x in numbers)
all_positive = all(x > 0 for x in numbers)
```

## Common Patterns

### Slicing
```python
arr = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
first_three = arr[:3]        # [0, 1, 2]
last_three = arr[-3:]        # [7, 8, 9]
middle = arr[2:5]            # [2, 3, 4]
every_second = arr[::2]      # [0, 2, 4, 6, 8]
reversed_arr = arr[::-1]     # [9, 8, 7, ..., 0]
```

### Unpacking
```python
# Basic unpacking
a, b, c = [1, 2, 3]

# Star unpacking
first, *middle, last = [1, 2, 3, 4, 5]

# Dictionary unpacking
def process(name, age, city):
    pass

data = {"name": "John", "age": 30, "city": "NYC"}
process(**data)
```

### String Methods
```python
text = "  Hello, World!  "
text.strip()           # Remove whitespace
text.lower()           # Convert to lowercase
text.upper()           # Convert to uppercase
text.split(',')        # Split by delimiter
text.replace('o', '0') # Replace characters
text.startswith('He')  # Check prefix
text.endswith('!')     # Check suffix

# f-strings (Python 3.6+)
name = "Alice"
age = 30
message = f"My name is {name} and I'm {age} years old"
```

## Important Standard Library Modules

### collections
```python
from collections import Counter, defaultdict, deque, namedtuple

# Counter
count = Counter(['a', 'b', 'c', 'a', 'b', 'a'])
# Counter({'a': 3, 'b': 2, 'c': 1})

# defaultdict
dd = defaultdict(list)
dd['key'].append(1)  # No KeyError

# deque
dq = deque([1, 2, 3])
dq.appendleft(0)
dq.pop()

# namedtuple
Point = namedtuple('Point', ['x', 'y'])
p = Point(10, 20)
```

### itertools
```python
from itertools import combinations, permutations, product, chain

# Combinations
list(combinations([1, 2, 3], 2))  # [(1,2), (1,3), (2,3)]

# Permutations
list(permutations([1, 2, 3], 2))  # [(1,2), (1,3), (2,1), (2,3), (3,1), (3,2)]

# Cartesian product
list(product([1, 2], ['a', 'b']))  # [(1,'a'), (1,'b'), (2,'a'), (2,'b')]

# Chain
list(chain([1, 2], [3, 4]))  # [1, 2, 3, 4]
```

## Performance Tips

### Use Built-in Functions
```python
# Fast
sum([1, 2, 3, 4, 5])

# Slow
total = 0
for num in [1, 2, 3, 4, 5]:
    total += num
```

### List Comprehensions vs Loops
```python
# Faster
squares = [x**2 for x in range(1000)]

# Slower
squares = []
for x in range(1000):
    squares.append(x**2)
```

### Use Sets for Membership Testing
```python
# Fast: O(1)
items = {1, 2, 3, 4, 5}
if 3 in items:
    pass

# Slow: O(n)
items = [1, 2, 3, 4, 5]
if 3 in items:
    pass
```

## Common Interview Topics

### Mutable vs Immutable
- **Immutable**: int, float, str, tuple, frozenset
- **Mutable**: list, dict, set

```python
# Immutable strings
s = "hello"
s[0] = 'H'  # Error!

# Mutable lists
lst = [1, 2, 3]
lst[0] = 0  # OK
```

### Shallow vs Deep Copy
```python
import copy

original = [[1, 2], [3, 4]]

# Shallow copy
shallow = original.copy()
shallow[0][0] = 99  # Affects original!

# Deep copy
deep = copy.deepcopy(original)
deep[0][0] = 99  # Doesn't affect original
```

### GIL (Global Interpreter Lock)
- Only one thread executes Python bytecode at a time
- Use multiprocessing for CPU-bound tasks
- Threading OK for I/O-bound tasks

### Memory Management
- Reference counting
- Garbage collection for cycles
- `del` to remove references

## Best Practices

1. **Follow PEP 8**: Style guide
2. **Use descriptive names**: `calculate_total` not `calc_tot`
3. **Write docstrings**: Document functions
4. **Use type hints**: Improve code clarity
5. **Avoid mutable default arguments**
6. **Use context managers**: For resources
7. **Prefer list comprehensions**: More Pythonic
8. **Use f-strings**: Modern string formatting
9. **Handle exceptions properly**: Don't use bare except
10. **Keep functions small**: Single responsibility

## Interview Questions

- Explain the difference between list and tuple
- What are decorators?
- What is the GIL?
- Explain generators and yield
- What's the difference between `is` and `==`?
- How does Python manage memory?
- What are list comprehensions?
- Explain `*args` and `**kwargs`
- What's the difference between shallow and deep copy?
- How do you handle exceptions in Python?
