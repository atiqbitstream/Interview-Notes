# In-place Reversal of Linked List

## Overview
This pattern reverses parts or all of a linked list in-place without using extra space.

## When to Use
- Reverse entire linked list
- Reverse sublist
- Reverse in groups
- Palindrome checking
- Reordering linked list

## Basic Pattern
```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

def reverse_list(head):
    """Reverse entire linked list"""
    prev = None
    current = head
    
    while current:
        next_node = current.next  # Save next
        current.next = prev       # Reverse link
        prev = current            # Move prev forward
        current = next_node       # Move current forward
    
    return prev
```

## Common Problems

### 1. Reverse Linked List (Recursive)
```python
def reverse_list_recursive(head):
    """Reverse linked list recursively"""
    if not head or not head.next:
        return head
    
    new_head = reverse_list_recursive(head.next)
    head.next.next = head
    head.next = None
    
    return new_head
```

### 2. Reverse Between
```python
def reverse_between(head, left, right):
    """Reverse nodes from position left to right"""
    if not head or left == right:
        return head
    
    dummy = ListNode(0)
    dummy.next = head
    prev = dummy
    
    # Move to node before left
    for _ in range(left - 1):
        prev = prev.next
    
    # Reverse sublist
    current = prev.next
    for _ in range(right - left):
        next_node = current.next
        current.next = next_node.next
        next_node.next = prev.next
        prev.next = next_node
    
    return dummy.next
```

### 3. Reverse Nodes in k-Group
```python
def reverse_k_group(head, k):
    """Reverse nodes in groups of k"""
    def reverse_list(head, k):
        prev = None
        current = head
        
        while k > 0:
            next_node = current.next
            current.next = prev
            prev = current
            current = next_node
            k -= 1
        
        return prev
    
    # Check if at least k nodes left
    count = 0
    ptr = head
    while count < k and ptr:
        ptr = ptr.next
        count += 1
    
    if count == k:
        reversed_head = reverse_list(head, k)
        head.next = reverse_k_group(ptr, k)
        return reversed_head
    
    return head
```

### 4. Reverse Alternate k-Group
```python
def reverse_alternate_k(head, k):
    """Reverse every alternate k nodes"""
    if not head or k == 1:
        return head
    
    current = head
    prev = None
    
    while current:
        # Remember connection point
        last_of_prev_part = prev
        last_of_current_part = current
        
        # Reverse k nodes
        i = 0
        while current and i < k:
            next_node = current.next
            current.next = prev
            prev = current
            current = next_node
            i += 1
        
        # Connect reversed part
        if last_of_prev_part:
            last_of_prev_part.next = prev
        else:
            head = prev
        
        last_of_current_part.next = current
        
        # Skip k nodes
        i = 0
        while current and i < k:
            prev = current
            current = current.next
            i += 1
    
    return head
```

### 5. Rotate List
```python
def rotate_right(head, k):
    """Rotate list to the right by k places"""
    if not head or not head.next or k == 0:
        return head
    
    # Find length and last node
    length = 1
    last = head
    while last.next:
        last = last.next
        length += 1
    
    # Make circular
    last.next = head
    
    # Find new tail
    k = k % length
    steps_to_new_head = length - k
    new_tail = head
    for _ in range(steps_to_new_head - 1):
        new_tail = new_tail.next
    
    # Break circle
    new_head = new_tail.next
    new_tail.next = None
    
    return new_head
```

### 6. Reorder List
```python
def reorder_list(head):
    """Reorder list: L0→Ln→L1→Ln-1→L2→Ln-2→..."""
    if not head or not head.next:
        return
    
    # Find middle
    slow = fast = head
    while fast.next and fast.next.next:
        slow = slow.next
        fast = fast.next.next
    
    # Reverse second half
    second = slow.next
    slow.next = None
    prev = None
    while second:
        next_node = second.next
        second.next = prev
        prev = second
        second = next_node
    second = prev
    
    # Merge two halves
    first = head
    while second:
        temp1, temp2 = first.next, second.next
        first.next = second
        second.next = temp1
        first, second = temp1, temp2
```

### 7. Palindrome Linked List
```python
def is_palindrome(head):
    """Check if linked list is palindrome"""
    if not head or not head.next:
        return True
    
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

## Reversal Template
```python
def reverse_template(head):
    prev = None
    current = head
    
    while current:
        # 1. Save next node
        next_node = current.next
        
        # 2. Reverse the link
        current.next = prev
        
        # 3. Move pointers forward
        prev = current
        current = next_node
    
    return prev  # New head
```

## Time and Space Complexity
**Time:** O(n)
**Space:** O(1) - in-place reversal

## Key Points
1. **Three pointers**: prev, current, next
2. **Save next**: Before changing pointers
3. **Reverse link**: current.next = prev
4. **Move forward**: Update both pointers
5. **Return prev**: New head after reversal

## Common Mistakes
1. Not saving next node before reversing
2. Losing reference to rest of list
3. Not handling empty list
4. Not handling single node
5. Off-by-one errors in partial reversal

## Tips
1. **Draw it out**: Visualize pointer movements
2. **Use dummy node**: For complex operations
3. **Break into steps**: Find middle, reverse, merge
4. **Test edge cases**: Empty, one node, two nodes
5. **Practice**: This pattern is very common

## More Problems
1. **Reverse Linked List**
2. **Reverse Linked List II**
3. **Reverse Nodes in k-Group**
4. **Swap Nodes in Pairs**
5. **Rotate List**
6. **Reorder List**
7. **Palindrome Linked List**
8. **Add Two Numbers II**
