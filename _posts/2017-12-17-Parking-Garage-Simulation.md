## Simulation of parking garage using Monte Carlo method

### Problem statement

There are *S* stalls in a parking garage. The length of time between successive arrivals of cars at the entrance to the garage is a random variable from a Poisson distribution with mean *A* minutes.

If a car arrives at the garage and a stall is available, it occupies one of the stalls. The length of time a car stays in the garage is a random variable from a normal distribution with mean *M* minutes and standard deviation *M/4* minutes. If a car arrives at the entrance and no stalls are available, the car is turned away. 

We wish to determine the steady-state characteristics of the parking garage using Monte Carlo method.

### Implementation

Time is modeled in minutes as a real variable and is zero at the start of the simulation. Parking garage stalls is modeled as an array of structure variable containing the state of occupancy and time of leaving of the car.

At the beginning of the simulation all the stalls are empty and the time of leaving is empty. Each time a car arrives, the stalls array is traversed to find an empty slot to admit the car. If an empty slot is found then the change of its occupancy is changed to ‘occupied’ and a random number normal distribution is generated which is added to the current time of arrival so as to predict the time at which the car is likely to leave. The normal distribution has mean to be *M* and standard deviation to be *M/4*.

If there is no empty slot found then that car is turned away. Simultaneously the other slots are checked and if any slots having time of car leaving less than the current time, meaning the car in the slot have left the garage, have their occupancy set to ‘unoccupied’. 

Since the car arrivals are modeled as Poisson random variables the inter-arrival times are modeled as exponential random variable. Hence we can use the expression _-A*ln(u)_ to determine the next car arrival time, where *u* is a uniform random variable from _\[0,1)_. We increment the current time *t* by this amount to get the next arrival and look for the next arrival and the aforesaid process continues. To generate normal variates we can use the Box-Mueller transformation.

The code is written in C and parallelized using MPI calls and OpenMP directives.

### Scope of parallelization: 

The exponential random numbers are generated by sequence splitting method and each of the random number in the sub-sequence to be generated by that process is generated parallely by OpenMP threads. All the sub-sequences are collected by the process 0 using the MPI_Gather call. These random numbers are used as the inter-arrival times for our simulation. Also initializing the statuses of the garage stalls and generating inter-arrival times are be done parallely by executing them using two separate OpenMP threads.

The final part is that of assigning the number of cars that have arrived, which is given by the size of Monte-carlo simulation, to the stalls in the garage. The stalls in the garage are traversed to find an empty slot for the incoming car else it is turned away.

At the end the average number of stalls occupied and probability of a car being turned away due to full parking space are calculated and displayed.

### Code

Code is available at my github repository [high-performance-computing](https://github.com/varshaneya/high-performance-computing/tree/master/garageSim).

### References

*Parallel programming in C with MPI and OpenMP*, by Michael J Quinn

### Comments

Comments, bugs and issues for this post can be raised [here](https://github.com/varshaneya/high-performance-computing/issues).