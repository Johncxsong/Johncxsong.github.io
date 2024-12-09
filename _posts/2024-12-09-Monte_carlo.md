---
layout: post
title: Monte Carlo Sampling
date: 2024-12-09 08:01:00
description: Monte Carlo Sampling technique for algorithm optimization
tags: MachineLearning Coding
categories: Computer_Science
featured: false
related_posts: false
---

> Shapley value optimization by applying Monte Carlo technique.    

## Conclusion:  

| Method                | Subset/Permutation Growth | Big-O Complexity                             | Description                               |
|-----------------------|---------------------------|---------------------------------------------|-------------------------------------------|
| **Data Shapley**      | $$2^{n-1}$$ subsets       | $$O(n \cdot 2^{n-1})$$ | Computes Shapley value by evaluating all subsets; exponential in \(n\). |
| **Monte Carlo Sampling** | $$P$$ permutations      | $$O(n \cdot P)$$       | Approximates Shapley value by sampling  linear in $$P$$. |
  


----

## 1. Data Shapley  

$$

\phi_i = \frac{1}{n!} \sum_{S \subseteq D \setminus \{i\}} \frac{V(S \cup \{i\}) - V(S)}{\binom{n-1}{|S|}}

$$

From this equestion, elements will generate $$2^{n}$$ subsets for each players.  

### Walkthrough Example  

We are calculating the Shapley value for a dataset D = \{a, b, c\}, where the model's performance metric V(S) for a subset S is given as follows:

| Subset \(S\)      | V(S) | $$V(S \cup \{a\})$$ | Marginal Contribution $$V(S \cup \{a\}) - V(S)$$ |
|-------------------|----------|---------------------|--------------------------------------------------|
| \{\}          | 0.0      | 0.2                 | 0.2                                              |
| \{b\}         | 0.3      | 0.4                 | 0.1                                              |
| \{c\}         | 0.4      | 0.5                 | 0.1                                              |
| \{b, c\}      | 0.6      | 0.7                 | 0.1                                              |

---

#### **Step 1: Marginal Contributions**
For each subset \(S\), the **marginal contribution** of data point $$a$$ is calculated as:

$$
\text{Marginal Contribution} = V(S \cup \{a\}) - V(S)
$$

From the table:
- Subset \{\}: V(\{\}) = 0.0, $$V(\{\} \cup \{a\}) = 0.2$$, Contribution = \(0.2 - 0.0 = 0.2\).
- Subset \{b\}: V(\{b\}) = 0.3, $$V(\{b\} \cup \{a\}) = 0.4$$, Contribution = \(0.4 - 0.3 = 0.1\).
- Subset \{c\}: V(\{c\}) = 0.4, $$V(\{c\} \cup \{a\}) = 0.5$$, Contribution = \(0.5 - 0.4 = 0.1\).
- Subset \{b, c\}: V(\{b, c\}) = 0.6, $$V(\{b, c\} \cup \{a\}) = 0.7$$, Contribution = \(0.7 - 0.6 = 0.1\).

---

#### **Step 2: Weight Each Contribution**

The weight is calculated as:
$$
\text{Weight for subset } S = \frac{1}{\binom{n-1}{|S|}}
$$

Where:
- $$n = 3$$ (total number of elements in \(D\)),
- $$\binom{n-1}{S}$$: Binomial coefficient representing the number of subsets of size \(S\) from $$n-1 = 2$$ elements.


| Subset Size $$S$$ | Subsets            | $$\binom{2}{S}$$ | Weight $$1 / \binom{2}{S}$$ |
|---------------------|--------------------|--------------------|------------------------------|
| 0                   | \{\}         | 1              | 1.0                     |
| 1                   | \{b\}, \{c\}  | 2              | 0.5                     |
| 2                   | \{b, c\}      | 1              | 1.0                     |

---

#### **Step 3: Calculate Weighted Contributions**

