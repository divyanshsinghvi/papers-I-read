---
layout: post
title: Mastering Atari, Go, Chess and Shogi by Planning with a Learned Model
comments: True
excerpt: 
tags: ['2019', 'Deep Reinforcement Learning', 'Reinforcement Learning', AI, DRL, Model-Based, Model-Free, Planning, RL]

---

## Introduction

* The paper presents the MuZero algorithm that performs planning with a learned model.

* The algorithm achieves state of the art results on Atari suite (where generally model-free approaches perform the best) and on planning-oriented games like Chess and Go (where generall planning approaches perform the best).

* [Link to the paper](https://arxiv.org/abs/1911.08265)

## Relation to standard Model-Based Approaches

* Model-based approaches generally focus on reconstructing the true environment state or the sequence of full observations.

* MuZero focuses on predicting only those aspects that are most relevant for planning - policy, value functions, and rewards.

## Approach

* The model consists of three components: (representation) encoder, dynamics function, and the prediction network.

* The learning agent has two kinds of interactions - real interactions (ie the actions that are actually executed in the real environment) and hypothetical or imaginary actions (ie the actions that are executed in the learned model or the dynamics function).

* At any timestep *t*, the past observations *o<sub>1</sub>*, ... *o<sub>t</sub>* are encoded into the state *s<sub>t</sub>* using the encoder.

* Now the model takes hypothetical actions for the next *K* timesteps by unrolling the model for *K* steps.

* For each timestep *k = 1, ..., K*, the dynamics model predicts the immediate reward *r<sub>k</sub>* and a new hidden state *h<sub>k</sub>* using the previous hidden state *h<sub>k-1</sub>* and action *a<sub>k</sub>*.

* At the same time, the policy *p<sup>k</sup>* and the value function *v<sup>k</sup>* are computed using the prediction network.

* The initial hidden state *h<sub>0</sub>* is initialized using the state *s<sub>t</sub>*

* Any MDP Planning algorithm can be used to search for optimal policy and value function given the state transitions and the rewards induced by the dynamics function.

* Specifically, the MCTS (Monte Carlo Tree Search) algorithm is used and the action *a<sub>t+1</sub>* (ie the action that is executed in the actual environment) is selected from the policy outputted by MCTS.

## Collecting Data for the Replay Buffer

* At each timestep *t*, the MCTS algorithm is executed to choose the next action (which will be executed in the real environment).

* The resulting next observation *o<sub>t+1</sub>* and reward *r<sub>t+1</sub>* are stored and the trajectory is written to the replay buffer (at the end of the episode).

## Objective

* For every hypothetical step *k*, match the predicted policy, value, and reward to the actual target values. 

* The target policy is generated by the MCTS algorithm.

* The target value function and reward are generated by actually playing the game (or the MDP).

## Relation to AlphaZero

* MuZero leverages the search-based policy iteration from AlphaZero.

* It extends AlphaZero to setups with a single agent (where self-play is not possible) and setups with a non-zero reward at the intermediate time steps.

* The encoder and the predictions functions are similar to ones used by AlphZero.

## Results

* *K* is set to 5.

* Environments: 57 games in Atari along with Chess, Go and Shogi

* MuZero achieves the same level of performance as AlphaZero for Chess and Shogi. In Go, MuZero slightly outperforms AlphaZero despite doing fewer computations per node in the search tree.

* In Atari, MuZero achieves a new state-of-the-art compared to both model-based and model-free approaches.

* The paper considers a variant called MuZero Reanalyze that reanalyzes old trajectories by re-running the MCTS algorithm with the updated network parameter. The motivation is to have a better sample complexity.

* MuZero performs well even when using a single simulation of MCTS (during inference).

* During training, using more simulations of MCTS helps to achieve better performance through even just 6 simulations per move is sufficient to learn a good model for Ms. Pacman.