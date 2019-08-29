# Model Documentation

The computation of trajectories that meets all the specifications, namely sticking to the speed limits, no big acceleration and jerk, no collisions, staying in lanes and changing lanes are all done in `main.cpp`.

## Speed limits

The allowed speed limit of 50mp/h is hardcoded as a value in the code. We set the reference velocity to `49.5` and we make sure this is not exceeded:

[` 54:   double TARGET_VEL = 49.5;`](https://github.com/chaupow/CarND-Path-Planning-Project/blob/master/src/main.cpp#L54)
[`239:   if (ref_vel > TARGET_VEL) ref_vel = TARGET_VEL;`](https://github.com/chaupow/CarND-Path-Planning-Project/blob/master/src/main.cpp#L239)

## No big jerk

Using the spline library, we create smooth splines that start at the car's current position and take into account previous positions (and therefore implicitly its heading) and follow the center line of the lane we want to car to be in further ahead. We then interpolate points on the spline to provide a trajectory for the car. Because the splines are smooth, the trajectories are also smooth and do not introduce uncomfortable jerk.

## No big accelerations

We set a maximum difference in velocity in the code that we allow to happen every time step:

[`55: double MAX_VEL_DIFF = .224;`](https://github.com/chaupow/CarND-Path-Planning-Project/blob/master/src/main.cpp#L55)

Whenever we set any change of velocity (whether it's slowing down or accelerating), we only change the velocity by this step to ensure there are no big jumps in acceleration.

## No collisions

Using the information given to the car by sensor fusion, the car [detects whether there is a car in front of it](https://github.com/chaupow/CarND-Path-Planning-Project/blob/master/src/main.cpp#L123-L127). If there is one and it's speed is slower, [the car will slow down](https://github.com/chaupow/CarND-Path-Planning-Project/blob/master/src/main.cpp#L159) in order to not crash into the car.

## Staying in lanes

As described in the measures to ensure no big jerks, we use splines that are similar to the road geometry. We achieve this by using the Frenet coordinates, keeping the [`d` value in the center of the lanes](https://github.com/chaupow/CarND-Path-Planning-Project/blob/master/src/main.cpp#L197) we want to be in and therefore staying in lanes.

## Changing lanes

By using splines that go through center of lanes we want to be in, we find smooth trajectories that changes lanes almost for free. We only have to set waypoints that the splines should go through to points on our target lane. The car changes lanes [only if there is a car in front of it](https://github.com/chaupow/CarND-Path-Planning-Project/blob/master/src/main.cpp#L143-L155) that goes slowly. To ensure, the lane changes does not create collisions, sensor fusion information is used to observe [if there are cars on the left or right lane](https://github.com/chaupow/CarND-Path-Planning-Project/blob/master/src/main.cpp#L128-L138). Only if a lane change can be done safely, it will be considered. There are no multiple candidates for trajectories and no cost functions to pick the best one. if both, a right and left lane change is possible, [the one to the left lane is taken](https://github.com/chaupow/CarND-Path-Planning-Project/blob/master/src/main.cpp#L145).