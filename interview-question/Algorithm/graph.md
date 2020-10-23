1. Detect cycle in undirected graph

Use DFS, DFS for a connected graph produces a tree. There is a cycle in a graph only if there is a back edge present in the graph. A back edge is an edge that is joining a node to itself (self-loop) or one of its ancestor in the tree produced by DFS.
         To find the back edge to any of its ancestor keep a visited array and if there is a back edge to any visited node then there is a loop and return true.
         https://www.geeksforgeeks.org/detect-cycle-undirected-graph/

BFS
https://www.geeksforgeeks.org/detect-cycle-in-an-undirected-graph-using-bfs/
2. Detetc Cycle in directed graph

