# Tennis

### Introduction

This is my implementation to solve the Tennis project on the Udacity Reinforcement Learning Nanodegree. 

In this environment, a double-jointed arm can move to target locations. A reward of +0.1 is provided for each step that the agent's hand is in the goal location. Thus, the goal of your agent is to maintain its position at the target location for as many time steps as possible.

The observation space consists of 33 variables corresponding to position, rotation, velocity, and angular velocities of the arm. Each action is a vector with four numbers, corresponding to torque applicable to two joints. Every entry in the action vector should be a number between -1 and 1.

The task is a continuous task, and in order to solve the environment, your agent must get an average score of 30 over 100 consecutive episodes.

This has been implemented using a Deep Deterministic Policy Gradients (DDPG) algorithm illustrated here: https://arxiv.org/pdf/1509.02971.pdf

### My results
```
Episode 100	Average Score: 3.622
Episode 200	Average Score: 10.811
Episode 300	Average Score: 19.244
Episode 400	Average Score: 26.500
Episode 455, Average Score: 30.12
Environment solved in 455 episodes!	Average Score: 30.12

```
![graph]

[graph]: https://github.com/bidimensional/Continuous-Control/blob/main/concontrol.png?raw=true


### Model Architecture
The model architecture for the DDPG I implemented is based on the findings of the original research paper at the following address: https://arxiv.org/pdf/1509.02971.pdf

Actor and Critic have 2 hidden layers with 400 and 300 neurons each as suggested by the paper.

In line with the same I initialized the weights from a normal distribution and I add noise using the Ornstein-Uhlenbeck process to maintain exploration.

Activation function is ReLU in the Actor and LeakyRELU for the critic, I came up with this variation as I was trying to identify what caused my very low learning initially.

I also used Batch normalisation as it was suggested as a good way to improve learning.

### Learning algorithm
The agent learns maximising the reward at each episode using an Actor Critic method implemented through 2 Neural Netowrks. Adam is used as an optimizer using the LR specified below.

The training creates an **actor.pth** and a **critic.pth** when successful that can be used to restore the weights in the neural network at later stage to let the agent interact with the world.

### Hyperparameters
These are the parameters I used. The model appears very susceptible to variation of these.

### Agent
```
BUFFER_SIZE = int(1e6)  # replay buffer size \
BATCH_SIZE = 128        # minibatch size, i tried multiple values, this worked ok \
GAMMA = 0.99            # discount factor, from https://arxiv.org/pdf/1509.02971.pdf \
TAU = 1e-3              # for soft update of target parameters from https://arxiv.org/pdf/1509.02971.pdf \
LR_ACTOR = 1e-3         # I didn't use the value from the paper, but a value 10 time larger and it worked ok \
LR_CRITIC = 1e-3        # Larning rate of the critic from https://arxiv.org/pdf/1509.02971.pdf \
WEIGHT_DECAY = 0        # L2 weight decay from - using 0 rather than value found in the paper as learning was a problem \
UPDATE_EVERY = 20       # timesteps between updates, I found this suggestion in the Udacity comments and worked well \
NUM_UPDATES = 1         # num of update passes when updating, I found this suggestion in the Udacity comments and worked well \
EPSILON_DECAY = 1e-6    # decay for epsilon above
```
### Training
```
n_episodes=600 \
steps_per_epidoes=1000
```

### Future research
- Fine tune the Hyperparameters, in particular the size and depth of the neural networks to see how they impact the learning and how batch normalization is used.
- Adopt Trust Region Policy Optimization (TRPO) or Truncated Natural Policy Gradient (TNPG) and compare the results
- Try the Distributed Distributional Deterministic Policy Gradients (D4PG) algorithm as another method for adapting DDPG for continuous control.


