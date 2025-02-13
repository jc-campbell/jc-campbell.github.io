---
layout: post
title: Modelling Stock Market Returns
description: a statistial model of asset prices using a heirachical Bayesian model
thumbnail: /assets/img/AAPL_predictions.png
date: 2025-02-08 11:59:00-0400
tags: stock-market, statistics, statistical-modelling
categories: MATH415, financial-economics
disqus_comments: false
related_posts: true
pretty_table: true
---


For the past few weeks, I've been working on a [Portfolio Optimization Showcase](/projects/financial_econ) as an exercise showing my progress in MATH 415: Math Financial Economics. In order to to solve the problem posed in Modern Portfolio Theory, it is first necessary to estimate the expected prices of the set of market assets available for inclusion in a portfolio. In this post, I will expound the model I used in the showcase, which follows the model in Attilio Meucci's *Risk and Asset Allocation*[^fn], and then show how to implement it in Python.

## **Model Specification**

We model the vector of asset returns as a multivariate normal distribution:

$$\begin{equation}r \sim \mathcal{N}(\mu, \Sigma)\end{equation}$$

where:
- $\mu$ is the $N \times 1$ vector of expected returns
- $\Sigma$ is the $N \times N$ covariance matrix of returns

### **Priors**

We model the prior distribution for $\mu$ and $\Sigma$ using a [normal-inverse-Wishart distribution](https://en.wikipedia.org/wiki/Normal-inverse-Wishart_distribution), which is a natural fit because of the way it plays nicely with the inference step.

$$\mu | \Sigma \sim \mathcal{N}(\mu_0, \Sigma / \kappa_0) \hspace{16pt} \Sigma \sim \text{Inv-Wishart}(\Psi_0, v_0)$$

where
- $\mu_0 \in \mathbb{R}^N$ is the prior expected return vector
- $\kappa_0 \in \mathbb{R}^+$ is the investor's confidence in the prior view on the mean
- $v_0$ is the degrees of freedom
- $\Psi_0$ is the scale matrix, a prior on $\Sigma^{-1}$.

If you're like me, reading all that gave you a headache. Before embarking on this project, I had never heard of the Inverse Wishart distribution, but it's a fascinating topic that I cover in [this post](/blog/2025/invwishart).

### **Bayesian Inference**

The Normal-Inverse-Wishart distribution is conjugate to the multivariate normal liklihood, so the posterior is also Normal-Inverse-Wishart with updated hyperparameters:

- $\kappa_n = \kappa_0 + T$
- $\mathbf{\mu}_n = \frac{\kappa_0 \mathbf{\mu}_0 + T \bar{\mathbf{r}}}{\kappa_0 + T}$ ($\bar{\mathbf{r}}$ being the vector of sample means)
- $\nu_n = \nu_0 + T$
- $\Psi_n = \Psi_0 + S +c \frac{\kappa_0 T}{\kappa_0 + T}(\bar{\mathbf{r}} - \mu_0)(\bar{\mathbf{r}} - \mu_0)^T$ ($S$ being the covariance matrix of the sampled returns)

In order to use the new hyperparameters to generate a posterior distribution, we need to sample from the multivariate t-distribution formed by integrating out $\mu$ and $\Sigma$:

$$\mathbf{r}_{T+1} | \{\mathbf{r}_{t}\}_{t=1}^T \sim t_{\underbrace{\nu_n - d + 1}_{\text{dof}}}\left(\mu_n, \underbrace{\frac{\kappa_n + 1}{\kappa_n (\nu_n - d + 1)}\Psi_n}_{\text{Predictive }\Sigma}\right)$$

## **Implementation in Python**

We'll use Python for this project, so let's start by importing libraries:

```python
import yfinance as yf # To get stock price history
import pandas as pd
import numpy as np
import scipy.stats as stats
import matplotlib.pyplot as plt
```

We'll pull the history of a set of 30 tickers from the NYSE which are, for demonstration purposes, divided into three groups: Financial, Utilities, and Miscellaneous.

```python
n_days = 100   # Feel free to adjust based on your confidence the yahoo won't rate limit you
tickers = [
    # Group 1: Financials (tightly correlated)
    "JPM", "BAC", "WFC", "C", "GS", "MS", "AXP", "PNC", "USB", "SCHW",
    # Group 2: Utilities (tightly correlated but hopefully somewhat uncorrelated with Group 1)
    "DUK", "SO", "EXC", "D", "NEE", "EIX", "AEP", "XEL", "PEG", "WEC",
    # Group 3: Diverse, relatively uncorrelated assets
    "IBM", "JNJ", "PG", "GE", "XOM", "AAPL", "UPS", "AMT", "T", "NKE"
]


# Fetch the historical data for the stocks from Yahoo finance
data = yf.download(tickers,
                   period=f'{n_days}d',   # Number of days to fetch
                   interval='1d'   # We will use daily returns, feel free to adjust
                    )

# Extract the DataFrame with daily closing prices
closing_prices = data['Close']

# Get Logged Returns
log_returns = closing_prices.apply(np.log).diff().dropna()

# Print a sample to console to check that all data are available
log_returns.iloc[-5:]
```

You should now see a table with the five log returns per stock.

### **Define Priors**

```python
d = log_returns.shape[1]  # Number of stocks

mu_0 = np.zeros(d) # Prior expected returns
kappa_0 = 0.01 # Prior scaling factor
nu_0 = d + 2 # Prior DOF
Psi_0 = np.eye(d) # Prior inverse covariance matrix (sort of)
```

### **Inference Step**

Using the formulas in the [Bayesian Inference](#bayesian-inference) section, we update the hyperparameters:

```python
T = log_returns.shape[0]  # Number of time periods

r_mean = log_returns.mean().to_numpy()  # Sample mean
X = (log_returns - r_mean).to_numpy()
S = X.T @ X # Sample covariance

kappa_n = kappa_0 + T
mu_n = (kappa_0 * mu_0 + T * r_mean) / (kappa_0 + T)
nu_n = nu_0 + T
Psi_n = Psi_0 + S + ((kappa_0 * T) / (kappa_0 + T)) * (r_mean - mu_0) @ (r_mean - mu_0).T
```

### **Posterior Distribution**

All that's left to do now is to build the multivariate t-distribution that represents our prediction for the distribution of market log-returns.

```python
df = nu_n - d + 1 # The degrees of freedom
predictive_cov = ((kappa_n + 1) / (kappa_n * df)) * \
    Psi_n  # The predictive covariance matrix

predictive_dist = stats.multivariate_t(
  loc=mu_n, 
  shape=predictive_cov, 
  df=df
)
```

To visualize this distribution, I built this scatterplot with plotly. It plots the expected volatility (standard deviation) of each stock on the x-axis against expected returns on the y-axis. Mouse over a point, and the other points will change color to represent the level of correlation with your selected stock.

<div style="aspect-ratio: 16/9">
  <iframe src="{{ '/assets/plotly/stockreturnmodelling_cov_scatterplot.html' | relative_url }}" frameborder='0' scrolling='no' height="100%" width="100%"></iframe>
</div>

## **Conclusion**

The stock market is a noisy data generating process, and predicting is difficult (read: impossible). This model is useful, as it will allow us to continue into the portfolio optimization step, but it ultimately lacks clarity. A better model likely involves exogenous factors and the introduction of time series dependence. Perhaps, someday soon, I'll built such a model. For now, however, please feel free to read the next chaper of this series, where I explore [Modern Portfolio Optimization](/projects/financial_econ).

---
## **References**

[^fn]: [Attilio Meucci, Risk and Asset Allocation, 1st ed. 2005. Corr. 3rd printing 2009 edition (Berlin ; New York: Springer, 2009)](https://link.springer.com/book/10.1007/978-3-540-27904-4).