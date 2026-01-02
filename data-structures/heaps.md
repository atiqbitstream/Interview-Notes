# Heaps

## Overview
A heap is a complete binary tree that satisfies the heap property. It's commonly used to implement priority queues.

## Types of Heaps

### 1. Min Heap
- Parent node ≤ all children nodes
- Root is the minimum element
- Used for: finding minimum, ascending priority

### 2. Max Heap
- Parent node ≥ all children nodes
- Root is the maximum element
- Used for: finding maximum, descending priority

## Time Complexity
- **Insert**: O(log n)
- **Delete Min/Max**: O(log n)
- **Get Min/Max**: O(1)
- **Heapify**: O(n)
- **Search**: O(n)

## Space Complexity
O(n)

## Array Representation
Heaps are typically stored in arrays:
- Parent of node at index i: (i - 1) // 2
- Left child of node at index i: 2 * i + 1
- Right child of node at index i: 2 * i + 2

## Implementation in Python

### Using heapq (Min Heap)
```python
import heapq

# Create a min heap
heap = []
heapq.heappush(heap, 5)
heapq.heappush(heap, 3)
heapq.heappush(heap, 7)

# Get minimum element
min_element = heapq.heappop(heap)  # Returns 3

# Convert list to heap
arr = [5, 3, 7, 1, 9]
heapq.heapify(arr)  # O(n) operation

# N smallest/largest elements
heapq.nsmallest(3, arr)
heapq.nlargest(3, arr)
```

### Max Heap in Python
```python
import heapq

# Use negative values for max heap
max_heap = []
heapq.heappush(max_heap, -5)
heapq.heappush(max_heap, -3)
heapq.heappush(max_heap, -7)

# Get maximum element
max_element = -heapq.heappop(max_heap)  # Returns 7
```

### Custom Min Heap Implementation
```python
class MinHeap:
    def __init__(self):
        self.heap = []
    
    def parent(self, i):
        return (i - 1) // 2
    
    def left_child(self, i):
        return 2 * i + 1
    
    def right_child(self, i):
        return 2 * i + 2
    
    def swap(self, i, j):
        self.heap[i], self.heap[j] = self.heap[j], self.heap[i]
    
    def insert(self, val):
        self.heap.append(val)
        self._heapify_up(len(self.heap) - 1)
    
    def _heapify_up(self, i):
        while i > 0 and self.heap[i] < self.heap[self.parent(i)]:
            self.swap(i, self.parent(i))
            i = self.parent(i)
    
    def extract_min(self):
        if not self.heap:
            return None
        if len(self.heap) == 1:
            return self.heap.pop()
        
        min_val = self.heap[0]
        self.heap[0] = self.heap.pop()
        self._heapify_down(0)
        return min_val
    
    def _heapify_down(self, i):
        min_index = i
        left = self.left_child(i)
        right = self.right_child(i)
        
        if left < len(self.heap) and self.heap[left] < self.heap[min_index]:
            min_index = left
        if right < len(self.heap) and self.heap[right] < self.heap[min_index]:
            min_index = right
        
        if min_index != i:
            self.swap(i, min_index)
            self._heapify_down(min_index)
    
    def peek(self):
        return self.heap[0] if self.heap else None
```

## Common Patterns

### 1. K Largest/Smallest Elements
```python
def k_largest(nums, k):
    # Use min heap of size k
    heap = nums[:k]
    heapq.heapify(heap)
    
    for num in nums[k:]:
        if num > heap[0]:
            heapq.heapreplace(heap, num)
    
    return heap
```

### 2. Merge K Sorted Lists
```python
def merge_k_sorted_lists(lists):
    heap = []
    result = []
    
    # Add first element from each list
    for i, lst in enumerate(lists):
        if lst:
            heapq.heappush(heap, (lst[0], i, 0))
    
    while heap:
        val, list_idx, elem_idx = heapq.heappop(heap)
        result.append(val)
        
        if elem_idx + 1 < len(lists[list_idx]):
            next_val = lists[list_idx][elem_idx + 1]
            heapq.heappush(heap, (next_val, list_idx, elem_idx + 1))
    
    return result
```

### 3. Top K Frequent Elements
```python
def top_k_frequent(nums, k):
    from collections import Counter
    count = Counter(nums)
    return heapq.nlargest(k, count.keys(), key=count.get)
```

### 4. Median Finder (Two Heaps)
```python
class MedianFinder:
    def __init__(self):
        self.small = []  # max heap (negative values)
        self.large = []  # min heap
    
    def add_num(self, num):
        heapq.heappush(self.small, -num)
        
        # Balance heaps
        if self.small and self.large and -self.small[0] > self.large[0]:
            val = -heapq.heappop(self.small)
            heapq.heappush(self.large, val)
        
        # Maintain size property
        if len(self.small) > len(self.large) + 1:
            val = -heapq.heappop(self.small)
            heapq.heappush(self.large, val)
        if len(self.large) > len(self.small):
            val = heapq.heappop(self.large)
            heapq.heappush(self.small, -val)
    
    def find_median(self):
        if len(self.small) > len(self.large):
            return -self.small[0]
        return (-self.small[0] + self.large[0]) / 2
```

## Common Problems

1. **Kth Largest Element in Array**
2. **Top K Frequent Elements**
3. **Merge K Sorted Lists**
4. **Find Median from Data Stream**
5. **Ugly Number II**
6. **Sliding Window Median**
7. **Task Scheduler**
8. **Reorganize String**
9. **K Closest Points to Origin**
10. **Meeting Rooms II**

## When to Use Heaps

Use heaps when you need to:
- Find min/max element repeatedly
- Implement a priority queue
- Find k largest/smallest elements
- Maintain a running median
- Merge k sorted arrays/lists
- Schedule tasks by priority
- Find k closest elements

## Tips
- Python's heapq implements min heap by default
- For max heap, negate values or use custom comparator
- Heaps are great for "top k" problems
- Two heaps technique useful for median problems
- Consider heaps when you need partial sorting
- Heapify operation is more efficient than individual inserts
- Heap + hash map combination is powerful

## Interview Questions
- Explain the difference between min heap and max heap
- What's the time complexity of building a heap?
- How would you find the kth largest element?
- Explain how to find running median using heaps
- When would you use a heap over sorting?
- How is a heap different from a binary search tree?
