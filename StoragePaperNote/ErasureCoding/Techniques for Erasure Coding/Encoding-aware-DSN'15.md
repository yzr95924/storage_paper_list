---
typora-copy-images-to: paper_figure
---
# Enabling Efficient and Reliable Transition from Replication to Erasure Coding for Clustered File Systems
@DSN'15 @Encoding-aware replication
[TOC]

## Summary
***Motivation of this paper***: When data blocks are first stored with replication, replica placement plays a critical role in determining both performance and availability of the subsequent encoding operation,
> *random replication (RR)* brings both performance and availability issues to the subsequent encoding operation.

***Encoding-Aware Replication***
- **Main Idea**: For each group of data blocks to be encoded together, EAR keeps one replica of each data block in the same rack, while storing the remaining replicas in other racks by equivalently solving a maximum matching problem.
> It assumes that cross-rack data transfer is the performance bottleneck in a CFS architecture.

- RR potentially harms performance and availability. 
> The primary reason is the replica layout of each data block is independently determined, while the data blocks are actually related when they are encoded together. 
> ![1537101426419](paper_figure/1537101426419.png) 

**Eliminate Cross-Rack Downloads**
1. Formation of a stripe: blocks with at least one replica stored in the same rack.\
> call this rack the **core rack**.

**Guarantee the reliability requirements without relocation**
1. Model the replica layout to a bipartite graph.
2. Modeling Reliability problem to a **Max matching** $\rightarrow$ a **Max flow problem**  in bipartite graph. 

***Implementation and Evaluation***:
**Implementation**:
![1537113167500](paper_figure/1537113167500.png)
**Evaluation**:
1. Testbed Experiment: 13-nodes Hadoop cluster
>a. Encoding Throughput
>b. Write Reponse Time
>c. Impact on MapReduce Jobs

2. Discrete-Event Simulations
## Strength (Contributions of the paper)
1. This paper presents EAR, a new replica placement algorithm that addresses both performance and availability issues encoding operation.
2. It implements EAR on HDFS implementation, with only a few modification to the source code.
3. It conducts testbed experiments on a 13-machine cluster and discrete-event simulations based on CSIM 20.
4. Examine the replica distribution of EAR, and show that it maintains load balancing in storage and read requests as in RR. 
## Weakness (Limitations of the paper)
1. This paper considers a very simple scenarios, and ignores the heterogeneous workloads and hardware resources.
## Future Works
1. I think it can further study the scenarios with heterogeneous workload and hardware resources.