# Arrays and Strings

## Overview
Arrays and strings are fundamental data structures that store elements in contiguous memory locations.

## Key Concepts

### Arrays
- **Definition**: Collection of elements stored at contiguous memory locations
- **Time Complexity**:
  - Access: O(1)
  - Search: O(n)
  - Insertion: O(n)
  - Deletion: O(n)
- **Space Complexity**: O(n)

### Strings
- **Definition**: Sequence of characters
- **Immutability**: In many languages (Java, Python), strings are immutable
- **Operations**: Concatenation, substring, searching, pattern matching

## Common Techniques

### 1. Two Pointers
Use two pointers to iterate through the array from different positions.
```python
def reverse_array(arr):
    left, right = 0, len(arr) - 1
    while left < right:
        arr[left], arr[right] = arr[right], arr[left]
        left += 1
        right -= 1
```

### 2. Sliding Window
Maintain a window of elements while traversing.
```python
def max_sum_subarray(arr, k):
    max_sum = sum(arr[:k])
    window_sum = max_sum
    for i in range(k, len(arr)):
        window_sum = window_sum - arr[i - k] + arr[i]
        max_sum = max(max_sum, window_sum)
    return max_sum
```

### 3. Hash Maps for Fast Lookup
Use dictionaries/maps to track elements.
```python
def two_sum(nums, target):
    seen = {}
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
```

## Common Problems

1. **Two Sum**: Find two numbers that add up to a target
2. **Valid Anagram**: Check if two strings are anagrams
3. **Longest Substring Without Repeating Characters**
4. **Container With Most Water**
5. **Product of Array Except Self**
6. **Rotate Array**
7. **Valid Palindrome**
8. **String to Integer (atoi)**

## Tips
- Always check for edge cases: empty array, single element, duplicates
- Consider in-place operations to optimize space
- String manipulation: use StringBuilder in Java for efficient concatenation
- Be aware of integer overflow in calculations
- Practice common string algorithms: KMP, Rabin-Karp

## Interview Questions
- How would you reverse a string in-place?
- Explain the difference between mutable and immutable strings
- What's the time complexity of string concatenation in a loop?
- How do you remove duplicates from an array?
