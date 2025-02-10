---
layout: page
title: Portfolio Optimization Showcase
description: "a showcase for concepts and visualizations from the various portfolio optimization techniques developed in MATH 415: Math Financial Economics."
img: assets/img/project_covers/financial_econ_coverimage.png
importance: 1
category: school
tabs: true
toc:
  sidebar: left
---

---

# Risk and Return

In this chapter, we learned about different ways to measure the risk and volatility of financial assets as a lead-up to the CAPM model. Our book offered the following as a non-exhaustive list:

- **Variance**: This is a useful measure, but is more effective as a mathematical tool than an interpretive one.
- **Standard Deviation**: A more interpretable measure, commonly called *volatility* by the textbook.
- $\mathbf{P(\text{Loss})}$: An interpretable, and very important measure. Very difficult to work with analytically.

<!-- <div class="l-body">
  <iframe src="{{ '/assets/plotly/distribution.html' | relative_url }}" frameborder='0' scrolling='no' height="600px" width="100%"></iframe>
</div> -->

{% tabs data-struct %}

{% tab data-struct yaml %}

```yaml
hello:
  - "whatsup"
  - "hi"
```

{% endtab %}

{% tab data-struct json %}

```json
{
  "hello": ["whatsup", "hi"]
}
```

{% endtab %}

{% endtabs %}

---

# Modelling Risk and Return in the Stock Market

Markowitz Portfolio Optimization requires the construction of a multivariate random variable representing the expected returns of the set of available securities. For the purposes of the following, I will model the log-returns of Stock $i$, $\mathbf{r}_i$ as the following:

$$\mathbf{r}_i \sim  \mathcal{N}(\bar r_i, \sigma_i)$$