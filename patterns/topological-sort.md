# Topological Sort Pattern

## Overview
Topological sort is a linear ordering of vertices in a Directed Acyclic Graph (DAG) such that for every directed edge (u, v), vertex u comes before v.

## When to Use
- Dependency resolution (build systems, package managers)
- Course scheduling
- Task scheduling with prerequisites
- Compilation order
- Any directed acyclic graph problem

## Basic Algorithms

### 1. DFS-Based Approach
```python
def topological_sort_dfs(graph):
    """Topological sort using DFS"""
    visited = set()
    stack = []
    
    def dfs(node):
        visited.add(node)
        
        for neighbor in graph.get(node, []):
            if neighbor not in visited:
                dfs(neighbor)
        
        stack.append(node)
    
    for node in graph:
        if node not in visited:
            dfs(node)
    
    return stack[::-1]
```

### 2. Kahn's Algorithm (BFS-Based)
```python
from collections import defaultdict, deque

def topological_sort_bfs(vertices, edges):
    """Topological sort using Kahn's algorithm"""
    # Build graph and calculate in-degrees
    graph = defaultdict(list)
    in_degree = {i: 0 for i in range(vertices)}
    
    for parent, child in edges:
        graph[parent].append(child)
        in_degree[child] += 1
    
    # Start with nodes having in-degree 0
    queue = deque([node for node in in_degree if in_degree[node] == 0])
    result = []
    
    while queue:
        node = queue.popleft()
        result.append(node)
        
        # Reduce in-degree of neighbors
        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    # Check for cycle
    if len(result) != vertices:
        return []  # Cycle detected
    
    return result
```

## Common Problems

### 1. Course Schedule
```python
def can_finish(num_courses, prerequisites):
    """Check if all courses can be finished"""
    graph = defaultdict(list)
    in_degree = [0] * num_courses
    
    for course, prereq in prerequisites:
        graph[prereq].append(course)
        in_degree[course] += 1
    
    queue = deque([i for i in range(num_courses) if in_degree[i] == 0])
    completed = 0
    
    while queue:
        course = queue.popleft()
        completed += 1
        
        for next_course in graph[course]:
            in_degree[next_course] -= 1
            if in_degree[next_course] == 0:
                queue.append(next_course)
    
    return completed == num_courses
```

### 2. Course Schedule II
```python
def find_order(num_courses, prerequisites):
    """Find valid course order"""
    graph = defaultdict(list)
    in_degree = [0] * num_courses
    
    for course, prereq in prerequisites:
        graph[prereq].append(course)
        in_degree[course] += 1
    
    queue = deque([i for i in range(num_courses) if in_degree[i] == 0])
    order = []
    
    while queue:
        course = queue.popleft()
        order.append(course)
        
        for next_course in graph[course]:
            in_degree[next_course] -= 1
            if in_degree[next_course] == 0:
                queue.append(next_course)
    
    return order if len(order) == num_courses else []
```

### 3. Alien Dictionary
```python
def alien_order(words):
    """Determine alien dictionary order"""
    # Build graph
    graph = defaultdict(set)
    in_degree = {c: 0 for word in words for c in word}
    
    # Compare adjacent words
    for i in range(len(words) - 1):
        w1, w2 = words[i], words[i + 1]
        min_len = min(len(w1), len(w2))
        
        # If w1 is prefix but longer, invalid
        if len(w1) > len(w2) and w1[:min_len] == w2[:min_len]:
            return ""
        
        # Find first different character
        for j in range(min_len):
            if w1[j] != w2[j]:
                if w2[j] not in graph[w1[j]]:
                    graph[w1[j]].add(w2[j])
                    in_degree[w2[j]] += 1
                break
    
    # Topological sort
    queue = deque([c for c in in_degree if in_degree[c] == 0])
    result = []
    
    while queue:
        char = queue.popleft()
        result.append(char)
        
        for neighbor in graph[char]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    return "".join(result) if len(result) == len(in_degree) else ""
```

