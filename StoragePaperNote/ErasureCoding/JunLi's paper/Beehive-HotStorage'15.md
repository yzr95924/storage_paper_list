---
typora-copy-images-to: paper_figure
---
# Beehive: Erasure Codes for Fixing Multiple Failures in Distributed Storage Systems
@HotStorage'15 @Multiple Failure @MSR code
[TOC]

## Summary
***Motivation of this paper***: Generally, distributed storage systems will reconstruct multiple failure blocks separately. However, data unavailability events can be **correlated**, specifically, many disks fail at similar ages. <u>To leveage the advantage of the correlated failures, this paper wants to reconstruct multiple missing blocks in batches, to save both network transfer and disk I/O during reconstruction.</u>

***Beehive***: 
- An instant benefit of Beehive is each block will only be **read once** to reconstruct multiple blocks. 
![1534820931384](paper_figure/1534820931384.png)
- The construction of Beehive codes is built on top of product-matrix MSR codes, based on one particular *product-matrix* construction proposed by Rashmi.
> 1. MSR codes constructed are systematic
> 2. Unlike other constructions that impose constraints on specific values of $d$ or $k$, the construction proposed in product-matrix is much more general by only requiring $d \geq 2k-2$

**Encode**:
It divides one generation of the original data into two parts that contains $k$ and $k-1$ blocks. In first part each block contains $d-k+1$ segments. In second par each block contains $t-1$ segments.
From the original data, Beehive computes $n$ blocks. each block contains the $d-k+1$ segments from $g_iF$ and the $t-1$ segments from $\sum_{l=1}^{k-1}a_{i,l}c_l$
![1534843008014](paper_figure/1534843008014.png)

**Decode**:
- The helpers computes the decoding information and sends it to newcomer $j$.
- At the side of newcomers, it can divide their operation into two stages. In the first stage, it will receive $d$ segments from helpers and do the calculation. In the second stage, it will send the calculation result to another newcomer $j^{'}$, and it will receive $t-1$ segments from other newcomers as well.
- During reconstruction, each newcomer will receive $d+t-1$ segments, achieving the optimal network transfer to reconstruct $t$ blocks.


***Implementation and Evaluation***:
Implement Beehive in C++ by using the Intel storage acceleration library (ISA-L) for the finite field arithmetic. 
## Strength (Contributions of the paper)
1. this paper propose a new family of erasure codes that can reconstruct multiple blocks at same time. And each block will only be read once to reconstrcut multiple blocks. 
## Weakness (Limitations of the paper)
1. Need additional storage overhead 
2. Its encoding process is based on MSR code, so Beehive's encoding operation is bit slower than MSR codes
## Future Works
1. A very improtant issue I consider is how to combine Beehive with practical distributed storage system.

