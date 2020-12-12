# Tennis

### Introduction

This is my implementation to solve the Tennis project on the Udacity Reinforcement Learning Nanodegree. 

In this environment, two agents control rackets to bounce a ball over a net. If an agent hits the ball over the net, it receives a reward of +0.1. If an agent lets a ball hit the ground or hits the ball out of bounds, it receives a reward of -0.01. Thus, the goal of each agent is to keep the ball in play.

The observation space consists of 8 variables corresponding to the position and velocity of the ball and racket. Each agent receives its own, local observation. Two continuous actions are available, corresponding to movement toward (or away from) the net, and jumping.

The task is episodic, and in order to solve the environment, your agents must get an average score of +0.5 (over 100 consecutive episodes, after taking the maximum over both agents). Specifically,

After each episode, we add up the rewards that each agent received (without discounting), to get a score for each agent. This yields 2 (potentially different) scores. We then take the maximum of these 2 scores.
This yields a single score for each episode.
The environment is considered solved, when the average (over 100 episodes) of those scores is at least +0.5.

This has been implemented using a Multi Agent Deep Deterministic Policy Gradients (MADDPG), a variation of the algorithm illustrated here: https://arxiv.org/pdf/1509.02971.pdf 

### My results
```
Episode: 100.000 Average Score: 0.009
Episode: 200.000 Average Score: 0.060
Episode: 300.000 Average Score: 0.061
Episode: 400.000 Average Score: 0.073
Episode: 500.000 Average Score: 0.091
Episode: 600.000 Average Score: 0.090
Episode: 700.000 Average Score: 0.108
Episode: 800.000 Average Score: 0.139
Episode: 900.000 Average Score: 0.148
Episode: 1000.000 Average Score: 0.154
Episode: 1100.000 Average Score: 0.162
Episode: 1200.000 Average Score: 0.201
Episode: 1300.000 Average Score: 0.194
Episode: 1400.000 Average Score: 0.195
Episode: 1500.000 Average Score: 0.212
Episode: 1600.000 Average Score: 0.235
Episode: 1700.000 Average Score: 0.289
Episode: 1800.000 Average Score: 0.296
Episode: 1900.000 Average Score: 0.343
Episode: 2000.000 Average Score: 0.306
Episode: 2100.000 Average Score: 0.360
Episode: 2200.000 Average Score: 0.373
Episode: 2300.000 Average Score: 0.416
Episode: 2400.000 Average Score: 0.339
Episode: 2500.000 Average Score: 0.425
Episode 2580.000, Average Score: 0.501
Environment solved in 2580 episodes!	Average Score: 0.501

```
![graph]

[graph]: https://github.com/bidimensional/Tennis/blob/main/tennis-plot.png


### Model Architecture
The model architecture for the DDPG I implemented is based on the findings of the original research paper at the following address: https://arxiv.org/pdf/1509.02971.pdf

Actor and Critic are neural network with 2 Fully Connected hidden layers, with 400 and 300 neurons each, as suggested by the paper.

In line with the same I initialized the weights from a normal distribution and I add noise using the Ornstein-Uhlenbeck process to maintain exploration.

Activation function is ReLU in the Actor and LeakyRELU for the critic, I came up with this variation as I was trying to identify what caused my very low learning initially.

I also used Batch normalisation as it was suggested as a good way to improve learning.

### Learning algorithm
The agent learns maximising the reward at each episode using an Actor Critic method implemented through 2 Neural Netowrks. Adam is used as an optimizer using the LR specified below. In this Multi-Agent scenario there are 2 Agents each one with his own Critic.
Even though I am using a target network to keep the learning more stable, in practice I do 1 pass of learning at every step as I found that this worked way better.

Actions are concatenated and sent to the environment and each agent learns according to the reward it receives as a consequence of its actions.

### Hyperparameters
These are the parameters I used. The model appears very susceptible to variation of these and sometime in a counterintuitive way.

### Agent
```
BUFFER_SIZE = int(1e6)  # replay buffer size
BATCH_SIZE = 128        # minibatch size, i tried multiple values, this worked ok
GAMMA = 0.99            # discount factor, 
TAU = 8e-3              # for soft update of target parameters 
LR_ACTOR = 1e-3         # learning rate of the actor 
LR_CRITIC = 1e-3        # learning rate of the critic 
WEIGHT_DECAY = 0        # L2 weight decay from -
UPDATE_EVERY = 1        # The agents do 1 pass of learning each step. This worked well in practice.
NUM_UPDATES = 1         # 
EPSILON_DECAY = 1e-5    # decay for epsilon above
EPS_START = 5           # starting value 
EPS_FINAL = 0.05        # lowest value for eps
```

### Future research
- I found that there is too much variability in the performance of this algorithm with small changes of the hyperparameters. Before adding any further complication to this architecture, one should understand better why is that occurring. 
- I eventually settled for neural networks with 2 hidden layers of 400 and 300 neurons as in the DDPG paper, but I also found that network way smaller (64 and 32) actually learned quite well, at least at the beginning. Given that smaller networks require less computation, one should understand what is really needed to learn this to better isolate the effect of all the hyperparameters.
- I settled to learn once at every step, even if this is supposed to perform less well than updating the same less often. In the single agent Continuous Control, this worked well, but in this one I wasn't able to learn at all. This should be understood, based on my search people have found that doing multiple pass of learning every step performs even better. A better theory around how this impact learning is needed.

