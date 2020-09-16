---
typora-copy-images-to: paper_figure
---
# Rethinking Erasure Codes for Cloud File Systems: Minimizing I/O for Recovery and Degraded Reads
@FAST'12 @ Minimizing Recovery I/O
[TOC]

## Summary
***Motivation of this paper***: Existing erasure codes are not designed with recovery I/O optimization in mind. So it needs
>1. optimize existing codes for these operations 
>2. new codes which are intrinsically designed for these operations

***Algorithm for minimizing number of symbols***: 
The algorithm takes as input a **Generator matrix** whose symbols are single bits and the identity of a failed disk and outputs equations to decode each failed symbol. 
This algorithm firstly finds a decoding equation for each failed bit while minimizing the number of total symbols accessed. Given a **code generator matrix** and a list of failed symbols, the algorithm outputs **decoding equations** to recover each failed symbol.
> 1. enumerate all valid decoding equations for each failed symbol
> 2. **directed graph** formulation of problem makes it convenient to solve.*(convert this NP-Hard problem to finding the shortest path through the graph)*
>![1533978994514](paper_figure/1533978994514.png)

For the expensive computational overhead, solutions to all common failure common failure combinations may be computed offline **a prior** and stored for future use.

***Rotated Reed-Solomon Codes***: Rotated Reed-Solomon Codes have the modification to standard Reed-Solomon codes. These codes have been designed  to optimize the performance of degraded reads for single disk failures. (**improve the penalty of degraded reads**)
![1533991845908](paper_figure/1533991845908.png)
![1533992512318](paper_figure/1533992512318.png)

***Analysis and Evaluation***:
1. Reading from the $P$ drive or using standard Reed-Solomon codes is not a good idea in cloud storage system.
2. Generally, optimally-sparse and minimum-density codes perform best for disk reconstruction.
**Evaluation**:
1. Data Recovery Rate v.s. Symbol Size
2. Data Recovery Rate v.s. Different erasure code 


## Strength (Contributions of the paper)
1. Algorithm minimizes the amount of data needed for recovery and it is applicable to **any XOR** based erasure code.
2. Its implementation and evaluation of this algorithm demonstrates that minimizing recovery data translates directly into improved I/O performance for cloud file systems.
3. It develops a new class of codes, called Rotated Reed-Solomon codes. A new class of Reed-Solomon Codes which optimize degraded read performance. Better choice than standard Reed-Solomon codes for the cloud. (**arbitrary numbers of disks and failures**)

## Weakness (Limitations of the paper)
1. This paper mentions that its algorithm is computationally expensive (from seconds to hours of compute-time)

## Future Work 
1. This paper can be extended by considering how to decrease the computation overhead instead of computing offline **a prior** and storing it for future use.

