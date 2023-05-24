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

The main task of the environment is to expose tunable parameters to the other domains, 
morphology optimization uses these parameters to i.e. increase the disc size, tweak the number of segments per arm, ...
Controller optimization uses i.e. wrappers and configuration parameters to extend the environment and to set the target location.

Some of the most important changes include a more realistic environment, all kinds of manipulations for the target position,
exposure of important functionality and the integration of HD quality cameras.

### Realism
To make the simulation more realistic, we changed the medium of the simulation to water.
We increased the density to 1.000 and the viscosity to 0.0009. 
By doing this the brittle star lost the ability to jump around in the medium.
MuJoCo also allows us to create currents with wind, although this is not used by the other domains, the environment still provides this parameter (for future research).

### Target positions
The simulation environment provides multiple parameters to control the location of targets.
First of all, the distance from the origin can be set, knowing that the robot spawns at the origin, we can conclude that this is also the distance to the robot.
By setting this parameter the rotation of the target will be randomised but the distance will remain the same.

If the controller optimization wants to randomize this location, they can provide a range with a minimum and a maximum value.
The target will spawn at a random angle but at a distance in the provided range.

A final way to set the targets is by placing them along a circle at a certain distance, 
the radian of this circle can be set by the previously described arguments but only the initial angle of the first target will be randomised.
The gif below this paragraph gives an example of a circle with a radian of 5 and with 5 different target locations.
The angle of the first target will be between 0 and 2*PI divided by the number of target locations.
![Target locations evenly spread around circle](/images/pos.gif)

### Miscellaneous functionality

- FixedParam instead of Parameter
- Env wrappers
- z-angle removed
- Passing custom reward functions
- Passing controller specification

### Cameras 
The simulation environment adds two cameras so that we can record our findings in an efficient manner.
This means that for the normal locomotion environment there are now 3 cameras, 
the first one (index 0) is the free camera, the second one (index 1) is one that tracks the brittle star 
and the third camera provides a topdown view of the target. 
Our other environment, the Terrain Generation Environment only has the two latter cameras. 
For this environment the indices are 0 and 1 instead of 1 and 2.
The quality of these cameras is set to 1920x1080 during the initialization of the environment.
When rendering a video one can pass the width and height parameters to the `render` method to actually record with this quality.

#### Camera that tracks the brittle star (1920x1080):
![Tracking camera](/images/camera1.png)

#### Camera that gives a topdown view of the target (1920x1080):
![Topdown view camera](/images/camera2.png)

## Terrain Generation
Apart from the original `LocomotionEnvironmentConfig` we also provide a `TerrainGenerationEnvironmentConfig`.
This configuration class returns an environment with a *custom terrain*. 
These terrains can be provided by the user as png files. These terrains should be topdown greyscale images of the terrain.
The user can either provide these individually or as a map, if a map is provided a terrain will be chosen randomly.

Another possibility is *random generation*, these maps are generated using perlin noise and can be used to make an infinite amount of random terrains.
By using perlin noise the difference between 2 adjacent pixels is minimized to avoid steep climbs or drops.

We also provide *6 default heightmaps* that are ordered by increasing difficulty, 
the size and height of these maps can be adjusted by providing the correct arguments.
All of these are generated with png images that can be found in the simulation environment repository.
![Topdown view camera](/images/all-levels-trans.png)

The robot should spawn a little higher than the floor and fall down to avoid clipping, 
if the terrain is too rough and the robot does get stuck the *spawn-height* can be altered by providing the correct arguments.

## CLI, builders and JSON schemas
To ease the process of making a robot with the required specifications we added
