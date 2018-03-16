---
title: "The Exponential Family: Getting Weird Expectations!"
layout: post
date: 2018-03-16 13:30
headerImage: false
tag:
- statistics
category: blog
hidden: false
author: zhiyzuo
description: exponential family expectations
---

I spent quite some time delving into the beauty of variational inference in the recent month. I did not realize how simple and convenient it is to derive the expectations of various forms (e.g. logarithm) of random variables under variational distributions until I finally got to understand (partially, :sweat_smile:) how to make use of properties of the exponential family. I would recommend the following two readings for getting to know this cool concept of unifying a set of probabilistic distributions:

- Chapter 4.2.4 and 10.4 of [Pattern Recognition and Machine Learning](https://www.amazon.com/Pattern-Recognition-Learning-Information-Statistics/dp/0387310738)
- http://www.cs.columbia.edu/~jebara/4771/tutorials/lecture12.pdf
- https://people.eecs.berkeley.edu/~jordan/courses/260-spring10/other-readings/chapter8.pdf

In the following, I would like to share ___just enough___ knowledge of exponential family, which can help us obtain weird expectations that we may encounter in probabilistic graphical models. For complete coverage, please refer to the above materials.

### Definition
We say a random variable $x$ follows an exponential family of distribution if the probabilistic density function can be written in the following form:
<div>
\begin{equation}
\label{eq:pdf}
p(x\vert \eta) = h(x) exp\big\{ \eta^T T(x) - A(\eta) \big\} 
\end{equation}
</div>

where:
- $\eta$ is a vector of parameters
- $T(x)$ is the ___sufficient statistics___
- $A(\eta)$ is the ___cumulant function___

The presence of $T(x)$ and $A(\eta)$ actually confuses me a lot when I first start to read materials on exponential family of distributions. It turns out that they can be understood by getting a little bit deeper into this special formulation of density functions.

### Fanscinating properties
We start by focusing on the property of $A(\eta)$. According to the definition of probabilities, the integral of density function in Equation \eqref{eq:pdf} over $x$ equals to 1:

<div>
\begin{align}
\int p(x\vert \eta) dx &= exp(-A(\eta)) \int h(x)exp\{\eta^T T(x)\} dx = 1 \nonumber \\
A(\eta) &= log~\int h(x)exp\{\eta^T T(x)\} dx  
\label{eq:a_eta}
\end{align}
</div>

From the equation above, we can tell that $A(\eta)$ can be viewed as the logarithm of the normalizer. Its value depends on $T(x)$ and $h(x)$.

An interesting thing happens now - if we take the derivative of Equation \eqref{eq:a_eta} with respect to $\eta$, we have:

<div>
\begin{align}
\dfrac{\partial A(\eta)}{\partial \eta} &= \dfrac{\partial}{\partial \eta} log~\int h(x)exp\{\eta^T T(x)\} dx \nonumber \\[4pt]
&= \dfrac{\int h(x) exp\{ \eta^T T(x) \} T(x) dx}{\int h(x) exp\{\eta^T T(x)\}dx } \nonumber \\[4pt]
&= \dfrac{\int h(x) exp\{ \eta^T T(x) \} T(x) dx}{exp{A(\eta)}} \nonumber \\[4pt]
&= \int h(x) exp\{ \eta^T T(x) - A(\eta) \} T(x) dx \nonumber \\[4pt]
&\big[ \text{Note that } p(x\vert \eta) = h(x) exp\{ \eta^T T(x) - A(\eta) \} \text{ is a density function}\big] \nonumber \\[4pt]
&= E\big[T(x)\big] \label{eq:e_t}
\end{align}
</div>

How neat it is! It turns out the first derivative of the cumulant function $A(\eta)$ is actually the expectation of the sufficient statistic $T(x)$! If we go further - take the second derivative - we will get the variance of $T(x)$:

<div>
\begin{align}
\dfrac{\partial A(\eta)}{\partial \eta \partial \eta^T} &= \dfrac{\partial}{\partial \eta^T}  \int h(x) exp\{ \eta^T T(x) - A(\eta) \} T(x) dx \nonumber \\[4pt]
&= \int h(x) exp\{ \eta^T T(x) - A(\eta) \} T(x) (T(x) - A'(\eta)) dx \nonumber \\[4pt]
&= \int p(x\vert \eta) T^2(x) dx - A'(\eta) \int p(x\vert \eta) T(x) dx  \nonumber \\[4pt]
&= E\big[T^2(x)\big] - E\big[T(x)\big] E\big[T(x)\big] \nonumber \\[4pt]
&= V\big[T(x)\big] \label{eq:v_t}
\end{align}
</div>

In fact, we can go deeper and deeper to generate higher order moment of $T(x)$. 

### Examples

Now let's take a look at some familar probability distributions that belong to the exponential family.

#### Dirichlet distribution
Suppose a random variable $\theta$ is drawn from a Dirichlet distribution parameterized by $\alpha$, we have the following density function:

<div>
\begin{align*}

\end{align*}
</div>


