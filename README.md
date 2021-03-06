# Introduction
Consider the following optimization problem. We are given vertices V<sub>1</sub>, ..., V<sub>n</sub> and possible parent sets for each vertex. Each parent set has an associated cost. Let the O be an ordering (a permutation) of the vertices. we say that a parent set of a vertex V<sub>i</sub> is consistent with an ordering O if all of the parents come before the vertex in the ordering. Let mc(V<sub>i</sub>, O) be the minimum cost over all of the parent sets of vertex V<sub>i</sub> that are consistent with ordering O. The task is to find an ordering O that minimizes the total cost of : mc(V<sub>1</sub>, O) + … + mc(V<sub>n</sub>, O).
The problem is NP Complete and so presumably intractable. The local search algorithm provided in this repo attempts to find a good ordering of the variables for each of the data sets.



# How To Run Code:
1. place data files in the same folder as main.cpp
2. compile by typing make in the same folder as main.cpp in terminal
3. to run, type:
  > ./run
4. The program has the option of running a specified amount of time or run a
default of time. It will return the best found answer in the given time frame.
To enter a time limit, type "y" or "Y" and then enter the time limit in minutes.
Otherwise, input anything else and the default time limit is 60 mins.
5. Now you will be asked what data set you would like to use for the local search.
Enter the name (example: "data1.txt").
6. The program will run for the specified time on the data set.
7. Every time it discovers an ordering that is better than the best ordering thus far,
it will print the list out and its associated cost.
8. Finally, when the time limit is up, it will print the best ordering it has discovered.

# Investigation
## Neighborhood Function
### First Approach
At first, I utilized a first improving neighbor to choose which node to visit first. I found that the differences from one node to the next were sometimes very little, I wanted to explore more possible neighboring nodes before moving nodes.
### Second Approach
Randomly generates two indexes from 1 to numVertices and then swaps these elements in the list. I would hash these indexes to avoid selecting them again. If the updated ordering (node) was not found in the tabuList, then it determines if the cost of the node is less than the lowest cost node we’ve seen thus far. After doing numVertices * (numVerticees /2) possible swaps, it returns the best one discovered so far.
### Realizations
I realized that storing hashing pairs of indexes and hashing every visited node in my tabuList was the main memory hog for my program. I was using approximately 0.5 mbps of ram, which just wasn’t scalable. I also found that randomly generating these indexes was not a good strategy because it was easy to randomly generate a pair of indexes that I’ve already considered.
### Final Approach
Consider all the possible neighbors of a given ordering by swapping any two indexes (n^2 possible swappings). I was able to minimize the amount of ram usage by using several C++ optimizations. I was able to reduce memory usage to 0.1 mbps (~300mb per hour of run time).


## Local Search Function
### Initial Approach
The function starts off with a randomly generated list of vertices from the randomStart() function. I used iterative improvement to determine which node to visit next. I didn’t use a tabulist for the general purpose of revisiting nodes that are worst cost, but rather to avoid revisiting nodes all together. Once I visited a node, it became part of the tabu list and was not revisited.
The stopping criteria I used was the number of iterations since the last big improvement. If there was a 5% improvement, then the counter is reset to 0. If there is less than a 5% improvement, then the counter is increased. Once the counter hits 15, the search is terminated and return the best result discovered.
### Final Approach
I started using a traditional tabu list where I would consider the best neighbor node, even if it’s cost was higher. This allowed me to consider other nodes even if I came to a dead end.
## Random Starts
The randomStart() function keeps generating a random ordering of vertices for as long as there is time left. This starting point is then used as a starting point for the local search function. This function keeps track of the best list generated by the local search function.
The random ordering of the vertices is accomplished in O(n) time using the Fisher Yates Shuffle Algorithm (refer to randomize() function).
## Conclusions:
Comparing my second approach and final approach, I noticed a significant time improvement in finding a solution that was low enough. It takes approximately 5 minutes to find a solution that is good for data5.txt as opposed to 34 minutes. These improvements were consistent with multiple runs. However, I can’t really conclude if it was from my improvements or if my initial random ordering was just luckier for both trials.  
![Alt text](table_results.PNG?raw=true )


# Results
Each data set was running for 60 mins. The ordering and cost is provided.
### data1.txt
11 15 8 3 5 2 9 6 4 12 7 10 1 13 14 16 17  
773.486
### data2.txt
1 4 24 22 5 15 16 14 7 17 9 6 11 23 8 10 13 3 21 20 19 12 18 2 25 26  
1577.542000
### data3.txt
10 3 1 20 22 14 25 18 8 12 7 16 4 6 15 23 5 2 13 9 19 11 17 21 24 26 27  
14350.475000
### data4.txt
18 23 6 7 17 24 22 21 20 8 12 13 28 14 2 27 19 1 11 25 5 26 10 9 3 16 4 15  
18827.433
### data5.txt
26 29 28 18 27 3 11 7 13 21 2 9 17 10 6 15 8 14 19 16 4 12 20 23 31 25 5 24 22 30
16610.130
