# Sliding Window Pattern

## Overview
The sliding window technique maintains a subset of elements (window) that slides through an array or string to find optimal solutions.

## When to Use
- Contiguous subarray/substring problems
- Finding maximum/minimum of subarrays
- Finding longest/shortest substring with condition
- String pattern matching
- Running averages

## Types of Sliding Windows

### 1. Fixed-Size Window
Window size is constant.

```python
def max_sum_fixed_window(arr, k):
    """Maximum sum of subarray of size k"""
    if len(arr) < k:
        return None
    
    # Calculate first window
    window_sum = sum(arr[:k])
    max_sum = window_sum
    
    # Slide window
    for i in range(k, len(arr)):
        window_sum = window_sum - arr[i - k] + arr[i]
        max_sum = max(max_sum, window_sum)
    
    return max_sum
```

### 2. Variable-Size Window
Window size changes based on condition.

```python
def longest_substring_k_distinct(s, k):
    """Longest substring with at most k distinct characters"""
    char_count = {}
    left = 0
    max_length = 0
    
    for right in range(len(s)):
        # Expand window
        char_count[s[right]] = char_count.get(s[right], 0) + 1
        
        # Shrink window if needed
        while len(char_count) > k:
            char_count[s[left]] -= 1
            if char_count[s[left]] == 0:
                del char_count[s[left]]
            left += 1
        
        # Update max length
        max_length = max(max_length, right - left + 1)
    
    return max_length
```

## Common Problems

### 1. Maximum Average Subarray
```python
def find_max_average(nums, k):
    """Find maximum average of subarray of length k"""
    window_sum = sum(nums[:k])
    max_sum = window_sum
    
    for i in range(k, len(nums)):
        window_sum += nums[i] - nums[i - k]
        max_sum = max(max_sum, window_sum)
    
    return max_sum / k
```

### 2. Longest Substring Without Repeating Characters
```python
def length_of_longest_substring(s):
    """Length of longest substring without repeating characters"""
    char_index = {}
    left = 0
    max_length = 0
    
    for right in range(len(s)):
        if s[right] in char_index and char_index[s[right]] >= left:
            left = char_index[s[right]] + 1
        
        char_index[s[right]] = right
        max_length = max(max_length, right - left + 1)
    
    return max_length
```

### 3. Minimum Window Substring
```python
from collections import Counter

def min_window(s, t):
    """Minimum window substring containing all characters of t"""
    if not s or not t:
        return ""
    
    # Count characters in t
    required = Counter(t)
    needed = len(required)
    
    left = 0
    formed = 0
    window_counts = {}
    result = (float('inf'), None, None)  # length, left, right
    
    for right in range(len(s)):
        # Add character from right
        char = s[right]
        window_counts[char] = window_counts.get(char, 0) + 1
        
        if char in required and window_counts[char] == required[char]:
            formed += 1
        
        # Shrink window from left
        while formed == needed and left <= right:
            # Update result if smaller window found
            if right - left + 1 < result[0]:
                result = (right - left + 1, left, right)
            
            # Remove character from left
            char = s[left]
            window_counts[char] -= 1
            if char in required and window_counts[char] < required[char]:
                formed -= 1
            
            left += 1
    
    return "" if result[0] == float('inf') else s[result[1]:result[2] + 1]
```

### 4. Longest Repeating Character Replacement
```python
def character_replacement(s, k):
    """Longest substring after replacing at most k characters"""
    char_count = {}
    left = 0
    max_count = 0
    max_length = 0
    
    for right in range(len(s)):
        char_count[s[right]] = char_count.get(s[right], 0) + 1
        max_count = max(max_count, char_count[s[right]])
        
        # If current window size - max frequency > k, shrink
        while (right - left + 1) - max_count > k:
            char_count[s[left]] -= 1
            left += 1
        
        max_length = max(max_length, right - left + 1)
    
    return max_length
```

