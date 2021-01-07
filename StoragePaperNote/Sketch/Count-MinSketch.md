---
typora-copy-images-to: paper_figure
---
An improved data stream summary: the count-min sketch and its applications
------------------------------------------
|           Venue            | Category |
| :------------------------: | :------: |
| Journal of Algorithms 2003 |  sketch  |
[TOC]

## 1. Summary
### Motivation of this paper
In data stream scenario, algorithm for computing data stream context needs to satisfy the following requirement:
1. the space used by the algorithm should be small, at most polylogarithmic in $n​$.
2. processing an update should be fast and simple, with a usable accuracy guarantees.

This paper proposes a new sketch construction with
>1. space used is proportional to $\frac{1}{\varepsilon}$
>2. the update time is sublinear in the size of the sketch
>3. improve the space bounds of previous results from $\frac{1}{{\varepsilon}^2}​$ to $\frac{1}{\varepsilon}​$
>4. improve the time bounds from $\frac{1}{{\varepsilon}^2}$ to 1. 

### Count-Min Sketch
Two parameters:
> $\varepsilon$ and $\delta$: the error in answering a query is within a factor of $\varepsilon$ with probability $1 - \delta$

The space and update time will consequently depend on $\varepsilon$ and $\delta​$. 

It is named after the two basic operations used to answer point queries, counting first and computing the minimum next.

- Three types:
>1. a point query
>2. a range query
>3. an inner product query

- Data structure 
Two-dimensional array counts with width $w$ and depth $d$, set $w = \frac{e}{\varepsilon}$, and $d = ln(\frac{1}{\delta})$
$d$ hash functions:
$$
h_1,..., h_d \space (\{1,...,n\} \rightarrow \{1,...,w\})
$$

- Update procedure
An update $(i_t, c_t)$ arrives, i.e., item $a_{i_t}$ is updated by a quantity of $c_t$
> 1. $c_t$ is added to one count in each row, and the counter is determined by hash function $h_j$.
> 2. the array of $w \times d$ counts
$$
count[j, h_j(i_t)] \leftarrow count[j, h_j(i_t)] + c_t, \forall 1 \leq j \leq d
$$
![1554376060947](paper_figure/1554376060947.png)

- The analysis of point query



### Implementation and Evaluation

## 2. Strength (Contributions of the paper)

## 3. Weakness (Limitations of the paper)

## 4. Future Works
