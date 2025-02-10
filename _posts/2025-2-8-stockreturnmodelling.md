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

We model the prior distribution for $\mu$ and $\Sigma$

---
## **References**

[^fn]: [Attilio Meucci, Risk and Asset Allocation, 1st ed. 2005. Corr. 3rd printing 2009 edition (Berlin ; New York: Springer, 2009)](https://link.springer.com/book/10.1007/978-3-540-27904-4).