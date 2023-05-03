Download Link: https://assignmentchef.com/product/solved-cs210-project-4-8-puzzle
<br>
<table width="719">

 <tbody>

  <tr>

   <td width="719">This document only contains the description of the project and the project problems. For the programming exercises on concepts needed for the project, please refer to the <a href="https://www.swamiiyer.net/cs210/project4_checklist.pdf">project checklist</a>  <a href="https://www.swamiiyer.net/cs210/project4_checklist.pdf">.</a></td>

  </tr>

 </tbody>

</table>

<strong>The Problem </strong>The purpose of this assignment is to write a program to solve the 8-puzzle problem (and its natural generalizations) using the <em>A<sup>? </sup></em>search algorithm. The <a href="https://en.wikipedia.org/wiki/Fifteen_puzzle">8-puzzle</a> problem is a puzzle invented and popularized by Noyes Palmer Chapman in the 1870s. It is played on a 3-by-3 grid with 8 square blocks labeled 1 through 8 and a blank square. Your goal is to rearrange the blocks so that they are in order. You are permitted to slide blocks horizontally or vertically into the blank square. The following shows a sequence of legal moves from an initial board position (left) to the goal position (right).

1 3                     1            3                       1 2 3                           1 2 3                           1 2 3

4 2 5                  =&gt;            4 2 5            =&gt;       4            5        =&gt;         4 5                =&gt;           4 5 6

7 8 6                                  7 8 6                           7 8 6                           7 8 6                         7 8

initial                                                                                                                                      goal

<strong>Best-First Search </strong>Now, we describe a solution to the problem that illustrates a general artificial intelligence methodology known as the <em>A<sup>? </sup></em>search algorithm. We define a <em>search node </em>of the game to be a board, the number of moves made to reach the board, and the previous search node. First, insert the initial search node (the initial board, 0 moves, and a null previous search node) into a priority queue. Then, delete from the priority queue the search node with the minimum priority, and insert onto the priority queue all neighboring search nodes (those that can be reached in one move from the dequeued search node). Repeat this procedure until the search node dequeued corresponds to a goal board. The success of this approach hinges on the choice of <em>priority function </em>for a search node. We consider two priority functions:

<ul>

 <li><em>Hamming priority function. </em>The number of tiles in the wrong position, plus the number of moves made so far to get to the search node. Intuitively, a search node with a small number of tiles in the wrong position is close to the goal, and we prefer a search node that have been reached using a small number of moves.</li>

 <li><em>Manhattan priority function. </em>The sum of the Manhattan distances (sum of the vertical and horizontal distance) from the tiles to their goal positions, plus the number of moves made so far to get to the search node.</li>

</ul>

For example, the Hamming and Manhattan priorities of the initial search node below are 5 and 10, respectively.

8 1 3                                  1 2 3                              1 2 3 4 5 6 7 8                                      1 2 3 4 5 6 7 8

4               2                       4 5 6                            ———————-                                    ———————-

7 6 5                                7 8                                   1 1 0 0 1 1 0 1                                      1 2 0 0 2 2 0 3

initial                                  goal                           Hamming = 5 + 0                                 Manhattan = 10 + 0

We make a key observation: To solve the puzzle from a given search node on the priority queue, the total number of moves we need to make (including those already made) is at least its priority, using either the Hamming or Manhattan priority function. (For Hamming priority, this is true because each tile that is out of place must move at least once to reach its goal position. For Manhattan priority, this is true because each tile must move its Manhattan distance from its goal position. Note that we do not count the blank square when computing the Hamming or Manhattan priorities.) Consequently, when the goal board is dequeued, we have discovered not only a sequence of moves from the initial board to the goal board, but one that makes the fewest number of moves. (Challenge for the mathematically inclined: prove this fact.)

<strong>A Critical Optimization </strong>Best-first search has one annoying feature: search nodes corresponding to the same board are enqueued on the priority queue many times. To reduce unnecessary exploration of useless search nodes, when considering the neighbors of a search node, don’t enqueue a neighbor if its board is the same as the board of the previous search node.

8 1 3                               8 1 3                       8 1                       8 1 3                    8 1 3

4               2                  4 2                             4 2 3              4            2                4 2 5

7 6 5                               7 6 5                         7 6 5                  7 6 5                  7 6

previous                     search node                  neighbor            neighbor             neighbor

(disallow)

