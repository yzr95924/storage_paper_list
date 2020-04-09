# A Tale of Two Erasure Codes in HDFS

@FAST'15 @Techniques for Erasure Codes @Adaptive Coding

[TOC]

## Summary

***Motivation of this paper***: The increase in the amount of data to be read and transferred during recovery for an erasure-coded system results in two major problems: **high degraded read** and **longer reconstruction time**. This paper wants to exploit the **data access characteristics** of Hadoop workloads to achieve better recovery cost and storage efficiency than the existing HDFS architecture.
![1533785674947](paper_figure/1533785674947.png)
***Hadoop Adaptively-Coded Distributed File System (HACFS)***: 
HACFS is the first sustem that uses a combination of two codes to dynamically adapt with workload changes and provide both low recovery cost and storage overhead.

- Adaptive coding：
  It designs an adaptive coding module in HDFS and uses a **state machine** to describe the state transitions in HDFS. To maintain this state machine, HAFCS also accounts for the read accesses to data blocks in a file.

> Read hot files with a high read count are encoded with a *fast code*.
> Read cold files with a low read count are encoded with a *compact code*.
> ![1533801963837](paper_figure/1533801963837.png)

***Implementation and Evaluation***:

1. Implementation
   This paper has implemented HACFS as an extension to the HDFS-RAID module in the Hadoop Distributed File System. Its key module is the adaptive coding module, which tracks the system states and invokes the different coding interfaces. It includes about 3000 LOCs.
   ![1533803970846](paper_figure/1533803970846.png)
2. Evaluation
   Compare with Colossus FS, FB HDFS, Azure

- Degraded read latency
- Reconstruction time
- Storage overhead

## Strength (Contributions of the paper)

1. This paper designs the Hadoop Adaptively-Coded Distributed File System (HACFS) that adapts to workload changes by using two different erasure codes.

> **fast code**: optimize recovery cost of degraded reads and reconstruction
> **compact code**: provide low and bounded storage overhead 

1. A novel conversion mechanism in HACFS to efficiently up/down-code data blocks between the two codes. 
2. Implement HACFS as an extension to HDFS and demonstrate its efficacy using two cases studies with Product and LRC family codes.

## Weakness (Limitations of the paper)

1. This paper mentions that the conversion cost is high when the HACFS system aggressively converts blocks to limit the storage overhead by upcoding hot files into compact code. So this can be a issue

## Future Work

1. The paper can be extended by solving the problem of how to reduce the conversion cost. 

