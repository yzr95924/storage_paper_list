---
typora-copy-images-to: paper_figure
---
How to Share a Secret
------------------------------------------
|          Venue           |         Category         |
| :----------------------: | :----------------------: |
| Communication of the ACM 1979 | Secret Sharing Algorithm |
[TOC]

## 1. Summary
### Motivation of this paper
Goal: design a convenient secret sharing algorithm $(k, n)$ *threshold scheme*.
### Shamir Secret Sharing
Based on **polynimial interpolation**, for a $(k, n)$ scheme, and data $D$
1. pick a random $k - 1$ degree polynomial, 
$$
q(x) = a_0 + a_1x + a_2x^2 + a_3x^3 + ...... + a_{k-1}x^{k-1}
$$
2. Generate $n​$ shares
$$
a_0 = D, D_1 = q(1), D_2 = q(2), ......, D_n = q(n)
$$
3. Given any subset of $k$ of there $D_i$ values, with their identifying indices, it can calculate the coefficients of $q(x)$ by interpolation, and then evaluate $a_0 = q(0) = D$

The efficiency of  polynomial evaluation and interpolation
> $O(nlog^2n)$

- To make the claim more precise, it can use **modular arithmetic** instead of real arithmetic.

- If $D$ is long, it is advisable to break it into shorter blocks of bits (can be handled separately.)

## 2. Strength (Contributions of the paper)
1. When $k$ is kept fixed, $D_i$ pieces can be dynamically added or deleted *without affecting the other $D_i$ pieces*.

2. It is easy to change the $D_i$ pieces without changing the original data $D$.

## 3. Weakness (Limitations of the paper)
1. The storage overhead (Storage space blow up) is very large.

## 4. Future Works
