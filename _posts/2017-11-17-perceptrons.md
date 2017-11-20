---
layout: post
title:  "[WIP] Analysis of a Four-Layer Series-Coupled Perceptron. II"
excerpt: "Understanding the maths of Block, Knight and Rosenblatt"
date:   2017-11-17 11:00:00
mathjax: true
comments: true
---
In this post we will study and attempt to understand the maths in the paper ["Analysis of a Four-Layer Series-Coupled Perceptron. II"](http://digitalcommons.rockefeller.edu/cgi/viewcontent.cgi?article=1013&context=knight_laboratory). The paper discusses a Multilayer Perceptron not too dissimilar from the neural networks of today; trained in an unsupervised setting. It could even be argued that this paper presents one of the earliest examples of what may be described as deep learning. It also attempts to prove several properties of the network using maths that is obfuscated to say the least. We will attempt to understand this maths and show all of the derivations and transitions along the way.

# Introduction

The introduction of the paper discusses a 'cross-coupled' network, analagous to having a layer which is fully connected to itself. In a previous paper this network was studied and shown to have the ability to generalize over its inputs (stimuli in the paper). The authors then go on to introduce the main focus of the paper; to prove this property in a more rigorous fashion for a simpler 'series-coupled' network. A 'series-coupled' network is much closer to a more traditional perceptron.

# The Model

In this section the authors introduce the model that will be studied in the paper. As we have already stated, this is a 'series-copupled' network. In other words, a feed-forward neural network. Here we have a sensory field that is arbitrarily connected to an 'Associator Unit' (read: neuron) in the first layer. A way to model these connections is to think of the inputs as being fully connected (for each pixel) to the first layer with weights that are all either \\(1\\), \\(0\\) or \\(-1\\) (predominantly zero to model sparse connectivity).

Next we have two 'Associator' layers. The authors state that units in the first layer are in one-to-one correspondence with those in the second layer. Interpret this to mean that each layer has an equal number of neurons. The activation of the units is a step function with threshold \\(\theta\\). The threshold is constant for the operation of the network, it does not change with time. It is not stated whether \\(\theta\\) is defined globally or layer-wise. However, this has no effect on the maths.

When a neuron in the first associator layer fires (i.e. its total input from the sensory field is greater than \\(\theta\\)), it transmits a signal of \\(\theta\\) to the corresponding neuron in the second layer. It also transmits some weight to all neurons in the second layer (including its counterpart). We can picture this as the first layer being fully connected to the second layer with weights \\(\upsilon_{\mu\nu} \in \Upsilon\\) where the all values are initially \\(0\\). The motivation for having the additional \\(\theta\\) transmission is not entirely clear. Certainly, the necessary maths is more complex as a result and there is no given motivation for it. It seems that this transmission is there purely to ensure that the neurons in the second layer have some activation for training puroses.

Finally, we have the response layer. This is not mentioned in any great detail in the paper but is simply stated as a layer with weights that can be trained via re-inforcement. In other words, we may ammend the connections and weights in this layer arbitrarily in order to get a particular neuron to fire for a particular class. These days of course, we would use gradient descent back-propogation for this. Although re-inforcing the weights based on an error computation can produce similar results for a single layer.

## Training

The training of the neural network (described as changes to \\(\Upsilon\\) over time) consists of two main update rules (with corresponding learning rates \\(\eta\\) and \\(\delta\\)).

The first rule is derived from the well known Hebbian learning principle in neuroscience (also known as Hebb's postulate). This rule is often characterised by the phrase "neurons that fire together, wire together" and states that the 'strength' of a synapse which connects two neurons is increased if the pre-synaptic neuron fires before the post-synaptic neuron. That is, if the pre-synaptic neuron contributes to the activation of the post-synaptic neuron, we increase the weight. In the paper, the authors define this as follows. If some first layer neuron \\(a_\mu^I \in A^I\\) is active at time \\(t\\) and some second layer neuron \\(a_\nu^{II} \in A^{II}\\) is active at time \\(t + \Delta t\\) then the connection weight (\\(\upsilon_{\mu\nu}\\)) recieves an increment of \\(\eta\times\Delta t\\).

The second rule is merely a decrement rule. If we are to only increment weights throughout operation of the model, these weights will tend to explode as time goes on. To control this, at each time step, weights are decremented by \\(\delta\times\Delta t\times\upsilon_{\mu\nu}\\). Note that as a consequence of this, a larger weight will decrement by a larger amount. Thus, for given learning rates (\\(\eta\\) and \\(\delta\\)) there will be a theorhetical maximum value for the weights.

# Analysis

For this section we will focus on the maths, deriving each equation as we go. In equation 1 we are definining the total input (\\(\alpha_\nu\\)) to a given unit (\\(a_\nu^{II}\\)) in the second layer, for some input stimulus \\(i\\).

\begin{equation} \tag{1}
   \alpha_\nu^i\(t\) = \theta e_{\nu i} + \sum_{\mu} \upsilon_{\mu\nu}\(t\) e_{\mu i}
\end{equation}

In words, this equation states that we have an input of \\(\theta\\) if the corresponding layer one neuron is active and also an have input of \\(\upsilon_{\mu\nu}\\) for each active neuron (\\(a_\mu^I\\)) in the first layer.

Equations 2, 3 and 4 simply redefine the \\(\theta\\) signal as a bias term \\(\beta_\nu\\) and the weighted sum as a function of time \\(\gamma_\nu\(t\)\\).

\begin{equation} \tag{2}
   \beta_\nu^i = \theta e_{\nu i}
\end{equation}

\begin{equation} \tag{3}
   \gamma_\nu^i\(t\)=\sum_{\mu} \upsilon_{\mu\nu}\(t\) e_{\mu i}
\end{equation}

\begin{equation} \tag{4}
   \alpha_\nu^i\(t\)=\beta_\nu^i + \gamma_\nu^i\(t\)
\end{equation}

In the next step the authors obtain an expression for the difference between the weights in the second layer over a single time step. Specifically, moving from \\(t_0 + \Delta t\\) with stimulus \\(j\\) to \\(t_0 + 2\Delta t\\) with stimulus \\(k\\), what is the change in the connection weight (\\(\upsilon_{\mu\nu}\\)) between units \\(a_\mu^I\\) and \\(a_\nu^{II}\\). Note that we define a threshold function (\\(\Phi\(x\)\\)) here which outputs \\(1\\) if \\(x \geq \theta\\) and \\(0\\) otherwise.

\begin{equation} \tag{5}
   \upsilon_{\mu\nu}\(t_0 + 2\Delta t\) - \upsilon_{\mu\nu}\(t_0 + \Delta t\) = \(\eta\Delta t\)\(e_{\mu j}\)\Phi\[\alpha_\nu^k\(t_0 + \Delta t\)\] - \(\delta\Delta t\)\upsilon_{\mu\nu}\(t_0 + \Delta t\)
\end{equation}

In words, equation 5 states that the change in the weight for a particular connection when shown two images is made up of both an increment term and a decrement term. The increment term is the product of a learning rate (\\(\eta\\)) and two binary variables which represent the activations of each of the neurons. That is, if the layer one neuron is active for the first image (\\(e_{\mu j} = 1\\)) and the layer two neuron is active for the second image (\\(\Phi\[\alpha_\nu^k\(t_0 + \Delta t\)\] = 1\\)) we increment by the learning rate, \\(\eta\\). Actually, we increment by \\(\eta\Delta t\\), but this is purely to preserve the learning rate when we subsequently take the gradient with respect to \\(t\\). In neuroscience we might say that these neurons are coordinated and therefore exhibit an increase in synaptic efficacy.

For the next step, the authors obtain an expression for the change in total weight input (\\(\gamma_\nu^i\\)) to a unit (\\(a_\nu^{II}\\)) in the second layer over one timestep. This derivation is somewhat straight-forward. However, I said we'd look at all the maths, simple or otherwise. So, we begin with our expression from equation 3 and take the difference between \\(t_0 + 2\Delta t\\) and \\(t_0 + \Delta t\\) as in equation 5.1. Next, as addition and subtraction are associative, we can compress the sum to obtain equation 5.2. Finally, as in the paper, we can expand out the \\(e_{\mu i}\\) term to form equation 5.3.

\begin{equation} \tag{5.1}
   \gamma_\nu^i\(t_0 + 2\Delta t\) - \gamma_\nu^i\(t_0 + \Delta t\)=\sum_{\mu} \upsilon_{\mu\nu}\(t_0 + 2\Delta t\) e_{\mu i} - \sum_{\mu} \upsilon_{\mu\nu}\(t_0 + \Delta t\) e_{\mu i}
\end{equation}

\begin{equation} \tag{5.2}
   =\sum_{\mu} \upsilon_{\mu\nu}\(t_0 + 2\Delta t\) e_{\mu i} - \upsilon_{\mu\nu}\(t_0 + \Delta t\) e_{\mu i}
\end{equation}

\begin{equation} \tag{5.3}
   =\sum_{\mu} \[\upsilon_{\mu\nu}\(t_0 + 2\Delta t\) - \upsilon_{\mu\nu}\(t_0 + \Delta t\)\] e_{\mu i}
\end{equation}

We now substitute equation 5 back into equation 5.3, giving equation 5.4. Next we expand the sum to give equations 5.5 and 5.6. As \\(\eta\Delta t\\), \\(\Phi\[\alpha_\nu^k\(t_0 + \Delta t\)\]\\) and \\(\delta\Delta t\\) are not dependant on \\(\mu\\), we can move them outside of their respective sums to give equation 5.7 as in the paper.

\begin{equation} \tag{5.4}
   =\sum_{\mu} \[\(\eta\Delta t\)\(e_{\mu j}\)\Phi\[\alpha_\nu^k\(t_0 + \Delta t\)\] - \(\delta\Delta t\)\upsilon_{\mu\nu}\(t_0 + \Delta t\)\] e_{\mu i}
\end{equation}

\begin{equation} \tag{5.5}
   =\sum_{\mu} \(\eta\Delta t\)\(e_{\mu j}\)\Phi\[\alpha_\nu^k\(t_0 + \Delta t\)\]\(e_{\mu i}\) - \(\delta\Delta t\)\upsilon_{\mu\nu}\(t_0 + \Delta t\)\(e_{\mu i}\)
\end{equation}

\begin{equation} \tag{5.6}
   =\sum_{\mu} \(\eta\Delta t\)\(e_{\mu j}\)\Phi\[\alpha_\nu^k\(t_0 + \Delta t\)\]\(e_{\mu i}\) - \sum_{\mu}\(\delta\Delta t\)\upsilon_{\mu\nu}\(t_0 + \Delta t\)\(e_{\mu i}\)
\end{equation}

\begin{equation} \tag{5.7}
   =\(\eta\Delta t\)\Phi\[\alpha_\nu^k\(t_0 + \Delta t\)\]\sum_{\mu} e_{\mu j}e_{\mu i} - \(\delta\Delta t\)\sum_{\mu}\upsilon_{\mu\nu}\(t_0 + \Delta t\)\(e_{\mu i}\)
\end{equation}

Next, we define \\(n_{ij}^I\\) to be the number of associator units in the first layer (\\(A^I\\)) which are active for both stimulus \\(i\\) and stimulus \\(j\\) to give equation 6 from the paper

\begin{equation} \tag{6}
   \gamma_\nu^i\(t_0 + 2\Delta t\) - \gamma_\nu^i\(t_0 + \Delta t\)=\(\eta\Delta t\)\Phi\[\alpha_\nu^k\(t_0 + \Delta t\)\]n_{ij}^I - \(\delta\Delta t\)\sum_{\mu}\upsilon_{\mu\nu}\(t_0 + \Delta t\)\(e_{\mu i}\)
\end{equation}

where \\(n_{ij}^I = \sum_{\mu} e_{\mu j}e_{\mu i}\\).
