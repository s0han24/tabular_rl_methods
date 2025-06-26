# tabular_rl_methods
Implementations for tabular Reinforcement Learning methods for various problem settings

## Planning Methods
Methods to solve MDPs where all dynamics and reward systems are known

### The Machine Replacement Problem
- An introductory problem in the context of solving Markov Decision Processes(MDPs) in the setting where the model of the environment is known
- The basic problem statement has been taken from [[1](#1)]

#### The Base Problem statement:
- A machine can be in any of the states 1,2, ... , N considering a finite set of states for convenience of implementation
- At the beginning of each day, the state of the machine is noted and a decision upon whether or not to replace the machine is made
- If the decision to replace is made, then we assume that the machine is instantaneously replaced by a new machine whose state is O and a replacement cost is incurred
- a maintenance cost is incurred each day which is a function of current state
- The dynamics are markovian under both actions

### Infinite Horizon Case
- It can be proved that when the transition probabilities and costs are defined as given [here](DP_MDP.ipynb), the optimal policy for the infinite horizon case is a threshold type policy
- Refer [here](DP_MDP.ipynb) for the precise problem definition and the Dynamic Programming solution for the base machine replacement problem.
- The notebook implements Modified Policy Iteration to solve the problem.

### Finite Horizon Case
- Here, a small modification is used to bring up a finite horizon version of the problem
- To explore a **time dependant policy** a payout is introduced at terminal step
- The payout is higher for lower valued states(states are assumed to be completely ordered) to introduce a tradeoff between the payout and the replacement cost
- The idea is to see how the policy takes into account the following:
    1. Replacing the machine when the problem is close to terminating makes sure we get a higher payout
    2. But replacing the machine involves a replacement cost
- The tradeoff between these two makes the policy interesting especially when the discount factor is greater than 0.7
- Refer [here](finite_horizon.ipynb) for the solution for this version of the machine replacement problem.

## Reinforcement Learning
Now we move to the case where the dynamics and rewards are not known. What we do have is a way to sample a sequence of states, actions and rewards given by $s_1, a_1, r_1, s_2, a_2, r_2, ...$ which may or may not terminate depending on the nature of the problem.

### The Game of Nim
#### Introduction:
- Nim is a combinatorial game, it is central to combinatorial game theory because its generalization gives a framework to describe any combinatorial game as a game of single heap Nim(not the simple nim game which is used here). Refer to the [Sprague-Grundy Theorem](https://en.wikipedia.org/wiki/Sprague%E2%80%93Grundy_theorem).
- The optimal strategy for any combinatorial game is the same: get to a zero nim sum position(or a position with a Grundy number of zero) whenever possible and if such a position isn't available to you then you have no way to secure a win unless your opponent makes a mistake(chooses a move with non-zero nim sum)
- However, this strategy is not always practical to implement
- What I aim to do by exploring a Reinforcement Learning Solution to The Game of Nim is to see how RL methods fare on small combinatorial games and how close to the known optimal policy do they get, to be perfectly clear the goal is not to build an agent but to see how these agents perform on a task with a known optimal strategy.

#### Implementation details:
- Self-Play is a method to train agents to play games that is responsible for the success of [AlphaGo Zero](https://deepmind.google/discover/blog/alphago-zero-starting-from-scratch/) [[2](#2)], but to start off I implemented a static opponent who always plays optimally when an optimal strategy is available and plays a move that prolongs the game as much as possible when there isn't an optimal move.
- The agent is trained using simulations from opponents using the following strategy:
 1. Choose the optimal action with some probability $\epsilon$
 2. Choose a random action with some probability $1-\epsilon$
- This policy is similar to an $epsilon$-greedy policy but the crucial difference lies in the way we choose the optimal action which in this case is not one with best predicted return but the one with the proven best return.
- Using this static opponent is feasible here because we have an easily implementable optimal strategy.
- For a detailed description of Simple Nim and the implementation of the opponent refer [this](nim_MC_on_policy.ipynb)

#### Without Self Play
- So this section is basically cheating for most games because we don't usually have an optimal policy.
- On policy Monte-Carlo(MC) Control implemented on The Game of Nim [here](nim_MC_on_policy.ipynb)
- Off policy MC control methods not implemented yet
- Temporal Difference methods: Sarsa, Sarsa($\lambda$) and Q-learning implemented [here](nim_TD.ipynb).  
- Performance is measured based on number of winning positions fumbled, i.e. number of states where the optimal strategy guarantees winning but the agent makes a move such that against an optimal opponent the game is lost(ends turn on a non-zero nim sum when a position with zero nim sum is possible).

#### Observations for undiscounted case:
- Q-value vs episode plots indicate that convergence is much more stable for self play than for an optimal opponent
- But, by analysing the fumble rates, self play does much worse, indicating that self play seems to converge to some local optimum
- The reason for this seems to be that optimal opponents harshly punish any suboptimal move, which causes drastic drops in the Q-value along the entire sample path containing suboptimal moves
- Zero reward on losing(no negative reward) shows smoother convergence maybe because suboptimal moves are penalised less

**Conclusion**: let's try discounting so that suboptimal moves along a sample path don't punish **all** moves along the path equally

#### Observations after discounting:
- Initially blamed jagged Q-values on lack of discounting but it turns out I just needed to train longer for convergence. ¯\\\_(ツ)\_/¯
- Discounting did help reduce fumble rates for SARSA though
- Q-learning performs badly throughout, it has room for improvement

## References
<a id="1">[1]</a> 📚 S. M. Ross, Applied Probability Models with Optimization Applications, Holden-Day, 1970.  
<a id="2">[2]</a> 📄 AlphaGo Zero: Silver et al., Mastering the game of Go without human knowledge, Nature, 2017. DOI: 10.1038/nature24270

## Contributing:
Feel free to open issues for suggestions, or contribute with a pull request
