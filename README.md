# Tabular Reinforcement Learning and Planning Methods

This repository contains implementations and experiments with tabular reinforcement learning (RL) and planning methods, applied to structured problems with **known or computable optimal solutions**. The aim is to study the behavior of RL algorithms in controlled environments, compare them to ground-truth policies, and understand convergence properties, exploration effects, and policy structures.

Problems explored:
- The **Machine Replacement Problem**, a classic stochastic control task
- The **Game of Nim**, a combinatorial game with a provably optimal strategy

Methods covered:
- Value Iteration, Policy Iteration, Modified Policy Iteration
- Monte Carlo Control (on-policy, off-policy)
- Temporal Difference (TD) methods: Q-learning, SARSA, SARSA($\lambda$)
- Evaluation via policy structure and special metrics

### Problem 1: The Machine Replacement Problem
This is a classic example of solving Markov Decision Processes (MDPs) when the transition model is known. The formulation is based on Ross’s Applied Probability Models [[1](#1)].

A machine can be in one of `N` discrete states. Each day, a decision must be made:
- **Continue**: Pay a state-dependent maintenance cost
- **Replace**: Reset to state 0 and pay a fixed replacement cost

The goal is to minimize expected cumulative cost over time. The model is fully known (MDP), making it ideal for **planning and RL comparison**.

### Infinite Horizon (Discounted)
- Solved using **Modified Policy Iteration**
- Results in a **threshold policy**: replace only if state ≥ threshold
- Threshold varies with the discount factor $\gamma$ and cost function  
📓 [Planning Methods Notebook](machine_replacement/infinite_horizon.ipynb)
📓 [TD Methods Notebook](machine_replacement/TD_machine_replacement_infinite_horizon.ipynb)


### Finite Horizon Variant
- Adds a terminal reward encouraging lower states at episode end
- Produces a **time-dependent policy**
- Solved using backward dynamic programming  
📓 [Planning Methods Notebook](machine_replacement/finite_horizon.ipynb)

### Problem 2: The Game of Nim

Nim is a combinatorial game with a **known optimal policy** derived from the **Sprague-Grundy theorem**. This makes it an ideal benchmark for evaluating RL agents.

#### Known Optimal Strategy
- Each game state has a **Grundy number** (computed via XOR of heap sizes)
- States with nim-sum = 0 are losing positions
- Goal: Always move to a state with nim-sum = 0

#### Learning Setup
- State space: tuple of heap sizes
- Action space: (heap index, number to remove)
- Reward: +1 for winning, -1 for losing, 0 otherwise

Agents are trained using:
- Monte Carlo Control
- SARSA, SARSA($\lambda$), and Q-learning

Two training setups:
1. **Against ε-optimal opponents** (mix of optimal and random moves)
2. **Self-play**, where the agent plays against its own evolving policy

Known value function $V^*$ for undiscounted case:
- +1 for winning positions
- -1 for losing positions

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

## Evaluation and Insights
### Convergence Behavior
- All TD and MC methods **converge to optimal policy** when trained against optimal or self-play opponents
- **Noisy opponents (low ε)** slow learning and increase fumbles
- After fixing a bug, **self-play matched optimal opponent** in performance

### Special Metric: Winning Positions Fumbled
Because Nim has a known optimal policy, we define:
> **% of winning positions fumbled**: fraction of states where the agent takes a losing action from a winning state

This highlights not just average performance but **policy reliability**.

#### Afterstates:
To improve generalization, the agent can be modified to learn afterstate values(refer [[4](#4)]) instead of action-values. This helps reuse knowledge across state-action pairs leading to the same resulting state — an efficient trick in structured games like Nim.
- Afterstates have not been implemented yet and are the planned next step

### Key Observations
- MC control with exploring starts learns quickly against optimal opponents
- Convergence fails entirely against noisy opponents without proper structure
- TD methods (especially SARSA) recover optimal behavior given enough episodes even against suboptimal opponents with a reasonable rate of optimal play(> 0.4)
- Discounting had **no effect** on convergence post-bug-fix (discounted and undiscounted curves matched)

## Summary and Takeaways

- Tabular RL methods successfully recover optimal strategies in structured domains
- Nim provides a powerful benchmark due to its known optimal policy
- Self-play is surprisingly effective in tabular settings
- Evaluation metrics beyond reward (like fumble rate) offer deeper insights


## References
<a id="1">[1]</a> 📚 S. M. Ross, Applied Probability Models with Optimization Applications, Holden-Day, 1970.  
<a id="2">[2]</a> 📄 AlphaGo Zero: Silver et al., Mastering the game of Go without human knowledge, Nature, 2017. DOI: 10.1038/nature24270
<a id="3">[3]</a> 📄 Long-Ji Lin, Reinforcement Learning for Robots Using Neural Networks, Technical Report, DTIC Document, 1993.
<a id="3">[4]</a> 📚 R. S. Sutton and A. G. Barto, Reinforcement Learning: An Introduction, MIT Press, 2018. (2nd Edition)

## Contributing:
Feel free to open issues for suggestions, or contribute with a pull request
