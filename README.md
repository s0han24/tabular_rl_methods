# tabular_rl_methods
Implementations for tabular Reinforcement Learning methods for various problem settings

## Table of Contents


## The Machine Replacement Problem
- An introductory problem in the context of solving Markov Decision Processes(MDPs) in the setting where the model of the environment is known
- The basic problem statement has been taken from [[1](#1)]

### The Base Problem statement:
- A machine can be in any of the states 1,2, ... , N considering a finite set of states for convenience
- At the beginning of each day, the state of the machine is noted and a decision upon whether or not to replace the machine is made
- If the decision to replace is made, then we assume that the machine is instantaneously replaced by a new machine whose state is O
- The cost of replacing the machine will be denoted by R
- a maintenance cost C(i) is incurred each day that the machine is in state i.
- we let $P_{ij}$ represent the probability that a machine in state i at the beginning of one day will be in state j at the beginning of the next day.

### Precise definition:
- the above is a two-action Markov decision model in which action 1 is the replacement action and action 2 the nonreplacement action.
- I have taken the liberty of fixing the transition probabilities as follows for ease of implementation
- In general as long as the transition probabilities satisfy [stochastic monotonicity](https://adityam.github.io/stochastic-control/mdps/monotone-mdps.html) the optimal policy can be shown to have some interesting properties.

#### Transition Probabilities
$$P_{ij}(0) = P_{0j}. \\
P_{ij}(1) = P_{ij}$$
for all $i\ge 0$

#### One stage costs(rewards)
$$C(i,0) = R+C(0). \\
C(i,1) = C(i)$$
for all $i\ge 0$

## Infinite Horizon Case
- It can be proved that when the transition probabilities and costs are defined as above, the optimal policy for the infinite horizon case is a threshold type policy
- Refer [here](DP_MDP.ipynb) for the Dynamic Programming solution for the base machine replacement problem.
- The notebook implements Modified Policy Iteration to solve the problem.

## Finite Horizon Case
- Here, a small modification is used to bring up a finite horizon version of the problem
- To explore a **time dependant policy** a payout is introduced at terminal step
- The payout is higher for lower valued states(states are assumed to be completely ordered) to introduce a tradeoff between the payout and the replacement cost
- The idea is to see how the policy takes into account the following:
    1. Replacing the machine when the problem is close to terminating makes sure we get a higher payout
    2. But replacing the machine involves a replacement cost
- The tradeoff between these two makes the policy interesting especially in the undiscounted case

### References
<a id="1">[1]</a> Ross S. M. (1970) Applied Probability Models with Optimization Applications. Holden-Day: San Francisco, CA.
