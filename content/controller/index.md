---
title: "Controller"
date: 2023-05-18T00:49:07+02:00
draft: false
---

## QLearning
At the start of the project we began by following the tutorials about QLearning to try to create a controller for a default BrittleStar.
We ran against a few errors in this tutorial which were mostly related to typos and return type issues from typing the code from the videos. After finding the bugs, which was a tedious job that took watching all the video's over and over again, we were able to fix the qlearn controller.  
This resulted in a simple qtable for our controller that had 3 actions (straight, left, right) for a simple two arm morphology.  
  
We changed a few hardcoded things and added some extra features:
- define actions based on number of arms
- generate arm amplitudes based on number of arms
- define states to divide the z-angle equally among the number of actions
- discretize observations based on states

Due to these changes we were able to easily change the number of arms to try different morphologies and different numbers of actions.  

In order to make the progress during training trackable we added some extra features to the WandDB logger.  

We were able to create a good working controller by changing the epsilon value in the EvalDuringLearningEvaluationCallback. At the end of every evaluate step, the epsilon values was recalculated by looking at the final reward. A high final reward resulted in a lower epsilon value for the next training loop and vice versa.  
The video below shows the final result of our qlearning for a default two arm morphology.  
{{<youtube j1NyO1EOkjg>}}


## PPO 
After creating a qlearning based controller, we turned our attention to more complex reinforcement methods for our controller.
We chose for ppo since it is supposed to learn rather quickly. 

### Goal
The goal is to create a controller that can steer directly towards it's target wihtout rotating it's so called frontside towards the target.
This would mean that our 5 arm Brittlestar could use anyone of its five arms as it's frontside. The goal would be for this leading arm to be lifted up in the arm while the other four arms make the robot move towards the target. This would result in a Brittlestar that moves in a quick and very natural way.

### StableBaselines
We use [StableBaselines](https://stable-baselines3.readthedocs.io/en/master/modules/ppo.html) as the framework for our ppo reinforcement learning.
This allows us to perform ppo without having to fully implement this ourselves. We use ppo with a mutli input policy and a ReLu activation function. 

### Implementation
#### Wrappers
#### Observations
We simplified our observationspace in order to simplify the model input and to speed up the training process. Our observationspace only contains a single parameter namely the normalized z angle towards the target. This is the angle between the fixed frontside of the robot and the target in a XY-plane. This means that the robot has no motion of its distance from the target, only a direction.

#### Actions
Since we want our Brittlestar to be able to use every arms as its leading arm, we define the same actions for every arm.
We tried two options for the actions:
- only allow straight ahead movements

The idea behind these actions is that it can go straight towards its target and steer lightly by alternating its leading arm. 
- allow three movement options per arm, straight, left and right

In the other option the leading arm wouldn't change as much since it can also turn the Brittlestar slighty to keep itself on course towards the target.

#### Reward
Our reward functions needs to make the connection between our angle observation and a good action that should be rewarded. We do this by rewarding every action that places the robot closer to the target. This is simply done by returning a reward based on the difference in distance to the target after the action and before. In our original reward we also greatly rewarded our Brittlestar when it reached the target. However it turns out that ppo is not good at handling a non-deterministic reward function that overrewards some results. ??how to describe this??  
Our final reward function is simply the signed value of the square difference of the target distances.

### Training difficulties
In our effort to create a succesfull ppo controller we stumbled uppon a series of difficulties. We will shortly describe some of the most important ones here.

#### training is very sensitive
Right from the start of our first ppo training we noticed that it was very sensitive. The training reward was very unstable and did not always end on it's highest value. In order to be able to compare different training settings we used longer trainingruns and went for a default of one million timesteps.
The graph below is a good representatio of this unstable training, this was mostly due to the reward function.
![Training graph with irregular path](/images/chart_sensitive_training.png)

#### hyperparameters
The ppo algorithm from StableBaselines has an extended set of hyperparamters that can be tweaked to impact the training results. 
We started off with the default hyperparamters and experimented our way in to a good working result. We ran many tests, often with not very good results. 
We ended up changing the entropy coefficient for the loss function from 0 to 0.0001. This improved our overall training performance as can be seen in the graph below.
![Training graph with different entropy on 1 million timesteps](/images/chart_controller_entcoef.png)
We also ended up changing the n_steps, which was needed for a more discretized learning approach as will be described in the next section.

#### discretized locations for an epoch
The biggest reason why our ppo wasn't able to give good results on an extended training run with a million timesteps was due to the way targets spawned during training. The environment created targets in a random fashion, the distance from the target was fixed at 5 but the angle was random. This meant that during training a target spawned randomly on a circle with radius 5 around the brittlestar robot.
Since the n_steps parameter of ppo was set at 2048 and the max runtime of a training at 20s, a single epoch would see only 4 target locations. It was thus possible that this epoch would see only targets in the same half circle or even the same quadrant leading to overfitting and unstable learning.
We fixed this issue by discretizing the target locations so that every epoch would see a full circle of targets. Herefore the n_steps parameter had to be calculated as a function of the simulation time and the number of target locations. This led to way better results and is by far our biggest improvement towards a good ppo controller. We experimented with various numbers of target locations, INSERT GRAPH BELOW

#### simulation time 8s vs 20s
Another issue we stumbled upon was the fact that longer simulation times led to a more zigzaging robot. Ppo is build to maximize the reward during its full simulation time, so if the simulation time would be longer than necessary for the fastest path it would induce creativity to rake up extra rewards.
This is exactly what happend with our initial simulation time of 20 seconds. This created a controller that took a zigzaging path towards the target since more steps would mean more rewards. 
By reducing our simulation time to 8 seconds, ppo learned to take a more direct path towards the target. Within this simulation time, the robot could almost never reach the target, however this was not necessary since no reward was given upon reaching it. In fact this is a good thing since our reward was the square of difference in distance towards the target, taking big steps towards the target resulted in big rewards. The path of maximum reward over the full simulation time was now the path straight on to the target, which was exactly our goal.
![Training graph showing difference in simulation time, 20s vs 8s](/images/chart_controller_simtime.png)

### Result
VIDEO and discuss result
