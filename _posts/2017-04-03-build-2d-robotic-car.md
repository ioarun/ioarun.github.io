---
layout : default
title : Build a 2D Robotic Car!
---

In the last blog post I explained what is a particle filter and how we can build one using pygame and python.In this post, I will walk you through the steps to build a 2D robotic car and get it running using PD control.

<!--more-->

I am building visual demonstration of the problems given in Udacity's Course on Artificial Intelligence for Robotics.So, let's begin!

## Bicycle Model

Our robotic car has two fixed rear wheels and two front steereable wheels.The vehicle motion can be explained by splitting the vehicle vertically so that we get something called a bicycle model as shown below.

{% include image.html img="images/2017/robotic-car/bicycle-model1.png" title="Bicycle Model" caption="Bicycle model - local coordinates" %}

This model is useful in understanding the vehicle motion especially when taking a turn.Let's say our car starts to take a right turn. Assuming the steering angle to be `alpha`, the radius of the circle that the rear wheel makes while taking the turn to be `R`, and the distance between the front and rear wheel be `L`.Then the radius `R` can be clearly stated as follows.

<html>
<head>
<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.0/MathJax.js?config=TeX-MML-AM_CHTML">
</script>
<head>
<body>
 $$R = {L \over \tan \alpha;}.$$
</body>
<html>

{% include image.html img="images/2017/robotic-car/bicycle-model2.png" title="Bicycle Model" caption="Bicycle model - global coordinates" %}

We are interested in finding what would be the position and orientation of our car after it rotates by an angle `beta`.Let initial orientation with respect to a global `x = 0` be `theta` and the initial position be `(x, y)`.Then from the above figure, it is clear that the center of the circle which the car makes while steering, can be written as -

<html>
<head>
<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.0/MathJax.js?config=TeX-MML-AM_CHTML">
</script>
<head>
<body>
 $$Cx = {x \+ Rsin \theta;}.$$
 $$Cy = {y \- Rcos \theta;}.$$
</body>
<html>

Since we are using Pygame to build this car, the second equation above changes a bit. As the origin of a pygame screen would be on the top left corner and will increase as we go right or downwards, going up would mean decrease in the value.Hence, the second equation becomes

<html>
<head>
<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.0/MathJax.js?config=TeX-MML-AM_CHTML">
</script>
<head>
<body>
 $$Cy = {y \- Rcos \theta;}.$$
</body>
<html>