### 4. Sequence Reconstruction
```python
def sequence_reconstruction(org, seqs):
    """Check if org is only sequence that can be reconstructed"""
    graph = defaultdict(list)
    in_degree = {num: 0 for num in org}
    
    # Build graph
    for seq in seqs:
        for i in range(len(seq)):
            if seq[i] not in in_degree:
                return False
            if i > 0:
                if seq[i] not in graph[seq[i-1]]:
                    graph[seq[i-1]].append(seq[i])
                    in_degree[seq[i]] += 1
    
    # Check if unique topological order
    queue = deque([num for num in org if in_degree[num] == 0])
    index = 0
    
    while queue:
        if len(queue) > 1:
            return False  # Not unique
        
        num = queue.popleft()
        if org[index] != num:
            return False
        index += 1
        
        for neighbor in graph[num]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    return index == len(org)
```

### 5. Minimum Height Trees
```python
def find_min_height_trees(n, edges):
    """Find roots of minimum height trees"""
    if n == 1:
        return [0]
    
    # Build graph
    graph = defaultdict(list)
    degree = [0] * n
    
    for a, b in edges:
        graph[a].append(b)
        graph[b].append(a)
        degree[a] += 1
        degree[b] += 1
    
    # Start with leaf nodes
    queue = deque([i for i in range(n) if degree[i] == 1])
    remaining = n
    
    # Remove leaf layers
    while remaining > 2:
        size = len(queue)
        remaining -= size
        
        for _ in range(size):
            node = queue.popleft()
            
            for neighbor in graph[node]:
                degree[neighbor] -= 1
                if degree[neighbor] == 1:
                    queue.append(neighbor)
    
    return list(queue)
```

### 6. All Ancestors
```python
def get_ancestors(n, edges):
    """Find all ancestors of each node"""
    graph = defaultdict(list)
    in_degree = [0] * n
    
    for parent, child in edges:
        graph[parent].append(child)
        in_degree[child] += 1
    
    # Topological sort with ancestor tracking
    queue = deque([i for i in range(n) if in_degree[i] == 0])
    ancestors = [set() for _ in range(n)]
    
    while queue:
        node = queue.popleft()
        
        for child in graph[node]:
            ancestors[child].add(node)
            ancestors[child].update(ancestors[node])
            
            in_degree[child] -= 1
            if in_degree[child] == 0:
                queue.append(child)
    
    return [sorted(list(anc)) for anc in ancestors]
```

## Cycle Detection
```python
def has_cycle(graph):
    """Detect cycle in directed graph"""
    WHITE, GRAY, BLACK = 0, 1, 2
    color = {node: WHITE for node in graph}
    
    def dfs(node):
        color[node] = GRAY
        
        for neighbor in graph.get(node, []):
            if color[neighbor] == GRAY:
                return True  # Back edge = cycle
            if color[neighbor] == WHITE and dfs(neighbor):
                return True
        
        color[node] = BLACK
        return False
    
    for node in graph:
        if color[node] == WHITE:
            if dfs(node):
                return True
    
    return False
```

## Time and Space Complexity
**Time:** O(V + E) where V = vertices, E = edges
**Space:** O(V + E)

## Key Points
1. Only works on DAG (Directed Acyclic Graph)
2. Multiple valid orderings possible
3. Cycle detection is crucial
4. In-degree tracking is key for Kahn's algorithm
5. DFS approach uses stack (post-order)

## Tips
1. **Check for cycles**: Invalid if cycle exists
2. **Track in-degrees**: For Kahn's algorithm
3. **Process sources first**: Nodes with in-degree 0
4. **Handle multiple components**: Iterate all nodes
5. **Verify completeness**: All nodes should be processed

## More Problems
1. **Build Order**
2. **Task Scheduler**
3. **Parallel Courses**
4. **Sort Items by Groups Respecting Dependencies**
5. **Find Eventual Safe States**
