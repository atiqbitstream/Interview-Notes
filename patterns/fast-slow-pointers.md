# Fast and Slow Pointers

## Overview
The fast and slow pointers (also called Floyd's Cycle Detection or Tortoise and Hare) technique uses two pointers moving at different speeds to detect cycles or find middle elements in linked structures.

## When to Use
- Detecting cycles in linked list
- Finding middle of linked list
- Finding cycle start in linked list
- Happy number problems
- Palindrome linked list

## Basic Pattern
```python
def has_cycle(head):
    """Detect if linked list has a cycle"""
    slow = fast = head
    
    while fast and fast.next:
        slow = slow.next        # Move 1 step
        fast = fast.next.next   # Move 2 steps
        
        if slow == fast:
            return True
    
    return False
```

## Common Problems

### 1. Linked List Cycle
```python
def detect_cycle(head):
    """Return node where cycle begins, or None"""
    slow = fast = head
    
    # Detect cycle
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            break
    else:
        return None
    
    # Find cycle start
    slow = head
    while slow != fast:
        slow = slow.next
        fast = fast.next
    
    return slow
```

### 2. Middle of Linked List
```python
def find_middle(head):
    """Find middle node of linked list"""
    slow = fast = head
    
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    
    return slow
```

### 3. Happy Number
```python
def is_happy(n):
    """Check if number is happy"""
    def get_next(num):
        total = 0
        while num > 0:
            digit = num % 10
            total += digit ** 2
            num //= 10
        return total
    
    slow = fast = n
    
    while True:
        slow = get_next(slow)
        fast = get_next(get_next(fast))
        
        if fast == 1:
            return True
        if slow == fast:
            return False
```

### 4. Palindrome Linked List
```python
def is_palindrome(head):
    """Check if linked list is palindrome"""
    # Find middle
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    
    # Reverse second half
    prev = None
    while slow:
        next_node = slow.next
        slow.next = prev
        prev = slow
        slow = next_node
    
    # Compare both halves
    left, right = head, prev
    while right:
        if left.val != right.val:
            return False
        left = left.next
        right = right.next
    
    return True
```

### 5. Circular Array Loop
```python
def circular_array_loop(nums):
    """Check if circular array has a cycle"""
    def get_next_index(i):
        return (i + nums[i]) % len(nums)
    
    for i in range(len(nums)):
        slow = fast = i
        forward = nums[i] > 0
        
        while True:
            slow = get_next_index(slow)
            if nums[slow] > 0 != forward:
                break
            
            fast = get_next_index(fast)
            if nums[fast] > 0 != forward:
                break
            fast = get_next_index(fast)
            if nums[fast] > 0 != forward:
                break
            
            if slow == fast:
                if slow == get_next_index(slow):
                    break
                return True
    
    return False
```

### 6. Find Duplicate Number
```python
def find_duplicate(nums):
    """Find duplicate in array (cycle detection)"""
    # Treat array as linked list where nums[i] points to index nums[i]
    slow = fast = nums[0]
    
    # Detect cycle
    while True:
        slow = nums[slow]
        fast = nums[nums[fast]]
        if slow == fast:
            break
    
    # Find cycle start (duplicate)
    slow = nums[0]
    while slow != fast:
        slow = nums[slow]
        fast = nums[fast]
    
    return slow
```

## Time and Space Complexity
**Time:** O(n)
**Space:** O(1)

## Tips
1. Fast pointer moves twice as fast as slow
2. If there's a cycle, they will meet
3. To find cycle start, reset one pointer to head
4. Works on any structure that can be traversed
5. Great for O(1) space solutions

## More Problems
1. **Remove Nth Node From End**
2. **Reorder List**
3. **Linked List Cycle II**
4. **Intersection of Two Linked Lists**
5. **Rotate List**
