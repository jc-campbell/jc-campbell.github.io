---
layout: post
title: Modelling Stock Market Returns
description: a statistial model of asset prices using a heirachical Bayesian model
date: 2025-02-08 11:59:00-0400
tags: stock-market, statistics, statistical-modelling
categories: MATH415, financial-economics
disqus_comments: false
related_posts: true
---


For the past few weeks, I've been working on a [Portfolio Optimization Showcase](/projects/financial_econ) as an exercise showing my progress in MATH 415: Math Financial Economics. In order to to solve the problem posed in Modern Portfolio Theory, it is first necessary to estimate the expected prices of the set of market assets available for inclusion in a portfolio. In this post, I will expound the model I used in the showcase, which follows the model in Attilio Meucci's *Risk and Asset Allocation*[^fn]

## **Model Specification**

We model the vector of asset returns as a multivariate normal distribution:

$$\begin{equation}r \sim \mathcal{N}(\mu, \Sigma)\end{equation}$$

where:
- $\mu$ is the $N \times 1$ vector of expected returns
- $\Sigma$ is the $N \times N$ covariance matrix of returns

### Priors

We model the prior distribution for $\mu$ and $\Sigma$ using a [normal-inverse-Wishart distribution](https://en.wikipedia.org/wiki/Normal-inverse-Wishart_distribution), which is a natural fit because of the way it plays nicely with the inference step.

$$\mu | \Sigma \sim \mathcal{N}(\mu_0, \Sigma / \kappa_0) \hspace{16pt} \Sigma \sim \text{Inv-Wishart}(\Psi_0, v_0)$$

where
- $\mu_0 \in \mathbb{R}^N$ is the prior expected return vector
- $\kappa_0 \in \mathbb{R}^+$ is the investor's confidence in the prior view on the mean
- $v_0$ is the degrees of freedom
- $\Psi_0$ is the scale matrix, a prior on $\Sigma$.

If you're like me, reading all that gave you a headache. Before embarking on this project, I had never heard of the 1. 

<div style="aspect-ratio: 16/9">
  <iframe src="{{ '/assets/plotly/stockreturnmodelling_cov_scatterplot.html' | relative_url }}" frameborder='0' scrolling='no' height="100%" width="100%"></iframe>
</div>

---
## **References**

[^fn]: [Attilio Meucci, Risk and Asset Allocation, 1st ed. 2005. Corr. 3rd printing 2009 edition (Berlin ; New York: Springer, 2009)](https://link.springer.com/book/10.1007/978-3-540-27904-4).