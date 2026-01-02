# Graph Algorithms

## Graph Representations

### 1. Adjacency Matrix
```python
# For n vertices
graph = [[0] * n for _ in range(n)]
# Add edge from u to v
graph[u][v] = 1  # or weight
```
- Space: O(V²)
- Edge lookup: O(1)
- Iterate all edges: O(V²)

### 2. Adjacency List
```python
from collections import defaultdict
graph = defaultdict(list)
# Add edge from u to v
graph[u].append(v)
```
- Space: O(V + E)
- Edge lookup: O(degree)
- Iterate all edges: O(E)

## Graph Traversals

### Depth-First Search (DFS)

#### Recursive
```python
def dfs(graph, node, visited):
    visited.add(node)
    print(node)
    
    for neighbor in graph[node]:
        if neighbor not in visited:
            dfs(graph, neighbor, visited)
```

#### Iterative
```python
def dfs_iterative(graph, start):
    visited = set()
    stack = [start]
    
    while stack:
        node = stack.pop()
        if node not in visited:
            visited.add(node)
            print(node)
            for neighbor in graph[node]:
                if neighbor not in visited:
                    stack.append(neighbor)
```

### Breadth-First Search (BFS)
```python
from collections import deque

def bfs(graph, start):
    visited = set([start])
    queue = deque([start])
    
    while queue:
        node = queue.popleft()
        print(node)
        
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
```

## Shortest Path Algorithms

### 1. Dijkstra's Algorithm
Single-source shortest path for non-negative weights.

```python
import heapq

def dijkstra(graph, start):
    distances = {node: float('inf') for node in graph}
    distances[start] = 0
    pq = [(0, start)]
    
    while pq:
        curr_dist, curr = heapq.heappop(pq)
        
        if curr_dist > distances[curr]:
            continue
        
        for neighbor, weight in graph[curr]:
            distance = curr_dist + weight
            
            if distance < distances[neighbor]:
                distances[neighbor] = distance
                heapq.heappush(pq, (distance, neighbor))
    
    return distances
```
- Time: O((V + E) log V) with binary heap
- Space: O(V)

### 2. Bellman-Ford Algorithm
Single-source shortest path, handles negative weights.

```python
def bellman_ford(edges, V, start):
    distances = [float('inf')] * V
    distances[start] = 0
    
    # Relax edges V-1 times
    for _ in range(V - 1):
        for u, v, weight in edges:
            if distances[u] != float('inf'):
                distances[v] = min(distances[v], distances[u] + weight)
    
    # Check for negative cycles
    for u, v, weight in edges:
        if distances[u] != float('inf') and \
           distances[u] + weight < distances[v]:
            return None  # Negative cycle exists
    
    return distances
```
- Time: O(VE)
- Can detect negative cycles

### 3. Floyd-Warshall Algorithm
All-pairs shortest path.

```python
def floyd_warshall(graph, V):
    dist = [[float('inf')] * V for _ in range(V)]
    
    # Initialize
    for i in range(V):
        dist[i][i] = 0
    
    for u in range(V):
        for v, weight in graph[u]:
            dist[u][v] = weight
    
    # Floyd-Warshall
    for k in range(V):
        for i in range(V):
            for j in range(V):
                dist[i][j] = min(dist[i][j], 
                                dist[i][k] + dist[k][j])
    
    return dist
```
- Time: O(V³)
- Space: O(V²)

## Minimum Spanning Tree

### 1. Kruskal's Algorithm
Uses Union-Find.

```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n
    
    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
    
    def union(self, x, y):
        px, py = self.find(x), self.find(y)
        if px == py:
            return False
        if self.rank[px] < self.rank[py]:
            px, py = py, px
        self.parent[py] = px
        if self.rank[px] == self.rank[py]:
            self.rank[px] += 1
        return True

def kruskal(edges, V):
    edges.sort(key=lambda x: x[2])  # Sort by weight
    uf = UnionFind(V)
    mst = []
    
    for u, v, weight in edges:
        if uf.union(u, v):
            mst.append((u, v, weight))
            if len(mst) == V - 1:
                break
    
    return mst
```
- Time: O(E log E)

