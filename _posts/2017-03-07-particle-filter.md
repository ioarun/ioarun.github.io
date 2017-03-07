---
layout : default
title : Robot Localization using Particle Filter
title-img : images/2017/particle-filter/particle-filter.gif
---

Robot world is exciting! For people completely unaware of what goes inside the robots and how they manage to do what they do, it seems almost magical.In this post I will try to scratch the surface of one very important part of robotics called robot localization.

<!--more-->

## What is localization?

Imagine that you are blind folded and somehow all you know about the surrounding is as far as you can stretch out your hands to touch the surrounding obstacles.This would give you an approximation of the distance of an obstacle from you in the surrounding but not your orientation in global coordinate system(surrounding).This information is not sufficient to know your exact location in the surrounding.

Trying to find the near exact location of the robot in a given space using noisy sensor data is called as *localization*

Take another example of a moving car.The GPS might show the car coordinates pretty well but when it comes to relying on just GPS values for autonomous driving, it is not that pretty.The error may go as far as 10 meters which can be fatal to the car.

Localization makes sure that we reduce this error by as much as possible from the given sensor information and dynamics of the car.


## What is particle filter?

Consider the first example where you had to examine the surrounding by your hands.Suppose there are N of you and are randomly spread out in the surrounding and each of you have a distance vector which contains the distances from each of the obstacles.Since there are 1000 of yous, many of them would be in the vicinity of you near the same obstacle.Ofcourse the orientation may vary.Currently all the human beings are uniformally distributed.Until the next step which is called resampling.In resampling process only the humans near you would remain and others will disappear.You are said to be somewhat localized.

Formally, a particle is a discrete guess of where a robot may be located.Also, regularly, these particles are resampled from the distribution so that the particles consistent with the measurements *survive*.After successfull localization, the particles are collected in a region of high probability of the robot.

