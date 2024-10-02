---
layout: post
title: Prov-Gigapath Setup Tutorial
date: 2024-07-15 21:01:00
description: walk through setup for pretrain model
tags: MachineLearning Coding
categories: Computer_Science
featured: true
related_posts: false
---



{::nomarkdown}
{% assign jupyter_path = 'assets/jupyter/whole_slide_beginning.ipynb' | relative_url %}
{% capture notebook_exists %}{% file_exists assets/jupyter/whole_slide_beginning.ipynb %}{% endcapture %}
{% if notebook_exists == 'true' %}
  {% jupyter_notebook jupyter_path %}
{% else %}
  <p>Sorry, the notebook you are looking for does not exist.</p>
{% endif %}
{:/nomarkdown}
