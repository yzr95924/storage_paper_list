---
typora-copy-images-to: paper_figure
---
# Parallelism-Aware Locally Repairable Code for Distributed Storage Systems
@ICDCS'18 @Data Parallelism
[TOC]

## Summary
***Motivation of this paper***: The existing designs of locally repairable codes suffer from lilited data parallelism, since original data can only be read from specific servers. In this paper, it proposes a novel family of locally repairable codes that can achieve **low disk I/O** during reconstrcution and meanwhile **extend data parallelism from specific servers to all servers**.

***Galloper codes***
- The Galloper codes focus on the low disk I/O during reconstruction and high data parallelism at the same time.
> Main Challenge: how to spread the parity block and maintain the original properites of Pyramid codes in terms of locality and failure tolerance.
![1537156395887](paper_figure/1537156395887.png)

- Galloper codes use the **symbol remapping** to achieve the "moving" the original data from the data blocks  to all blocks. To this end, each block will need to contain both original data and parity data.

- Given a $(k, l, g)$ Galloper code, there will be $k+l+g$ blocks in total, including $k$ data blocks, $l$ local parity blocks, and $g$ global parity blocks. Among the total $k+g+l$ blocks, it also allows to associate each block with a weight that corresponds to the performance of its server. (e.g., the throughput of sequential disk read)
![1537170968620](paper_figure/1537170968620.png)
Compared with a $(4, 1)$ Reed-Solomon code, this Galloper code achieves the same failure tolerance, i.e., all original data can be decoded from any four blocks.

- The idea to convert the Reed-Solomon code into a Galloper code is to find another set of $kN$ stripes as a new basis.

**Weight Assignment**
If the performance of a server is too much higher than the rest of the servers. It should "limit" the performance of that server. Then, it can determine the actual performance of each server by solving the following **linear programming problem**.

***Implementation and Evaluation***
**Implementation**:
1. use Intel's storage acceleration library (ISA-L) to implement the finite field operations
2. also implement a prototype on Apache Hadoop

**Evaluation**
comparing with Reed-Solomon codes and Pytamid codes
>1. performance of Encoding, Decoding, and Reconstruction
>2. performance of Running Hadoop Jobs

Galloper codes achieve similar performance during most coding operations as existing Pyamid codes, but significantly improve the performance of running data analytical jobs on the coded data, on both homogeneous and heterogeneous servers.

## Strength (Contributions of the paper)
1. This paper proposes Galloper codes, a novel family of locally repairable codes, that achieve low disk I/O during reconstruction and meanwhile extend data parallelism from specific servers to all servers.
2. Galloper codes can arbitrarily determine the amount of original data placed on all servers, based on the performance of the coresponding server.
3. It also develops a prototype with Apache Hadoop.
## Weakness (Limitations of the paper)
1. the construction of Galloper code needs to choose a larger stripes set as the a basis, which can impose a high overhead of construction.
## Future Works
1. How to reduce the overhead of the construction in Galloper code can be the future work.