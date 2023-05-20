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
We chose for PPO since it's know to be stable and learn rather quickly. 

### Goal
The goal is to create a controller that can steer directly towards it's target wihtout rotating it's so called frontside towards the target.
This would mean that our 5 arm Brittlestar could use anyone of its five arms as it's frontside. The goal would be for this leading arm to be lifted up in the arm while the other four arms make the robot move towards the target. This would result in a Brittlestar that moves in a quick and very natural way.

### StableBaselines
We use [StableBaselines](https://stable-baselines3.readthedocs.io/en/master/modules/ppo.html) as the framework for our PPO reinforcement learning.
This allows us to perform PPO without having to fully implement this ourselves. We use PPO with a mutli input policy and a ReLu activation function. 
DESCRIBE HYPERPARAMETERS

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
Our reward functions needs to make the connection between our angle observation and a good action that should be rewarded. We do this by rewarding every action that places the robot closer to the target. This is simply done by returning a reward based on the difference in distance to the target after the action and before. In our original reward we also greatly rewarded our Brittlestar when it reached the target. However it turns out that PPO is not good at handling a non-deterministic reward function that overrewards some results. ??how to describe this??  
Our final reward function is simply the signed value of the square difference of the target distances.

#### Training difficulties
In our effort to create a succesfull PPO controller we stumbled uppon a series of difficulties. We will shortly describe some of most important ones here.

##### training is very sensitive

##### hyperparameters
The PPO algorithm from StableBaselines has an extended set of hyperparamters that can be tweaked to impact the training results. 
We started off with the default hyperparamters and tweaked our way in to a good working result. We changed the entropy coefficient for the loss function from 0 to 0.0001. This improved..., INSERT GRAPH
We also ended up changing the n_steps, which was needed for a more discretized learning approach as will be described in the next section.

##### discretized locations in an episode
The biggest reason why our PPO wasn't able to give good results on an extended set of testing locations was due to the way targets spawned during training.



- simulation time 8s vs 20s