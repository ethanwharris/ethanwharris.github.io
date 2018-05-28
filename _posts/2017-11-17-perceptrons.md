---
layout: post-links
title:  "Analysis of a Four-Layer Series-Coupled Perceptron. II"
excerpt: "Understanding the maths of Block, Knight and Rosenblatt"
date:   2017-11-17 11:00:00
mathjax: true
published: false
comments: true
---
In this post we will study and attempt to understand the maths in the paper ["Analysis of a Four-Layer Series-Coupled Perceptron. II"](http://digitalcommons.rockefeller.edu/cgi/viewcontent.cgi?article=1013&context=knight_laboratory). The paper discusses a Multilayer Perceptron not too dissimilar from the neural networks of today; trained in an unsupervised setting. It could even be argued that this paper presents one of the earliest examples of what may be described as deep learning. It also attempts to prove several properties of the network using maths that is obfuscated to say the least. We will attempt to understand this maths and show all of the derivations and transitions along the way.

# Introduction

The introduction of the paper discusses a 'cross-coupled' network, analogous to having a layer which is fully connected to itself. In a previous paper this network was studied and shown to have the ability to generalize over its inputs (stimuli in the paper). The authors then go on to introduce the main focus of the paper; to prove this property in a more rigorous fashion for a simpler 'series-coupled' network. A 'series-coupled' network is much closer to a more traditional perceptron.

# The Model

In this section the authors introduce the model that will be studied in the paper. As we have already stated, this is a 'series-copupled' network. In other words, a feed-forward neural network. Here we have a sensory field that is arbitrarily connected to an 'Associator Unit' (read: neuron) in the first layer. A way to model these connections is to think of the inputs as being fully connected (for each pixel) to the first layer with weights that are all either \\(1\\), \\(0\\) or \\(-1\\) (predominantly zero to model sparse connectivity).

Next we have two 'Associator' layers. The authors state that units in the first layer are in one-to-one correspondence with those in the second layer. Interpret this to mean that each layer has an equal number of neurons. The activation of the units is a step function with threshold \\(\theta\\). The threshold is constant for the operation of the network, it does not change with time. It is not stated whether \\(\theta\\) is defined globally or layer-wise. However, this has no effect on the maths.

When a neuron in the first associator layer fires (i.e. its total input from the sensory field is greater than \\(\theta\\)), it transmits a signal of \\(\theta\\) to the corresponding neuron in the second layer. It also transmits some weight to all neurons in the second layer (including its counterpart). We can picture this as the first layer being fully connected to the second layer with weights \\(\upsilon_{\mu\nu} \in \Upsilon\\) where the all values are initially \\(0\\). The motivation for having the additional \\(\theta\\) transmission is not entirely clear. Certainly, the necessary maths is more complex as a result and there is no given motivation for it. It seems that this transmission is there purely to ensure that the neurons in the second layer have some activation for training purposes.

Finally, we have the response layer. This is not mentioned in any great detail in the paper but is simply stated as a layer with weights that can be trained via re-inforcement. In other words, we may amend the connections and weights in this layer arbitrarily in order to get a particular neuron to fire for a particular class. These days of course, we would use gradient descent back-propogation for this. Although re-inforcing the weights based on an error computation can produce similar results for a single layer.

## Training

The training of the neural network (described as changes to \\(\Upsilon\\) over time) consists of two main update rules (with corresponding learning rates \\(\eta\\) and \\(\delta\\)).

The first rule is derived from the well known Hebbian learning principle in neuroscience (also known as Hebb's postulate). This rule is often characterised by the phrase "neurons that fire together, wire together" and states that the 'strength' of a synapse which connects two neurons is increased if the pre-synaptic neuron fires before the post-synaptic neuron. That is, if the pre-synaptic neuron contributes to the activation of the post-synaptic neuron, we increase the weight. In the paper, the authors define this as follows. If some first layer neuron \\(a_\mu^I \in A^I\\) is active at time \\(t\\) and some second layer neuron \\(a_\nu^{II} \in A^{II}\\) is active at time \\(t + \Delta t\\) then the connection weight (\\(\upsilon_{\mu\nu}\\)) receives an increment of \\(\eta\times\Delta t\\).

The second rule is merely a decrement rule. If we are to only increment weights throughout operation of the model, these weights will tend to explode as time goes on. To control this, at each time step, weights are decremented by \\(\delta\times\Delta t\times\upsilon_{\mu\nu}\\). Note that as a consequence of this, a larger weight will decrement by a larger amount. Thus, for given learning rates (\\(\eta\\) and \\(\delta\\)) there will be a theoretical maximum value for the weights.

# Analysis

For this section we will focus on the maths, deriving each equation as we go. In equation 1 we are defining the total input (\\(\alpha_\nu\\)) to a given unit (\\(a_\nu^{II}\\)) in the second layer, for some input stimulus \\(i\\).

\begin{equation} \tag{1}
   \alpha_\nu^i\(t\) = \theta e_{\nu i} + \sum_{\mu} \upsilon_{\mu\nu}\(t\) e_{\mu i}
\end{equation}

In words, this equation states that we have an input of \\(\theta\\) if the corresponding layer one neuron is active and also an have input of \\(\upsilon_{\mu\nu}\\) for each active neuron (\\(a_\mu^I\\)) in the first layer.

Equations 2, 3 and 4 simply redefine the \\(\theta\\) signal as a bias term \\(\beta_\nu\\) and the weighted sum as a function of time \\(\gamma_\nu\(t\)\\).

\begin{align}\nonumber
   \beta_\nu^i & = \theta e_{\nu i}\tag{2}
   \\\\ \gamma_\nu^i\(t\) & =\sum_{\mu} \upsilon_{\mu\nu}\(t\) e_{\mu i}\tag{3}
   \\\\ \alpha_\nu^i\(t\) & =\beta_\nu^i + \gamma_\nu^i\(t\)\tag{4}
\end{align}

In the next step the authors obtain an expression for the difference between the weights in the second layer over a single time step. Specifically, moving from \\(t_0 + \Delta t\\) with stimulus \\(j\\) to \\(t_0 + 2\Delta t\\) with stimulus \\(k\\), what is the change in the connection weight (\\(\upsilon_{\mu\nu}\\)) between units \\(a_\mu^I\\) and \\(a_\nu^{II}\\). Note that we define a threshold function (\\(\Phi\(x\)\\)) here which outputs \\(1\\) if \\(x \geq \theta\\) and \\(0\\) otherwise.

\begin{equation} \tag{5}
   \upsilon_{\mu\nu}\(t_0 + 2\Delta t\) - \upsilon_{\mu\nu}\(t_0 + \Delta t\) = \(\eta\Delta t\)\(e_{\mu j}\)\Phi\[\alpha_\nu^k\(t_0 + \Delta t\)\] - \(\delta\Delta t\)\upsilon_{\mu\nu}\(t_0 + \Delta t\)
\end{equation}

In words, equation 5 states that the change in the weight for a particular connection when shown two images is made up of both an increment term and a decrement term. The increment term is the product of a learning rate (\\(\eta\\)) and two binary variables which represent the activations of each of the neurons. That is, if the layer one neuron is active for the first image (\\(e_{\mu j} = 1\\)) and the layer two neuron is active for the second image (\\(\Phi\[\alpha_\nu^k\(t_0 + \Delta t\)\] = 1\\)) we increment by the learning rate, \\(\eta\\). Actually, we increment by \\(\eta\Delta t\\), but this is purely to preserve the learning rate when we subsequently take the gradient with respect to \\(t\\). In neuroscience we might say that these neurons are coordinated and therefore exhibit an increase in synaptic efficacy.

For the next step, the authors obtain an expression for the change in total weight input (\\(\gamma_\nu^i\\)) to a unit (\\(a_\nu^{II}\\)) in the second layer over one timestep when presented with some stimulus \\(i\\). This derivation is somewhat straight-forward. However, I said we'd look at all the maths, simple or otherwise. So, we begin with our expression from equation 3 and take the difference between \\(t_0 + 2\Delta t\\) and \\(t_0 + \Delta t\\) as in equation 5.1. Next, as addition and subtraction are associative, we can compress the sum to obtain equation 5.2. Finally, as in the paper, we can expand out the \\(e_{\mu i}\\) term to form equation 5.3.

\begin{align}\nonumber
  \gamma_\nu^i\(t_0 + 2\Delta t\) &- \gamma_\nu^i\(t_0 + \Delta t\) \\\\ & =\sum_{\mu} \upsilon_{\mu\nu}\(t_0 + 2\Delta t\) e_{\mu i} - \sum_{\mu} \upsilon_{\mu\nu}\(t_0 + \Delta t\) e_{\mu i}\tag{5.1}
  \\\\ & =\sum_{\mu} \upsilon_{\mu\nu}\(t_0 + 2\Delta t\) e_{\mu i} - \upsilon_{\mu\nu}\(t_0 + \Delta t\) e_{\mu i}\tag{5.2}
  \\\\ & =\sum_{\mu} \[\upsilon_{\mu\nu}\(t_0 + 2\Delta t\) - \upsilon_{\mu\nu}\(t_0 + \Delta t\)\] e_{\mu i}\tag{5.3}
\end{align}

We now substitute equation 5 back into equation 5.3, giving equation 5.4. Next we expand the sum to give equations 5.5 and 5.6. As \\(\eta\Delta t\\), \\(\Phi\[\alpha_\nu^k\(t_0 + \Delta t\)\]\\) and \\(\delta\Delta t\\) are not dependent on \\(\mu\\), we can move them outside of their respective sums to give equation 5.7 as in the paper.

\begin{align}\nonumber
   & =\sum_{\mu} \[\(\eta\Delta t\)\(e_{\mu j}\)\Phi\[\alpha_\nu^k\(t_0 + \Delta t\)\] - \(\delta\Delta t\)\upsilon_{\mu\nu}\(t_0 + \Delta t\)\] e_{\mu i}\tag{5.4}
   \\\\ & =\sum_{\mu} \(\eta\Delta t\)\(e_{\mu j}\)\Phi\[\alpha_\nu^k\(t_0 + \Delta t\)\]\(e_{\mu i}\) - \(\delta\Delta t\)\upsilon_{\mu\nu}\(t_0 + \Delta t\)\(e_{\mu i}\)\tag{5.5}
   \\\\ & =\sum_{\mu} \(\eta\Delta t\)\(e_{\mu j}\)\Phi\[\alpha_\nu^k\(t_0 + \Delta t\)\]\(e_{\mu i}\) - \sum_{\mu}\(\delta\Delta t\)\upsilon_{\mu\nu}\(t_0 + \Delta t\)\(e_{\mu i}\)\tag{5.6}
   \\\\ & =\(\eta\Delta t\)\Phi\[\alpha_\nu^k\(t_0 + \Delta t\)\]\sum_{\mu} e_{\mu j}e_{\mu i} - \(\delta\Delta t\)\sum_{\mu}\upsilon_{\mu\nu}\(t_0 + \Delta t\)\(e_{\mu i}\)\tag{5.7}
\end{align}

Next, we insert the \\(\gamma_\nu^i\\) term from equation 3 and define \\(n_{ij}^I\\) to be the number of associator units in the first layer (\\(A^I\\)) which are active for both stimulus \\(i\\) and stimulus \\(j\\). This gives equation 6 from the paper

\begin{align}\nonumber
   \gamma_\nu^i\(t_0 + 2\Delta t\) &- \gamma_\nu^i\(t_0 + \Delta t\)
   \\\\ & =\(\eta\Delta t\)\Phi\[\alpha_\nu^k\(t_0 + \Delta t\)\]n_{ij}^I - \(\delta\Delta t\)\gamma_\nu^i\(t_0 + \Delta t\)\tag{6}
\end{align}

where \\(n_{ij}^I = \sum_{\mu} e_{\mu j}e_{\mu i}\\). Note that in the paper, they omit the subscript \\(\nu\\) for brevity.

We now wish to define a sequence of stimuli which will be shown to the network. The notation in the paper is somewhat confusing as the authors define a sequence \\(S_{j0}, S_{j1},\cdot\cdot\cdot, S_{jM}\\). This makes it unclear as to whether this is simply a sequence containing \\(M\\) copies of the \\(j^{\text{th}}\\) image, or a sequence of \\(M\\) images sampled from the set of all possible images, \\(S\\). I believe it is the latter, the presence of \\(j\\) indicating that this stimulus is simply one of the possible stimuli, sampled with replacement. We therefore have that some image in our sequence, \\(S_{jm}\\), is the \\(j^{\text{th}}\\) image in \\(S\\) being presented to the network at time \\(t_0 + m\Delta t\\). We now seek to obtain an expression for the change in weighted input to some unit in the second associator layer as we move through the sequence. To do this, we take \\(t_0\\) to be \\(t + m\Delta t\\) from equation 6 to get equation 7. In other words, if my network has 'seen' all stimuli up to \\(m\\), and I now show it stimulus \\(m + 1\\), what is the change in activation for some stimulus \\(i\\).

\begin{align}\nonumber
   \gamma_\nu^i\(t + \(m + 2\)\Delta t\) &- \gamma_\nu^i\(t + \(m + 1\)\Delta t\)
   \\\\ & =\(\eta\Delta t\)\Phi\[\alpha_\nu^{j_{m+1}}\(t + \(m + 1\)\Delta t\)\]n_{ij_m}^I - \(\delta\Delta t\)\gamma_\nu^i\(t + \(m + 1\)\Delta t\)\tag{7}
\end{align}

Now, we wish to aggregate this change over the whole sequence. That is, if I present images to my network in sequence, what is the change over the whole sequence in terms of my responses to some stimulus \\(i\\). We define this as in equation 8, summing over each stimulus (\\(m\\)) in the sequence.

\begin{align}\nonumber
   \gamma_\nu^i\(t + \(M + 1\)\Delta t\) &- \gamma_\nu^i\(t + \Delta t\)
   \\\\ & =\sum_{m=0}^{M-1}\(\eta\Delta t\)\Phi\[\alpha_\nu^{j_{m+1}}\(t + \(m + 1\)\Delta t\)\]n_{ij_m}^I - \(\delta\Delta t\)\gamma_\nu^i\(t + \(m + 1\)\Delta t\)\tag{8}
\end{align}

The authors now say that we divide by \\(M\Delta t\\) and take \\(\lim_{\Delta t \to 0}\\). This is gradient by definition as shown in the graph below. Here we can see that if we let \\(\Delta t\\) approach zero, we reach an expression for the gradient of \\(y\\) with respect to \\(t\\). We divide by \\(M\Delta t\\) as we are considering \\(M\\) steps of size \\(\Delta t\\).

<img src="/assets/gradient.svg" class="figure d-block mx-auto" alt="Gradient" title="Gradient Diagram">

Dividing by \\(M\Delta t\\), we obtain equation 8.1. Then we set \\(\Delta t\\) to zero to reach equation 9. Note that we accumulate \\(\frac{\delta\gamma_\nu^i\(t\)}{M}\\) \\(M\\) times and therefore we can simply move it outside of the sum as it does not depend on \\(m\\).

\begin{align}\nonumber
   & =\sum_{m=0}^{M-1}\frac{\eta}{M}\Phi\[\alpha_\nu^{j_{m+1}}\(t + \(m + 1\)\Delta t\)\]n_{ij_m}^I - \frac{\delta}{M}\gamma_\nu^i\(t + \(m + 1\)\Delta t\)\tag{8.1}
   \\\\ \frac{d\gamma_\nu^i}{dt} & =\sum_{m=0}^{M-1}\left(\frac{\eta}{M}\Phi\[\alpha_\nu^{j_{m+1}}\(t\)\]n_{ij_m}^I\right) - \delta\gamma_\nu^i\(t\)\tag{9}
\end{align}

Moving from equation 9 to equation 10 in the paper is not immediately trivial. First we define \\(F_{jk}\\) as in the paper to be the number of times the pair \\(S_j S_k\\) occurs in the sequence. Next, we say that the sum over all \\(m\\) in equation 9 will visit each pair in the sequence in turn. If a pair occurs twice in the sequence we will add the value inside the sum twice for that pair. In fact, for each pair that occurs we have \\(F_{jk}\\) additions.

Now consider that we may instead wish to perform a dual summation over all possible pairs of stimuli but obtain the same result as in equation 9. For this summation, we will only visit each pair once, even if it occurs multiple times in the sequence. We will also visit any possible pairs which are not present in the sequence. We therefore wish to multiply each value in the sum by the number of times the pair occurs. Conveniently, we have already defined this value \\(F_{jk}\\). Doing this we obtain equation 9.1 below.

\begin{equation}\tag{9.1}
   \frac{d\gamma_\nu^i}{dt}=\sum_{j=1}^{n}\sum_{k=1}^{n}\left( F_{jk}\frac{\eta}{M}\Phi\[\alpha_\nu^{k}\(t\)\]n_{ij}^I \right) - \delta\gamma_\nu^i\(t\)
\end{equation}

Now that we have equation 9.1, the next move is trivial. As in the paper we define \\(f_{jk} = F_{jk} / M\\) and obtain equation 10.

\begin{equation}\tag{10}
   \frac{d\gamma_\nu^i}{dt}=\sum_{j=1}^{n}\sum_{k=1}^{n}\left( \eta f_{jk}\Phi\[\alpha_\nu^{k}\(t\)\]n_{ij}^I \right) - \delta\gamma_\nu^i\(t\)
\end{equation}

Now, lets take stock for a moment. So far, we have produced a series of equations which describe the dynamics of the network (equations 1-4). We have subsequently considered how these dynamics change with time (equation 5). Specifically, we have obtained equations which describe how the total input to units in the second layer changes as a function of learning rate and as we traverse a sequence of inputs (equations 6 and 7). We have then produced an expression for the total change in dynamics, observing an entire sequence. Next, we have said that for very small intervals, we can obtain a derivative with respect to time (equation 9). That is, an equation which models the change in weighted input to units in the second layer over time for a given sequence. Finally, we have demonstrated that this derivative can be thought of as a sum over all possible pairs for a set of stimuli with some filter term \\(f_{jk}\\) (equation 10).

In the next step, we swap the indices \\(j\\) and \\(k\\). This can cause some confusion so we do this explicitly in equation 10.1. Nopte that we have not changed any of the meaning in the equation.

\begin{equation}\tag{10.1}
   \frac{d\gamma_\nu^i}{dt}=\sum_{j=1}^{n}\sum_{k=1}^{n}\left( \eta f_{kj}\Phi\[\alpha_\nu^{j}\(t\)\]n_{ik}^I \right) - \delta\gamma_\nu^i\(t\)
\end{equation}

We may now define the matrix \\(K_{ij}\\) as in the paper to produce equation 11 without confusion. Note that we also expand the \\(\alpha\\) term as in equation 4.

\begin{align}\nonumber
   K_{ij} & = \sum_{k=1}^{n}n_{ik}^I f_{kj}\nonumber
   \\\\ \frac{d\gamma_\nu^i}{dt} &=\eta\sum_{j=1}^{n}\left( K_{ij} \Phi\[\beta_\nu^j + \gamma_\nu^j\(t\)\] \right) - \delta\gamma_\nu^i\(t\)\tag{11}
\end{align}

Given equation 11, we can consider the equilibrium case. This is obtained by setting the derivative to zero and arranging for \\(\gamma_\nu^i\\). Equations 11.1, 11.2 and 12 show this explicitly. Note that we now consider \\(f_{kj}\\) and thus \\(K_{ij}\\) to be constant and so remove the dependency on time.

\begin{align}\nonumber
   0 &=\eta\sum_{j=1}^{n}\left( K_{ij} \Phi\[\beta_\nu^j + \gamma_\nu^j\] \right) - \delta\gamma_\nu^i\tag{11.1}
   \\\\ \delta\gamma_\nu^i &=\eta\sum_{j=1}^{n} K_{ij} \Phi\[\beta_\nu^j + \gamma_\nu^j\] \tag{11.2}
   \\\\ \gamma_\nu^i &=\frac{\eta}{\delta}\sum_{j=1}^{n} K_{ij} \Phi\[\beta_\nu^j + \gamma_\nu^j\] \tag{12}
\end{align}

As a next step the authors prove that equation 12 has a unique minimal solution. Before proceeding it seems important to try to characterise precisely what this means. I am reluctant to give a concrete definition for this as it seems there are many possible interpretations. Instead, I will tentatively give the interpretation here as I see it.

## Interpretation - Convergence

- First, consider that the stimulus \\(i\\) is a frame of reference. That is, we consider change of weights over time in the context of a response to some \\(i\\).
- Next, we observe that in equation 8 we consider the total change after observing the whole sequence.
- We may now state that the derivative in equation 9 is the rate of change in weights observing the whole sequence with respect to time.
- The set of \\(\gamma^j\\) values which satisfy the equilibrium equation 12 are therefore the values for which, after observing the whole sequence, no change in \\(\gamma^i\\) will be observed.
- In other words, the number of iterations required to reach \\(\gamma^j\\) which satisfy equation 12 is the number of epochs required for convergence.

## Proof

We now proceed to the proof that a unique minimal solution exists to equation 12. The first step is to turn equation 12 into an iterative equation. That is, given a starting value for all \\(\gamma^j\\), iteratively obtain new values \\(\gamma^i\\) by re-substituting into the equation. From our interpretation above, we can say that this is akin to asking, given some initial set of weights, what will the weights be after each iteration of the sequence. The result of this is in equation 13, where \\(m\\) is the iteration number and \\(\gamma_{\nu_0}^i = 0\\) for all second layer units \\(\nu\\).

\begin{equation}\tag{13}
   \gamma_{\nu_{m + 1}}^i =\frac{\eta}{\delta}\sum_{j=1}^{n} K_{ij} \Phi\[\beta_\nu^j + \gamma_{\nu_m}^j\]
\end{equation}
