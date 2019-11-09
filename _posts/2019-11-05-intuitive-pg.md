---
id: 7
title: 'Intuitive Explanation of Policy Gradient'
date: 2019-11-05T21:56:19+00:00
author: arun
author_profile: false
excerpt: "In this post I will give an intuitive explaination of Policy Gradient so that it becomes easier to implement. Much of what is known to a developer about the Policy Gradient method in Reinforcement Learning is still not very clear given the kind of language used in the research papers and the blog articles."

permalink: /posts/2019/11/intuitive-pg/
categories:
  - Academic Works
  - Unpublished
tags:
  - reinforcement-learning
  - policy-gradient
  - artificial-intelligence
---
<html>
<head>
<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.0/MathJax.js?config=TeX-MML-AM_CHTML">
</script>
<head>
<body>

In this post I will give an intuitive explaination of Policy Gradient so that it becomes easier to implement. Much of what is known to a developer about the Policy Gradient method in Reinforcement Learning is still not very clear given the kind of mathematical language used in the research papers and the blog articles. It might be confusing as to how the algorithm is applied to discrete and continuous actions with very similar structure of the policy gradient. The reason for writing this article is to put down my thoughts and understanding of the topic and hopefully give a helpful insights to the reader. So let's get started.<br/><br/>
<!--more-->

To summarize Policy Gradient in one line, it can be said that it is <b>the method by which we try to maximize the likelihood of the sequence of actions that cause favourable outcomes</b>. These favourable outcomes can be winning a game of DOTA, landing a rover on moon, achieving walking gait by a robot, etc. And how do we maximize the likelihood of favourable outcomes ? We will come to that later but first let's start from the begining. The policy in policy gradient is represented (mostly) by a function approximator like a neural network. How do we model a neural network? We model it using network parameters `\theta` that is nothing but network weights. We can call this policy network as `\pi_theta` (policy π parameterized with `\theta`). The input to this network (policy) is the current state (`s_t`) and the output is a <b>probability distribution</b> over next action.

<h2> Policy Gradient for Discrete Actions </h2>

{% include image.html img="images/2019/pg/policy-network.png" title="policy" caption="A neural network policy. Being stochastic in nature, it doesn't predict the exact actions to take but the probability distribution over possible actions." url="http://arunkrweb.github.io/images/2019/pg/policy-network.png" %}

Let's take an example of Pong. There are two discrete actions - UP and DOWN. At any instant, we have to chose either of the two actions. There can never be a scenario where we have to chose both the actions simultaneously (a case where we use joint probability distribution is discussed later).<br/><br/>

So, the agent observes the current state and the policy network outputs a probability over two possible actions : p(UP|state) and p(DOWN|state) The output can be modelled as a softmax function where the sum of the outputs equals 1. The next step would be to <b>sample</b> from this output. This makes our policy a stochastic one. The way it is usually accomplished is something like {% highlight python %} np.random.choice([0, 1], 1, p=[prob, 1-prob]) {% endhighlight %} where the first argument is the choice of actions we have (0=UP, 1=DOWN), second argument is their corresponding probabilities predicted as explained before. 
<br/><br/>

Let's say probability of UP is 0.6 and probability of DOWN is 0.4. During sampling, it's possible to have sampled the action DOWN (ofcourse with lesser probability of occurance). In the next step, this action is executed and the next state and reward is observed. The process repeats till the end of the episode and we maintain a history of the transitionss [current_state, action, next_state, reward] through this episode. At the end of it, the discounted sum of the rewards is calculated and we'll use it for updating our policy.<br/><br/>

The update to the network happens by backpropagating the <b>gradient of the product of the log probability of the trajectory and the discounted sum of rewards</b>.

$$
\nabla_{\theta}\log{P(\tau^{(i)};\theta)R(\tau^{(i)})}
$$

In actual implementation, we gather `m` such episodes (trajectories) and find average of the gradient of the above form and backpropagate the result. Let's dive into the term `\log{P(\tau^{(i)})}` from the above equation. This term is known as <b>log-likelihood</b>. It measures the likelihood of the observed trajectory or the sequence of actions. The likelihood, in other words is nothing but probability distribution. <b>If this term is high, the corresponding sequence of actions are more likely to occur or to be observed.</b> Our goal is to update our policy network weights in such a way that the log-likelihood of favourable sequences/trajectories/episodes increase and that of the unfavourable ones decrease. Further simplifying the expression as follows we have :
$$
\nabla_{\theta}\log{P(\tau^{(i)};\theta)} = \nabla_{\theta}\log{[\prod_{t=0}^{H}P(s_{t+1}^{(i)}|s_{t}^{(i)}, u_{t}^{(i)}).\pi_\theta(u_{t}^{(i)}|s_{t}^{(i)})]}
$$

Note that individual trajectory `\tau^{i}` is composed of a state-action sequence `s_0, u_0,..., s_H, u_H`. From the multiplication rule in probability it can be said that the probability of a trajectory is the product of the probabilities of each transition that occurred from initial state `s_0` to be the terminal state `s_H`.Now how do we calculate probability of each transition? The probability of a transition is the product of the probability of ending up in the next state `s_{t+1}` from current state `s_t`(dynamics model) and the probability of taking an action `u_t` given current state `s_t` under the policy `\pi_\theta`. We can rewrite the above expression as follows:

