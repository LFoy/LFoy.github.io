---
layout: post
title:  Matrix & Graph
date:   2024-06-27 13:51:29 +1000
categories: LeetCode
--- 

# Actually _matrix_ is _2D_ arrays!

# Graph

## Unweighted Graph can be represented by __Matrix__ or __List__.

### i) By Matrix
Just fill the all elements by `0`, and set `1` if the number of vertex is connected by others.  
This is not appreciable bc it ends up taking $O(n^2)$ space. So only for small graph.

### ii) By adjacency List
`ArrayList<ArrayList<>>`
Space Complexity : $O(2n)$ if graph is undirected.

## Weighted Graph
also can be represented by _Matrix_ or _List_ 

### i) By Matrix
Just fill the all elements by `0 or -ve`, and set `weight` if the number of vertex is connected by others.  
This is not appreciable bc it ends up taking $O(n^2)$ space. So only for small graph.

### ii) By adjacency List
`ArrayList<ArrayList<Pair>>`
We just use Pair for {connected node, weigth}. The other are same with unweight graph.

### iii) By HashTable
If the node's name is not number. we can't use a name as index of List. So for efficiency, we have to use `HashMap<String, HashMap<String, Integer>>`
[[2024-06-27-Matrix&Graph#^9ac034| directed weight graph]]

## Topological Sort in directed Graph
A linear ordering of its vertices such that for _every directed edge(u, v)_ from vertex u to vertex v, __u comes before v in the ordering__ In a directed graph, There can be _so many different(multiple)_ topological sort linear order.

### i) By DFS 
TC : $O(V+E)$

We need a `Set` and a `Stack`.
_Set_ has all the _visited_ vertices.
_Stack_ has all the vertices in topological _sorted order_. If we visited _all child(directed) of a vertex_, then we push that vertex on the `Stack`.

```java
public Deque<<Vertex<T>> topSort(Graph graph) {
	Deque<Vertex<T>> stack = new ArrayDeque<>();
	Set<Vertex<T>> visited = new HashSet<>();

	for (Vertex<T> vertex : graph.getAllVertex()) {
		if (visited.contains(vertex)) continue;
		topSortUtil(vertex, stack, visited);
	}
	
	return stack;
}

private void topSortUtil(Vertex<T> vertex, Deque<Vertex<T>> stack, Set<Vertex> visited) {
	for(Vertex<T> childVertex : vertex.getAdjacentVetexes()) {
		if(visited.contains(childVertex)) continue;
		topSortUtil(childVertex, stack, visited);
	}
	stack.offerFirst(vertex);
}
```

### ii) By BFS (Kahn's Algorithm)
Kahn's Algorithm is about _Dependency_.
TC : $O(V+E)$ ^42192e
> Repeatedly remove nodes without any dependencies from the graph and Add them to the topological ordering.
> we repeat removing nodes without dependencies from the graph until _all nodes are processed_ or a _cycle is discovered_.

1) Begin by counting the _incoming degree_ of each node
2) Maintain a queue of all nodes with no incoming edges
3) Remove a node from queue, and _decrease the degree of all affected nodes_.
	-> Maintain removed node for topological order. 
 1) _Add any new nodes_ which an _incoming degree of 0_ to the queue.
5) Repeat Phase 3, 4
```pseudocode
// `g` is a DAG represented as an adjacency list
function topSort(g) :
	n = g.size()
	// size n
	in_degree - [0, 0, ..., 0]
	// count incoming degree
	for (i = 0; i < n; i++) :
		for (to in g[i]):
			in_degree[to] += 1;

	// `q` always contains the set nodes with no incoming edges.
	q = empty
	for (i=0; i < n; i++):
		if(in_degree[i] == 0):
			q.add(i)
	index = 0
	// size n
	order = [0, 0, ..., 0]

	while (!.isEmpty()):
		at = q.dequeue();
		order[index++] = at
		for (to in g[at]):
			in_degree[to] -= 1
			if(in_degree[to] == 0):
				q.add(to)
	
	if index != n:
		// graph contains a cycle
		return null
	return order
			
```


