# Breadth First Search
## AIM

To develop an algorithm to find the route from the source to the destination point using breadth-first search.

## THEORY
Breadth-first search (BFS) is an algorithm for traversing or searching tree or graph data structures. It starts at the tree root, and explores all of the neighbour nodes at the present depth prior to moving on to the nodes at the next depth level. BFS is a graph traversal approach in which you start at a source node and layer by layer through the graph, analyzing the nodes directly related to the source node. Then, in BFS traversal, you must move on to the next-level neighbor nodes.

## DESIGN STEPS

### STEP 1:
Identify a location in the google map

### STEP 2:
Select a specific number of nodes with distance

### STEP 3:
Initialize a queue. All nodes in which nearby nodes have already been visited; you must remove them from the queue.Because the queue is now empty, the bfs traversal has ended.

### STEP 4:
Include each node and its distance separately in the dictionary data structure.

### STEP 5:
End of program.


## ROUTE MAP
#### Example map
![image](https://user-images.githubusercontent.com/75235159/167568105-ec030486-5db4-4a8a-b5b1-5a18f456a984.png)



## PROGRAM
```python
Student name : Dinesh.S
Reg.no : 212220230011
 ```
 ```python
 %matplotlib inline
import matplotlib.pyplot as plt
import random
import math
import sys
from collections import defaultdict, deque, Counter
from itertools import combinations

class Problem(object):
    def __init__(self, initial=None, goal=None, **kwds): 
        self.__dict__.update(initial=initial, goal=goal, **kwds) 
        
    def actions(self, state):        
        raise NotImplementedError
    def result(self, state, action): 
        raise NotImplementedError
    def is_goal(self, state):        
        return state == self.goal
    def action_cost(self, s, a, s1): 
        return 1
    
    def __str__(self):
        return '{0}({1}, {2})'.format(
            type(self).__name__, self.initial, self.goal)
            
class Node:
    def __init__(self, state, parent=None, action=None, path_cost=0):
        self.__dict__.update(state=state, parent=parent, action=action, path_cost=path_cost)

    def __str__(self): 
        return '<{0}>'.format(self.state)
    def __len__(self): 
        return 0 if self.parent is None else (1 + len(self.parent))
    def __lt__(self, other): 
        return self.path_cost < other.path_cost
        
failure = Node('failure', path_cost=math.inf) 
cutoff  = Node('cutoff',  path_cost=math.inf)

def expand(problem, node):
    "Expand a node, generating the children nodes."
    s = node.state
    for action in problem.actions(s):
        s1 = problem.result(s, action)
        cost = node.path_cost + problem.action_cost(s, action, s1)
        yield Node(s1, node, action, cost)
        

def path_actions(node):
    "The sequence of actions to get to this node."
    if node.parent is None:
        return []  
    return path_actions(node.parent) + [node.action]


def path_states(node):
    "The sequence of states to get to this node."
    if node in (cutoff, failure, None): 
        return []
    return path_states(node.parent) + [node.state]
    
FIFOQueue = deque

def breadth_first_search(problem):
    "Search shallowest nodes in the search tree first."
    node = Node(problem.initial)
    if problem.is_goal(problem.initial):
        return node
    frontier = FIFOQueue([node])
    reached = {problem.initial}
    while frontier:
        node = frontier.pop()
        for child in expand(problem, node):
            s = child.state
            if problem.is_goal(s):
                return child
            if s not in reached:
                reached.add(s)
                frontier.appendleft(child)
    return failure
    
class RouteProblem(Problem):
    """A problem to find a route between locations on a `Map`.
    Create a problem with RouteProblem(start, goal, map=Map(...)}).
    States are the vertexes in the Map graph; actions are destination states."""
    
    def actions(self, state): 
        """The places neighboring `state`."""
        return self.map.neighbors[state]
    
    def result(self, state, action):
        """Go to the `action` place, if the map says that is possible."""
        return action if action in self.map.neighbors[state] else state
    
    def action_cost(self, s, action, s1):
        """The distance (cost) to go from s to s1."""
        return self.map.distances[s, s1]
    
    def h(self, node):
        "Straight-line distance between state and the goal."
        locs = self.map.locations
        return straight_line_distance(locs[node.state], locs[self.goal])
        
 class Map:
    """A map of places in a 2D world: a graph with vertexes and links between them. 
    In `Map(links, locations)`, `links` can be either [(v1, v2)...] pairs, 
    or a {(v1, v2): distance...} dict. Optional `locations` can be {v1: (x, y)} 
    If `directed=False` then for every (v1, v2) link, we add a (v2, v1) link."""

    def __init__(self, links, locations=None, directed=False):
        if not hasattr(links, 'items'): # Distances are 1 by default
            links = {link: 1 for link in links}
        if not directed:
            for (v1, v2) in list(links):
                links[v2, v1] = links[v1, v2]
        self.distances = links
        self.neighbors = multimap(links)
        self.locations = locations or defaultdict(lambda: (0, 0))

        
def multimap(pairs) -> dict:
    "Given (key, val) pairs, make a dict of {key: [val,...]}."
    result = defaultdict(list)
    for key, val in pairs:
        result[key].append(val)
    return result
    
Mapping_locations = Map(
    {('T.nagar', 'Guindy'): 10,('T.nagar', 'Kodambakkam'): 14,('Guindy', 'Velachery'): 2,('Guindy', 'Porur'): 8,('Velachery', 'Chromepet'): 5,('Chromepet',          'Tambaram'): 3,('Tambaram', 'Perungalathur'): 8,('Perungalathur', 'Padappai'): 9,('Padappai', 'Vallam'): 14,('Vallam', 'Mother'): 3,
    ('Mother', 'Walajabad'): 11,('Walajabad', 'Kachipuram'): 5,('Perungalathur', 'Guduvanchery'): 7,('Guduvanchery', 'Singaperumal Kovil'): 5,('Singaperummal Kovil', 'Chengalpattu'): 8,('Chengalpattu', 'Palayaseevaram'): 6,('Palayaseevaram', 'Kattavakkam'): 8,('Kattavakkam', 'Enathur'): 5,('Enathur', 'Kanchipuram'): 11,
    ('Porur', 'Poonamallee'): 8,('Poonamalle', 'Sriperumabadur'): 14,('Sriperumbadur', 'Sunguvarchathiram'): 9,('Sriperumabadur', 'Mudichur'): 8, ('Sunguvarchathiram', 'Neervalur'): 4,('Neervalur', 'Kilambi'): 5,('Kilambi', 'Damal'): 4,('Neervalur', 'Enathur'): 7,('Enathur', 'Kanchipuram'): 9,('Kanchipuram', 'Kilambi'): 4,('Kilambi', 'Damal'): 4,('Kodambakkam', 'Porur'): 9})


r0 = RouteProblem('T.nagar', 'Velachery', map=Mapping_locations)
r1 = RouteProblem('Chromepet', 'Padappai', map=Mapping_locations)
r2 = RouteProblem('Velachery', 'Poonamallee', map=Mapping_locations)
r3 = RouteProblem('Sriperumabadur', 'Mudichur', map=Mapping_locations)
r4 = RouteProblem('Enathur', 'Damal', map=Mapping_locations)
print(r0)
print(r1)
print(r2)
print(r3)
print(r4)

goal_state_path=breadth_first_search(r0)
path_states(goal_state_path) 
print("GoalStateWithPath:{0}".format(goal_state_path))
print("Total Distance={0} Kilometers".format(goal_state_path.path_cost))

goal_state_path=breadth_first_search(r1)
path_states(goal_state_path) 
print("GoalStateWithPath:{0}".format(goal_state_path))
print("Total Distance={0} Kilometers".format(goal_state_path.path_cost))

goal_state_path=breadth_first_search(r2)
path_states(goal_state_path) 
print("GoalStateWithPath:{0}".format(goal_state_path))
print("Total Distance={0} Kilometers".format(goal_state_path.path_cost))

goal_state_path=breadth_first_search(r3)
path_states(goal_state_path) 
print("GoalStateWithPath:{0}".format(goal_state_path))
print("Total Distance={0} Kilometers".format(goal_state_path.path_cost))

goal_state_path=breadth_first_search(r4)
path_states(goal_state_path) 
print("GoalStateWithPath:{0}".format(goal_state_path))
print("Total Distance={0} Kilometers".format(goal_state_path.path_cost))

```
## OUTPUT:
![image](https://user-images.githubusercontent.com/75235159/167574682-2355f4e6-7f99-4294-aae2-de125b0c6ab9.png)
![image](https://user-images.githubusercontent.com/75235159/167572231-b1619c28-e925-41ac-abce-eb6bcf624c11.png)
![image](https://user-images.githubusercontent.com/75235159/167572291-9cb8e197-cb6d-43b8-896f-8dcee079f186.png)
![image](https://user-images.githubusercontent.com/75235159/167574742-9fd0d52f-8030-48b4-abec-1724a237e09e.png)
![image](https://user-images.githubusercontent.com/75235159/167574794-a0b56665-e134-4223-9bb6-57c9c930a740.png)
![image](https://user-images.githubusercontent.com/75235159/167574851-d0c99181-2888-43e0-9473-e9765901b51e.png)


## SOLUTION JUSTIFICATION:
Route follow the minimum distance between locations using breadth-first search. As BFS traverses the tree “shallowest node first”, it would always pick the shallower branch until it reaches the solution.

## RESULT:
Thus an algorithm to find the route from the source to the destination point using breadth-first search is developed and executed successfully.
