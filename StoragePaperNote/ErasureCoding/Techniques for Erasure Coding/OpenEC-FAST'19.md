# OpenEC: Toward Unified and Configurable Erasure Coding Management in Distributed Storage Systems
@Erasure code @ongoing work
[TOC]

## Summary
***Motivation of this paper***: 
Integrating new erasure coding solutions into existing distributed storage systems is arguably a challenging task that requires non-trivial re-engineering of the underlying storage workflows.
Limitation of EC management
>1. Limited support for adding advanced erasure codes
>2. Limited configurability for workflows of coding operations
>3. Limited configurability for placement of coding operations  

***OpenEC***:
- Main Idea:
Its main idea is to decouple erasure coding management from the underlying DSS operations
First, this paper design a new erasure coding programming model which is not based on low-level data buffers but based on high-level abstractions, called **ECDAG**. ECDAG is a directed graph to describe the erasure coding process. It has freedom to be enforced with different underlined physical meanings. ECDAG provides the opportunity for control flow and data flow design in erasure coding process.
> Centralized application, distributed application

For the OpenEC design, it foucs on two types design: **Online EC design** and **Offline EC design**. And it also provides three levels of optimimzation to custom ECDAG designs for offline erasure coding.
>1. Level 0: leverage the **Bind** to avoid the redundant traffic in distributed application of ECDAG.
>2. Level 1: In offline erasure coding, OpenEC leverages the **AddConstraint** takes the locality into consideration and choose location from the locations of all the childs. 
>3. Level 2: The optimization works in **hierarchical data center**. OpenEC applies pipelining technique. And it can sort the children according to their regions to check whether layering can be applied to reduce the cross region network traffic.

## Strength (Contributions of the paper)
1. this paper propose a new programming model for EC implementation and deployment **ECDAG**, which defines the flows if erasure coding operations as a directed acyclic graph.
2. this paper also designs OpenEC, which translates ECDAGs into erasure coding operations atop a DSS. In particular, OpenEC can self-customize ECDAGs for hierarchical topologies to improve repair performance.
3. it also prototypes OpenEC on HDFS-RAID and Hadoop 3.0 HDFS.
4. It conducts extensive experiments on OpenEC in a local cluster and Amazon EC2.

## Weakness (Limitations of the paper)

## Future Work

