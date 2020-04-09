---
typora-copy-images-to: paper_figure
---
# Erasure Coding in Windows Azure Storage 
@ATC'12 @ Local Reconstruction Codes
[TOC]

## Summary
***Motivation of this paper***: To provide durability for customers' data and to keep the cost of storage low, Windows Azure Storage uses erasure coding. To reduce the number of erasure coding fragments that are offline, while still keeping the storage overhead low, this paper propses Local Reconstruction Codes (LRC)

The **goals** of LRC are:
1. reduce the minimal number of fragments that need to be read from to reconstruct a data fragment.
2. provide significant reduction in storage overhead while maintaining higher durability than a system that keeps 3 replicas for the data.

***Local Reconstruction Codes***:
To achieve these goals, it computes some of parities from a subset of the data fragments. (**Global parities** and **Local parities**)
>Global parities: are computed from **all** the data fragments
>Local parities: are computed from **a group** of data fragments 

![1534147681755](paper_figure/1534147681755.png)
However, LRC needs to add more parities that Reed-Solomon Code, it does the tradeoff between storage overhead and reconstruction cost. And LRC is not Maximum Distance Separable (MDS). It exists the type of **information-theoretically non-decodable**.

Thus, it is also a challenge to construct a single set of **coding equations** that achieves the *Maximally Recoverable (MR)* property, or being able to decode all the information-theoretically decodable failure patterns. And this paper also discusses how to construct coding equations.

For checking decodability, it also proposes an algorithm: *Firstly, check each local group, then, examine the data fragments and the global parities.* 

In general, the key properties of a $(k, l, r)$ LRC are: 1) single data fragment failure can be decoded from $\frac{k}{l}$ fragments; 2) arbitrary failures up to $r+1$ can be decoded.

***Reliability Model and Code Selection***:
For its reliability model, it adds a simple extension to generalize the Markov Model, in order to capture the unique state transitions in LRC. (because failure mode not only depends on the size of node fails, but also which subset of nodes fails.).
This paper also plots the trade-off curve between the **storage overhead** and **reconstruction cost**, and compares it with RS code and Modern codes.

***Implementation and Evaluation***
**Implementation:** Its choice is to implement erasure coding inside the stream layer. It is based on the fact that it fits the overall WAS architecture where the stream layer is responsible for keeping the data durable within a stamp.

**Evaluation:** It compare the LRC's performance with RS code for **small I/Os** and **large I/Os**, respectively.
>1. small I/Os: latency and the number of I/Os taken by the requests;
>2. large I/Os: latency and bandwidth consumption
>3. decoding latency

## Strength (Contributions of the paper)
This paper proposes the Local Reconstruction Codes which can 1) reduce the minimal number of fragments that need to be read from to reconstruct a data fragment and 2) provide significant reduction in storage overhead while maintaining higher durability than a system that keep 3 replicas for the data. 

## Weakness (Limitations of the paper)
1. LRC is not a MDS code, it can not tolerant all the failure, it needs to identify whether the failure mode is recoverable. 
2. In its reliability model, it not mentions how to deal with correlated failures in detail.
3. To construct coding equation for achieving the maximum recoverable property, it also is a challengeable issue.

## Future Work
For the first weakness, it can also further consider how to improve the fault tolerance better.

