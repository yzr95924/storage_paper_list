---
typora-copy-images-to: paper_figure
---
# Repair Pipelining for Erasure-Coded Storage
@ATC'17 @ECPipe
[TOC]

## Summary
***Motivation of this paper***: While many paper tried to reduce the repair time effectively, it still remains higher than the normal read time in general. So this paper wants to solve the question that "Can it further reduce the repair time of erasure code to almost the same as the normal read time?"
In the previous work called PPR, it solves the problem that the bandwidth usage distribution is highly skewed. However, it still remains not fully balanced.

### ECpipe
#### 1. Main Idea
In this paper, it assumes **the network bandwidth is the bottleneck**. And Repair pipelining is designed for speeding up the repair of a single failed block per stripe. 
The main argured point:
> The amplification of repair traffic implies the congestion ar the **downlink** of the requestor, thereby increasing the overall repair time. 

The goal of ECPipe:
1. eliminating bottlenecked links (no link transmits more traffic than others)
2. effectively utilizing bandwith resoureces during repair (i.e., links should not be idle for most times)
3. Repair Pipelining is designed for speeding up the repair of **a single block** failed per stripe.

#### 2. Motivation
the drawback of conventional repair is the bandwidth usage distribution is highly **skewed**
> the download link of the requestor is highly congested, while the links among helpers are not fully utilized.



***Implementation and Evaluation***:

## Strength (Contributions of the paper)
1. This paper addresses two types of repair operations, including **degraded reads** and **full-node recovery**. And the repair pipelining can achieves $O(1)$ repair time in homogeneous environments.
2. It also considers the case in heterogeneous environments (i.e., link bandwidths are different)
>a. parallel reads of reconstructed data 
>b. find an optimal repair path across storage nodes

3. Implement a repair pipelining prototype, which runs as a middleware layer atop an existing storage system and performs repair operations on behalf of the storage system.
4. Evaluate repair pipelining on a local cluster and two geo-distributed Amazon EC2 clusters
## Weakness (Limitations of the paper)
1. If a stripe has multiple failed blocks, it triggers a multi-failure repair, which resorts to conventional repair. So this paper not addresses the problem of how to solve it by using pipelining.
## Future Works


## Reviewer Feedback
### Weaknesses
- The authors argue PPR does not evenly distribute the network bandwidth. But it is easily fixable given the linearity of earsure codes.
- Chaining storage nodes in a pipeline is also dangerous, any link failure between the helpers or slowdown of any helper can now affect the whole pipleline.
- Contributions are not necessarily novel.
- The implementation is weak, as a standalone module in oreder to make implementation easier. A direct implementation inside HDFS and QFS and making them accepted into upstream open source would be a much stronger contribution, because I think this work has practical value. 
- Over-simplified assumptions (e.g., ignoring computation and memory overhead, static and uniform network links) make it more like a research prototype instead of a readily deployble product.
- Netowrk bandwidth as a bottleneck
- Realistic considerations 
