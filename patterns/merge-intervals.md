# Merge Intervals Pattern

## Overview
The merge intervals pattern deals with overlapping intervals and involves merging, inserting, or finding intersections.

## When to Use
- Overlapping intervals
- Merging intervals
- Finding conflicts (meetings, appointments)
- Interval intersection problems

## Basic Pattern
```python
def merge(intervals):
    """Merge overlapping intervals"""
    if not intervals:
        return []
    
    # Sort by start time
    intervals.sort(key=lambda x: x[0])
    merged = [intervals[0]]
    
    for current in intervals[1:]:
        last = merged[-1]
        
        # Overlapping intervals
        if current[0] <= last[1]:
            last[1] = max(last[1], current[1])
        else:
            merged.append(current)
    
    return merged
```

## Common Problems

### 1. Insert Interval
```python
def insert(intervals, new_interval):
    """Insert and merge interval"""
    result = []
    i = 0
    n = len(intervals)
    
    # Add all intervals before new_interval
    while i < n and intervals[i][1] < new_interval[0]:
        result.append(intervals[i])
        i += 1
    
    # Merge overlapping intervals
    while i < n and intervals[i][0] <= new_interval[1]:
        new_interval[0] = min(new_interval[0], intervals[i][0])
        new_interval[1] = max(new_interval[1], intervals[i][1])
        i += 1
    result.append(new_interval)
    
    # Add remaining intervals
    while i < n:
        result.append(intervals[i])
        i += 1
    
    return result
```

### 2. Interval List Intersections
```python
def interval_intersection(A, B):
    """Find intersection of two interval lists"""
    result = []
    i = j = 0
    
    while i < len(A) and j < len(B):
        # Check if intervals overlap
        start = max(A[i][0], B[j][0])
        end = min(A[i][1], B[j][1])
        
        if start <= end:
            result.append([start, end])
        
        # Move pointer of interval that ends first
        if A[i][1] < B[j][1]:
            i += 1
        else:
            j += 1
    
    return result
```

### 3. Meeting Rooms
```python
def can_attend_meetings(intervals):
    """Check if person can attend all meetings"""
    intervals.sort(key=lambda x: x[0])
    
    for i in range(1, len(intervals)):
        if intervals[i][0] < intervals[i-1][1]:
            return False
    
    return True
```

### 4. Meeting Rooms II
```python
def min_meeting_rooms(intervals):
    """Minimum number of meeting rooms required"""
    if not intervals:
        return 0
    
    start_times = sorted([i[0] for i in intervals])
    end_times = sorted([i[1] for i in intervals])
    
    rooms = 0
    max_rooms = 0
    s = e = 0
    
    while s < len(intervals):
        if start_times[s] < end_times[e]:
            rooms += 1
            max_rooms = max(max_rooms, rooms)
            s += 1
        else:
            rooms -= 1
            e += 1
    
    return max_rooms
```

### 5. Employee Free Time
```python
def employee_free_time(schedule):
    """Find common free time intervals"""
    # Flatten all intervals
    intervals = []
    for employee in schedule:
        intervals.extend(employee)
    
    # Sort by start time
    intervals.sort(key=lambda x: x.start)
    
    # Find gaps between merged intervals
    result = []
    prev_end = intervals[0].end
    
    for interval in intervals[1:]:
        if interval.start > prev_end:
            result.append(Interval(prev_end, interval.start))
        prev_end = max(prev_end, interval.end)
    
    return result
```

### 6. Non-overlapping Intervals
```python
def erase_overlap_intervals(intervals):
    """Minimum intervals to remove to make non-overlapping"""
    if not intervals:
        return 0
    
    intervals.sort(key=lambda x: x[1])  # Sort by end time
    count = 0
    end = intervals[0][1]
    
    for i in range(1, len(intervals)):
        if intervals[i][0] < end:
            count += 1
        else:
            end = intervals[i][1]
    
    return count
```

### 7. Minimum Number of Arrows to Burst Balloons
```python
def find_min_arrow_shots(points):
    """Minimum arrows to burst all balloons"""
    if not points:
        return 0
    
    points.sort(key=lambda x: x[1])
    arrows = 1
    end = points[0][1]
    
    for start, point_end in points[1:]:
        if start > end:
            arrows += 1
            end = point_end
    
    return arrows
```

## Time Complexity
- Sorting: O(n log n)
- Merging: O(n)
- **Total:** O(n log n)

## Space Complexity
O(n) for result storage

## Tips
1. **Always sort first** (usually by start time)
2. **Check overlap condition**: `start1 <= end2 and start2 <= end1`
3. **Track previous end** for merging
4. **Use heap** for k-way merging
5. **Consider edge cases**: Empty input, single interval

## More Problems
1. **Task Scheduler**
2. **Car Pooling**
3. **My Calendar series**
4. **Data Stream as Disjoint Intervals**
5. **Range Module**
