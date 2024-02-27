# Cellular Automata

Cellular automata are a class of models that can be used to simulate various natural phenomena. They have been applied to model tumor development through three-dimensional tissue mapping and predict cell growth over time. In neuroscience, cellular automata can be used to study the population dynamics of neuron activation. There are many other applications, including in chemistry, biology, ecology, and many other scientific fields.

At their core, cellular automata consist of a spatially ordered set of units called cells. Each cell performs one of the possible actions, or assumes one of the possible states, depending on its immediate environment. Perhaps the most well-known example of cellular automata is Conway's Game of Life.

## Game of Life

Conway's Game of Life is a cellular automaton that consists of a rectangular grid of cells. Each cell can be in one of two states: alive or dead. The game progresses through time iterations, where cells compute their state in the next iteration based on their current state, the current state of all 8 immediate neighboring cells, and the following rules:

+ If a cell has fewer than 2 live neighbors, it will be dead in the next iteration.
+ If a cell has more than 3 live neighbors, it will be dead in the next iteration.
+ If a cell is alive and has 2 or 3 live neighbors, it will remain alive in the next iteration.
+ If a cell is dead and has 3 live neighbors, it will become alive in the next iteration.

The basic data structure of the Game of Life is a state matrix, where each cell a[i, j] contains information about whether the cell is alive or dead. When implementing the game, a problem arises with neighbor cells located at the edge of the grid (cells in the first column have no "left" neighbors, etc.). There are several solutions to this problem, such as:

+ Assuming that non-existent neighbors are always dead.
+ Introducing "wraps" so that the last column is the "left" neighbor of the first column, and the last row is the "top" neighbor of the first row (effectively a torus).
+ For the purpose of this task, use the first approach.


## Task
The task is to implement a parallelized version of the Game of Life in Python in several ways. Ensure that after executing a given number of iterations, a series of state matrices over time (system states in each iteration) is saved, compatible with a given animation function (next cell in the file).

Using threads that simulate one cell each and synchronization:
(a) With locks (threading.Lock)
(b) With locks using a condition (threading.Condition)
(c) With locks using a barrier (threading.Condition)

Each thread simulates the operation of one cell in the system and has access to the states of its neighbors.

Cells do not have access to a global iteration counter; instead, each cell internally keeps track of the number of current iterations.

For each cell, there is a counter of neighbors who have read its current value. A cell ready to change its value periodically checks whether all existing neighbors have read its value, in which case it can reset the counter to zero, make the change, and wait for the start of the next iteration.

Iteration ends (and the next begins) by:
(a) Threads passing through a "waiting room", which needs to be implemented using two counters (integer variables) and one logical (boolean) variable.
(b) Threads passing through a "waiting room", implemented using a condition (Condition) and a single integer counter.
(c) Threads passing through a "waiting room", implemented using a barrier (Barrier).

Using processes that simulate one cell each and synchronization with message queues:
Each process simulates the operation of one cell in the system. The state of each cell is kept within the cell (the system operation does not rely on a shared state matrix). Cells exchange data about their state through iterations via message queues. All synchronization must be organized through messages. Solutions using mechanisms such as Lock, Condition, and Barrier will not be accepted.

For analyzing the operation, implement a special service (an additional process) to which all cells report their new state when it changes (where messages contain the cell's coordinates, iteration number, and new state). The service should reconstruct and save (or return to the main program) a series of state matrices.

Using multiple processes through a process pool, generating tasks at the level of a set of cells:
Divide the state matrix into N parts (where N is a configurable parameter) and generate a task for each part (calling a function whose parameters define which part of the matrix to process). The function should return a series of coordinates of cells and their new values, and the matrix for the next iteration can be created in the main program. Current values of cells and their neighbors can be read from a shared matrix.
