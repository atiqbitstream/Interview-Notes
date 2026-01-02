# Hash Tables

## Overview
A hash table (hash map) is a data structure that maps keys to values using a hash function for efficient lookup.

## Key Concepts

### Hash Function
- Converts keys into array indices
- Should distribute keys uniformly
- Deterministic: same input → same output

### Time Complexity
- **Average Case**:
  - Insert: O(1)
  - Delete: O(1)
  - Search: O(1)
- **Worst Case**: O(n) when all keys hash to same bucket

### Space Complexity
O(n) where n is number of key-value pairs

## Collision Resolution

### 1. Chaining
Store colliding elements in a linked list at each bucket.
```python
class HashTable:
    def __init__(self, size=10):
        self.size = size
        self.table = [[] for _ in range(size)]
    
    def hash_function(self, key):
        return hash(key) % self.size
    
    def insert(self, key, value):
        index = self.hash_function(key)
        for i, (k, v) in enumerate(self.table[index]):
            if k == key:
                self.table[index][i] = (key, value)
                return
        self.table[index].append((key, value))
    
    def get(self, key):
        index = self.hash_function(key)
        for k, v in self.table[index]:
            if k == key:
                return v
        return None
```

### 2. Open Addressing
Find next available slot when collision occurs.
- **Linear Probing**: Check next slot sequentially
- **Quadratic Probing**: Check slots at quadratic intervals
- **Double Hashing**: Use second hash function

## Common Patterns

### 1. Frequency Counting
```python
def count_frequencies(arr):
    freq = {}
    for item in arr:
        freq[item] = freq.get(item, 0) + 1
    return freq
```

### 2. Two Sum Pattern
```python
def two_sum(nums, target):
    seen = {}
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
    return []
```

### 3. Group Anagrams
```python
def group_anagrams(strs):
    anagram_map = {}
    for s in strs:
        key = ''.join(sorted(s))
        if key not in anagram_map:
            anagram_map[key] = []
        anagram_map[key].append(s)
    return list(anagram_map.values())
```

### 4. Sliding Window with Hash Map
```python
def length_of_longest_substring(s):
    char_index = {}
    max_length = 0
    start = 0
    
    for end, char in enumerate(s):
        if char in char_index and char_index[char] >= start:
            start = char_index[char] + 1
        char_index[char] = end
        max_length = max(max_length, end - start + 1)
    
    return max_length
```

## Hash Set

### Overview
Stores only keys without values, used for membership testing.

```python
# Python set operations
s = set([1, 2, 3])
s.add(4)           # Add element
s.remove(2)        # Remove element
3 in s             # Check membership: O(1)
s1.union(s2)       # Union
s1.intersection(s2) # Intersection
s1.difference(s2)   # Difference
```

## Common Problems

1. **Two Sum**
2. **Group Anagrams**
3. **Valid Anagram**
4. **Longest Substring Without Repeating Characters**
5. **First Unique Character in String**
6. **Intersection of Two Arrays**
7. **Contains Duplicate**
8. **Subarray Sum Equals K**
9. **LRU Cache**
10. **Design HashMap**

## Advanced: LRU Cache

Combines hash map with doubly linked list for O(1) operations.

```python
class Node:
    def __init__(self, key, val):
        self.key = key
        self.val = val
        self.prev = None
        self.next = None

class LRUCache:
    def __init__(self, capacity):
        self.capacity = capacity
        self.cache = {}
        self.head = Node(0, 0)
        self.tail = Node(0, 0)
        self.head.next = self.tail
        self.tail.prev = self.head
    
    def _remove(self, node):
        node.prev.next = node.next
        node.next.prev = node.prev
    
    def _add_to_head(self, node):
        node.next = self.head.next
        node.prev = self.head
        self.head.next.prev = node
        self.head.next = node
    
    def get(self, key):
        if key in self.cache:
            node = self.cache[key]
            self._remove(node)
            self._add_to_head(node)
            return node.val
        return -1
    
    def put(self, key, value):
        if key in self.cache:
            self._remove(self.cache[key])
        node = Node(key, value)
        self._add_to_head(node)
        self.cache[key] = node
        
        if len(self.cache) > self.capacity:
            lru = self.tail.prev
            self._remove(lru)
            del self.cache[lru.key]
```

## Tips
- Hash tables are ideal for O(1) lookup requirements
- Use for frequency counting, caching, and fast lookup
- Consider load factor for performance
- Python dict maintains insertion order (3.7+)
- Use defaultdict for simpler frequency counting
- Counter class for counting operations
- Hash tables don't maintain order (unless OrderedDict)

## Interview Questions
- How does a hash function work?
- Explain collision resolution strategies
- What's the difference between HashMap and HashSet?
- How would you implement a hash table from scratch?
- When would you use a hash table vs array?
- What's the time complexity of hash table operations?
