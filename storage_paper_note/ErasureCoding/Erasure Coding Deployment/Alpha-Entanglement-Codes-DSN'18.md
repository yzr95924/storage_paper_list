---
typora-copy-images-to: paper_figure
---
# Alpha Entanglement Codes: Practical Erasure Codes to Archive Data in Unreliable Environments
@DSN'18 @ Redundancy Propagation
[TOC]

## Summary
***Motivation of this paper***: this paper intends to design flexible and practical erasure codes with high fault-tolerance to improve data durability and availability even in catastrophic scenarios.

***Alpha Entanglement Codes (AEC) $(\alpha, s, p)$***: The idea of AE code is to create redundancy by **tangling (mixing)** new data and blocks with old ones, building entangled data chains that are woven into a growing mesh of interdependent content. 
>1. $\alpha$: determine the local connectivity, the number of parities created per data block.
>2. $s$ amd $p$: determin the global connectiviry of data blocks in the grid. $p$ defines the number of helical strands. $s$ the number of horizontal strands

Each node belongs to $\alpha$ strands each edge belongs to only one strand.

- Alpha Entanglements permit changes in the paramenters without the need to encode the content again (**dynamic** fault-tolerance, long-term storage system)
- The encoder builds block chains, strands, that alternate data and redundant blocks.
> The entanglement function computes the exclusive-or (XOR) of two consecutive blocks at the head of a strand and inserts the output adjacent to the last block.

- The interwined graph of AEC:
![1535630992315](paper_figure/1535630992315.png)
![1535718654925](paper_figure/1535718654925.png)

- The decoder repairs a node using two adjacent edges that belong to the same strand, thus, there are $\alpha$ options.

***Implementation and Evaluation***:
**Implementation**: It mentions two use cases of entangled storage system
>1. A Geo-Replicated Backup
>2. Disk Arrays

**Evaluation**: It examines the design of AE codes to understand how the code settings impact on fault tolerance and write performance.
>1. Code Parameters and Write Performance 
>2. Code Parameters and Fault Tolerance

## Strength (Contributions of the paper)
1. These mechanisms based on the novel redundancy propagation, which are robust and sound, but they are also efficient and easily implementable in real system.
2. The encoder and decoder are lightweight, based on exclusive-or operations, and offer promising trade-offs between security, resource usage and performance. 
3. This paper also mentions the use cases for distributed and centralised storage systems. 
## Weakness (Limitations of the paper)
1. This paper does not mention how to improve the efficiency of repairs. It just focuses on reducing the maximum possible storage overhead.

## Future Works
1. A point that can be considered is how to improve its efficiency of repairs.