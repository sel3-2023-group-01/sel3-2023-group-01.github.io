---
title: "Environment"
date: 2023-05-18T00:46:27+02:00
draft: false
---
## Morphology
The morphology did not change a lot as opposed to our starting point, 
the most changes are related to the tendons of the robot. 
Parameters for the amount of tendons, the stretch factor and contraction factor have been added.
To place these tendons and configure their attachment points an `equidistant point` function has been developed.
This function generates points on a circle around a given position around a given axis.
It is used for placing the arms, tendons and later on for the target locations. 
An important quirk of this function is that the first point can be set at an offset, 
the tendon start will always be at the top of the circle (at + PI/2), this can be seen in the gif.

![Tendons evenly spread around circle](/images/tendon.gif)

The simulation environment also has an option to place the tendons between segments,
this is not used by the controller and morphology but it is available because it is more realistic.
## Environment
- Air to water (viscosity, wind, density)
- z-angle removed
- FixedParam instead of Parameter
- Env wrappers
- Passing custom reward functions
- Passing controller specification
- Target distance randomization
- Target distance in range
- Custom cameras
- Target spawn on equidistant points (fixed distance)
## Terrain Generation
- Terrain using perlin noise & height maps
- Default height maps
- Interpolation
- 

## CLI, builders and JSON schemas
