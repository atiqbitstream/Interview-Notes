# Sorting and Searching

## Sorting Algorithms

### 1. Bubble Sort
**Time**: O(n²), **Space**: O(1)
```python
def bubble_sort(arr):
    n = len(arr)
    for i in range(n):
        swapped = False
        for j in range(0, n - i - 1):
            if arr[j] > arr[j + 1]:
                arr[j], arr[j + 1] = arr[j + 1], arr[j]
                swapped = True
        if not swapped:
            break
```

### 2. Selection Sort
**Time**: O(n²), **Space**: O(1)
```python
def selection_sort(arr):
    n = len(arr)
    for i in range(n):
        min_idx = i
        for j in range(i + 1, n):
            if arr[j] < arr[min_idx]:
                min_idx = j
        arr[i], arr[min_idx] = arr[min_idx], arr[i]
```

### 3. Insertion Sort
**Time**: O(n²), **Space**: O(1)
- Efficient for small or nearly sorted data
```python
def insertion_sort(arr):
    for i in range(1, len(arr)):
        key = arr[i]
        j = i - 1
        while j >= 0 and arr[j] > key:
            arr[j + 1] = arr[j]
            j -= 1
        arr[j + 1] = key
```

### 4. Merge Sort
**Time**: O(n log n), **Space**: O(n)
- Stable, divide and conquer
```python
def merge_sort(arr):
    if len(arr) <= 1:
        return arr
    
    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])
    
    return merge(left, right)

def merge(left, right):
    result = []
    i = j = 0
    
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    
    result.extend(left[i:])
    result.extend(right[j:])
    return result
```

### 5. Quick Sort
**Time**: O(n log n) average, O(n²) worst, **Space**: O(log n)
- In-place, not stable
```python
def quick_sort(arr, low, high):
    if low < high:
        pi = partition(arr, low, high)
        quick_sort(arr, low, pi - 1)
        quick_sort(arr, pi + 1, high)

def partition(arr, low, high):
    pivot = arr[high]
    i = low - 1
    
    for j in range(low, high):
        if arr[j] <= pivot:
            i += 1
            arr[i], arr[j] = arr[j], arr[i]
    
    arr[i + 1], arr[high] = arr[high], arr[i + 1]
    return i + 1
```

### 6. Heap Sort
**Time**: O(n log n), **Space**: O(1)
```python
def heap_sort(arr):
    n = len(arr)
    
    # Build max heap
    for i in range(n // 2 - 1, -1, -1):
        heapify(arr, n, i)
    
    # Extract elements
    for i in range(n - 1, 0, -1):
        arr[0], arr[i] = arr[i], arr[0]
        heapify(arr, i, 0)

def heapify(arr, n, i):
    largest = i
    left = 2 * i + 1
    right = 2 * i + 2
    
    if left < n and arr[left] > arr[largest]:
        largest = left
    if right < n and arr[right] > arr[largest]:
        largest = right
    
    if largest != i:
        arr[i], arr[largest] = arr[largest], arr[i]
        heapify(arr, n, largest)
```

### 7. Counting Sort
**Time**: O(n + k), **Space**: O(k)
- For integers in a limited range
```python
def counting_sort(arr):
    max_val = max(arr)
    count = [0] * (max_val + 1)
    
    for num in arr:
        count[num] += 1
    
    index = 0
    for num in range(len(count)):
        for _ in range(count[num]):
            arr[index] = num
            index += 1
```

## Searching Algorithms

### 1. Linear Search
**Time**: O(n), **Space**: O(1)
```python
def linear_search(arr, target):
    for i, val in enumerate(arr):
        if val == target:
            return i
    return -1
```

### 2. Binary Search
**Time**: O(log n), **Space**: O(1)
- Requires sorted array
```python
def binary_search(arr, target):
    left, right = 0, len(arr) - 1
    
    while left <= right:
        mid = left + (right - left) // 2
        
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    
    return -1
```

### Binary Search Variations

#### Find First Occurrence
```python
def find_first(arr, target):
    left, right = 0, len(arr) - 1
    result = -1
    
    while left <= right:
        mid = left + (right - left) // 2
        if arr[mid] == target:
            result = mid
            right = mid - 1
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    
    return result
```

#### Find Last Occurrence
```python
def find_last(arr, target):
    left, right = 0, len(arr) - 1
    result = -1
    
    while left <= right:
        mid = left + (right - left) // 2
        if arr[mid] == target:
            result = mid
            left = mid + 1
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    
    return result
```

#### Search in Rotated Sorted Array
```python
def search_rotated(nums, target):
    left, right = 0, len(nums) - 1
    
    while left <= right:
        mid = left + (right - left) // 2
        
        if nums[mid] == target:
            return mid
        
        if nums[left] <= nums[mid]:
            if nums[left] <= target < nums[mid]:
                right = mid - 1
            else:
                left = mid + 1
        else:
            if nums[mid] < target <= nums[right]:
                left = mid + 1
            else:
                right = mid - 1
    
    return -1
```

## Common Problems

### Sorting Problems
1. **Merge Intervals**
2. **Largest Number**
3. **Sort Colors** (Dutch National Flag)
4. **Meeting Rooms II**
5. **K Closest Points to Origin**

### Searching Problems
1. **Find Peak Element**
2. **Search in Rotated Sorted Array**
3. **Find Minimum in Rotated Sorted Array**
4. **Search a 2D Matrix**
5. **Find First and Last Position**
6. **Sqrt(x)**
7. **Valid Perfect Square**

## Tips
- Binary search requires sorted data
- Watch for integer overflow: use `mid = left + (right - left) // 2`
- Consider binary search for "find minimum/maximum value that satisfies condition"
- Quick sort is often fastest in practice
- Merge sort is stable and predictable
- Use counting sort when range is limited
- Binary search variations are common in interviews

## Interview Questions
- Explain time/space complexity of different sorting algorithms
- When would you choose merge sort over quick sort?
- How does binary search work on rotated array?
- What's the difference between stable and unstable sort?
