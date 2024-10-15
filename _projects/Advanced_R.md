---
layout: page
title: Adanced R
description: A Jupyter Notebook representing my progress in learning the internals of the R language.
img: assets/img/projects/advanced_r.png
importance: 1
category: Research
---

{::nomarkdown}
{% assign jupyter_path = 'assets/jupyter/advanced_r.ipynb' | relative_url %}
{% capture notebook_exists %}{% file_exists assets/jupyter/blog.ipynb %}{% endcapture %}
{% if notebook_exists == 'true' %}
  {% jupyter_notebook jupyter_path %}
{% else %}
  <p>Sorry, the notebook you are looking for does not exist.</p>
{% endif %}
{:/nomarkdown}