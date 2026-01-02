# Tree BFS/DFS Pattern

## Overview
Tree traversal patterns for exploring tree nodes systematically using Breadth-First Search (level-order) or Depth-First Search (preorder, inorder, postorder).

## Binary Tree Node
```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

## Breadth-First Search (BFS)

### Level-Order Traversal
```python
from collections import deque

def level_order(root):
    """Level-order traversal (BFS)"""
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

## Depth-First Search (DFS)

### 1. Preorder (Root-Left-Right)
```python
def preorder(root):
    """Preorder traversal"""
    if not root:
        return []
    return [root.val] + preorder(root.left) + preorder(root.right)

def preorder_iterative(root):
    """Preorder traversal (iterative)"""
    if not root:
        return []
    
    result = []
    stack = [root]
    
    while stack:
        node = stack.pop()
        result.append(node.val)
        
        if node.right:
            stack.append(node.right)
        if node.left:
            stack.append(node.left)
    
    return result
```

### 2. Inorder (Left-Root-Right)
```python
def inorder(root):
    """Inorder traversal"""
    if not root:
        return []
    return inorder(root.left) + [root.val] + inorder(root.right)

def inorder_iterative(root):
    """Inorder traversal (iterative)"""
    result = []
    stack = []
    current = root
    
    while current or stack:
        while current:
            stack.append(current)
            current = current.left
        
        current = stack.pop()
        result.append(current.val)
        current = current.right
    
    return result
```

### 3. Postorder (Left-Right-Root)
```python
def postorder(root):
    """Postorder traversal"""
    if not root:
        return []
    return postorder(root.left) + postorder(root.right) + [root.val]

def postorder_iterative(root):
    """Postorder traversal (iterative)"""
    if not root:
        return []
    
    result = []
    stack = [root]
    
    while stack:
        node = stack.pop()
        result.append(node.val)
        
        if node.left:
            stack.append(node.left)
        if node.right:
            stack.append(node.right)
    
    return result[::-1]  # Reverse
```

## Common BFS Problems

### 1. Binary Tree Right Side View
```python
def right_side_view(root):
    """Nodes visible from right side"""
    if not root:
        return []
    
    result = []
    queue = deque([root])
    
    while queue:
        level_size = len(queue)
        
        for i in range(level_size):
            node = queue.popleft()
            
            # Last node of level
            if i == level_size - 1:
                result.append(node.val)
            
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
    
    return result
```

### 2. Binary Tree Zigzag Level Order
```python
def zigzag_level_order(root):
    """Zigzag level order traversal"""
    if not root:
        return []
    
    result = []
    queue = deque([root])
    left_to_right = True
    
    while queue:
        level_size = len(queue)
        level = deque()
        
        for _ in range(level_size):
            node = queue.popleft()
            
            if left_to_right:
                level.append(node.val)
            else:
                level.appendleft(node.val)
            
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        
        result.append(list(level))
        left_to_right = not left_to_right
    
    return result
```

### 3. Minimum Depth
```python
def min_depth(root):
    """Minimum depth of tree"""
    if not root:
        return 0
    
    queue = deque([(root, 1)])
    
    while queue:
        node, depth = queue.popleft()
        
        if not node.left and not node.right:
            return depth
        
        if node.left:
            queue.append((node.left, depth + 1))
        if node.right:
            queue.append((node.right, depth + 1))
    
    return 0
```

## Common DFS Problems

### 1. Maximum Depth
```python
def max_depth(root):
    """Maximum depth of tree"""
    if not root:
        return 0
    return 1 + max(max_depth(root.left), max_depth(root.right))
```

### 2. Path Sum
```python
def has_path_sum(root, target_sum):
    """Check if path with given sum exists"""
    if not root:
        return False
    
    if not root.left and not root.right:
        return root.val == target_sum
    
    return (has_path_sum(root.left, target_sum - root.val) or
            has_path_sum(root.right, target_sum - root.val))
```

### 3. All Root-to-Leaf Paths
```python
def binary_tree_paths(root):
    """All root-to-leaf paths"""
    if not root:
        return []
    
    if not root.left and not root.right:
        return [str(root.val)]
    
    paths = []
    for path in binary_tree_paths(root.left):
        paths.append(str(root.val) + "->" + path)
    for path in binary_tree_paths(root.right):
        paths.append(str(root.val) + "->" + path)
    
    return paths
```

### 4. Lowest Common Ancestor
```python
def lowest_common_ancestor(root, p, q):
    """Find LCA of two nodes"""
    if not root or root == p or root == q:
        return root
    
    left = lowest_common_ancestor(root.left, p, q)
    right = lowest_common_ancestor(root.right, p, q)
    
    if left and right:
        return root
    return left if left else right
```

### 5. Diameter of Binary Tree
```python
def diameter_of_binary_tree(root):
    """Longest path between any two nodes"""
    diameter = [0]
    
    def height(node):
        if not node:
            return 0
        
        left_height = height(node.left)
        right_height = height(node.right)
        
        diameter[0] = max(diameter[0], left_height + right_height)
        
        return 1 + max(left_height, right_height)
    
    height(root)
    return diameter[0]
```

### 6. Validate BST
```python
def is_valid_bst(root):
    """Check if tree is valid BST"""
    def validate(node, min_val, max_val):
        if not node:
            return True
        
        if node.val <= min_val or node.val >= max_val:
            return False
        
        return (validate(node.left, min_val, node.val) and
                validate(node.right, node.val, max_val))
    
    return validate(root, float('-inf'), float('inf'))
```

## When to Use BFS vs DFS

### Use BFS When:
- Level-order traversal needed
- Finding shortest path
- Finding minimum depth
- Processing nodes level by level

### Use DFS When:
- Need to explore all paths
- Finding maximum depth
- Checking path properties
- In-order traversal for BST

## Time and Space Complexity

### BFS
- **Time:** O(n)
- **Space:** O(w) where w is max width

### DFS
- **Time:** O(n)
- **Space:** O(h) where h is height

## More Problems

### BFS:
1. **Average of Levels**
2. **Level Order Bottom**
3. **Populating Next Right Pointers**
4. **Serialize and Deserialize Binary Tree**

### DFS:
1. **Sum Root to Leaf Numbers**
2. **Path Sum II**
3. **Maximum Path Sum**
4. **Construct Binary Tree from Traversals**
5. **Flatten Binary Tree to Linked List**
