
The efficiency of an algorithm depends on two parameters:
1. Time complexity: Time Complexity is defined as the number of times a particular instruction set is executed rather than the total time taken.

2. Space complexity: Space Complexity is the total memory space required by the program for its execution. 

Both are calculated as the function of input size(n).

## Types Of Time Complexity:
1. Best Time Complexity
2. Average Time Complexity
3. Worst Time Complexity


## Sorting Algorithm
| Algorithm ||Time Complexity  || Space Complexity|
| --- | --- | --- | --- | --- |
|  | Best | Average | Worst | Worst |
| Selection Sort | $\Omega(n^2)$ | $\Theta(n^2)$| $O(n^2)$ | O(1) |


## BFS and DFS
| Parameters| BFS | DFS |
| --- | --- | --- |
|1. Stands for | BFS for Breadth First  Search | DFS stands for Depth First Search|
| 2. Data Structure | BFS uses Queue | DFS uses stack |
| 3. Definition | 	BFS is a traversal approach in which we first walk through all nodes on the same level before moving on to the next level.  | DFS is also a traversal approach in which the traverse begins at the root node and proceeds through the nodes as far as possible until we reach the node with no unvisited nearby nodes. |
| 4. Technique | BFS can be used to find a single source shortest path in an unweighted graph because, in BFS, we reach a vertex with a minimum number of edges from a source vertex.  | In DFS, we might traverse through more edges to reach a destination vertex from a source. | 
| 5. Conceptual Difference | BFS builds the tree level by level. | DFS builds the tree sub-tree by sub-tree. |
| 6. Approach used | It works on the concept of FIFO (First In First Out).  | It works on the concept of LIFO (Last In First Out). |
| 7. Suitable for | BFS is more suitable for searching vertices closer to the given source. | DFS is more suitable when there are solutions away from source. |
| 8. Time Complexity | $O(V+E)$ when Adjacency List is used and $O(V^2)$ when Adjacency Matrix is used, where V for vertices and E for edges. | $O(V+E)$ when Adjacency List is used and $O(V^2)$ when Adjacency Matrix is used. | 
| 9. Visiting of Siblings/ Children | Here, siblings are visited before the children. | Here, children are visited before the siblings. |
| 10. Removal of Traversed Nodes | Nodes that are traversed several times are deleted from the queue. | The visited nodes are added to the stack and then removed when there are no more nodes to visit.|
| 11. Backtracking | 	In BFS there is no concept of backtracking.  | DFS algorithm is a recursive algorithm that uses the idea of backtracking |
| 12. Applications | BFS is used in various applications such as bipartite graphs, shortest paths, etc. | DFS is used in various applications such as acyclic graphs and topological order etc.|
| 13. Memory  |  BFS requires more memory.  | DFS requires less memory. |
| 14. Optimality | BFS is optimal for finding the shortest path. | DFS is not optimal for finding the shortest path. |
| 15. Space complexity |  	In BFS, the space complexity is more critical as compared to time complexity. |  DFS has lesser space complexity because at a time it needs to store only a single path from the root to the leaf node. | 
| 16. Speed | BFS is slow as compared to DFS. | DFS is fast as compared to BFS. |
| 17. When to use? | When the target is close to the source, BFS performs better.  | When the target is far from the source, DFS is preferable. |