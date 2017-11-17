---
layout: post
title:  "[WIP] Analysis of a Four-Layer Series-Coupled Perceptron. II"
excerpt: "Understanding the maths of Block, Knight and Rosenblatt"
date:   2017-11-17 11:00:00
mathjax: true
---
In this post we will study and attempt to understand the maths in the paper ["Analysis of a Four-Layer Series-Coupled Perceptron. II"](https://journals.aps.org/rmp/pdf/10.1103/RevModPhys.34.135). The paper discusses a Multilayer Perceptron not too dissimilar from the neural networks of today; trained in an unsupervised setting. It could even be argued that this paper presents one of the earliest examples of what may be described as deep learning. It also attempts to proves several properties of the network using maths that is obfuscated to say the least. We will attempt to understand this maths and show all of the derivations and transitions along the way.

# Introduction

The introduction of the paper discusses a 'cross-coupled' network, analagous to having a layer which is fully connected to itself. In a previous paper this network was studied and shown to have the ability to generalize over its inputs (stimuli in the paper). The authors then go on to introduce the main focus of the paper; to prove this property in a more rigorous fashion for a simpler 'series-coupled' network. A 'series-coupled' network is a more traditional perceptron without the recurrent connections.

# The Model

In this section the authors introduce the model that will be studied in the paper. As we have already stated, this is a 'series-copupled' network. In other words, a feed-forward neural network. Here we have a sensory field that is arbitrarily connected to an 'Associator Unit' (read: neruon) in the first layer. A way to model these connections is to think of the inputs as being fully connected (for each pixel) to the first layer with weights that are all either \\(1\\), \\(0\\) or \\(-1\\) (predominantly zero to model sparse connectivity).

Next we have two 'Associator' layers. The authors state that units in the first layer are in one-to-one correspondence with those in the second layer. Interpret this to mean that each layer has an equal number of neurons. The activation of the units is a step function with threshold \\(\theta\\). The threshold is constant for the operation of the network, it does not change with time. It is not stated whether \\(\theta\\) is defined globally or layer-wise. However, this has no effect on the maths.

When a neuron in the first associator layer fires (i.e. its total input from the sensory field is greater than \\(\theta\\)), it transmits a signal of \\(\theta\\) to the corresponding neuron in the second layer. It also transmits some weight to all neurons in the second layer (including its counterpart). We can picture this as the first layer being fully connected to the second layer with weights \\(\upsilon_{\mu\nu} \in \Upsilon\\) where the all values are initially \\(0\\). The motivation for having the additional \\(\theta\\) transmission is not entirely clear. Certainly, the necessary maths is more complex as a result and there is no given motivation for it. It seems that this transmission is there purely to ensure that the neurons in the second layer have some activation for training puroses.

Finally, we have the response layer. This is not mentioned in any great detail in the paper but is simply stated as a layer with weights that can be trained via re-inforcement. In other words, we may ammend the connections and weights in this layer arbitrarily in order to get a particular neuron to fire for a particular class. These days of course, we would use gradient descent back-propogation for this. Although re-inforcing the weights based on an error computation can produce the same result for a single layer.

## Training

The training of the neural network (described as changes to \\(\Upsilon\\) over time) consists of two main update rules (with corresponding learning rates \\(\eta\\) and \\(\delta\\)).

The first rule is derived from the well known Hebbian learning principle in neuroscience (also known as Hebb's postulate). This rule is often characterised by the phrase "neurons that fire together, wire together" and states that the 'strength' of a synapse which connects two neurons is increased if the pre-synaptic neuron fires before the post-synaptic neuron. That is, if the pre-synaptic neuron contributes to the activation of the post-synaptic neuron, we increase the weight. In the paper, the authors define this as follows. If some first layer neuron \\(a_\mu^I \in A^I\\) is active at time \\(t\\) and some second layer neuron \\(a_\nu^{II} \in A^{II}\\) is active at time \\(t + \Delta t\\) then the connection weight (\\(\upsilon_{\mu\nu}\\)) recieves an increment of \\(\eta\times\Delta t\\).

The second rule is merely a decrement rule. If we are to only increment weights throughout operation of the model, these weights will tend to explode as time goes on. To control this, at each time step, weights are decremented by \\(\delta\times\Delta t\times\upsilon_{\mu\nu}\\). Note that as a consequence of this, a larger weight will decrement by a larger amount. Thus, for given learning rates (\\(\eta\\) and \\(\delta\\)) there will be a theorhetical maximum value for the weights.

# Analysis

For this section we will focus on the maths, deriving each equation as we go. In equation 1 we can omit the index of the current stimulus (\\(i\\)) and instead simply say that we are definining the total input to a given node in the second layer (\\(\alpha_\nu\\)) for some input stimulus \\(i\\).

\begin{equation} \tag{1}
   \alpha_\nu\(t\)=\theta e_\nu + \sum_{\mu} \upsilon_{\mu\nu}\(t\) e_\mu
\end{equation}

In words, this equation states that we have an input of \\(\theta\\) if the corresponding layer one neuron is active and also an have input of \\(\upsilon_{\mu\nu}\\) for each active neuron (\\(\alpha_\mu\\)) in the first layer.

Equations 2, 3 and 4 simply redifine the \\(\theta\\) signal as a bias term \\(\beta_\nu\\) and the weighted sum as a function of time \\(\gamma_\nu\(t\)\\).
