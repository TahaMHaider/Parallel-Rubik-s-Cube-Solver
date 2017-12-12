[Project Proposal](index.md) / [Project Checkpoint](project_checkpoint.md) / [Final Project Report](final_project_report.md) / [Tasks](tasks.md)

## CHECKPOINT

For this checkpoint we are revisiting the expected goals and explaining what we found so far in the process of developing this project. In the project proposal our main goal was to find the optimal Rubik's cube solution within a search tree. 
 

### WORK COMPLETED SO FAR

The most important thing that we developed is knowledge about the problem space and different approaches to solve this problem. We were set back in the first two weeks of the project schedule because we had to revise our initial idea. We ended up revising it completely and taking a whole new direction. Consequently we are a little behind schedule in our project, but we have accomplished some major goals. 

- We used assignment 3 for our code base and heavily modified it to support our project. We have gotten it to work with the Xeon Phis. 
- We have implemented the input/output to our program. It reads in a file with the cube permutation encoded in it (we have made a template for this) and the output is simply the sequence found, which is printed in human-readable format. 
- We have created memory-efficient data structures for storing cube state and performing operations/modifying cube states. In doing so we learned that representing a Rubik’s cube and even the concept of making a move are not as simple as we thought. However, we are almost done implementing the make_move function that modifies the state of a Rubik’s cube in our representation given a move that needs to be made. 
- We have written ~1000 lines of code, most of which is helper functions that help modify cube states or encode/decode data from it. Going forward this makes it simpler to achieve the more complex goals we have in mind. 
Both from our own experience thus far and in research we have done (we examined a couple papers we found on the internet), we have determined a specific set of tasks to achieve one by one. 


### GOALS AND DELIVERABLES

Our goals for the final project poster presentation are still the same. We will show the different graphs comparing the performance and we will show the algorithm in practice, having as an output the solution steps for the given rubik's cube. We may not be able to have an audience member permute the cube arbitrarily and ask for a solution because 1) it takes some time to encode the input file to our program; and 2) our program may not necessarily be able to solve cubes that require close to 26 moves. However we will at least show it come up with a solution for a scrambled cube that won’t take too long to solve. We are not as sure about achieving high exponential speedup however, even though we specified that in the “Hope to achieve".
  
### SUMMARY OF NEXT STEPS

First, we will finish the make_move function and implement a simple serial solution that naively searches the space using the helper functions we wrote. Then, we will create a parallel version of the code, take measurements, and speed up the code we wrote. At the end of the final project, we will have different serial versions of the algorithm and different solutions when we parallelize those, so that we can compare the different results. We want to spend more time on parallelizing the serial code than just writing the serial portion. It will be interesting to see at the end that the best serial code maybe is not the best solution once we have a parallel computer and a different approach makes more sense.

### ISSUES THAT CONCERN US

We fear that no matter how much we optimize our code, the nature of the problem may be exponential and thus we may not be able to solve the hardest scrambled cubes in minutes as we hope. We have yet to measure the code performance on this, but this is an area of concern. There are no remaining unknowns in terms of using the hardware and software resources available to us; we already have the documentation we need for OpenMP. One unknown we have and will have to explore however, is the maximum size ‘pattern database’ we can maintain on the Phis. We hope to answer this through experimentation and measurement. 

### REVISED SCHEDULE

Please see the [Tasks](tasks.md) page for the full list of tasks and see the progress made so far.
