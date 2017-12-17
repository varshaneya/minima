## Simulation of parking garage using Monte Carlo method
### Problem statement
There are S stalls in a parking garage. The length of time between successive arrivals of cars at the entrance to the garage is a random variable from a Poisson distribution with mean A minutes. 
If a car arrives at the garage and a stall is available, it occupies one of the stalls. The length of time a car stays in the garage is a random variable from a normal distribution with mean M minutes and standard deviation M /4 minutes. If a car arrives at the entrance and no stalls are available, the car is turned away. 
We wish to determine the steady-state characteristics of the parking garage using Monte Carlo method.

### Implementation
Time is modeled in minutes as a real variable and is zero at the start of the simulation. Parking garage stalls is modeled as an array of structure variable containing the state of occupancy and time of leaving of the car. 
At the beginning of the simulation all the stalls are empty and the time of leaving is empty. Each time a car arrives, the stalls array is traversed to find an empty slot to admit the car. If an empty slot is found then the change of its occupancy is changed to ‘occupied’ and a random number normal distribution is generated which is added to the current time of arrival so as to predict the time at which the car is likely to leave. The normal distribution has mean to be M and standard deviation to be M/4.
If there in no empty slot found then that car is turned away. Simultaneously the other slots are checked and if any slots having time of car leaving less than the current time, meaning the car in the slot have left the garage, have their occupancy set to ‘unoccupied’. 
Since the car arrivals are modeled as Poisson random variables the inter-arrival times are modeled as exponential random variable. Hence we can use the expression $ -A*ln(u) $ to determine the next car arrival time, where u is a uniform random variable from $[0,1)$. We increment the current time $t$ by this amount to get the next arrival and look for the next arrival and the aforesaid process continues. To generate normal variates we canuse the Box-Mueller transformation.
The code is parallelized using MPI calls and OpenMP directives.

### Scope of parallelization: 
The exponential ra
