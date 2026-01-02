# Two Pointers Pattern

## Overview
The two pointers technique uses two pointers to iterate through a data structure, typically moving toward or away from each other or at different speeds.

## When to Use
- Array or string problems
- Finding pairs with specific properties
- Removing duplicates
- Palindrome problems
- Merging sorted arrays
- Partitioning arrays

## Types of Two Pointers

### 1. Opposite Direction (Converging)
Pointers start at opposite ends and move toward each other.

```python
def two_sum_sorted(arr, target):
    """Find two numbers that sum to target in sorted array"""
    left, right = 0, len(arr) - 1
    
    while left < right:
        current_sum = arr[left] + arr[right]
        
        if current_sum == target:
            return [left, right]
        elif current_sum < target:
            left += 1
        else:
            right -= 1
    
    return []
```

### 2. Same Direction (Slow and Fast)
Both pointers move in same direction at different speeds.

```python
def remove_duplicates(nums):
    """Remove duplicates from sorted array in-place"""
    if not nums:
        return 0
    
    slow = 0
    for fast in range(1, len(nums)):
        if nums[fast] != nums[slow]:
            slow += 1
            nums[slow] = nums[fast]
    
    return slow + 1
```

### 3. Sliding Window (Special Case)
Two pointers maintain a window that slides through array.

```python
def max_sum_subarray(arr, k):
    """Maximum sum of subarray of size k"""
    window_sum = sum(arr[:k])
    max_sum = window_sum
    
    for i in range(k, len(arr)):
        window_sum = window_sum - arr[i - k] + arr[i]
        max_sum = max(max_sum, window_sum)
    
    return max_sum
```

## Common Problems

### 1. Valid Palindrome
```python
def is_palindrome(s):
    """Check if string is palindrome"""
    left, right = 0, len(s) - 1
    
    while left < right:
        # Skip non-alphanumeric
        while left < right and not s[left].isalnum():
            left += 1
        while left < right and not s[right].isalnum():
            right -= 1
        
        # Compare
        if s[left].lower() != s[right].lower():
            return False
        
        left += 1
        right -= 1
    
    return True
```

### 2. Container With Most Water
```python
def max_area(height):
    """Maximum area between two lines"""
    left, right = 0, len(height) - 1
    max_water = 0
    
    while left < right:
        width = right - left
        current_water = min(height[left], height[right]) * width
        max_water = max(max_water, current_water)
        
        # Move pointer with smaller height
        if height[left] < height[right]:
            left += 1
        else:
            right -= 1
    
    return max_water
```

### 3. Three Sum
```python
def three_sum(nums):
    """Find all triplets that sum to zero"""
    nums.sort()
    result = []
    
    for i in range(len(nums) - 2):
        # Skip duplicates
        if i > 0 and nums[i] == nums[i - 1]:
            continue
        
        # Two pointers for remaining elements
        left, right = i + 1, len(nums) - 1
        
        while left < right:
            total = nums[i] + nums[left] + nums[right]
            
            if total == 0:
                result.append([nums[i], nums[left], nums[right]])
                
                # Skip duplicates
                while left < right and nums[left] == nums[left + 1]:
                    left += 1
                while left < right and nums[right] == nums[right - 1]:
                    right -= 1
                
                left += 1
                right -= 1
            elif total < 0:
                left += 1
            else:
                right -= 1
    
    return result
```

### 4. Move Zeroes
```python
def move_zeroes(nums):
    """Move all zeros to end, maintain order of non-zeros"""
    slow = 0
    
    for fast in range(len(nums)):
        if nums[fast] != 0:
            nums[slow], nums[fast] = nums[fast], nums[slow]
            slow += 1
```

### 5. Sort Colors (Dutch National Flag)
```python
def sort_colors(nums):
    """Sort array of 0s, 1s, and 2s in-place"""
    low, mid, high = 0, 0, len(nums) - 1
    
    while mid <= high:
        if nums[mid] == 0:
            nums[low], nums[mid] = nums[mid], nums[low]
            low += 1
            mid += 1
        elif nums[mid] == 1:
            mid += 1
        else:  # nums[mid] == 2
            nums[mid], nums[high] = nums[high], nums[mid]
            high -= 1
```

### 6. Trapping Rain Water
```python
def trap(height):
    """Calculate trapped rainwater"""
    if not height:
        return 0
    
    left, right = 0, len(height) - 1
    left_max, right_max = height[left], height[right]
    water = 0
    
    while left < right:
        if left_max < right_max:
            left += 1
            left_max = max(left_max, height[left])
            water += left_max - height[left]
        else:
            right -= 1
            right_max = max(right_max, height[right])
            water += right_max - height[right]
    
    return water
```

## Problem Recognition Patterns

### Use Two Pointers When You See:
- **Sorted array** problems
- **Pairs** with target sum/difference
- **Palindrome** verification
- **In-place** modifications required
- **Removing** duplicates/elements
- **Partitioning** arrays
- **Merging** sorted arrays

### Keywords to Look For:
- "sorted array"
- "in-place"
- "without extra space"
- "pairs that..."
- "two elements that..."
- "palindrome"
- "remove duplicates"

## Common Mistakes to Avoid

1. **Off-by-one errors**: Carefully check loop conditions
2. **Not handling edge cases**: Empty array, single element
3. **Infinite loops**: Ensure pointers always progress
4. **Forgetting to skip duplicates**: In problems like 3Sum
5. **Not sorting when needed**: Some problems require sorted input

## Time and Space Complexity

**Time:** O(n) - typically single pass through array
**Space:** O(1) - only using pointers, no extra space

## Tips

1. **Draw it out**: Visualize pointer movements
2. **Check invariants**: What's true before/after each iteration?
3. **Consider edge cases**: Empty, single element, all same
4. **Sort if needed**: Many problems easier with sorted input
5. **Practice movement**: Understand when to move which pointer

## More Practice Problems

1. **Two Sum II - Input Array Is Sorted**
2. **Remove Duplicates from Sorted Array**
3. **Squares of a Sorted Array**
4. **Reverse String**
5. **Valid Palindrome II**
6. **Partition List**
7. **Linked List Cycle** (fast/slow pointers)
8. **Middle of Linked List** (fast/slow pointers)
9. **Remove Nth Node From End** (two pointers with gap)
10. **Intersection of Two Arrays**

## Comparison with Other Patterns

**vs. Sliding Window:** 
- Two pointers: More general, various movements
- Sliding window: Specific case, maintains window

**vs. Binary Search:**
- Two pointers: Linear traversal, multiple elements
- Binary search: Logarithmic, finding single element

**vs. Hash Map:**
- Two pointers: O(1) space, requires sorted/special structure
- Hash map: O(n) space, works on unsorted data
