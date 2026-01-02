# Trees and Graphs

## Trees

### Overview
A tree is a hierarchical data structure with a root node and subtrees of children nodes.

### Binary Tree
```python
class TreeNode:
    def __init__(self, val=0):
        self.val = val
        self.left = None
        self.right = None
```

### Types of Trees

#### 1. Binary Search Tree (BST)
- Left subtree < node < right subtree
- Search, Insert, Delete: O(log n) average, O(n) worst case

#### 2. Balanced Trees
- **AVL Tree**: Height-balanced BST
- **Red-Black Tree**: Self-balancing BST
- Operations: O(log n) guaranteed

#### 3. Heap
- Complete binary tree with heap property
- Min Heap: Parent ≤ children
- Max Heap: Parent ≥ children
- Operations: O(log n) insert/delete, O(1) peek

#### 4. Trie (Prefix Tree)
- Tree for storing strings
- Used for autocomplete, spell checking
- Search: O(m) where m is length of word

### Tree Traversals

#### Depth-First Search (DFS)
```python
# Inorder (Left-Root-Right)
def inorder(root):
    if not root:
        return []
    return inorder(root.left) + [root.val] + inorder(root.right)

# Preorder (Root-Left-Right)
def preorder(root):
    if not root:
        return []
    return [root.val] + preorder(root.left) + preorder(root.right)

# Postorder (Left-Right-Root)
def postorder(root):
    if not root:
        return []
    return postorder(root.left) + postorder(root.right) + [root.val]
```

#### Breadth-First Search (BFS)
```python
from collections import deque

def level_order(root):
    if not root:
        return []
    result = []
    queue = deque([root])
    while queue:
        level_size = len(queue)
        current_level = []
        for _ in range(level_size):
            node = queue.popleft()
            current_level.append(node.val)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        result.append(current_level)
    return result
```

## Graphs

### Overview
A graph consists of vertices (nodes) and edges connecting them.

### Representations

#### 1. Adjacency Matrix
```python
graph = [[0, 1, 1],
         [1, 0, 1],
         [1, 1, 0]]
```

#### 2. Adjacency List
```python
graph = {
    0: [1, 2],
    1: [0, 2],
    2: [0, 1]
}
```

### Types of Graphs
- **Directed vs Undirected**
- **Weighted vs Unweighted**
- **Cyclic vs Acyclic**
- **Connected vs Disconnected**

### Graph Traversals

#### Depth-First Search (DFS)
```python
def dfs(graph, start, visited=None):
    if visited is None:
        visited = set()
    visited.add(start)
    print(start)
    for neighbor in graph[start]:
        if neighbor not in visited:
            dfs(graph, neighbor, visited)
    return visited
```

#### Breadth-First Search (BFS)
```python
from collections import deque

def bfs(graph, start):
    visited = set([start])
    queue = deque([start])
    while queue:
        vertex = queue.popleft()
        print(vertex)
        for neighbor in graph[vertex]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
```

### Common Graph Algorithms

#### 1. Dijkstra's Algorithm (Shortest Path)
```python
import heapq

def dijkstra(graph, start):
    distances = {node: float('inf') for node in graph}
    distances[start] = 0
    pq = [(0, start)]
    
    while pq:
        current_dist, current = heapq.heappop(pq)
        if current_dist > distances[current]:
            continue
        for neighbor, weight in graph[current]:
            distance = current_dist + weight
            if distance < distances[neighbor]:
                distances[neighbor] = distance
                heapq.heappush(pq, (distance, neighbor))
    return distances
```

#### 2. Union-Find (Disjoint Set)
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
```

#### 3. Topological Sort
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

## Common Problems

### Tree Problems
1. **Maximum Depth of Binary Tree**
2. **Validate Binary Search Tree**
3. **Lowest Common Ancestor**
4. **Serialize and Deserialize Binary Tree**
5. **Binary Tree Level Order Traversal**
6. **Invert Binary Tree**
7. **Path Sum**
8. **Balanced Binary Tree**

### Graph Problems
1. **Number of Islands**
2. **Clone Graph**
3. **Course Schedule** (cycle detection)
4. **Network Delay Time** (Dijkstra)
5. **Word Ladder**
6. **Pacific Atlantic Water Flow**
7. **Graph Valid Tree**
8. **Minimum Spanning Tree** (Kruskal/Prim)

## Tips
- Draw the problem to visualize
- Choose appropriate traversal: DFS for paths, BFS for shortest path
- Use visited set to avoid cycles
- Consider both recursive and iterative approaches
- For BST, use inorder traversal for sorted output
- Union-Find is great for connectivity problems
- Topological sort for dependency graphs

## Interview Questions
- Difference between BFS and DFS?
- When would you use each traversal?
- How do you detect a cycle in a graph?
- Explain different types of trees and their use cases
