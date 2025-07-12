# Tabular Reinforcement Learning Methods
This repository contains implementations and experiments on tabular reinforcement learning and planning methods applied to structured problems with known or computable optimal policies. The goal is to study algorithmic behavior in controlled settings and explore theoretical insights into the working of tabular reinforcement learning methods, the foundational precursors to modern Deep RL.

### Problem 1: The Machine Replacement Problem
This is a classic example of solving Markov Decision Processes (MDPs) when the transition model is known. The formulation is based on Ross’s Applied Probability Models [[1](#1)].

#### The Base Problem statement:
- A machine can be in any of the states 1,2, ... , N considering a finite set of states for convenience of implementation
- Each day, we observe the state and decide whether to continue or replace the machine.
- Replacement resets the machine to a new state 0 and incurs a cost, replacement is instantaneous.
- A maintenance cost is also incurred daily based on the current state.
- The dynamics are markovian under both actions

### Infinite Horizon Case (Discounted Rewards)
- Solved using Modified Policy Iteration ([see notebook](machine_replacement/DP_MDP.ipynb)).
- Known to yield a threshold policy: replace only when state exceeds a fixed value.
- Notebook explores convergence behavior, value functions, and policies.
- Solved in the RL case using TD methods ([see notebook](machine_replacement/TD_machine_replacement.ipynb)).

### Finite Horizon Variant
- Here, a small modification is used to bring up a finite horizon version of the problem
- To explore a **time dependant policy** a payout is introduced at terminal step which favours lower valued states.
- Highlights tradeoffs:
    1. Replace at later time steps to maximize terminal payout
    2. But replacing the machine involves a replacement cost
- The tradeoff between these two makes the policy interesting especially when the discount factor is greater than 0.7
- Dynamic Programming used to explore optimal time-varying policies
([see notebook](machine_replacement/finite_horizon.ipynb))

### Problem 2: The Game of Nim
#### Introduction:
- Nim is a simple yet rich combinatorial game with well-studied optimal strategies. This makes it ideal for evaluating how RL agents learn in the presence of a **known optimal policy.**
- Nim is central to combinatorial game theory and is an especially powerful method to represent combinatorial games(refer: [The Sprague-Grundy Theorem](https://en.wikipedia.org/wiki/Sprague%E2%80%93Grundy_theorem))

#### Goal of Experiment:
The purpose here is not to train a competitive agent but to:
- Evaluate how well RL methods recover the known optimal strategy
- Understand the limitations of various exploration and learning mechanisms

#### Strategy
##### Static opponent:
Use a static opponent that plays optimally when possible. This is only possible because we know the optimal policy for Nim.

The agent is initially trained using simulations with an ε-optimal policy:
- With probability ε: opponent plays the optimal move
- With probability 1-ε: random move (to prolong the game)

This setup mimics ε-greedy but uses ground-truth optimal moves instead of learned Q-values.

##### Self-Play Setup
In addition to using static opponents, I implemented self-play, where the agent learns by competing against its own evolving policy. After correcting an earlier implementation bug, self-play achieved performance indistinguishable from training against an optimal opponent, and consistently outperformed ε-optimal opponents, especially at lower values of ε.

This result demonstrates that even in tabular settings like Nim:

- Self-play can recover the optimal policy, given enough training

- Training against suboptimal (noisy) opponents can slow down or misguide learning

These findings reinforce the value of self-play as a stable learning method even in simple MDPs, and offer insight into policy convergence dynamics when ground truth is known.

#### Implementation Overview

- Monte Carlo Control: [Notebook](Nim/nim_MC.ipynb)

- Temporal Difference Methods (SARSA, Q-learning, SARSA-λ): [Notebook](Nim/nim_TD.ipynb)

- Special performance metric: Percentage of winning positions fumbled - instances where the agent makes a losing move from a winning state. This metric is uniquely available in Nim due to the presence of a known, deterministic optimal policy.

#### Afterstates:
To improve generalization, the agent can be modified to learn afterstate values instead of action-values. This helps reuse knowledge across state-action pairs leading to the same resulting state — an efficient trick in structured games like Nim.
- Afterstates have not been implemented yet and are the planned next step

## References
<a id="1">[1]</a> 📚 S. M. Ross, Applied Probability Models with Optimization Applications, Holden-Day, 1970.  
<a id="2">[2]</a> 📄 AlphaGo Zero: Silver et al., Mastering the game of Go without human knowledge, Nature, 2017. DOI: 10.1038/nature24270
<a id="3">[3]</a> 📄 Long-Ji Lin, Reinforcement Learning for Robots Using Neural Networks, Technical Report, DTIC Document, 1993.
<a id="3">[4]</a> 📚 R. S. Sutton and A. G. Barto, Reinforcement Learning: An Introduction, MIT Press, 2018. (2nd Edition)

## Contributing:
Feel free to open issues for suggestions, or contribute with a pull request