1. [36. Valid Sudoku](https://leetcode.com/problems/valid-sudoku/), 27/Jun/24
Topics : Arrays, Hash Table, Matrix
[My solution](https://leetcode.com/problems/valid-sudoku/solutions/5375368/2-methods-using-2d-arrays-or-set-with-well-explanation)

We can check dup on 9*3 times (row, col, box)
But using nested loop  3 times is terrible to look.
Come up with _How to solve it using only one nested loop?_

We have to think about __"%"__ and __"/"__. These two operators can be helpful for _matrix traversal problems._

### i ) Using 2D Arrays. (Much faster than ii) method)

```java
class Solution {
    public boolean isValidSudoku(char[][] board) {
	    // 1D means check times, 2D means dup.
        int[][] row = new int[9][9];
        int[][] col = new int[9][9];
        int[][] box = new int[9][9];

        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                if (board[i][j] == '.') continue;
				// Convert char to int
                int val = Character.getNumericValue(board[i][j]);
                
                if (row[i][val - 1] == 0) row[i][val - 1] = 1;
                else return false;
				
                if (col[j][val - 1] == 0) col[j][val - 1] = 1;
                else return false;

                if (box[i/3 * 3 + j/3][val - 1] == 0) box[i/3 * 3 + j/3][val - 1] = 1;
                else return false;     
            }
        }

        return true;
        
    }
}
```

__It is really hard and essential to think about `box[i/3 * 3 + j/3]`.__
box order is like below.
>0 1 2
>3 4 5
>6 7 8

`i` index can be used to change row of boxes, and `j` index can be used to change col of boxes! 

### ii) Using `HashSet<String>`
Actually ii) method  is also same idea with `box[i/3 * 3 + j/3]`.

```java
class Solution {
    public boolean isValidSudoku(char[][] board) {
        HashSet<String> set = new HashSet<String>();
        
        for (int row = 0; row < board.length; row++) {
            for (int col = 0; col < board[1].length; col++) {
                char val = board[row][col];
                if (val == '.') continue;

                if ( !set.add(val + "in row " + row) ||
                    !set.add(val + "in col " + col) ||
                    !set.add(val + "in box " + row/3 * 3 + col/3)) return false;
            }
        }
        return true;
    }
}
```

So _essence of this problem_ is __"Can you make box index right?"__ `box[i/3 * 3 + j/3]`

2. [54. Spiral Matrix](https://leetcode.com/problems/spiral-matrix/), 27/Jun/24
Topics : Array, Matrix, Simulation
couldn't solve it with 40mi because of the condition when have to stop.

The essence of this problem is also _get indices for travel matrix!_

Well for some problems, the best way really is to come up with some algorithms for __simulation__. Basically, you need to simulate what the problem asks us to do.
We go _boundary by boundary_ and move inwards. So ==we have to keep track of boundary!== To present boundary of square needs _4 valuables_!

We can think like this.
>
(fixed first row, col until last col) // after this, increase first row by 1 
(fixed last col, row until last row) // decrease last col by 1
(fixed last row, col until first col) // decrease last row by 1
(fixed first col, row until first row) // increase first col by 1

But the most important thing is _knowing the time when we have to stop!_
That is really tricky.
We can use _size of answer list_!

```java
class Solution {
    public List<Integer> spiralOrder(int[][] matrix) {
        List<Integer> spiral = new ArrayList<Integer>();
        int firstRow = 0, firstCol = 0;
        int lastRow = matrix.length - 1, lastCol = matrix[0].length - 1;
        int m = matrix.length, n = matrix[0].length;

        while (spiral.size() < n*m) {
            for (int col = firstCol; col <= lastCol && spiral.size() < n*m; col++) {
                spiral.add(matrix[firstRow][col]);
            }
            firstRow++;

            for (int row = firstRow; row <= lastRow && spiral.size() < n*m; row++) {
                spiral.add(matrix[row][lastCol]);
            }
            lastCol--;

            for (int col = lastCol; col >= firstCol && spiral.size() < n*m; col--) {
                spiral.add(matrix[lastRow][col]);
            }
            lastRow--;

            for (int row = lastRow; row >= firstRow && spiral.size() < n*m; row--) {
                spiral.add(matrix[row][firstCol]);
            }
            firstCol++;
        }

        return spiral;

    }
}
```

3. [48. Rotate Image](https://leetcode.com/problems/rotate-image/), 27/Jun/24
Topics : Array, Math, Matrix

선형대수학(벡터, 행렬 등)
==This problem is related to linear algebra(vector, matrix etc.)==
Def FAANG likes _(Math + Array)_ category!

Orininal
```
1  2  3  4
5  6  7  8
9 10 11 12
13 14 15 16
```

After rotate by 90 degrees.

```
- - 5 1
- - 6 2
- - 7 3
- - 8 4
```

We can know it is related to __transport(switches row and coloumn, in other words, flips a matrix over its diagonal)!__

But after transport matrix is like below.
```
1 5 - -
2 6 - -
3 7 - -
4 8 - -
```

_Its diagonal indices is not changed!_

Then we can compare between after rotate by 90 degrees and after transport.
It is __flips a matrix over its vertical line__! it means _reverse every row!_

```java
class Solution {
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        // for swap
        int temp = 0;

        // 1. Transport matrix
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                temp = matrix[j][i];
                matrix[j][i] = matrix[i][j];
                matrix[i][j] = temp;
            }
        }        
        // 2. Reverse every row.
        for (int i = 0; i < n; i++) {
            int left = 0;
            int right = n - 1;
            while (left < right) {
                temp = matrix[i][left];
                matrix[i][left] = matrix[i][right];
                matrix[i][right] = temp;
                left++;
                right--;
            }
        }
    }
}
```

4. [73. Set Matrix Zeroes](https://leetcode.com/problems/set-matrix-zeroes/), 28/Jun/24
Topics : Array, Hash Table, Matrix
solved it in 19mi using Hint and HashSet...
But it was not best solution cuz it use O(m + n) space not constant!
[My solution](https://leetcode.com/problems/set-matrix-zeroes/solutions/5380882/two-methods-with-thought-process-and-well-explanation)
# Intuition

The impotant thing is **recording its row and column indices which have to changed to "zero"!**  
Using another matrix is O(mn) space. so not that good.

How about recording _only row and col respectively_, not pair(row,col)?.  
We don't need to record all pairs. we can focus on entrie row and column to `zero`s.  
So we can use _Set_ for row and col respectively.

## i) Using Set (SC : O(m+n))

```java
class Solution {
    public void setZeroes(int[][] matrix) {
        int m = matrix.length;
        int n = matrix[0].length;

        HashSet<Integer> rowZero = new HashSet<Integer>();
        HashSet<Integer> colZero = new HashSet<Integer>();

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (matrix[i][j] == 0) {
                    rowZero.add(i);
                    colZero.add(j);
                }
            }
        }
        // respectively
        for (int row : rowZero) {
            for (int col = 0; col < n; col++) matrix[row][col] = 0;
        }
        for (int col : colZero) {
            for (int row = 0; row < m; row++) matrix[row][col] = 0;
        }
        // integrily same as above
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (rowZero.contains(i) || colZero.contains(j)) {
                    matrix[i][j] = 0;
                }
            }
        }        

    }
}
```

How we can solve it on constant space?  
It is def impossible just using some valuables.  
**Then we must use array but constant space! How?!**  
Use just given matrix as a record!

## ii) Using given matrix itself (SC : O(1))

We can use _a first row and a first column as a record!_  
But **matrix[0][0] cannot represent row and col respectively!**  
So we have to use 2 valuables to keep track of first row and first column. (firstRow, firstCol).

After recording, the matrix is like below.

```
0 0 0 0 1 2 5
0
0
7
8
```

When using this record, if for loop start index 0, **it will ruin our record!**  
Therefore we have to start from last index to first!

```java
class Solution {
    public void setZeroes(int[][] matrix) {
        int m = matrix.length, n = matrix[0].length;
        Boolean firstRow = false, firstCol = false;

        // 1. Record 
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (matrix[i][j] == 0) {
                    if (i == 0) firstRow = true;
                    if (j == 0) firstCol = true;
                    matrix[i][0] = 0;
                    matrix[0][j] = 0;
                }
            }
        }

        // 2. Using record, Set zeros except for firstRow, firstCol
        for (int i = m - 1; i > 0; i--) {
            for (int j = n - 1; j > 0; j--) {
                if (matrix[i][0] == 0 || matrix[0][j] == 0 ) matrix[i][j] = 0;
            }
        }
        
        if (firstRow == true) {
            for (int col = 0; col < n; col++) matrix[0][col] = 0;
        }

        if (firstCol == true) {
            for (int row = 0; row < m; row++) matrix[row][0] = 0;
        }

    }
}
```

5. [289. Game of Life](https://leetcode.com/problems/game-of-life/), 28/Jun/24
Topics : Array, Matrix, Simulation
could solve it in 25mi. but using O$(m*n)$ space... 
[My solution](https://leetcode.com/problems/game-of-life/solutions/5382292/2-methods-with-thought-process-sc-o-m-n-and-o-1)

Rules
1. live -> dead if live neighbors < 2
2. live -> live if live neighbors == 2 or 3
3. live -> dead if live neighbors > 3
4. dead -> live if live neighbors == 3

live nei cells | next state
------| ------
3 |  live
more than 3  | dead
2 | live -> live, dead -> dead // In other words it is same!
else | dead

### i) Using another matrix for counting live neighbor cells. (SC : $O(m*n)$)
```java
        int m = board.length;
        int n = board[0].length;
        int[][] liveCnt = new int[m][n];

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                for (int row = - 1; row < 2; row++) {
                    for (int col = -1; col < 2; col++) {
                        if (i + row >= 0 && i + row < m && j + col >= 0 && j + col < n) {
                            if (i + row == i && j + col == j) continue;
                            if (board[i + row][j + col] == 1) liveCnt[i][j] += 1;
                        }
                    }
                }
            }
        }

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (liveCnt[i][j] > 3) {
                    board[i][j] = 0;
                }
                else if ([i][j] == 3) {
                    board[liveCnti][j] = 1;
                }
                else if (liveCnt[i][j] == 2) continue;
                else board[i][j] = 0;
            }
        }  
```


==How to keep track of original state after changing to next state?==
That is the key concern here.

### ii) Using symbols that represent the original state and next state. (SC : $O(1)$)

Original | Next | Symbol
-------|-------|-------
0 | 0 | 0
0 | 1 | 2 // if symbol of 01 is 1, then we can't tell its original state or symbol.
1 | 0 | 1
1 | 1 | 3

```java
class Solution {
    public void gameOfLife(int[][] board) {
        int m = board.length;
        int n = board[0].length;
        
        // Mark symbols
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                markSymbol(i, j, board, m, n);
            }
        }

        // Return the next state using symbols
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
		        // else if 안하고 if 2개 썼다가 망할뻔;; 생각이 안났어ㅠㅠ
                if (board[i][j] == 1) board[i][j] = 0;
                else if (board[i][j] == 2 || board[i][j] == 3) board[i][j] = 1;
            }
        }        
    }

    public void markSymbol(int i, int j, int[][] board, int m, int n) {
        int[] dr = new int[] {-1, -1, -1, 0, 0, 1, 1, 1};
        int[] dc = new int[] {-1, 0, 1, -1, 1, -1, 0, 1};
        int liveCnt = 0;
            
        for (int d = 0; d < dr.length; d++) {
            int row = i + dr[d];
            int col = j + dc[d];
            
            if (row >= 0 && row < m && col >= 0 && col < n) {
                if (board[row][col] == 1 || board[row][col] == 3) liveCnt++; 
            }
        }

        if (board[i][j] == 1) {
            // except for this conditon, it will die, so symbol is still 1.
            if (liveCnt == 2 || liveCnt == 3) board[i][j] = 3;
        }
        else {
            // only on this condition, it become a live cell. so symbole is 2.
            if (liveCnt == 3) board[i][j] = 2;
        }
    }
}
```


6.  [200. Number of Islands](https://leetcode.com/problems/number-of-islands/), 13/Jul/24
Topics : #Array , #DFS , #BFS , #UnionFind, #Matrix
could solve it 15mi bc it was so boring to me... I've already solved this type in so many times!

```java
    public int numIslands(char[][] grid) {
        int island = 0;
        int m = grid.length, n = grid[0].length;

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == '1') {
                    dfs(grid, i, j);
                    island++;
                }
            }
        }
        return island;
    }

    private void dfs(char[][] grid, int row, int col) {
        grid[row][col] = '2';

        int[] dx = {-1, 0, 1, 0};
        int[] dy = {0, 1, 0, -1};

        for (int i = 0; i < dx.length; i++) {
            int x = row + dx[i];
            int y = col + dy[i];
            if (x >= 0 && x < grid.length && y >= 0 && y < grid[0].length) {
                if (grid[x][y] == '1') {
                    dfs(grid, x , y);
                }
            }
        }      
    }
```

My code is like BFS. So can change for using _base code_.

```java
private void DFSMarking(char[][] grid, int i, int j) {
	// in DFS, BASE CODE SO IMPORTANT!!!!! 
    if (i < 0 || j < 0 || i >= n || j >= m || grid[i][j] != '1') return;
    
    grid[i][j] = '0';
    DFSMarking(grid, i + 1, j);
    DFSMarking(grid, i - 1, j);
    DFSMarking(grid, i, j + 1);
    DFSMarking(grid, i, j - 1);
}
```

7. [130. Surrounded Regions](https://leetcode.com/problems/surrounded-regions/), 13/Jul/24
Topics: #Array , #DFS , #BFS , #UnionFind, #Matrix 
could solve it 35mi without any hint! haha feel good~ but I spend time a lot fixing this
```java
if (board[i][j] == 's') board[i][j] = 'O';
                else if (board[i][j] == 'O') board[i][j] = 'X'; 
```
I have to change order of `if` or `if` to `else if`.
And for representing edge of board using `i` and `j`.

Just Think reversely.
all region of edge will be same!
And the other all region('O') will be changed to `X`;

Phase 1 : "Save" every O-region touching the border, changing its cells to 's'.
Phase 2 : Change every 's' to 'O' and everything else to 'X'.

```java
    public void solve(char[][] board) {
        int m = board.length, n = board[0].length;
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                // for every edge
                if(i == 0 || i == m - 1 || j == 0 || j == n - 1) {
                    if (board[i][j] == 'O') dfs(board, i, j);
                }               
            }
        }
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (board[i][j] == 's') board[i][j] = 'O';
                else if (board[i][j] == 'O') board[i][j] = 'X';               
            }
        }   
    }

    private void dfs(char[][] board, int i, int j) {
        if (i < 0 || i >= board.length || j < 0 || j >= board[0].length) return;
        // save that region bc it is on the edge of the board.
        if(board[i][j] == 'O') {
            board[i][j] = 's';
            dfs(board, i - 1, j);
            dfs(board, i, j + 1);
            dfs(board, i + 1, j);
            dfs(board, i, j - 1);
        }
    }
```

### ii) These _count island_ problems can be solved by _Union Find_.

The idea is If we connect all 'O' nodes on the boundary to _a dummy node(dummy element)_, and then connect each 'O' nodes to its neighbour 'O' nodes, then we can tell directly whether a 'O' node is captured by checking whether it is connected to the dummy node(_parent is dummy_) or not.
The important things are __using 1D parent array__. So we have to __convert 2D graph to 1D Array__ or _reverse_

This code convert 2D graph to 1D array(for parent array)
```java
class Solution {
    int[] parent;
    public void solve(char[][] board) {
       int n = board.length;
       int m = board[0].length;
       // we have n * m elements and one dummy node
       int size = n * m + 1;
       parent = new int[size];
       // initialize the parent array
       for(int i=0;i<size;i++) parent[i] = i;

       // Loop over the board
       for(int i=0;i<n;i++){
           for(int j=0;j<m;j++){
                // if we are in the first, last row or col and 
                // the cell = O, union this cell with the dummy node
                if((j == m - 1 || i == n - 1 || j == 0 || i == 0) 
                    && board[i][j] == 'O'){
                   union(size - 1, i * m + j);
               }
               // if there are any two adjacent Os union them

               if(i < n - 1 && board[i][j] == 'O' && board[i + 1][j] == 'O'){
                   union((i+1)*m + j, i * m+ j);
               }

               if(j < m - 1 && board[i][j] == 'O' && board[i][j + 1] == 'O'){
                   union(i * m + j, i * m + j + 1);
               }
           }
       }
       // Get the parent of the dummy node to compare it with Os parents
       int flag = find(size - 1);
       for(int i=0;i<n;i++){
           for(int j=0;j<m;j++){
               // If they are not the same, this O is surrounded by Xs
               if(board[i][j] == 'O' && find(i * m + j) != flag ) {
                   board[i][j] = 'X';
               }
           }
       }
       return;
    }

    public boolean union(int x, int y){
        int xp = find(x);
        int yp = find(y);
        if(xp == yp) return false;
        parent[yp] = xp;
        return true;
    }

    public int find(int x){
        if(parent[x] != x) parent[x] = find(parent[x]);
        return parent[x];
    }    
}
```

8. [133. Clone Graph](https://leetcode.com/problems/clone-graph/), 14/Jul/24
Topics: #HashTable , #DFS , #BFS , #Graph
could solve it 1h without topics and any hint!, but I just took 17mi only to understand the question!

We don't have any pointer. We just know only 1st node and the neighbors of 1st node. So for all node, def we have to make each copy of node _in a way like a chain_!
So I just started to write `for` loop for 1st(given) node. And after finish that, I thought I need _recursion_ for all of neighbors! And to prevent eternal call, I wanna check to whether `i`th node is already made or not. So I want to choose `HashMap`.  **so we will travel all around our adjcant using DFS not BFS** And ta dah~ 

```java
/*
// Definition for a Node.
class Node {
    public int val;
    public List<Node> neighbors;
    public Node() {
        val = 0;
        neighbors = new ArrayList<Node>();
    }
    public Node(int _val) {
        val = _val;
        neighbors = new ArrayList<Node>();
    }
    public Node(int _val, ArrayList<Node> _neighbors) {
        val = _val;
        neighbors = _neighbors;
    }
}
*/

class Solution {
	    public Node cloneGraph(Node node) {
		// declaring map, to check whwther we have a copy of node or not and also to store copy
        HashMap<Integer, Node> hasNode = new HashMap<>();
        if (node == null) return null;
        return deepcopy(node, 1, hasNode);
    }

    private Node deepcopy(Node original, int val, HashMap<Integer, Node> hasNode) {
        Node deepcopy = new Node(val, new ArrayList<Node>());
        hasNode.put(val, deepcopy);

        for (int i = 0; i < original.neighbors.size(); i++) {
            Node adj = original.neighbors.get(i);
            Node copy;
            if (!hasNode.containsKey(adj.val)) copy = deepcopy(adj, adj.val, hasNode);
            else copy = hasNode.get(adj.val); 
            deepcopy.neighbors.add(copy);
        }

        return deepcopy;
    }
}
```

more compact DFSst code(using base line!)
```java
class Solution {
    public HashMap<Integer, Node> map = new HashMap<>();
    
    public Node cloneGraph(Node node) {
        return clone(node);
    }
    
    public Node clone(Node node) {
        if (node == null) return null;
        
        if (map.containsKey(node.val)) 
            return map.get(node.val);
        
        Node newNode = new Node(node.val, new ArrayList<Node>());
        map.put(newNode.val, newNode);
        for (Node neighbor : node.neighbors) 
            newNode.neighbors.add(clone(neighbor));
        return newNode;
    }
}
```

can be solved by _BFS_ using queue
It is similar with DFS but in different way. Bc in _DFS_ way, The _1st node is complete at last_ after other nodes was made. But in _BFS_, _The 1st node is complete at first_ than other nodes. While first node was being made, the neighbors node has just value and empty List. So _we have to store `original node` and `copy node` both and connect them._.
```java
    public Node cloneGraph(Node node) {
        // <original, copy>
        HashMap<Node, Node> hasNode = new HashMap<>();
        if (node == null) return null;
        Node clone = new Node(1, new ArrayList<>());
        // input only original nodes in queue.
        ArrayDeque<Node> q = new ArrayDeque<>();
        hasNode.put(node, clone);
        q.add(node);
        
        while(!q.isEmpty()) {
            // for each original node
            Node original = q.removeFirst();
            for (Node adj : original.neighbors) {
                if (!hasNode.containsKey(adj)) {
                    hasNode.put(adj, new Node(adj.val, new ArrayList<>()));
                    q.add(adj);
                }    
                hasNode.get(original).neighbors.add(hasNode.get(adj));
            }
        }
        return clone;
    }
```

9. [399. Evaluate Division](https://leetcode.com/problems/evaluate-division/), 15/July/24
Topics : #Array, #String, #DFS, #BFS, #UnionFind , #Graph , #ShortestPath
could solve it 2h using hint... OMG!!!!!! 
The Idea was simple. _We must recognize this problem as a graph problem._
>
<- reciprocal(역수)
-> multiple
a -> b -> c -> d ^9ac034

And graph is like
a : (b, 2)
b : (a, 1/2), (c, 3)
c : (b, 1/3)

We simply find a path using DFS from node `a` to node `c` and multiply the weights of edges passed, i.e. `2 * 3 = 6`.

So for that I have to use `HashMap`
But after coming up with this idea, I took two hours to implement `graph` and `find()`!!!!

```java
class Solution {
    public double[] calcEquation(List<List<String>> equations, double[] values, List<List<String>> queries) {
        // Store (src, weight)
        HashMap<String, HashMap<String, Double>> graph = new HashMap<>();
        double[] ans = new double[queries.size()];        

        // make a weighted graph.
        for(int i = 0; i < values.length; i ++){
            String src = equations.get(i).get(0);
            String dest = equations.get(i).get(1);
            HashMap<String, Double> adjs;
            // source -> destination
            if (!graph.containsKey(src)) {
                adjs = new HashMap<>();
                graph.put(src, adjs);
            }
            adjs = graph.get(src);
            adjs.put(dest, values[i]);
            // destination -> source is reciprocal 
            if (!graph.containsKey(dest)) {
                adjs = new HashMap<>();
                graph.put(dest, adjs);
            }
            adjs = graph.get(dest);
            adjs.put(src, 1.0 / values[i]);
        }

        for (int i = 0; i < queries.size(); i++) {
            HashSet<String> visited = new HashSet<String>();
            visited.add(queries.get(i).get(0));
            ans[i] = find(queries.get(i).get(0), queries.get(i).get(1), visited, graph);
        }
        return ans; 
    }

    private double find(String src, String dest, HashSet<String> visited,HashMap<String, HashMap<String, Double>> graph) {
        
        if (!graph.containsKey(src)) return -1.0;
        if (src.equals(dest) == true) return 1.0;
        if (graph.get(src).containsKey(dest)) return graph.get(src).get(dest);
        
        for (Map.Entry<String, Double> adj : graph.get(src).entrySet()) {
            if (visited.contains(adj.getKey())) continue;
            visited.add(adj.getKey());    
            double val = find(adj.getKey(), dest, visited, graph);
            visited.remove(adj.getKey());
            // Oh How Can I think this case? haha
            if (val != -1.0) return adj.getValue() * val;
        }
        return -1.0;
    }
}
```

The `visited.add` code can be just add before `for`loop start  in `find()`. And _don't need to remove_! __BECAUSE IF WE SEARCH A CERTAIN NODE(NEIGHBOUR) WE DON'T NEED TO SEARCH AGAIN! IT IS NOT PERMUTAION OR COMBINATION PROBLEM!!!!!!__
Think about, a - > b -> c.
1) a -> c
2) a -> b -> c
Two weight is SAME. So we if we already visit `c` node and don't find destination. we don't have to visit `b->c`! __It is not for visiting all possible route.__ Oh It was so confused to me...

And Just make `buildGraph()` and Use `putIfAbsent

And acually `[a, a]` case also don't have to add exceptional code bc `a - something - a` must exist. 

This is other's solution.
```java
 public double[] calcEquation(String[][] equations, double[] values, String[][] queries) {
        
        /* Build graph. */
        Map<String, Map<String, Double>> graph = buildGraph(equations, values);
        double[] result = new double[queries.length];
        
        for (int i = 0; i < queries.length; i++) {
            result[i] = getPathWeight(queries[i][0], queries[i][1], new HashSet<>(), graph);
        }  
        
        return result;
    }
    
    private double getPathWeight(String start, String end, Set<String> visited, Map<String, Map<String, Double>> graph) {
        
        /* Rejection case. */
        if (!graph.containsKey(start)) 
            return -1.0;
        
        /* Accepting case. */
        if (graph.get(start).containsKey(end))
            return graph.get(start).get(end);
        
        visited.add(start);
        for (Map.Entry<String, Double> neighbour : graph.get(start).entrySet()) {
            if (!visited.contains(neighbour.getKey())) {
                double productWeight = getPathWeight(neighbour.getKey(), end, visited, graph);
                if (productWeight != -1.0)
                    return neighbour.getValue() * productWeight;
            }
        }
        
        return -1.0;
    }
private Map<String, Map<String, Double>> buildGraph(String[][] equations, double[] values) {
        Map<String, Map<String, Double>> graph = new HashMap<>();
        String u, v;
        
        for (int i = 0; i < equations.length; i++) {
            u = equations[i][0];
            v = equations[i][1];
            graph.putIfAbsent(u, new HashMap<>());
            graph.get(u).put(v, values[i]);
            graph.putIfAbsent(v, new HashMap<>());
            graph.get(v).put(u, 1 / values[i]);
        }
        
        return graph;
    }
```

### It can be also solved by _BFS_. But in Java, we have to make _Pair class_...

### Wow... Floyd Warshall method... 
This method is __pre-caching all the possible ways__. But _DFS and BFS_ methods is ad-hoc for _each queries_. So If queries is so much big, this way is better than DFS.

```python
def calcEquation(self, equations, values, queries):
    quot = collections.defaultdict(dict)
    for (num, den), val in zip(equations, values):
        quot[num][num] = quot[den][den] = 1.0
        quot[num][den] = val
        quot[den][num] = 1 / val
    for k in quot:
        for i in quot[k]:
            for j in quot[k]:
                quot[i][j] = quot[i][k] * quot[k][j]
    return [quot[num].get(den, -1.0) for num, den in queries]
```

### By Union-Find
skip... 

10. [207. Course Schedule](https://leetcode.com/problems/course-schedule/), 15/Jul/24
Topics: #DFS , #BFS , #Graph , #TopologicalSort
could solve it 57mi, But So slow, and memory consume is so bad.
Bc I don't know what is _Topological Sort_!
By hints, this problem is typical _Topological Sort_ problem.

union-find? nono...
[a, b], [a, c]
if above case, who is parent of a? b? or c?

HashMap<Integer, HaseSet>
0 -> prerequisites.
1 -> prerequisites.
3 -> prerequisites.
(reverse also. just add Integer, not hashSet)
prerequisites. ->   
prerequisites. ->

after make graph.
for (graph) {
    if (a class can be taken?) total++;
}

My first idea is every lecture, Think about _Is it possible or not_?
So if a cycle is founded in a certain lecture, we can return `false`. (Actually my code did `total--`. But it can return just `false` if one cycle is founded)

we have to think about this case also.
>
0 -> 2
1 -> 0
2 -> 1


```java
class Solution {
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        // <course, set(prerequisites)>
        HashMap<Integer, HashSet<Integer>> graph = new HashMap<>();
        int total = numCourses;

        // build a directed graph.
        for (int[] course : prerequisites) {
            int lecture = course[0];
            int pre = course[1];

            graph.putIfAbsent(lecture, new HashSet<>());
            graph.get(lecture).add(pre);
            graph.putIfAbsent(pre, new HashSet<>());
        }

        for (int course : graph.keySet()) {
            if (isPossible(course, course, new HashSet<>(), graph) == false) total--;
        }
        return (total == numCourses) ? true : false; 
    }

    private boolean isPossible(int src, int target, HashSet<Integer> visited, HashMap<Integer, HashSet<Integer>> graph) {
        if (graph.get(src).contains(target)) return false;

        visited.add(src);
        for (int pre : graph.get(src)) {
            if (visited.contains(pre)) continue;
            if (isPossible(pre, target, visited, graph) == false) return false;
        }

        return true;
    }
}
```

### Using BFS Topological Sort (Kahn's algorithm)
[[2024-06-27-Matrix&Graph#^42192e| Kanh's algorithm]]

`ArrayList[] graph = new ArrayList[numCourses];`
_Oh it means make a array having  ArrayList as elements._ It is more good than `ArrayList<ArrayList<Integer>>`
```java
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        ArrayDeque<Integer> q = new ArrayDeque<>();
        int[] inDegree = new int[numCourses];
        Arrays.fill(inDegree, 0);
        ArrayList<ArrayList<Integer>> graph = new ArrayList<>();

        for (int i = 0; i < numCourses; i++) graph.add(new ArrayList<>());
        // build a directed graph
        for (int[] course : prerequisites) {
            int lecture = course[0];
            int pre = course[1];
            graph.get(pre).add(lecture);
        } 

        // count incoming degree of all vertexes.
        for (ArrayList<Integer> pre : graph) {
            for (int lecture : pre) {
                inDegree[lecture] += 1; 
            }
        }
        // Add all lecture if its inDegree is `0` to queue.
        for (int i = 0; i < numCourses; i++) {
            if (inDegree[i] == 0) q.addLast(i);
        }

        // find a cycle!
        int cnt = 0;
        while (!q.isEmpty()) {
            int pre = q.removeFirst();
            cnt++;

            for (int post : graph.get(pre)) {
                inDegree[post] -= 1;
                if (inDegree[post] == 0) q.addLast(post);
            }
        }
        return cnt == numCourses;
    }
```

### Using DFS / _Detect Cycle in Directed Graph Algorithm_
Use _white set (unvisited) , gray set (visiting, same recursion call), black set(visited)_
If _all the vertices_ of white set have been moved to _black set_, it means that there is no cycle in graph.
in below code, We don't use _gray set_, only use a _visited_(`boolean array sized n`). So if visited[course] is true, it means that course in _gray_ set! And if _we met that course in same recursion call(visited[course] is true)_. it means a cycle!
```java
        public boolean canFinish(int numCourses, int[][] prerequisites) {
            ArrayList[] graph = new ArrayList[numCourses];
            for(int i=0;i<numCourses;i++)
                graph[i] = new ArrayList();
                
            boolean[] visited = new boolean[numCourses];
            for(int i=0; i<prerequisites.length;i++){
                graph[prerequisites[i][1]].add(prerequisites[i][0]);
            }

            for(int i=0; i<numCourses; i++){
                if(!dfs(graph,visited,i))
                    return false;
            }
            return true;
        }

        private boolean dfs(ArrayList[] graph, boolean[] visited, int course){
            if(visited[course])
                return false;
            else
                visited[course] = true;;

            for(int i=0; i<graph[course].size();i++){
                if(!dfs(graph,visited,(int)graph[course].get(i)))
                    return false;
            }
            visited[course] = false;
            return true;
        }
```

10. [210. Course Schedule II](https://leetcode.com/problems/course-schedule-ii/), 16/Jul/24
Topics: #DFS , #BFS , #Graph , #TopologicalSort
could solve it bc 9. is same! But I struggle to fix error!
_Note that `ArrayList<Integer>[] graph` not `ArrayList[] graph`_ Because For __auto-converting__ the `Integer` to the `int`!. 
> Don't use generic type! Use specific type.

If you want to convert The `Deque<Integer> or LinkedList<Integer>` to `int[]`, you must convert that to `new Integer[]` first.  
I could solve it using BFS topological sort(Kahn's algorithm). But for practice, I just solve DFS with white, gray, black set!

The main Idea is _while recursion, if meet same vertex(visiting it means cycle! _
>
gray : visiting[i].
white : visited[i] is false;
black : visited[i] is true;

Oh... I didn't need to use `addFirst()`! just use _order_ as a _stack_. and use just `add` and `pop()` for order!
```java
    int[] orderArray = new int[adjs.size()];
    for (int i = 0; !order.isEmpty(); i++) orderArray[i] = order.pop();
```

```java
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        ArrayList<Integer>[] graph = new ArrayList[numCourses];
        ArrayDeque<Integer> order = new ArrayDeque<>();
        Boolean[] visited = new Boolean[numCourses];
        Boolean[] visiting = new Boolean[numCourses];
        Arrays.fill(visited, false);
        Arrays.fill(visiting, false);
        // build a directed graph
        for (int i = 0; i < numCourses; i++) graph[i] = new ArrayList<>();
        for (int[] course : prerequisites) {
            int lecture = course[0];
            int pre = course[1];
            graph[pre].add(lecture);
        }
        
        for (int i = 0; i < numCourses; i++) {
            if (visited[i] == false) {
                if(!topSort(order, visited, visiting, i, graph)) return new int[0];
            }
        }
        int[] ans = new int[numCourses];
        Integer[] convert = order.toArray(new Integer[0]);
        for (int i = 0; i < numCourses; i++) ans[i] = convert[i];
        return ans;
    }
    
    private boolean topSort(ArrayDeque<Integer> order, Boolean[] visited, Boolean[] visiting, int course, ArrayList<Integer>[] graph) {
        if (visiting[course]) return false;
        visiting[course] = true;

        for (int post : graph[course]) {            
            if (visited[post] == true) continue;
            if (!topSort(order, visited, visiting, post, graph)) return false;
        }
        order.addFirst(course);
        visited[course] = true;
        return true;
    }
```

I forgot reset the `visiting`. but I works! Why? because if there is no cycle in graph and once vertex is _visited(visiting) in recursion_, then it must be _visited_! 

11. [909. Snakes and Ladders](https://leetcode.com/problems/snakes-and-ladders/), 17/Jul/24
Topics : #Array , #BFS , #Matrix 
could solve it 1h 50mi... I took almost time to convert index of 2D(board[row][col]) to index of 1D(lable).

In BFS, If we want to find shortest path, at the first time we reach target, That must be minimum step(route).
Oh and if there is a cycle... it can't reach target!

board[0] 7 8 9 // left to right
board[1] 6 5 4 // right to left
board[2] 1 2 3 // left to right

I used `ArrayDeque<int[]> q`, for Pair() position, moves), but others use just `q.size()` for maintain `moves`.
And the Most cool method is use `another new ArrayDeque`! I will show you next to my code!

```java
    public int snakesAndLadders(int[][] board) {
        ArrayDeque<int[]> q = new ArrayDeque<>();
        int n = board.length;
        int[] lable = new int[n * n + 1] ;
        // To prevent a eternal loop. (find a cycle)
        boolean[] visited = new boolean[n * n + 1];
        Arrays.fill(visited, false);
        // index = label, value = board[r][c]
        // convert 2D to 1D array
        int row = 0;
        for (int i = n - 1; i >= 0; i--) {
            int reverse = n - 1;
            for (int j = 0; j < n; j++) {
                if (row % 2 == 0) {
                    lable[n*n - (i*n+reverse)] = board[i][j];
                    reverse--;
                }
                else {
                    lable[n*n - (i*n+j)] = board[i][j];
                } 
            }
            row++;
        }
        // [curr, moves]
        q.addLast(new int[] {1, 0});
        visited[1] = true;
        while(!q.isEmpty()) {
            int[] move = q.removeFirst();
            int curr = move[0];
            int moves = move[1];

            if (curr == n * n) return moves;
            for (int i = curr + 1; i <= Math.min(curr + 6, n * n); i++) {
                if (lable[i] != -1) {
                    if (visited[lable[i]]) continue;
                    q.addLast(new int[] {lable[i], moves + 1});
                    visited[lable[i]] = true;
                }
                else {
                    if (visited[i]) continue;
                    q.addLast(new int[] {i, moves + 1});
                    visited[i] = true;
                }
            } 
        }
        return -1;
    }
```

And I tried to convert index of 2D to index of 1D.
But most of people just convert 1D(lable) to index of 2D like this!

The Main idea to convert 1D to 2D is _row represented by `/` and col represented by `%`_
And the Main idea to convert 2D to 1D is _row represented by `*` and col represented by `+`_
And if need to alternating(Reverse), just start _`n - (something)`_
Matrix is about representing indices effectively.
And the `nextQ` way is so cool!

```java
    public int snakesAndLadders(int[][] board) {
        // To store current lable(position)
        ArrayDeque<Integer> q = new ArrayDeque<>();
        int n = board.length;
        // To prevent a eternal loop. (find a cycle)
        HashSet<Integer> visited = new HashSet<>();
        
        int moves = 0;
        q.addLast(1);
        visited.add(1);
        while(!q.isEmpty()) {
            ArrayDeque<Integer> nextQ = new ArrayDeque<>();
            while(!q.isEmpty()) {
                int curr = q.removeFirst();
                if (curr == n * n) return moves;
                
                for (int i = curr + 1; i <= Math.min(curr + 6, n*n); i++) {
                    int next = getAt(board, i);
                    if (next == -1) next = i;
                    if(visited.contains(next)) continue;
                    nextQ.addLast(next);
                    visited.add(next);
                }
            }
            moves++;
            q = nextQ;
        }
        return -1;
    }

    private int getAt(int[][] board, int curr) {
        int n = board.length;
        curr = curr - 1;
        int row = (n - 1) - curr / n;
        int col = (curr / n % 2 == 0) ? curr % n : (n - 1) - curr % n;
        return board[row][col];
    }
```

12. [433. Minimum Genetic Mutation](https://leetcode.com/problems/minimum-genetic-mutation/), 17/Jul/24
Topics : #HashTable , #String , #BFS 
could solve it 32mi bc I already know this is about BFS;;

The main idea is just add _all possible next mutation_ to `queue`. and if I visited(used it), then remove that mutation from `possible` set.
```java
class Solution {
    public int minMutation(String startGene, String endGene, String[] bank) {
        if (startGene.equals(endGene)) return -1;
        HashSet<String> possible = new HashSet<>();
        for (String gene : bank) possible.add(gene);
        ArrayDeque<String> q = new ArrayDeque<>();

        q.addLast(startGene);
        possible.remove(startGene);
        int mutate = 0;
        while(!q.isEmpty()) {
            for (int size = q.size(); size > 0; size--) {
                String curr = q.removeFirst();
                // while For loop using `possible`, cannot remove `possible` concurrently. 
                HashSet<String> temp = new HashSet<>(possible);
                for (String gene : temp) {
                    if (isMutation(curr, gene)) {
                        if (gene.equals(endGene)) return mutate + 1;
                        q.addLast(gene);
                        possible.remove(gene);
                    }
                }
            }
            mutate++;
        }
        return -1; 
    }

    private boolean isMutation(String origin, String change) {
        int cnt = 0;
        char[] ori = origin.toCharArray();
        char[] to = change.toCharArray();
        for (int i = 0; i < ori.length; i++) {
            if (ori[i] != to[i]) cnt++;
        }
        return (cnt == 1);
    }
}
```

Actually My code doesn't use the constraints that consist of only the characters `['A', 'C', 'G', 'T']`. Instead of that I use `bank` and mutation is different with origin only one single character.
SO popular another way is _to consider every possible change of origin._
> Generate 4 x 8 mutations (possible characters x each place)
And check if the bank contains that mutation.
So if bank contains that, just remove that from bank set(can use another visited set and add that to visited).

```java
    public int minMutation(String start, String end, String[] bank) {
        if(start.equals(end)) return 0;
        
        Set<String> bankSet = new HashSet<>();
        for(String b: bank) bankSet.add(b);
        
        char[] charSet = new char[]{'A', 'C', 'G', 'T'};
        
        int level = 0;
        Set<String> visited = new HashSet<>();
        Queue<String> queue = new LinkedList<>();
        queue.offer(start);
        visited.add(start);
        
        while(!queue.isEmpty()) {
            int size = queue.size();
            while(size-- > 0) {
                String curr = queue.poll();
                if(curr.equals(end)) return level;
                
                char[] currArray = curr.toCharArray();
                for(int i = 0; i < currArray.length; i++) {
                    char old = currArray[i];
                    for(char c: charSet) {
                        currArray[i] = c;
                        String next = new String(currArray);
                        if(!visited.contains(next) && bankSet.contains(next)) {
                            visited.add(next);
                            queue.offer(next);
                        }
                    }
                    currArray[i] = old;
                }
            }
            level++;
        }
        return -1;
    }

```