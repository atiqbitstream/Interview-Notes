# Stacks and Queues

## Stacks

### Overview
A stack is a LIFO (Last In First Out) data structure.

### Operations
- **Push**: O(1) - Add element to top
- **Pop**: O(1) - Remove element from top
- **Peek/Top**: O(1) - View top element
- **isEmpty**: O(1) - Check if stack is empty

### Implementation
```python
class Stack:
    def __init__(self):
        self.items = []
    
    def push(self, item):
        self.items.append(item)
    
    def pop(self):
        if not self.is_empty():
            return self.items.pop()
    
    def peek(self):
        if not self.is_empty():
            return self.items[-1]
    
    def is_empty(self):
        return len(self.items) == 0
```

### Common Stack Problems
1. **Valid Parentheses**
2. **Min Stack** (stack with O(1) min operation)
3. **Evaluate Reverse Polish Notation**
4. **Daily Temperatures**
5. **Largest Rectangle in Histogram**

## Queues

### Overview
A queue is a FIFO (First In First Out) data structure.

### Operations
- **Enqueue**: O(1) - Add element to rear
- **Dequeue**: O(1) - Remove element from front
- **Front**: O(1) - View front element
- **isEmpty**: O(1) - Check if queue is empty

### Implementation
```python
from collections import deque

class Queue:
    def __init__(self):
        self.items = deque()
    
    def enqueue(self, item):
        self.items.append(item)
    
    def dequeue(self):
        if not self.is_empty():
            return self.items.popleft()
    
    def front(self):
        if not self.is_empty():
            return self.items[0]
    
    def is_empty(self):
        return len(self.items) == 0
```

### Types of Queues

#### 1. Simple Queue
Standard FIFO queue.

#### 2. Circular Queue
Last position connects back to first position.

#### 3. Priority Queue
Elements have priorities; highest priority dequeued first.
```python
import heapq

class PriorityQueue:
    def __init__(self):
        self.heap = []
    
    def push(self, item, priority):
        heapq.heappush(self.heap, (priority, item))
    
    def pop(self):
        return heapq.heappop(self.heap)[1]
```

#### 4. Deque (Double-ended Queue)
Insertion and deletion at both ends.

## Common Patterns

### 1. Monotonic Stack
Maintain elements in monotonic order.
```python
def next_greater_element(nums):
    result = [-1] * len(nums)
    stack = []
    for i in range(len(nums)):
        while stack and nums[stack[-1]] < nums[i]:
            result[stack.pop()] = nums[i]
        stack.append(i)
    return result
```

### 2. BFS with Queue
Level-order traversal.
```python
def level_order(root):
    if not root:
        return []
    result = []
    queue = deque([root])
    while queue:
        level = []
        for _ in range(len(queue)):
            node = queue.popleft()
            level.append(node.val)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        result.append(level)
    return result
```

## Common Problems

### Stack Problems
1. **Implement Queue using Stacks**
2. **Baseball Game**
3. **Backspace String Compare**
4. **Simplify Path**

### Queue Problems
1. **Implement Stack using Queues**
2. **Design Circular Queue**
3. **Moving Average from Data Stream**
4. **Number of Recent Calls**

## Tips
- Use stacks for problems involving matching, nesting, or backtracking
- Use queues for BFS, level-order traversal, or scheduling
- Monotonic stacks are useful for "next greater/smaller" problems
- Consider using deque for sliding window problems
- Priority queues are great for "kth largest/smallest" problems

## Interview Questions
- Implement a min stack with O(1) operations
- How would you implement a queue using two stacks?
- Explain the difference between stack and queue
- When would you use a priority queue?
