# Linked Lists

## Overview
A linked list is a linear data structure where elements are stored in nodes, and each node points to the next node.

## Types of Linked Lists

### 1. Singly Linked List
Each node contains data and a pointer to the next node.
```python
class Node:
    def __init__(self, data):
        self.data = data
        self.next = None
```

### 2. Doubly Linked List
Each node has pointers to both next and previous nodes.
```python
class Node:
    def __init__(self, data):
        self.data = data
        self.next = None
        self.prev = None
```

### 3. Circular Linked List
Last node points back to the first node.

## Time Complexity
- Access: O(n)
- Search: O(n)
- Insertion (at head): O(1)
- Insertion (at position): O(n)
- Deletion (at head): O(1)
- Deletion (at position): O(n)

## Common Techniques

### 1. Two Pointers (Fast and Slow)
Detect cycles and find middle element.
```python
def has_cycle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            return True
    return False
```

### 2. Reverse Linked List
Classic problem with iterative and recursive solutions.
```python
def reverse_list(head):
    prev = None
    current = head
    while current:
        next_temp = current.next
        current.next = prev
        prev = current
        current = next_temp
    return prev
```

### 3. Dummy Head Node
Simplify edge cases by using a dummy node.
```python
def merge_two_lists(l1, l2):
    dummy = Node(0)
    current = dummy
    while l1 and l2:
        if l1.data < l2.data:
            current.next = l1
            l1 = l1.next
        else:
            current.next = l2
            l2 = l2.next
        current = current.next
    current.next = l1 or l2
    return dummy.next
```

## Common Problems

1. **Reverse Linked List**
2. **Detect Cycle in Linked List**
3. **Merge Two Sorted Lists**
4. **Remove Nth Node From End**
5. **Find Middle of Linked List**
6. **Palindrome Linked List**
7. **Intersection of Two Linked Lists**
8. **Add Two Numbers** (represented as linked lists)

## Tips
- Always check for null pointers
- Draw diagrams to visualize pointer changes
- Consider edge cases: empty list, single node, two nodes
- Use dummy head node to simplify code
- Fast and slow pointer technique is powerful
- Practice both iterative and recursive solutions

## Interview Questions
- How do you detect a cycle in a linked list?
- What's the advantage of linked lists over arrays?
- How would you find the middle element in one pass?
- Explain in-place reversal of a linked list
