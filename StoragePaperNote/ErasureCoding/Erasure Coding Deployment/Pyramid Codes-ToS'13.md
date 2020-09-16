---
typora-copy-images-to: paper_figure
---
# Pyramid Codes: Flexible Schemes to Trade Space for Access Efficiency in Reliable Data Storage Systems
@ToS'13 @Erasure Code
[TOC]

## Summary
***Motivation of this paper***: Replication and MDS-based ERC are regarded as two extremes of the tradeoffs between storage space and access efficiency. This paper intends to find the middle ground.

***Method***
- Four key metrics concerning any ERC scheme:
  1) Storage overhead 
  2) Fault Tolerance (unrecoverable probability) 
  3) Access Efficiency (the expected number of blocks required to serve an unavailable data block) 
  4) Update Complexity 


**Basic Pyramid Codes**:
![1537502009449](paper_figure/1537502009449.png)
It divides the six data blocks into two equal size groups $S_1 = \{d_1, d_2, d_3\}$, $S_2=\{d_4,d_5,d_6\}$. Then computer one redundant block for each group, denoted as $c_{1,1}$ for $S_1$ and $c_{1,2}$ for $S_2$. $c_{1,1}$ and $c_{1,2}$ are the **group redundant blocks**. $c_2$ and $c_3$ are **global redundant blocks**. **The Pyramid Codes are not MDS**.
$$
c_{1,1}=\sum_{i=1}^{3} \alpha_{i,1} d_i
$$
$$
c_{1,2}=\sum_{i=4}^{6} \alpha_{i,1} d_i
$$

- **Conclusion**: The BPC uses more storage space than the MDS code, but it gains in recpnstruction read cost and fault tolerance, while maintaining the same update complexity. 
(**higher unrecoverable probability**)

**Recpverability Limit**: 
It is possible to improve fault tolerance without affecting the remaining three metrics (storage overhead, update complexity, access efficiency)
Two concepts in ERC scheme: 
>1. coding dependency: defines which set of data blocks is used to compute each individual redundant block. (a bit vector) (**determines storage overhead, reconstruction read cost, and update complexity**)
>2. coding equations: defines how the set of data blocks compute each redundant block, or the coding coefficients for computing the redundant block.(**only affect fault tolerance**)

- The fault tolerance can be improved by modifying the coding equations. Thus it is possible to improve the fault tolerance without affecting storage overhead, access efficiency, and update complexity.
> achieved by modifying only coding equations while keeping coding dependency untouched.

**Maximally Recoverable Property**: An erasure-resilient coding scheme is said to hold the maximally recoverable (MR) property under predeterminded coding dependency, if all failure cases satisfying the matching condition are reocverable.

- To investigate the recoverability limit of a given predetermined coding dependency, it establishes a necessary condition, called the **matching condition** to characterize the limit of recoverability for the any failure case.
> Give a Theorem: a failure case is said to satisfy the matching condition whenever the corresponding **reduced decoding Tanner graph** contains <u>a full-size matching</u>
>
> ![1537519750596](paper_figure/1537519750596.png)

**Generalized Pyramid Codes**
GPC holds the maximally recoverable property. Compare with BPC, GPC also accomodates more flexible coding dependency. 
![1537523583894](paper_figure/1537523583894.png)


## Strength (Contributions of the paper)
1. It designs two new ERC schemes: basic pytamid codes (BPC) and generalized pyramid codes (GPC). Both schemes require slightly more storage space than MDS-based ERC, but significantly improve the performance of reconstruction.
2. It establishes a necessary matching condition to characterize the limit of failure recovery, that is, unless the matching condition is satisfied, a failure case is impossible to recover.
3. In addition, it also defines a maximally recoverable (MR) property. For all ERC schemes holding the MR property, the matching condition becomes sufficient.
## Weakness (Limitations of the paper)
1. For Pyramid code, the improvment of access efficiency comes at the cost of extra storage overhead.
2. The construction of GPC is very complex, and it needs to take an iterative approach which poses a high construction overhead.
## Future Works
1. How to reduce the construction overhead of the GPC?
