# Recursion and Backtracking

## Recursion

### Overview
Recursion is a technique where a function calls itself to solve smaller instances of the same problem.

### Key Components
1. **Base Case**: Stopping condition
2. **Recursive Case**: Function calls itself with modified input
3. **Progress**: Each call moves toward base case

### Basic Example
```python
def factorial(n):
    # Base case
    if n <= 1:
        return 1
    # Recursive case
    return n * factorial(n - 1)
```

### Recursion Tree
Visualize how recursive calls branch out to understand time complexity.

### Time Complexity
- Count number of recursive calls
- Multiply by work done in each call
- Example: `T(n) = T(n-1) + O(1)` → O(n)

## Backtracking

### Overview
Backtracking is a systematic way to try all possibilities by building candidates incrementally and abandoning (backtracking) when a candidate cannot lead to a solution.

### Template
```python
def backtrack(candidates, current_solution, results):
    # Base case: solution found
    if is_valid_solution(current_solution):
        results.append(current_solution[:])  # Copy!
        return
    
    # Iterate through candidates
    for candidate in candidates:
        # Try candidate
        current_solution.append(candidate)
        
        # Recurse
        if is_valid(current_solution):
            backtrack(remaining_candidates, current_solution, results)
        
        # Backtrack - undo choice
        current_solution.pop()
```

## Classic Problems

### 1. Permutations
Generate all permutations of an array.
```python
def permutations(nums):
    result = []
    
    def backtrack(path, remaining):
        if not remaining:
            result.append(path[:])
            return
        
        for i in range(len(remaining)):
            backtrack(
                path + [remaining[i]], 
                remaining[:i] + remaining[i+1:]
            )
    
    backtrack([], nums)
    return result
```

### 2. Combinations
Generate all k-size combinations from n numbers.
```python
def combinations(n, k):
    result = []
    
    def backtrack(start, path):
        if len(path) == k:
            result.append(path[:])
            return
        
        for i in range(start, n + 1):
            path.append(i)
            backtrack(i + 1, path)
            path.pop()
    
    backtrack(1, [])
    return result
```

### 3. Subsets
Generate all possible subsets.
```python
def subsets(nums):
    result = []
    
    def backtrack(start, path):
        result.append(path[:])
        
        for i in range(start, len(nums)):
            path.append(nums[i])
            backtrack(i + 1, path)
            path.pop()
    
    backtrack(0, [])
    return result
```

### 4. N-Queens
Place n queens on n×n chessboard so no two attack each other.
```python
def solve_n_queens(n):
    result = []
    board = [['.'] * n for _ in range(n)]
    
    def is_valid(row, col):
        # Check column
        for i in range(row):
            if board[i][col] == 'Q':
                return False
        
        # Check diagonal
        i, j = row - 1, col - 1
        while i >= 0 and j >= 0:
            if board[i][j] == 'Q':
                return False
            i -= 1
            j -= 1
        
        # Check anti-diagonal
        i, j = row - 1, col + 1
        while i >= 0 and j < n:
            if board[i][j] == 'Q':
                return False
            i -= 1
            j += 1
        
        return True
    
    def backtrack(row):
        if row == n:
            result.append([''.join(row) for row in board])
            return
        
        for col in range(n):
            if is_valid(row, col):
                board[row][col] = 'Q'
                backtrack(row + 1)
                board[row][col] = '.'
    
    backtrack(0)
    return result
```

### 5. Sudoku Solver
```python
def solve_sudoku(board):
    def is_valid(row, col, num):
        # Check row
        if num in board[row]:
            return False
        
        # Check column
        if num in [board[i][col] for i in range(9)]:
            return False
        
        # Check 3×3 box
        box_row, box_col = 3 * (row // 3), 3 * (col // 3)
        for i in range(box_row, box_row + 3):
            for j in range(box_col, box_col + 3):
                if board[i][j] == num:
                    return False
        
        return True
    
    def backtrack():
        for i in range(9):
            for j in range(9):
                if board[i][j] == '.':
                    for num in '123456789':
                        if is_valid(i, j, num):
                            board[i][j] = num
                            if backtrack():
                                return True
                            board[i][j] = '.'
                    return False
        return True
    
    backtrack()
```

