---
title: "Morphology Optimisation"
date: 2023-05-18T00:49:02+02:00
draft: false
---

## The Body
When we started with this project the robot could only change it's body by mutating the stifnesses of the joints. These changes influence how the robot can move, but these are not visible the the eye when ignoring the movement. We had set a few goals for ourselves to make the robot more modular in following ways:

- Mutable number of segments
- Each segment should be able to modify its dimensions
- The central disk should be able to change dimensions to increase performance

These goals are achieved using a **graph representation**. By doing this we can iteratively unfold recursive loops and add or remove segments with ease. This allows the robot to shape itself based on how it performs.

During training we give complete freedom to how it should be shaped. Based on how a Brittle Star looks in real life we expect that segments further away from the central disk become smaller. This is indeed something we see after training.

![test](/images/robot_body.png)


## CPG


#### Smoothening
This is a test of how this could look

## The evolutionary algorithm

Our evolutionary algorithm has a few features to increase the performance of the genomes over time.

#### Mutation
This was given by the start code and further improved with our extra additions to the body and CPG.

#### Cross-over
We added this to enable the possibility of creating new genomes that are a mix between 2 parents. This is part of a standard evolutionary algorithm but was not yet implemented in the beginning. Because of this, this was 1 of our first steps in improving the evolutionary algorithm.

#### Finetuning
We noticed that later on during training we kept making big steps in how we mutated the genomes. This is not wanted since later on in the evolution the body has already evolved to a better place. To make optimal use if this we **decrease** the mutation size during evolution. This has the effect that we only make small precise changes at the end of the evolutionary algorithm. This improves the performance of the resulting robot

## Multiple environments

## Results
another line