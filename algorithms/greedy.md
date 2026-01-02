# Greedy Algorithms

## Overview
Greedy algorithms make locally optimal choices at each step with the hope of finding a global optimum. They don't reconsider previous choices.

## Key Characteristics
1. **Greedy Choice Property**: Local optimal choice leads to global optimal solution
2. **Optimal Substructure**: Optimal solution contains optimal solutions to subproblems
3. **No Backtracking**: Once a choice is made, it's never reconsidered

## When to Use Greedy
- Problem has greedy choice property
- Optimal substructure exists
- Proof that greedy choice leads to optimal solution
- Common in optimization problems

## Classic Problems

### 1. Activity Selection
Select maximum number of non-overlapping activities.
```python
def activity_selection(start, end):
    n = len(start)
    activities = sorted(zip(start, end), key=lambda x: x[1])
    
    result = [activities[0]]
    last_end = activities[0][1]
    
    for i in range(1, n):
        if activities[i][0] >= last_end:
            result.append(activities[i])
            last_end = activities[i][1]
    
    return result
```

### 2. Fractional Knapsack
Take fractions of items to maximize value.
```python
def fractional_knapsack(weights, values, capacity):
    items = sorted(
        zip(weights, values), 
        key=lambda x: x[1]/x[0], 
        reverse=True
    )
    
    total_value = 0
    for weight, value in items:
        if capacity >= weight:
            capacity -= weight
            total_value += value
        else:
            total_value += value * (capacity / weight)
            break
    
    return total_value
```

### 3. Huffman Coding
Optimal prefix-free coding for data compression.
```python
import heapq
from collections import defaultdict

class Node:
    def __init__(self, char, freq):
        self.char = char
        self.freq = freq
        self.left = None
        self.right = None
    
    def __lt__(self, other):
        return self.freq < other.freq

def huffman_coding(text):
    # Build frequency map
    freq = defaultdict(int)
    for char in text:
        freq[char] += 1
    
    # Create min heap
    heap = [Node(char, f) for char, f in freq.items()]
    heapq.heapify(heap)
    
    # Build Huffman tree
    while len(heap) > 1:
        left = heapq.heappop(heap)
        right = heapq.heappop(heap)
        
        merged = Node(None, left.freq + right.freq)
        merged.left = left
        merged.right = right
        
        heapq.heappush(heap, merged)
    
    # Generate codes
    codes = {}
    def generate_codes(node, code=''):
        if node.char:
            codes[node.char] = code
            return
        if node.left:
            generate_codes(node.left, code + '0')
        if node.right:
            generate_codes(node.right, code + '1')
    
    generate_codes(heap[0])
    return codes
```

### 4. Minimum Coins (Greedy for specific coin systems)
Works for canonical coin systems (e.g., US coins).
```python
def min_coins_greedy(coins, amount):
    coins.sort(reverse=True)
    count = 0
    
    for coin in coins:
        if amount >= coin:
            num_coins = amount // coin
            count += num_coins
            amount -= num_coins * coin
    
    return count if amount == 0 else -1
```

### 5. Jump Game
Can you reach the last index?
```python
def can_jump(nums):
    max_reach = 0
    
    for i in range(len(nums)):
        if i > max_reach:
            return False
        max_reach = max(max_reach, i + nums[i])
        if max_reach >= len(nums) - 1:
            return True
    
    return True
```

### 6. Jump Game II
Minimum jumps to reach last index.
```python
def min_jumps(nums):
    if len(nums) <= 1:
        return 0
    
    jumps = 0
    current_end = 0
    farthest = 0
    
    for i in range(len(nums) - 1):
        farthest = max(farthest, i + nums[i])
        
        if i == current_end:
            jumps += 1
            current_end = farthest
            
            if current_end >= len(nums) - 1:
                break
    
    return jumps
```

### 7. Meeting Rooms II
Minimum number of conference rooms required.
```python
def min_meeting_rooms(intervals):
    if not intervals:
        return 0
    
    start_times = sorted([i[0] for i in intervals])
    end_times = sorted([i[1] for i in intervals])
    
    rooms = 0
    max_rooms = 0
    s_ptr = e_ptr = 0
    
    while s_ptr < len(intervals):
        if start_times[s_ptr] < end_times[e_ptr]:
            rooms += 1
            max_rooms = max(max_rooms, rooms)
            s_ptr += 1
        else:
            rooms -= 1
            e_ptr += 1
    
    return max_rooms
```

### 8. Gas Station
Can you complete circular tour?
```python
def can_complete_circuit(gas, cost):
    if sum(gas) < sum(cost):
        return -1
    
    tank = 0
    start = 0
    
    for i in range(len(gas)):
        tank += gas[i] - cost[i]
        
        if tank < 0:
            start = i + 1
            tank = 0
    
    return start
```

### 9. Assign Cookies
```python
def find_content_children(g, s):
    g.sort()
    s.sort()
    
    child = cookie = 0
    
    while child < len(g) and cookie < len(s):
        if s[cookie] >= g[child]:
            child += 1
        cookie += 1
    
    return child
```

### 10. Two City Scheduling
```python
def two_city_scheduling(costs):
    # Sort by difference: cost[A] - cost[B]
    costs.sort(key=lambda x: x[0] - x[1])
    
    n = len(costs) // 2
    total = 0
    
    # Send first n to city A, rest to city B
    for i in range(n):
        total += costs[i][0]
    for i in range(n, 2 * n):
        total += costs[i][1]
    
    return total
```

## Greedy vs Dynamic Programming

### Use Greedy When:
- Local optimal choice leads to global optimum
- No need to reconsider previous choices
- Faster: usually O(n log n) or O(n)

### Use DP When:
- Need to explore all possibilities
- Overlapping subproblems
- Local optimum doesn't guarantee global optimum

### Examples:
- **Fractional Knapsack**: Greedy works ✓
- **0/1 Knapsack**: Need DP (greedy fails) ✗
- **Activity Selection**: Greedy works ✓
- **Coin Change (arbitrary coins)**: Need DP ✗

## Common Greedy Patterns

### 1. Sorting
Often the first step in greedy solutions.

### 2. Two Pointers
After sorting, use two pointers for optimal matching.

### 3. Priority Queue/Heap
Maintain greedy choice at each step.

### 4. Interval Problems
Sort by start or end time, then process.

## Common Problems

1. **Activity Selection / Meeting Rooms**
2. **Fractional Knapsack**
3. **Huffman Coding**
4. **Jump Game** / **Jump Game II**
5. **Gas Station**
6. **Assign Cookies**
7. **Two City Scheduling**
8. **Partition Labels**
9. **Queue Reconstruction by Height**
10. **Minimum Number of Arrows to Burst Balloons**

## Tips
- Prove greedy choice property before implementing
- Sort data first (often by value, ratio, or end time)
- Consider counterexamples to validate approach
- Greedy doesn't always work – verify correctness
- Time complexity usually better than DP
- Draw examples to visualize greedy strategy

## Interview Questions
- How do you know when greedy will work?
- What's the difference between greedy and DP?
- Give an example where greedy fails
- How do you prove greedy correctness?
- What's the time complexity of typical greedy solutions?
