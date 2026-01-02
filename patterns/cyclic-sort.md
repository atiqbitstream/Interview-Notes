# Cyclic Sort Pattern

## Overview
Cyclic sort is used for problems involving arrays containing numbers in a given range. It places each number at its correct index.

## When to Use
- Array contains numbers from 1 to n
- Finding missing/duplicate numbers
- In-place sorting required
- O(n) time complexity needed

## Basic Pattern
```python
def cyclic_sort(nums):
    """Sort array where nums[i] should be at index nums[i]-1"""
    i = 0
    while i < len(nums):
        correct_index = nums[i] - 1
        
        if nums[i] != nums[correct_index]:
            # Swap to correct position
            nums[i], nums[correct_index] = nums[correct_index], nums[i]
        else:
            i += 1
    
    return nums
```

## Common Problems

### 1. Missing Number
```python
def find_missing_number(nums):
    """Find missing number from 0 to n"""
    i = 0
    n = len(nums)
    
    # Place each number at correct index
    while i < n:
        correct_index = nums[i]
        if nums[i] < n and nums[i] != nums[correct_index]:
            nums[i], nums[correct_index] = nums[correct_index], nums[i]
        else:
            i += 1
    
    # Find missing number
    for i in range(n):
        if nums[i] != i:
            return i
    
    return n
```

### 2. Find All Missing Numbers
```python
def find_disappeared_numbers(nums):
    """Find all numbers missing from 1 to n"""
    i = 0
    n = len(nums)
    
    # Cyclic sort
    while i < n:
        correct_index = nums[i] - 1
        if nums[i] != nums[correct_index]:
            nums[i], nums[correct_index] = nums[correct_index], nums[i]
        else:
            i += 1
    
    # Find missing numbers
    missing = []
    for i in range(n):
        if nums[i] != i + 1:
            missing.append(i + 1)
    
    return missing
```

### 3. Find Duplicate Number
```python
def find_duplicate(nums):
    """Find duplicate number in array"""
    i = 0
    
    while i < len(nums):
        if nums[i] != i + 1:
            correct_index = nums[i] - 1
            if nums[i] != nums[correct_index]:
                nums[i], nums[correct_index] = nums[correct_index], nums[i]
            else:
                return nums[i]
        else:
            i += 1
    
    return -1
```

### 4. Find All Duplicates
```python
def find_duplicates(nums):
    """Find all duplicates in array"""
    i = 0
    
    # Cyclic sort
    while i < len(nums):
        correct_index = nums[i] - 1
        if nums[i] != nums[correct_index]:
            nums[i], nums[correct_index] = nums[correct_index], nums[i]
        else:
            i += 1
    
    # Find duplicates
    duplicates = []
    for i in range(len(nums)):
        if nums[i] != i + 1:
            duplicates.append(nums[i])
    
    return duplicates
```

### 5. First Missing Positive
```python
def first_missing_positive(nums):
    """Find smallest missing positive integer"""
    n = len(nums)
    i = 0
    
    # Place positive integers at correct positions
    while i < n:
        correct_index = nums[i] - 1
        if 0 < nums[i] <= n and nums[i] != nums[correct_index]:
            nums[i], nums[correct_index] = nums[correct_index], nums[i]
        else:
            i += 1
    
    # Find first missing positive
    for i in range(n):
        if nums[i] != i + 1:
            return i + 1
    
    return n + 1
```

### 6. Find Corrupted Pair
```python
def find_error_nums(nums):
    """Find number that appears twice and number that's missing"""
    i = 0
    
    # Cyclic sort
    while i < len(nums):
        correct_index = nums[i] - 1
        if nums[i] != nums[correct_index]:
            nums[i], nums[correct_index] = nums[correct_index], nums[i]
        else:
            i += 1
    
    # Find duplicate and missing
    for i in range(len(nums)):
        if nums[i] != i + 1:
            return [nums[i], i + 1]
    
    return [-1, -1]
```

## Key Characteristics

### Pattern Recognition
Use when you see:
- Array with numbers from **1 to n** or **0 to n**
- Find **missing** or **duplicate** numbers
- **In-place** solution required
- **O(n) time** and **O(1) space**

### Cyclic Sort Conditions
```python
# Number should be at index: nums[i] - 1
# Swap condition: nums[i] != nums[correct_index]
# Skip condition: nums[i] out of range or already correct
```

## Time and Space Complexity
**Time:** O(n) - each number swapped at most once
**Space:** O(1) - in-place sorting

## Algorithm Steps
1. **Iterate** through array
2. **Calculate** correct index for current number
3. **Swap** if not at correct position
4. **Skip** if already correct or out of range
5. **Find** missing/duplicate in second pass

## Common Mistakes
1. Not handling out-of-range numbers
2. Infinite loops from incorrect swap conditions
3. Not checking if number already at correct position
4. Modifying while iterating in wrong way

## Tips
1. **Draw it out**: Visualize swaps
2. **Check bounds**: Handle numbers out of range
3. **Swap carefully**: Avoid infinite loops
4. **Second pass**: Usually needed to find answer
5. **Consider variations**: 0 to n vs 1 to n

## Comparison with Other Sorting

### vs Quick Sort
- Cyclic: O(n) for specific range
- Quick: O(n log n) general purpose

### vs Counting Sort
- Cyclic: O(1) space, in-place
- Counting: O(n) extra space

## More Problems
1. **Set Mismatch**
2. **Find the Duplicate Number** (various approaches)
3. **Missing Number**
4. **First Missing Positive**
5. **Find All Numbers Disappeared in Array**
6. **Find All Duplicates in Array**