### 2. Prim's Algorithm
```python
import heapq

def prim(graph, start):
    mst = []
    visited = set([start])
    edges = [(weight, start, to) for to, weight in graph[start]]
    heapq.heapify(edges)
    
    while edges:
        weight, frm, to = heapq.heappop(edges)
        if to not in visited:
            visited.add(to)
            mst.append((frm, to, weight))
            
            for next_to, next_weight in graph[to]:
                if next_to not in visited:
                    heapq.heappush(edges, (next_weight, to, next_to))
    
    return mst
```
- Time: O(E log V)

## Cycle Detection

### Undirected Graph (DFS)
```python
def has_cycle_undirected(graph):
    visited = set()
    
    def dfs(node, parent):
        visited.add(node)
        for neighbor in graph[node]:
            if neighbor not in visited:
                if dfs(neighbor, node):
                    return True
            elif neighbor != parent:
                return True
        return False
    
    for node in graph:
        if node not in visited:
            if dfs(node, -1):
                return True
    return False
```

### Directed Graph (DFS with rec stack)
```python
def has_cycle_directed(graph):
    visited = set()
    rec_stack = set()
    
    def dfs(node):
        visited.add(node)
        rec_stack.add(node)
        
        for neighbor in graph[node]:
            if neighbor not in visited:
                if dfs(neighbor):
                    return True
            elif neighbor in rec_stack:
                return True
        
        rec_stack.remove(node)
        return False
    
    for node in graph:
        if node not in visited:
            if dfs(node):
                return True
    return False
```

## Topological Sort

### DFS-based
```python
def topological_sort(graph):
    visited = set()
    stack = []
    
    def dfs(node):
        visited.add(node)
        for neighbor in graph[node]:
            if neighbor not in visited:
                dfs(neighbor)
        stack.append(node)
    
    for node in graph:
        if node not in visited:
            dfs(node)
    
    return stack[::-1]
```

### Kahn's Algorithm (BFS-based)
```python
from collections import deque, defaultdict

def topological_sort_kahn(graph, V):
    in_degree = defaultdict(int)
    
    for node in graph:
        for neighbor in graph[node]:
            in_degree[neighbor] += 1
    
    queue = deque([node for node in range(V) if in_degree[node] == 0])
    result = []
    
    while queue:
        node = queue.popleft()
        result.append(node)
        
        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    return result if len(result) == V else []
```

## Union-Find (Disjoint Set)
```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n
    
    def find(self, x):
        # Path compression
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
    
    def union(self, x, y):
        # Union by rank
        px, py = self.find(x), self.find(y)
        if px == py:
            return False
        
        if self.rank[px] < self.rank[py]:
            px, py = py, px
        self.parent[py] = px
        
        if self.rank[px] == self.rank[py]:
            self.rank[px] += 1
        
        return True
    
    def connected(self, x, y):
        return self.find(x) == self.find(y)
```
- Time: O(α(n)) ≈ O(1) per operation

## Common Problems

1. **Number of Islands**
2. **Clone Graph**
3. **Course Schedule** (cycle detection, topological sort)
4. **Network Delay Time** (Dijkstra)
5. **Cheapest Flights Within K Stops**
6. **Word Ladder**
7. **Alien Dictionary** (topological sort)
8. **Graph Valid Tree**
9. **Redundant Connection** (Union-Find)
10. **Minimum Spanning Tree**
11. **Shortest Path in Binary Matrix**
12. **Pacific Atlantic Water Flow**

## Tips
- Choose representation based on graph density
- BFS for shortest path in unweighted graph
- DFS for cycle detection, topological sort
- Dijkstra for shortest path with non-negative weights
- Bellman-Ford for negative weights
- Floyd-Warshall for all-pairs shortest path
- Union-Find for connectivity and MST
- Always check for disconnected components
- Consider bidirectional BFS for optimization

## Interview Questions
- Difference between DFS and BFS?
- When to use Dijkstra vs Bellman-Ford?
- How does Union-Find work?
- Explain topological sort
- How to detect cycles in directed/undirected graphs?