For each subset, multiply the marginal contribution by its weight:

| Subset $$ S $$      | Marginal Contribution | Weight $$1 / \binom{2}{S}$$ | Weighted Contribution |
|-------------------|-----------------------|------------------------------|------------------------|
| \{\}         | 0.2                   | 1.0                          | $$0.2 \times 1.0 = 0.2$$ |
| \{b\}         | 0.1                   | 0.5                          | $$0.1 \times 0.5 = 0.05$$ |
| \{c\}        | 0.1                   | 0.5                          | $$0.1 \times 0.5 = 0.05$$ |
| \{b, c\}      | 0.1                   | 1.0                          | $$0.1 \times 1.0 = 0.1$$ |

---

#### **Step 4: Sum the Weighted Contributions**

The Shapley value for \(a\) is the sum of all weighted contributions:

$$
\phi_a = 0.2 + 0.05 + 0.05 + 0.1 = 0.4
$$


The Shapley value for data point \(a\) is:
$$
\phi_a = 0.4
$$

---
## 2. Data Shapley--Monte Carlo  

### Walkthrough Example

We are using the Monte Carlo method to approximate the Shapley value for a dataset \(D = \{a, b, c\}\). Instead of calculating the Shapley value using all $$2^{n-1}$$ subsets, we randomly sample **permutations of data points** and compute the marginal contribution of each data point in each sampled permutation.

#### **Step 1: Random Permutations**
For \(n = 3\), the total number of permutations is \(3! = 6\):
\[
[a, b, c], [a, c, b], [b, a, c], [b, c, a], [c, a, b], [c, b, a]
\]

In the Monte Carlo method, we randomly sample a fixed number $$P$$ of these permutations. Suppose we sample \(P = 2\) permutations:
1. \([b, a, c]\)
2. \([a, c, b]\)

#### **Step 2: Marginal Contribution for Each Permutation**

For each permutation, we compute the **marginal contribution** of each data point as it is added to the subset formed by preceding elements in the permutation. 

**Permutation 1: \([b, a, c]\)**
- **b**: V(\{b\}) - V(\{\}) = 0.3 - 0.0 = 0.3
- **a**: V(\{b, a\}) - V(\{b\}) = 0.4 - 0.3 = 0.1
- **c**: V(\{b, a, c\}) - V(\{b, a\}) = 0.7 - 0.4 = 0.3

**Permutation 2: \([a, c, b]\)**
- **a**: V(\{a\}) - V(\{\}) = 0.2 - 0.0 = 0.2
- **c**: V(\{a, c\}) - V(\{a\}) = 0.5 - 0.2 = 0.3
- **b**: V(\{a, c, b\}) - V(\{a, c\}) = 0.7 - 0.5 = 0.2


#### **Step 3: Average Marginal Contributions**

To compute the approximate Shapley value for each data point, average the marginal contributions across all sampled permutations.

| Data Point | Contribution in Permutation 1 | Contribution in Permutation 2 | Average Contribution |
|------------|-------------------------------|-------------------------------|----------------------|
| a     | 0.1                     | 0.2                      | (0.1 + 0.2)/2 = 0.15 |
| b      | 0.3                       | 0.2                      | (0.3 + 0.2)/2 = 0.25 |
| c      | 0.3                      | 0.3                       | (0.3 + 0.3)/2 = 0.3  |


The Monte Carlo approximation gives the following Shapley values:
$$
\phi_a \approx 0.15, \quad \phi_b \approx 0.25, \quad \phi_c \approx 0.3
$$

---


## References:  
- What is your data worth? Equitable Valuation of Data  
- [https://en.wikipedia.org/wiki/Monte_Carlo_method](https://en.wikipedia.org/wiki/Monte_Carlo_method)
- [https://www.ibm.com/topics/monte-carlo-simulation](https://www.ibm.com/topics/monte-carlo-simulation)
- ChatGPT