$$
= \nabla_\theta[\sum_{t=0}^{H}\log{P(s_{t+1}^{(i)}|s_{t}^{(i)}, u_{t}^{(i)}) + \sum_{t=0}^{H}\log{\pi_\theta(u_{t}^{(i)}|s_{t}^{(i)})}}]
$$

As the first term doesn't depend on `\theta`, the above expression boils down to:

$$
= \sum_{t=0}^{H}\log{\pi_\theta(u_{t}^{(i)}|s_{t}^{(i)})}
$$

So the final expression doesn't depend on the dynamics model. Hence applying policy gradient is so convenient.

<h3>Log-likelihood for discrete actions</h3>

The term `\log\pi_\theta(u_{t}^{(i)}|s_{t}^{(i)})` is read as the the log of the probability of the action chosen (executed) given the current state. And we implement it as follows:

{% highlight python %}
prob = 0.6 # 1-prob = 0.4
actions = [0, 1]
# if action_selected = 1
log_likelihood = 0.log(prob) + 1.log(1-prob) # fake labels : 0, 1

# if action_selected = 0
log_likelihood = 1.log(prob) + 0.log(1-prob) # fake labels : 1, 0
{% endhighlight %}

<h2> Policy Gradient for Continuous Actions </h2>

{% include image.html img="images/2019/pg/policy-network-continuous.png" title="policy" caption="A neural network policy that outputs a probability distribution over actions." url="http://arunkrweb.github.io/images/2019/pg/policy-network-continuous.png" %}

The policy network for the continuous action space is similar to the discrete actions except that the probability distribution is not discrete but continuous and is defined by the parameters `\mu` and `\sigma` . These parameters are the output of the network. In case where there are multiple outputs (multi-dimensional actions, or multivariate outputs), the joint probability distribution is found by multiplying individual probability density functions. In case where the output is a single action (e.g, one motor torque), the likelihood (or probability distribution) is very straightforward. At every time-step in the episode, we sample action `x` from this distribution whose mean is `\mu` and std. deviation is `\sigma`. The way the sampling is done may look as follows:

{% highlight python %}
action = torch.normal(mu, torch.exp(log_sigma))
{% endhighlight %}

The likelihood of the sequence of actions or the probability distribution is represented as follows:

$$
= \frac{1}{2\pi\sigma^{2}}e^{-\frac{(X - \mu)^2}{2\sigma^{2}}}
$$

where X = the sequence of sampled action over one episode and `\mu` is the sequence of the predicted action (mean) by the policy network.

<h3>Log-likelihood for continuous actions</h3>
The log of the above expression is the log-likelihood and it is of the following form:

{% highlight python %}

log_likelihood = -log_stddev - (x - mu)**2 / (2 * torch.exp(log_stddev)**2)

{% endhighlight %}


<h2>What if we have multi-dimensional actions?</h2>

There can be scenarios where there are multiple actions occuring simultaneously. We might be required to build an agent that handles multiple discrete actions (e.g, GAS = ON, TURN_LEFT = ON, TURN_RIGHT = OFF, GO_FORWARD = OFF) or multiple continuous actions (e.g, control a 6 DOF arm that has 6 motors to control simulataneously). Let's take these two cases one by one.

<h3>Multi-dimensional discrete actions</h3>
The log-likelihood expression of the case of two actions (UP & DOWN as before) will change a bit.

{% highlight python %}

prob_gas = 0.6
prob_go_left = 0.8
prob_go_right = 0.2
prob_go_forward = 0.1

 # fake labels: [1, 1, 0, 0] => GAS, LEFT, RIGHT, FORWARD
# if action_selected = 1
log_likelihood = 1.log(prob_gas) + 1.log(prob_go_left) + 0.log(prob_go_right) + 0.log(prob_go_forward)

{% endhighlight %}

If we go one step back, the expression of the log_likelihood as written above will become clear. The log of likelihood is expressed as the sum of the log. prob terms. If we remove log terms from both sides of the equation, the expression on the right hand side becomes the <b>product of probabilities of individual actions</b> hence, we can say it is sort of joint probability distribution over actions.

<h3>Multi-dimensional continuous actions</h3>

Before estimating the log-likelihood, we have to find the corresponding probability distribution and the way we do it is by finding joint probability distribution. We do this because the multi-dimensional actions are independent of each other for e.g, in the case of a 6 DOF arm control that has 6 motors to control simulataneously. The final joint probability distribution is a multi-variate probability distribution that is the resultant of the <b>product of individual probability distribution of each action</b>. 

$$
 = \frac{1}{(2\pi)^{(n/2)}|\sum^{1/2}|}\exp(-\frac{1}{2}(x - \mu)^T\sum^{-1}(x - \mu))
$$

Note that for single action, the above equation boils down to the single-dimensional continuous action space case as explained in previous sections. The log of the above equation gives the log-likelihood of form very similar to the one discussed as in the case of single-dimensional action.

<h2>Maximize the likelihood by updating weights</h2>

The next step from here is to maximize this log-likelihood expression. And the way this can be done is by using various optimization algorithm. One such algorithm is gradient ascent. For that, we find the derivative of our log-likelihood term with respect to the network parameters (weights) and backpropagate the term through the network. 

{% highlight python %}

weights = weights + (learning_rate * derivative_of_the_log_likelihood_wrt_weights)

{% endhighlight %} 

<h2>Acknowledgement</h2>
I'd like to thank Pradeep Rengaswamy (IIT Kharagpur) for contributing to the discussions that led to the content of this article.


</body>
<html>



