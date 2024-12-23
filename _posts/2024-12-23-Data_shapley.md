---
layout: post
title: Data Shapley Implementation
date: 2024-12-23 08:01:00
description: Data Shapley Implementation through What is your Data worth? Equitable Valuation of Data
tags: MachineLearning Coding
categories: Computer_Science
featured: false
related_posts: false
---

## Algorithms

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/datashape1.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<hr>

### Explanation  
##### Input and Output

###### Input:
- $$ D = \{1, \dots, n\}$$: Dataset of $$ n $$ data points.  
- $$ A $$: Learning algorithm (e.g., a model training routine).  
- $$V $$: Performance metric (e.g., accuracy, MSE, F1-score).  

###### Output:
Estimated Shapley values $$ (\phi_1, \dots, \phi_n) $$, representing the contribution of each data point to model performance.

<hr>

##### Step by Step



1.**Initialization**
- Set $$ \phi_i = 0 $$ for all data points \( i \).  
- Initialize iteration counter \( t \).

2.**Main Loop (until convergence)  Monte Carlo Iteration**:
1. $$ t = t + 1 $$: Record the permutation number.

2.1 **Generate a random permutation of the data points**  
- $$ \pi $$: Generate a random permutation of data points (uniform distribution).

2.2 **Performance on Empty Set**  
- Compute $$ v_0^t = V(\emptyset, A) $$, the performance score with an empty dataset.

2.3 **Inner Loop** (Iterate through all data points based on the permutation $$ \pi $$):  
  - 2.3.1 **Truncation Condition Check**:  
    - If the difference between the performance of the full dataset and the current subset $$( \lvert V(D, A) - v_{j-1}^t \rvert )$$ is less than the performance tolerance:  
      - Set the last data point to the current data point.  
    - Else → **Marginal Contribution**:  
      - If not truncated, compute:  
        $$v_j^t = V(S \cup \{\pi_t[j]\}, A)$$
        where $$ S $$ is the subset of points processed so far in $$ \pi_t $$.  
      - The marginal contribution is:  
        $$
        v_j^t - v_{j-1}^t
        $$

  - 2.3.2 **Update Data Shapley Value**:  
    - Use the computed marginal contribution to update the Shapley value for the data points.
    - $$\phi_{\pi^{t}[j]} \leftarrow \frac{t - 1}{t} \phi_{\pi^{t-1}[j]} + \frac{1}{t} (v_j^t - v_{j-1}^t)$$
    - $$ \frac{t - 1}{t} \phi_{\pi^{t-1}[j]} $$ **is previous estimate**
    - $$ \frac{1}{t} (v_j^t - v_{j-1}^t) $$ **new marginal contribution**
    - The formula approximates the Shapley value by averaging contributions across all sampled permutations. The weights $$ \frac{t - 1}{t} $$ and $$ \frac{1}{t} $$ ensure proper averaging. 

2.4 **End Inner Loop**  

2.5 **If Convergence met, End Permutation**

<hr>

## References 

**Ghorbani, Amirata and Zou, James.**  
*Data Shapley: Equitable Valuation of Data for Machine Learning.*  
International Conference on Machine Learning, 2019, pp. 2242–2251.










