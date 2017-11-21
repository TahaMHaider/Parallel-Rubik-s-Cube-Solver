[Project proposal](index.md) / [Project checkpoint](project_checkpoint.md) / [Tasks](tasks.md)

## TITLE

“Project checkpoint” 

Diego Garza Rodriguez

Nihar Pol 

### SUMMARY

Given an arbitrary scrambled Rubik’s cube, we are going to implement a program that determines and finds the sequence with the fewest number of moves required to solve it. We plan to be using shared-address space model on the Xeon Phis. 

### BACKGROUND

The Rubik’s cube is a physical puzzle that humans often find difficult to solve. There are various strategies humans use for solving a Rubik’s cube. For example, one way is to solve it layer by layer. However, in this problem we are not just interested in finding a sequence of moves that solves the Rubik’s cube; we are interested in finding the sequence with the fewest number of moves. If there are multiple such sequences, we want to find one of them.

The Rubik’s cube is a difficult puzzle because the decision tree for it rapidly increases in breadth. If we define a move to be one quarter turn in either clockwise or counter-clockwise rotation, after one turn the cube could be in any of 2*8 = 16 possible states. After, say, 20 turns, it could be in any of at most 16^20 = 1208925819614629174706176 states, some of which may be identical. It is known and has been proven that for this definition of a turn, the maximum number of moves required to solve a 3x3 cube is 26 moves. To solve a 2x2 cube, the corresponding number is 14 moves. This is still 16^14 = 72057594037927936 states, some of which may be identical. 

The goal of our project is to reduce this search space as much as possible. One might think that because testing each individual sequence of moves in the decision tree is independent of all the others, it will be an easy problem to parallelize. However, this is not enough to speed up the search. What we really need to do is to develop a system to intelligently search the tree. We don’t want to move to exploring the children of a particular node unless we are quite confident that those children could represent sequences of moves that are potentially optimal. 

### THE CHALLENGE

When we introduce the issue of making a decision whether or not to go down a particular node in the decision tree, the problem becomes more interesting. It is not easy to look at a scrambled Rubik’s cube by itself and make this decision. To make this decision, we need to examine the sequence of moves that were made to reach this state, and determine whether that sequence has any redundant or inefficient subsequences inside it. For example, if we see that there are two successive moves that are the exact opposite of each other (one quarter turn clockwise on top face followed by one quarter turn counter-clockwise on top face), we know that this sequence is two steps too long and we can kill this node, knowing that some other sequence that is still alive will be covering this case minus those two unnecessary steps. We can also intelligently store information about moves that we have already seen. If we know, for example, that there is a certain sequence of moves that gets us from a current state to another state A in 6 moves, but that somewhere in our sequence of moves there is a subsequence of 10 moves that goes from a state C to a state B where B:C::A:Solved, then we know that this sequence is 4 moves too long and therefore this node can be killed. It is in our best interest to kill suboptimal move sequences.  Every time we decide we are going to keep a certain sequence of moves as potentially optimal, we must advance to the children of that node (the following moves that can be made) and repeat the analysis for all those children. 

We have many ideas for how we can decide to stop considering a node’s children because the node’s sequence of moves is suboptimal: 

- Remove redundant moves in a sequence (e.g. turn counterclockwise, followed by immediate clockwise). 
- When we first start to go down the decision tree, we are computing changes of cube states for very small numbers of moves (e.g. 4, 6, etc.). Of these cube state changes, the ones that accomplish their cube state change in the fewest moves are valuable to us. We can store them in a database and reference them later on. Using the above technique, we can use short algorithms to detect inefficiencies in longer sequences of moves. 
- We can add to this database as the algorithm progresses. As more optimal state changes are found, we can store them into the database so that they can be referenced by other parallel processes/threads. This effectively creates a complex data structure that will be essential for speeding up the algorithm yet will require intricate management for multi-threading. Also, although there are so different possible combinations of sequences, there is nowhere near as much memory on the computer available. This is a constraint that we will have to manage and work with. 
- It is not just that we are going down a decision tree that spawns into multiple different states. The Rubik’s Cube is unique in that it can actually be represented as a mathematical group, which means that regardless of the order or number of turns you apply to the cube, it will will remain in a certain set of permutations from which it cannot leave. This means that actually describing the ‘state’ of a Rubik’s cube is relative. To a human, we see a solved cube as having a ‘solved’ state, but these are relative. For example, if we take a solved cube and apply a sequence S of moves to it, it will go to state A. If we take a different, scrambled cube and apply S to it, it will go to a state that will be different than A in an absolute sense, but identical to A relative to the previous scrambled state. We can use the mathematical properties of a group to analyze the sequences of operations in an arbitrary sequence and make decisions about where it is optimal or likely to lead to the solution quickly. 
- Because of this property, a lot of the optimal sequences we find can be adaptable in other ways. For example, we may notice that a certain permutation is equivalent to another permutation if a couple colors are swapped. A more complicated example is: suppose we found a 7-length optimal sequence of moves in our database that looks similar to a 15-length subsequence of moves that is part of the sequence of moves for a node we are examining. If we noticed that changing, adding, or removing a few moves from the 7-length sequence made it functionally equivalent to the 15-length sequence (without exceeding 15), we have determined that this sequence is not optimal.
  
