from collections import defaultdict, deque

def dfs(graph, start, goal, depth_limit):
    """Depth-First Search with a depth limit."""
    visited = set()
    stack = [(start, 0)]  # (node, depth)

    while stack:
        node, depth = stack.pop()

        if node == goal:
            return True, node # Found the goal

        if node not in visited and depth <= depth_limit:
            visited.add(node)
            # Push neighbors onto stack, prioritizing exploration
            for neighbor in reversed(graph.get(node, [])):
                stack.append((neighbor, depth + 1))

    return False, None # Goal not found within depth limit

def iddfs(graph, start, goal, max_depth):
    """Iterative Deepening Depth-First Search."""
    for depth_limit in range(max_depth + 1):
        print(f"Exploring with depth limit: {depth_limit}")
        found, node = dfs(graph, start, goal, depth_limit)
        if found:
            print(f"Goal found at depth limit {depth_limit}")
            return True
    print("Goal not found within the maximum depth.")
    return False

# Example Usage:
graph = {
    'A': ['B', 'C'],
    'B': ['A', 'D', 'E'],
    'C': ['A', 'F', 'G'],
    'D': ['B'],
    'E': ['B', 'H'],
    'F': ['C'],
    'G': ['C'],
    'H': ['E']
}

start_node = 'A'
goal_node = 'H'
max_depth_limit = 5

iddfs(graph, start_node, goal_node, max_depth_limit)

start_node = 'A'
goal_node = 'Z' # A node that does not exist
max_depth_limit = 5
print("\nTrying to find a non-existent node:")
iddfs(graph, start_node, goal_node, max_depth_limit)
