---
layout : default
title : Build a 2D Robotic Car!
---

In the last blog post I explained what is a particle filter and how we can build one using pygame and python.In this post, I will walk you through the steps to build a 2D robotic car and get it running using PD control.

I am building visual demonstration of the problems given in Udacity's Course on Artificial Intelligence for Robotics.So, let's begin!

## Bicycle Model

Our robotic car has two fixed rear wheels and two front steereable wheels.The vehicle motion can be explained by splitting the vehicle vertically so that we get something called a bicycle model as shown below.

{% include image.html img="images/2017/robotic-car/next_condition.png" title="Localized car" caption="Small green dot is actually 1000 particles on top of each other localizing the car." %}

This model is useful in understanding the vehicle motion especially when taking a turn.Let's say our car starts to take a right turn. Assuming the steering angle to be `alpha`, the radius of the circle that the rear wheel makes while taking the turn to be `R`, and the distance between the front and rear wheel be `L`.

<html>
<title></title>
<head>
<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.0/MathJax.js?config=TeX-MML-AM_CHTML">
</script>
<head>
<body>
When $a \ne 0$, there are two solutions to \(ax^2 + bx + c = 0\) and they are
$$x = {-b \pm \sqrt{b^2-4ac} \over 2a}.$$
</body>
<html>