<strong>A Second Optimization </strong>To avoid recomputing the Manhattan distance of a board (or, alternatively, the Manhattan priority of a solver node) from scratch each time during various priority queue operations, compute it at most once per object; save its value in an instance variable; and return the saved value as needed. This caching technique is broadly applicable: consider using it in any situation where you are recomputing the same quantity many times and for which computing that quantity is a bottleneck operation.

<strong>Game Tree </strong>One way to view the computation is as a game tree, where each search node is a node in the game tree and the children of a node correspond to its neighboring search nodes. The root of the game tree is the initial search node; the internal nodes have already been processed; the leaf nodes are maintained in a priority queue; at each step, the A* algorithm removes the node with the smallest priority from the priority queue and processes it (by adding its children to both the game tree and the priority queue).

<strong>Detecting Unsolvable Puzzles </strong>Not all initial boards can lead to the goal board by a sequence of legal moves, including the two below:

1 2 3                                      1 2 3 4

4 5 6                                      5 6 7 8

8 7                                     9 10 11 12

13 15 14

unsolvable

unsolvable

To detect such situations, use the fact that boards are divided into two equivalence classes with respect to reachability: those that lead to the goal board; and those that cannot lead to the goal board. Moreover, we can identify in which equivalence class a board belongs without attempting to solve it.

<ul>

 <li><em>Odd board size. </em>Given a board, an <em>inversion </em>is any pair of tiles <em>i </em>and <em>j </em>where <em>i &lt; j </em>but <em>i </em>appears after <em>j </em>when considering the board in row-major order (row 0, followed by row 1, and so forth).</li>

</ul>

1 2 3                                        1 2 3                                        1 2 3                                         1 2 3                                           1 2 3

4 5 6                =&gt;                     4 5 6                =&gt;                4            6            =&gt;                         4 6              =&gt;                        4 6 7

8 7                                         8            7                                    8 5 7                                         8 5 7                                      8 5

1 2 3 4 5 6 8 7                           1 2 3 4 5 6 8 7                           1 2 3 4 6 8 5 7                            1 2 3 4 6 8 5 7                                 1 2 3 4 6 7 8 5

inversions = 1                            inversions = 1                           inversions = 3                           inversions = 3                               inversions = 3

(8-7)                                           (8-7)                          (6-5 8-5 8-7)                             (6-5 8-5 8-7)                                  (6-5 7-5 8-5)

If the board size <em>N </em>is an odd integer, then each legal move changes the number of inversions by an even number. Thus, if a board has an odd number of inversions, then it cannot lead to the goal board by a sequence of legal moves because the goal board has an even number of inversions (zero).

The converse is also true: if a board has an even number of inversions, then it can lead to the goal board by a sequence of legal moves.

1 3                                  1            3                                    1 2 3                                         1 2 3                                           1 2 3

4 2 5                =&gt;                     4 2 5                =&gt;                4            5            =&gt;                   4 5                     =&gt;                        4 5 6

7 8 6                                        7 8 6                                        7 8 6                                         7 8 6                                      7 8

1 3 4 2 5 7 8 6                           1 3 4 2 5 7 8 6                           1 2 3 4 5 7 8 6                            1 2 3 4 5 7 8 6                                 1 2 3 4 5 6 7 8

inversions = 4                            inversions = 4                           inversions = 2                           inversions = 2                               inversions = 0

(3-2 4-2 7-6 8-6)                      (3-2 4-2 7-6 8-6)                                       (7-6 8-6)                                   (7-6 8-6)

<ul>

 <li><em>Even board size. </em>If the board size <em>N </em>is an even integer, then the parity of the number of inversions is not invariant. However, the parity of the number of inversions plus the row of the blank square is invariant: each legal move changes this sum by an even number. If this sum is even, then it cannot lead to the goal board by a sequence of legal moves; if this sum is odd, then it can lead to the goal board by a sequence of legal moves.</li>

</ul>

1 2 3 4                                      1 2 3 4                                      1 2 3 4                                      1 2 3 4                                           1 2 3 4

5 6 8 =&gt; 5 6 8 =&gt; 5 6 7 8 =&gt; 5 6 7 8 =&gt; 5 6 7 8 9 10 7 11 9 10 7 11 9 10 11 9 10 11 9 10 11 12

13 14 15 12                               13 14 15 12                               13 14 15 12                               13 14 15 12                             13 14 15

blank row = 1 blank row = 1 blank row = 2 blank row = 2 blank row = 3 inversions = 6 inversions = 6 inversions = 3 inversions = 3 inversions = 0

