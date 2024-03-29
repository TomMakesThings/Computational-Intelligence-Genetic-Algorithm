<div align="center">
  <h1><b>Non-Dominated Sorting Genetic Algorithm (NSGA-II)</b></h1>
  <img src="https://images.weserv.nl/?url=avatars.githubusercontent.com/u/61354833?v=4&h=100&w=100&fit=cover&mask=circle&maxage=7d">
  <p><b>Code by <a href="https://github.com/TomMakesThings">TomMakesThings</a></b></p>
  <p><b><sub>November 2021</sub></b></p>
</div>

---

# **About**

### **Problem Definition**
The aim of this project is to solve the following multi-objective optimisation problem using the elitist non-dominated sorting genetic algorithm (NSGA-II)

$min\\{f_{1}, f_{2}\\}$

$f_{1} = \frac{(\frac{x_{1}}{2})^2 \text{+} (\frac{x_{2}}{4})^2 \text{+} (x_{3})^2}{3}$

$f_{2} = \frac{(\frac{x_{1}}{2} - 1)^2 \text{+} (\frac{x_{2}}{4} - 1)^2 \text{+} (x_{3} - 1)^2}{3}$

For $-4.0 \le x_{1}, x_{2}, x_{3} \le 4.0$

<br>

### **Implementation**

**Encoding**

The decision variables for each individual $x_{1}, x_{2}, x_{3}$ are set up for minimization with gray coding. This is an encoding in which adjacent numbers have a single digit differing by 1.

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

A population of 25 individuals is created and each is assigned a random 10-bit string when initialized. They are then evaluated to determine their fitness. First the gray code values are converted to real values within the range $[-4, 4]$ using equation:

$x_{i} = a_{i} + (b_{i} - a_{i}) \times \frac{c_{i}}{2^l - 1}$

<sub>where:</sub>
- <sub>$a_{i}$ is the lower bound $-4$</sub>
- <sub>$b_{i}$ is the upper bound $4$</sub>
- <sub>$c_{i}$ is the integer value of the gray code</sub>
- <sub>$l$ is the length of each gene</sub>

<br>

Then using the decoded values, $f_{1}$ and $f_{2}$ are calculated.

| Individual | $x_{1}$ | $x_{2}$ | $x_{3}$ | $f_{1}$  | $f_{2}$ |
| ---------- | ---------- | ---------- | ---------- | -------- | -------- |
| 1          | 1100000101 | 1110111000 | 1101000010 | 0.371571 | 0.433807 |
| 2          | 1110111000 | 1101001011 | 1110101100 | 1.05772  | 0.319697 |
| ...        | ...        | ...        | ...        | ...      | ...      |
| 24         | 1001111110 | 0110110001 | 0010111100 | 2.78652  | 4.50792  |
| 25         | 0010011011 | 0101100100 | 0000001110 | 5.49692  | 9.91497  |

<br>

Individuals are sorted into fronts using the efficient non-dominated sorting algorithm. First the population is copied, then sorted based on $f_{1}$. If the $f_{1}$ are equal, then sort on $f_{2}$. The first solution is assigned to front 1. All further solutions are compared the current front, starting with front 1. If they are dominated by at least 1 solution in the current front, a new front is created with this individual. Otherwise check the next front. If all fronts are checked, a new front is created containing this individual. 

| Individual | $x_{1}$ | $x_{2}$ | $x_{3}$ | $f_{1}$  | $f_{2}$ | Front |
| ---------- | ---------- | ---------- | ---------- | -------- | -------- | - |
| 1          | 1100000101 | 1110111000 | 1101000010 | 0.371571 | 0.433807 | 1 |
| 2          | 1110111000 | 1101001011 | 1110101100 | 1.05772  | 0.319697 | 1 |
| ...        | ...        | ...        | ...        | ...      | ...      | ... |
| 24         | 1001111110 | 0110110001 | 0010111100 | 2.78652  | 4.50792  | 7 |
| 25         | 0010011011 | 0101100100 | 0000001110 | 5.49692  | 9.91497  | 8 |

<br>

Once the individuals are separated into fronts, the crowding distance for individuals in each front is calculated. Solutions at the end of each front are assigned infinite distance, while those in between are assigned the average side length of its two neighbouring solutions.

| Individual | $x_{1}$ | $x_{2}$ | $x_{3}$ | $f_{1}$  | $f_{2}$ | Front | Crowding Distance |
| ---------- | ---------- | ---------- | ---------- | -------- | -------- | - | -|
| 1          | 1100000101 | 1110111000 | 1101000010 | 0.371571 | 0.433807 | 1 | $\infty$ |
| 2          | 1110111000 | 1101001011 | 1110101100 | 1.05772  | 0.319697 | 1 | 1| 
| ...        | ...        | ...        | ...        | ...      | ...      | ... | ... |
| 24         | 1001111110 | 0110110001 | 0010111100 | 2.78652  | 4.50792  | 7 | $\infty$ |
| 25         | 0010011011 | 0101100100 | 0000001110 | 5.49692  | 9.91497  | 8 | $\infty$ |

<br>

**Mate Selection**

Pairs of parents are selected through binary tournament selection until 25 offspring are produced. For each selected parent, two individuals are randomly selected from the population to compete. An individual is chosen if it dominates the other. If they are in the same front, the individual with the largest crowding distance is selected. If these are also equal, the selected individual is randomly chosen.

<br>

**Crossover and Mutation**

Uniform crossover is applied to parent pairs at a chance of 0.9. If no crossover occurs, parents’ genes are directly transferred to resulting offspring. Mutation is applied to the created offspring at a chance inversely proportionate to the chromosome size, i.e. 

$p = \frac{1}{L}$.

<br>

**Selection**

Parents and offspring populations are combined and sorted into fronts using non-dominated sorting. Individuals are assigned a crowding distance and individuals within each front are sorted by crowding distance in descending order. Fronts are combined into a list in ascending order, and the top 25 individuals are selected for the next population.

<img src="https://github.com/TomMakesThings/Computational-Intelligence-Genetic-Algorithm/blob/main/Images/Offspring.png">

<br>

**Evolution**

The algorithm is run for 30 generations causing the individuals to converge towards the Pareto frontier.  This is the shape formed by the optimal solutions in the objective space. In this case it is convex as this is a minimisation problem. To evaluate performance across generations, hypervolume is calculated using the worst objective values as a reference point. 

<img src="https://github.com/TomMakesThings/Computational-Intelligence-Genetic-Algorithm/blob/main/Images/Evolution.png">

# Instructions to Run the Code
1. [Click here](https://github.com/TomMakesThings/Computational-Intelligence-Genetic-Algorithm/archive/refs/heads/main.zip) to download the repository contents
2. Extract the [Jupyter notebook](https://github.com/TomMakesThings/Computational-Intelligence-Genetic-Algorithm/blob/main/Genetic_Algorithm.ipynb) from the ZIP
3. Import the notebook into [Colab](https://colab.research.google.com/)