### 6. Letter Combinations of Phone Number
```python
def letter_combinations(digits):
    if not digits:
        return []
    
    phone = {
        '2': 'abc', '3': 'def', '4': 'ghi', '5': 'jkl',
        '6': 'mno', '7': 'pqrs', '8': 'tuv', '9': 'wxyz'
    }
    
    result = []
    
    def backtrack(index, path):
        if index == len(digits):
            result.append(''.join(path))
            return
        
        for letter in phone[digits[index]]:
            path.append(letter)
            backtrack(index + 1, path)
            path.pop()
    
    backtrack(0, [])
    return result
```

### 7. Word Search
```python
def exist(board, word):
    m, n = len(board), len(board[0])
    
    def backtrack(i, j, k):
        if k == len(word):
            return True
        
        if (i < 0 or i >= m or j < 0 or j >= n or 
            board[i][j] != word[k]):
            return False
        
        temp = board[i][j]
        board[i][j] = '#'  # Mark as visited
        
        found = (backtrack(i+1, j, k+1) or
                backtrack(i-1, j, k+1) or
                backtrack(i, j+1, k+1) or
                backtrack(i, j-1, k+1))
        
        board[i][j] = temp  # Restore
        return found
    
    for i in range(m):
        for j in range(n):
            if backtrack(i, j, 0):
                return True
    return False
```

### 8. Palindrome Partitioning
```python
def partition(s):
    result = []
    
    def is_palindrome(sub):
        return sub == sub[::-1]
    
    def backtrack(start, path):
        if start == len(s):
            result.append(path[:])
            return
        
        for end in range(start + 1, len(s) + 1):
            substring = s[start:end]
            if is_palindrome(substring):
                path.append(substring)
                backtrack(end, path)
                path.pop()
    
    backtrack(0, [])
    return result
```

### 9. Generate Parentheses
```python
def generate_parentheses(n):
    result = []
    
    def backtrack(s, left, right):
        if len(s) == 2 * n:
            result.append(s)
            return
        
        if left < n:
            backtrack(s + '(', left + 1, right)
        if right < left:
            backtrack(s + ')', left, right + 1)
    
    backtrack('', 0, 0)
    return result
```

## Common Patterns

### 1. Pruning
Abandon branches early if they can't lead to solution.
```python
if not is_valid(current_path):
    return  # Prune this branch
```

### 2. Constraint Checking
Validate candidates before exploring further.

### 3. State Restoration
Undo changes when backtracking (very important!)

### 4. Memoization
Cache results to avoid redundant computation.

## Common Problems

1. **Permutations** / **Permutations II**
2. **Combinations** / **Combination Sum**
3. **Subsets** / **Subsets II**
4. **N-Queens**
5. **Sudoku Solver**
6. **Word Search** / **Word Search II**
7. **Letter Combinations of Phone Number**
8. **Palindrome Partitioning**
9. **Generate Parentheses**
10. **Restore IP Addresses**

## Tips
- Always have clear base case
- Make sure to backtrack (undo changes)
- Use `path[:]` or `path.copy()` to avoid reference issues
- Draw recursion tree to understand flow
- Consider pruning for optimization
- Watch for duplicate solutions
- Use visited set/array when needed
- Consider iterative approach for simple cases

## Complexity Analysis
- Time: Often O(b^d) where b is branching factor, d is depth
- Space: O(d) for recursion stack
- Backtracking explores solution space tree

## Interview Questions
- Explain the difference between recursion and backtracking
- How do you avoid infinite recursion?
- What's the space complexity of recursive solutions?
- When should you use backtracking vs dynamic programming?
- How do you optimize backtracking solutions?
