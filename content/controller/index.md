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