### 5. Permutation in String
```python
from collections import Counter

def check_inclusion(s1, s2):
    """Check if s2 contains permutation of s1"""
    if len(s1) > len(s2):
        return False
    
    s1_count = Counter(s1)
    window_count = Counter(s2[:len(s1)])
    
    if s1_count == window_count:
        return True
    
    for i in range(len(s1), len(s2)):
        # Add new character
        window_count[s2[i]] += 1
        
        # Remove old character
        window_count[s2[i - len(s1)]] -= 1
        if window_count[s2[i - len(s1)]] == 0:
            del window_count[s2[i - len(s1)]]
        
        if s1_count == window_count:
            return True
    
    return False
```

### 6. Maximum Fruits in Two Baskets
```python
def total_fruit(fruits):
    """Maximum fruits you can collect with 2 baskets (2 types)"""
    basket = {}
    left = 0
    max_fruits = 0
    
    for right in range(len(fruits)):
        basket[fruits[right]] = basket.get(fruits[right], 0) + 1
        
        # More than 2 types, shrink window
        while len(basket) > 2:
            basket[fruits[left]] -= 1
            if basket[fruits[left]] == 0:
                del basket[fruits[left]]
            left += 1
        
        max_fruits = max(max_fruits, right - left + 1)
    
    return max_fruits
```

### 7. Subarrays with K Different Integers
```python
def subarrays_with_k_distinct(nums, k):
    """Number of subarrays with exactly k distinct integers"""
    def at_most_k(k):
        count = {}
        left = 0
        result = 0
        
        for right in range(len(nums)):
            count[nums[right]] = count.get(nums[right], 0) + 1
            
            while len(count) > k:
                count[nums[left]] -= 1
                if count[nums[left]] == 0:
                    del count[nums[left]]
                left += 1
            
            result += right - left + 1
        
        return result
    
    return at_most_k(k) - at_most_k(k - 1)
```

## Sliding Window Template

```python
def sliding_window(arr):
    left = 0
    window = initialize_window_state()
    result = initial_result_value()
    
    for right in range(len(arr)):
        # Add arr[right] to window
        update_window(window, arr[right])
        
        # Shrink window if needed
        while should_shrink(window):
            # Remove arr[left] from window
            remove_from_window(window, arr[left])
            left += 1
        
        # Update result
        result = update_result(result, window)
    
    return result
```

## Problem Recognition

### Use Sliding Window When You See:
- **Contiguous** subarray/substring
- **Longest/shortest** substring/subarray
- **Maximum/minimum** sum/product
- **At most/exactly K** distinct elements
- **All anagrams** in string

### Keywords:
- "subarray" or "substring"
- "contiguous"
- "longest"
- "shortest"
- "minimum"
- "maximum"
- "at most k"
- "exactly k"

## Time and Space Complexity

**Time:** O(n) - each element visited at most twice
**Space:** O(k) - where k is window size or distinct elements

## Tips

1. **Identify window boundaries**: What defines the window?
2. **Decide expansion strategy**: When to expand right?
3. **Decide shrinking strategy**: When to shrink from left?
4. **Track window state**: Use hash map/counter
5. **Update result**: When and how?

## Common Mistakes

1. **Not handling empty input**: Check edge cases
2. **Wrong window size**: Off-by-one errors
3. **Not shrinking window**: Forgetting while loop
4. **Not updating result**: Update at right time
5. **Inefficient state tracking**: Use hash map efficiently

## More Practice Problems

1. **Minimum Size Subarray Sum**
2. **Fruit Into Baskets**
3. **Grumpy Bookstore Owner**
4. **Max Consecutive Ones III**
5. **Count Number of Nice Subarrays**
6. **Replace the Substring for Balanced String**
7. **Longest Substring with At Most Two Distinct Characters**
8. **Longest Substring with At Least K Repeating Characters**
9. **Find All Anagrams in a String**
10. **Substring with Concatenation of All Words**
