---
layout: page
title: Geocomputation With R
description: A Jupyter Notebook representing my progress in learning how to analyze geospatial data with R.
img: assets/img/projects/geocomputation.png
importance: 1
category: Research
---

{::nomarkdown}
{% assign jupyter_path = 'assets/jupyter/geocomputing_book.ipynb' | relative_url %}
{% capture notebook_exists %}{% file_exists assets/jupyter/blog.ipynb %}{% endcapture %}
{% if notebook_exists == 'true' %}
  {% jupyter_notebook jupyter_path %}
{% else %}
  <p>Sorry, the notebook you are looking for does not exist.</p>
{% endif %}
{:/nomarkdown}