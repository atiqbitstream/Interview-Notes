# Dynamic Programming

## Overview
Dynamic Programming (DP) is an optimization technique that solves complex problems by breaking them down into simpler subproblems and storing results to avoid redundant computation.

## Key Concepts

### 1. Optimal Substructure
Solution to problem can be constructed from solutions to subproblems.

### 2. Overlapping Subproblems
Same subproblems are solved multiple times.

### 3. Memoization (Top-Down)
Store results of function calls and return cached result when same inputs occur.

### 4. Tabulation (Bottom-Up)
Build solution iteratively from base cases up.

## Classic Problems

### 1. Fibonacci Sequence

**Recursive (Exponential)**
```python
def fib(n):
    if n <= 1:
        return n
    return fib(n - 1) + fib(n - 2)
```

**Memoization (Top-Down)**
```python
def fib_memo(n, memo={}):
    if n in memo:
        return memo[n]
    if n <= 1:
        return n
    memo[n] = fib_memo(n - 1, memo) + fib_memo(n - 2, memo)
    return memo[n]
```

**Tabulation (Bottom-Up)**
```python
def fib_tab(n):
    if n <= 1:
        return n
    dp = [0] * (n + 1)
    dp[1] = 1
    for i in range(2, n + 1):
        dp[i] = dp[i - 1] + dp[i - 2]
    return dp[n]
```

**Space Optimized**
```python
def fib_optimized(n):
    if n <= 1:
        return n
    prev, curr = 0, 1
    for _ in range(2, n + 1):
        prev, curr = curr, prev + curr
    return curr
```

### 2. Climbing Stairs
You can climb 1 or 2 steps. How many ways to reach top?
```python
def climb_stairs(n):
    if n <= 2:
        return n
    dp = [0] * (n + 1)
    dp[1], dp[2] = 1, 2
    for i in range(3, n + 1):
        dp[i] = dp[i - 1] + dp[i - 2]
    return dp[n]
```

### 3. Coin Change
Minimum coins needed to make amount.
```python
def coin_change(coins, amount):
    dp = [float('inf')] * (amount + 1)
    dp[0] = 0
    
    for i in range(1, amount + 1):
        for coin in coins:
            if coin <= i:
                dp[i] = min(dp[i], dp[i - coin] + 1)
    
    return dp[amount] if dp[amount] != float('inf') else -1
```

### 4. Longest Increasing Subsequence
```python
def longest_increasing_subsequence(nums):
    if not nums:
        return 0
    
    n = len(nums)
    dp = [1] * n
    
    for i in range(1, n):
        for j in range(i):
            if nums[i] > nums[j]:
                dp[i] = max(dp[i], dp[j] + 1)
    
    return max(dp)
```

### 5. Longest Common Subsequence
```python
def longest_common_subsequence(text1, text2):
    m, n = len(text1), len(text2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if text1[i - 1] == text2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1] + 1
            else:
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])
    
    return dp[m][n]
```

### 6. 0/1 Knapsack
```python
def knapsack(weights, values, capacity):
    n = len(weights)
    dp = [[0] * (capacity + 1) for _ in range(n + 1)]
    
    for i in range(1, n + 1):
        for w in range(1, capacity + 1):
            if weights[i - 1] <= w:
                dp[i][w] = max(
                    dp[i - 1][w],
                    values[i - 1] + dp[i - 1][w - weights[i - 1]]
                )
            else:
                dp[i][w] = dp[i - 1][w]
    
    return dp[n][capacity]
```

### 7. Edit Distance
```python
def edit_distance(word1, word2):
    m, n = len(word1), len(word2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    for i in range(m + 1):
        dp[i][0] = i
    for j in range(n + 1):
        dp[0][j] = j
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if word1[i - 1] == word2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1]
            else:
                dp[i][j] = 1 + min(
                    dp[i - 1][j],      # delete
                    dp[i][j - 1],      # insert
                    dp[i - 1][j - 1]   # replace
                )
    
    return dp[m][n]
```

### 8. House Robber
```python
def rob(nums):
    if not nums:
        return 0
    if len(nums) == 1:
        return nums[0]
    
    prev2, prev1 = 0, 0
    for num in nums:
        current = max(prev1, prev2 + num)
        prev2 = prev1
        prev1 = current
    
    return prev1
```

### 9. Maximum Subarray (Kadane's Algorithm)
```python
def max_subarray(nums):
    max_sum = current_sum = nums[0]
    
    for num in nums[1:]:
        current_sum = max(num, current_sum + num)
        max_sum = max(max_sum, current_sum)
    
    return max_sum
```

### 10. Unique Paths
```python
def unique_paths(m, n):
    dp = [[1] * n for _ in range(m)]
    
    for i in range(1, m):
        for j in range(1, n):
            dp[i][j] = dp[i - 1][j] + dp[i][j - 1]
    
    return dp[m - 1][n - 1]
```

## DP Patterns

### 1. Linear DP
- One-dimensional problems
- Examples: Climbing stairs, House robber

### 2. 2D Grid DP
- Two-dimensional problems
- Examples: Unique paths, Edit distance

### 3. Subsequence DP
- Finding subsequences
- Examples: LCS, LIS

### 4. Knapsack DP
- Selection with constraints
- Examples: 0/1 Knapsack, Coin change

### 5. String DP
- String manipulation
- Examples: Edit distance, Palindrome problems

### 6. State Machine DP
- Multiple states per position
- Examples: Stock problems

## Problem-Solving Steps

1. **Identify** if it's a DP problem:
   - Optimal substructure
   - Overlapping subproblems
   - "Count ways", "minimum/maximum", "longest/shortest"

2. **Define** state:
   - What does dp[i] or dp[i][j] represent?

3. **Find** recurrence relation:
   - How to compute current state from previous states?

4. **Determine** base cases:
   - What are the initial values?

5. **Implement** and optimize:
   - Start with memoization
   - Convert to tabulation
   - Optimize space if possible

## Common Problems

1. **Climbing Stairs**
2. **Coin Change**
3. **Longest Increasing Subsequence**
4. **Longest Common Subsequence**
5. **0/1 Knapsack**
6. **Edit Distance**
7. **House Robber**
8. **Maximum Subarray**
9. **Unique Paths**
10. **Word Break**
11. **Partition Equal Subset Sum**
12. **Longest Palindromic Substring**
13. **Regular Expression Matching**
14. **Buy and Sell Stock variants**

## Tips
- Start with recursive solution
- Identify overlapping subproblems
- Add memoization to recursive solution
- Convert to iterative tabulation
- Consider space optimization
- Draw the DP table for visualization
- Think about what each cell represents
- Look for patterns in transitions

## Interview Questions
- What's the difference between memoization and tabulation?
- How do you identify a DP problem?
- Explain optimal substructure
- What's the time/space complexity trade-off?
- When would you use DP vs greedy?