### RESOURCES

We have found a paper, “Solving the Rubik’s Cube with Parallel Processing,” written by Dr. Aslesha Nargolkar. This paper attempts to identify which specific algorithms are the most helpful when solving this problem. Although we will reference this paper throughout our project, we do want to work on the problem ourselves and optimize it for the hardware we are given with the ideas we have in mind. 

We want to program this from scratch. We want to use efficient C or C++ language and we don’t see the need to spend time learning how an existing code base works. We have already stated that input and output to and from the program will be fairly simple. Since we will be representing the data structures we need with structs or primitive types, we think we can start from scratch. We will of course, use scripts from Assignment III and IV for just the component of sending it to the Xeon Phi. 

### GOALS AND DELIVERABLES
#### PLAN TO ACHIEVE 

Because the nature of this problem is that the total work quickly becomes exponential in complexity, we hope to gain considerable speedup from parallelisation and optimization. 	A ballpark number we are putting out there is that we should obtain 20x speedup compared to a naive sequential implementation.

#### HOPE TO ACHIEVE

We hope to achieve a speedup much more than this however. While we don’t have a specific number in mind, we want to try to make our program fast enough that it terminates on difficult inputs in a matter of seconds or minutes. We suspect that the naive sequential implementation will take considerable time, at least hours, to terminate.

### PROBABLY SHOULD AT LEAST ACHIEVE

In case progress goes more slowly, we think we should be able to at least implement a fast solver for a 2x2 Rubik’s cube. The number of states for this is considerably less. However, this puzzle is still not easy (even humans have difficulty solving it) as it still has a rather large search space.

### DELIVERABLES

We want to be able to solve any random permutation of a Rubik’s cube, including the hardest ones (which require 26 quarter turns) efficiently. We will show speedup graphs as well as duration graphs relating the serial implementation performance to the parallel implementation performance. We also want to discuss some techniques we used to optimize the performance of our algorithm. Possible Demo during presentation: We could have an audience member scramble a cube, have the program find the solution, and then we could solve it. We are not sure how long this would take, however.

### PLATFORM CHOICE

We plan on using the Intel Xeon Phis with either the Shared Address space model or Message passing model because we will need real-time communication to occur between processors. We will need to manage multiple threads or multiple processes reading and modifying database(s) containing valuable information about optimal subsequences.

### SCHEDULE 

Monday, November 20th

Infrastructure code should be in place. This includes input, output, representing cube states, etc. in data structures. 
Serial implementation should be done and functionally correct.
Working parallel version should be in place with basic optimizations. This includes checking for redundancy in a sequence. It also includes keeping track of already-found sequences and directly mapping them to sequences that each node represents. Some basic pruning of the search space should be underway. 

Monday, November 27th

More search space pruning should be happening. The simplest optimizations described in CHALLENGES should be in place. 
Start working on more advanced optimization. Start implementing smarter ways of determining whether a sequence could be optimal or not. 
Start fine tuning the database that is limited by the amount of available memory. Experiment to figure out the most efficient way to manage access to database(s). Experiment to determine the most valuable sequences or other information that should be stored in them. 

Monday, December 4th

Keep developing advanced optimization methods. All the optimization methods described in CHALLENGES should be underway.
We should be experimenting with additional optimizations we may not have thought about before. 

Tuesday, December 12th

Fine tune optimization and database size/usage. We should be using all the memory available to us when the program is running. 
Most advanced pruning that we can think of should be implemented in the program. 
Speedup specified in the PLAN TO ACHIEVE should be attained.
