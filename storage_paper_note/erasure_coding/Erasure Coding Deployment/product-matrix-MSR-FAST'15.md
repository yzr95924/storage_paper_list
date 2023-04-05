---
typora-copy-images-to: paper_figure
---
# Having Your Cake and Eating It Too: Jointly Optimal Erasure Codes for I/O, Storage and Network-bandwidth
@FAST'15 @MSR
[TOC]

## Summary
***Motivation of this paper***: MSR codes are optimal with respect to storage and network transfers. However, MSR codes do not optimize with respect to I/Os. In general, its I/O overhead is higher than that in a system employing RS code. And with the increasing speeds of newer generation network interconnects, I/O is becoming the **primary bottleneck** in the performance of the storage system. This paper wants to design erasure codes that are <u>simultaneously optimal in terms of I/O, storage, and network bandwidth during reconstructions.</u> 

***Optimizing I/O during reconstruction***: In this paper, it proposes two algorithms to **transform MSR codes into codes that are I/O efficient as well**.
- Algorithm 1: transforms to minimizes I/O cost **locally** at each helper block
- Algorithm 2: builds on top of Algorithm 1 to minimize I/O cost **globally** across all blocks
For Algorithm 1, it wants to let the I/O consumed equal to Network Bandwidth consumed by using **Reconstruct-by-transfer (RBT)**.
![1533632589963](paper_figure/1533632589963.png)
The rational behind this algorithm are two properties in MSR code:
>1. Independence between helpers: Function computed at a helper is not dependent on which other blocks are helping.
>> Thus, a block computes **pre-determined functions** to aid in reconstruction of each of the other blocks.
>2. Independence between functions computed at helper block
>> ![1533635551383](paper_figure/1533635551383.png)
>> Under MSR, a block does minimum I/O when helping in RBT-fashion

For Algorithm 2, it focus on choosing RBT-helper assignment and minimize I/O cost globally across all blocks. And Algorithm 1 takes the assignment of "who acts as RBT helper to whom" as input. In its model, it uses two parameters $\delta$ and $p$
> $\delta$: the relative importance between systematic and parity blocks.
> $p$: $0 \leq p \leq 1$, aim to capture the fact that: when the reconstruction of a block is to be performed, every other block may individually be unavailable with **a probability $p$ independent of all other blocks.**

The expected reconstruction cost for any block, under a given number of RBT-helper blocks, can be easily computed using parameter $p$. And then it can compute number of RBT-helpers for each block and select the RBT-helper for each block. Two extreme cases:(***Maybe this part can be improved***)
> Complete preferential treatment for data blocks (**Systematic** pattern): Each block RBT-helps data blocks 
> Equality for all: no preferential treatment (**Cyclic** pattern): Each block RBT-helps following blocks

***Implementation and Evaluation***: It uses **Jerasure** and **GF-Complete** libraries for finite-field arithmetic operations. In its evaluations, it mainly focus on:
>1. Data transfers across the Network
>2. Data read and number of I/O
>3. I/O completion time during reconstruction.
>4. Decoding and encoding performance
>5. RBT-helper assignment algorithm

## Strength (Contributions of the paper)
1. This paper tries to solve the I/O problem in MSR, so that it is jointly optimal erasure codes for I/O, Storage, and Network-bandwidth. It proposes algorithm to transform MSR codes
2. Implemented and evaluated application onto Product-Matrix MSR codes
3. Analytical results on optimality.

## Weakness (Limitations of the paper)
1. This paper just uses the Product-Matrix MSR codes to do the transformation. How about other MSR codes? So it also needs to prove the generality.
2. For its global optimization model, I think it is a little simple because it just includes two parameters.

## Future Work
1. I think the global optimization model is a good point for extension and the helper assignment is a common problem in the EC storage system. 