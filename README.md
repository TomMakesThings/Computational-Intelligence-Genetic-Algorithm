# **About**

### **Problem Definition**
<a href="https://github.com/TomMakesThings/Computational-Intelligence/blob/main/Genetic%20Algorithm/Genetic_Algorithm.ipynb">Genetic_Algorithm.ipynn</a> solves the following multi-objective optimisation problem using the elitist non-dominated sorting genetic algorithm (NSGA-II)

<img src="https://render.githubusercontent.com/render/math?math=min\{f_{1}, f_{2}\}">

<img width=150 src="https://render.githubusercontent.com/render/math?math=f_{1} = \frac{(\frac{x_{1}}{2})^2 + (\frac{x_{2}}{4})^2 + (x_{3})^2}{3}">
<img width=220 src="https://render.githubusercontent.com/render/math?math=f_{2} = \frac{(\frac{x_{1}}{2} - 1)^2 + (\frac{x_{2}}{4} - 1)^2 + (x_{3} - 1)^2}{3}">

For <img src="https://render.githubusercontent.com/render/math?math=-4.0 \le x_{1}, x_{2}, x_{3} \le 4.0">

<br>

### **Implementation**

**Encoding**

The decision variables for each individual (<img src="https://render.githubusercontent.com/render/math?math=$x_{1}, x_{2}, x_{3}$">) are set up for minimization with gray coding. This is an encoding in which adjacent numbers have a single digit differing by 1.

| Binary | Gray Code |
| ------ | --------- |
| 000    | 000       |
| 001    | 001       |
| 010    | 011       |
| 011    | 010       |
| 100    | 110       |
| 101    | 111       |
| 110    | 101       |
| 111    | 100       |

<br>

**Population Initialisation and Evaluation**

A population of 25 individuals is created and each is assigned a random 10-bit string when initialized. They are then evaluated to determine their fitness. First the gray code values are converted to real values within the range <img src="https://render.githubusercontent.com/render/math?math=$[-4, 4]$"> using equation:

<img src="https://render.githubusercontent.com/render/math?math=$x_{i} = a_{i} + (b_{i} - a_{i}) \times \frac{c_{i}}{2^l - 1}$">

<sub>where:</sub>
- <sub><img src="https://render.githubusercontent.com/render/math?math=$a_{i}$"> is the lower bound <img src="https://render.githubusercontent.com/render/math?math=$-4$"></sub>
- <sub><img src="https://render.githubusercontent.com/render/math?math=$b_{i}$"> is the upper bound <img src="https://render.githubusercontent.com/render/math?math=$4$"></sub>
- <sub><img src="https://render.githubusercontent.com/render/math?math=$c_{i}$"> is the integer value of the gray code</sub>
- <sub>l is the length of each gene</sub>

<br>

Then using the decoded values, <img src="https://render.githubusercontent.com/render/math?math=$f_{1}$"> and <img src="https://render.githubusercontent.com/render/math?math=$f_{2}$"> are calculated.

| Individual | <img src="https://render.githubusercontent.com/render/math?math=$x_{1}$"> | <img src="https://render.githubusercontent.com/render/math?math=$x_{2}$"> | <img src="https://render.githubusercontent.com/render/math?math=$x_{3}$"> | <img src="https://render.githubusercontent.com/render/math?math=$f_{1}$">  | <img src="https://render.githubusercontent.com/render/math?math=$f_{2}$"> |
| ---------- | ---------- | ---------- | ---------- | -------- | -------- |
| 1          | 1100000101 | 1110111000 | 1101000010 | 0.371571 | 0.433807 |
| 2          | 1110111000 | 1101001011 | 1110101100 | 1.05772  | 0.319697 |
| ...        | ...        | ...        | ...        | ...      | ...      |
| 24         | 1001111110 | 0110110001 | 0010111100 | 2.78652  | 4.50792  |
| 25         | 0010011011 | 0101100100 | 0000001110 | 5.49692  | 9.91497  |

<br>

Individuals are sorted into fronts using the efficient non-dominated sorting algorithm. First the population is copied, then sorted based on <img src="https://render.githubusercontent.com/render/math?math=$f_{1}$">. If the <img src="https://render.githubusercontent.com/render/math?math=$f_{1}$"> are equal, then sort on <img src="https://render.githubusercontent.com/render/math?math=$f_{2}$">. The first solution is assigned to front 1. All further solutions are compared the current front, starting with front 1. If they are dominated by at least 1 solution in the current front, a new front is created with this individual. Otherwise check the next front. If all fronts are checked, a new front is created containing this individual. 