————– ————– ————– ————– ————-sum = 7 sum = 7 sum = 5 sum = 5 sum = 3

<strong>Problem 1. </strong>(<em>Board Data Type</em>) Create an immutable data type Board in Board.java with the following API:

<strong>method                                            description</strong>

<table width="720">

 <tbody>

  <tr>

   <td width="176">Board(int[][] tiles)</td>

   <td width="544">construct a board from an <em>N</em>-by-<em>N </em>array of tiles</td>

  </tr>

  <tr>

   <td width="176">int tileAt(int i, int j)</td>

   <td width="544">tile at row <em>i</em>, column <em>j </em>(or 0 if blank)</td>

  </tr>

  <tr>

   <td width="176">int size()</td>

   <td width="544">board size <em>N</em></td>

  </tr>

  <tr>

   <td width="176">int hamming()</td>

   <td width="544">number of tiles out of place</td>

  </tr>

  <tr>

   <td width="176">int manhattan()</td>

   <td width="544">sum of Manhattan distances between tiles and goal</td>

  </tr>

  <tr>

   <td width="176">boolean isGoal()</td>

   <td width="544">is this board the goal board?</td>

  </tr>

  <tr>

   <td width="176">boolean isSolvable()</td>

   <td width="544">is this board solvable?</td>

  </tr>

  <tr>

   <td width="176">boolean equals(Board that)</td>

   <td width="544">does this board equal <em>that</em>?</td>

  </tr>

  <tr>

   <td width="176">Iterable&lt;Board&gt; neighbors()</td>

   <td width="544">all neighboring boards</td>

  </tr>

  <tr>

   <td width="176">String toString()</td>

   <td width="544">string representation of this board (in the output format specified below)</td>

  </tr>

 </tbody>

</table>

<em>Performance requirements. </em>You may assume that the constructor receives an <em>N</em>-by-<em>N </em>array containing the <em>N</em><sup>2 </sup>integers between 0 and <em>N</em><sup>2</sup>−1, where 0 represents the blank square. Your implementation should support all Board methods in time proportional to <em>N</em><sup>2 </sup>(or better) in the worst case, with the exception that isSolvable() may take up to <em>N</em><sup>4 </sup>in the worst case.

<table width="720">

 <tbody>

  <tr>

   <td width="720">$ java Board data/puzzle05.txt55 false</td>

  </tr>

  <tr>

   <td width="720">true30 1 34 2 67 5 834 1 32 0 67 5 834 1 37 2 60 5 8$ java Board data/puzzle4x4-unsolvable.txt1213 false false43 2 0 81 6 4 12 5 10 7 11 9 13 14 1543 2 4 81 6 12 0 5 10 7 11 9 13 14 1543 2 4 81 6 7 12 5 10 0 11 9 13 14 1543 2 4 81 0 6 12 5 10 7 11 9 13 14 15</td>

  </tr>

 </tbody>

</table>

<strong>Problem 2. </strong>(<em>Solver Data Type</em>) Create an immutable data type Solver in Solver.java with the following API:

<strong>method                                          description</strong>

<table width="720">

 <tbody>

  <tr>

   <td width="171">Solver(Board initial)</td>

   <td width="549">find a solution to the initial board (using the <em>A<sup>? </sup></em>algorithm)</td>

  </tr>

  <tr>

   <td width="171">int moves()</td>

   <td width="549">the minimum number of moves to solve initial board</td>

  </tr>

  <tr>

   <td width="171">Iterable&lt;Board&gt; solution()</td>

   <td width="549">sequence of boards in a shortest solution</td>

  </tr>

 </tbody>

</table>

<em>Corner cases. </em>The constructor should throw a java.lang.NullPointerException if the initial board is null and a java.lang.IllegalArgumentException if the initial board is not solvable.

$ java Solver data/puzzle05.txt

Minimum number of moves = 5

3

0 1 3

4 2 6

7 5 8

3

1 0 3

4 2 6

7 5 8

3

1 2 3

4 0 6

7 5 8

3

1 2 3

4 5 6

7 0 8

3

1 2 3

4 5 6

7 8 0

Your program should work correctly for arbitrary <em>N</em>-by-<em>N </em>boards (for any 1 ≤ <em>N </em>≤ 32768), even if it is too slow to solve some of them in a reasonable amount of time.

<strong>Acknowledgements </strong>This project is an adaptation of the 8 Puzzle assignment developed at Princeton University by Robert Sedgewick and Kevin Wayne.