| Individual | <img src="https://render.githubusercontent.com/render/math?math=$x_{1}$"> | <img src="https://render.githubusercontent.com/render/math?math=$x_{2}$"> | <img src="https://render.githubusercontent.com/render/math?math=$x_{3}$"> | <img src="https://render.githubusercontent.com/render/math?math=$f_{1}$">  | <img src="https://render.githubusercontent.com/render/math?math=$f_{2}$"> | Front |
| ---------- | ---------- | ---------- | ---------- | -------- | -------- | - |
| 1          | 1100000101 | 1110111000 | 1101000010 | 0.371571 | 0.433807 | 1 |
| 2          | 1110111000 | 1101001011 | 1110101100 | 1.05772  | 0.319697 | 1 |
| ...        | ...        | ...        | ...        | ...      | ...      | ... |
| 24         | 1001111110 | 0110110001 | 0010111100 | 2.78652  | 4.50792  | 7 |
| 25         | 0010011011 | 0101100100 | 0000001110 | 5.49692  | 9.91497  | 8 |

<br>

Once the individuals are separated into fronts, the crowding distance for individuals in each front is calculated. Solutions at the end of each front are assigned infinite distance, while those in between are assigned the average side length of its two neighbouring solutions.

| Individual | <img src="https://render.githubusercontent.com/render/math?math=$x_{1}$"> | <img src="https://render.githubusercontent.com/render/math?math=$x_{2}$"> | <img src="https://render.githubusercontent.com/render/math?math=$x_{3}$"> | <img src="https://render.githubusercontent.com/render/math?math=$f_{1}$">  | <img src="https://render.githubusercontent.com/render/math?math=$f_{2}$"> | Front | Crowding Distance |
| ---------- | ---------- | ---------- | ---------- | -------- | -------- | - | -|
| 1          | 1100000101 | 1110111000 | 1101000010 | 0.371571 | 0.433807 | 1 | <img src="https://render.githubusercontent.com/render/math?math=$\infty$"> |
| 2          | 1110111000 | 1101001011 | 1110101100 | 1.05772  | 0.319697 | 1 | 1| 
| ...        | ...        | ...        | ...        | ...      | ...      | ... | ... |
| 24         | 1001111110 | 0110110001 | 0010111100 | 2.78652  | 4.50792  | 7 | <img src="https://render.githubusercontent.com/render/math?math=$\infty$"> |
| 25         | 0010011011 | 0101100100 | 0000001110 | 5.49692  | 9.91497  | 8 | <img src="https://render.githubusercontent.com/render/math?math=$\infty$"> |

<br>

**Mate Selection**

Pairs of parents are selected through binary tournament selection until 25 offspring are produced. For each selected parent, two individuals are randomly selected from the population to compete. An individual is chosen if it dominates the other. If they are in the same front, the individual with the largest crowding distance is selected. If these are also equal, the selected individual is randomly chosen.

<br>

**Crossover and Mutation**

Uniform crossover is applied to parent pairs at a chance of 0.9. If no crossover occurs, parents’ genes are directly transferred to resulting offspring. Mutation is applied to the created offspring at a chance inversely proportionate to the chromosome size, i.e. 

<img src="https://render.githubusercontent.com/render/math?math=$p = \frac{1}{L}$">.

<br>

**Selection**

Parents and offspring populations are combined and sorted into fronts using non-dominated sorting. Individuals are assigned a crowding distance and individuals within each front are sorted by crowding distance in descending order. Fronts are combined into a list in ascending order, and the top 25 individuals are selected for the next population.

<img src="https://github.com/TomMakesThings/Computational-Intelligence-Genetic-Algorithm/blob/main/Offspring.png">

<br>

**Evolution**

The algorithm is run for 30 generations causing the individuals to converge towards the Pareto frontier.  This is the shape formed by the optimal solutions in the objective space. In this case it is convex as this is a minimisation problem. To evaluate performance across generations, hypervolume is calculated using the worst objective values as a reference point. 

<img src="https://github.com/TomMakesThings/Computational-Intelligence-Genetic-Algorithm/blob/main/Evolution.png">
