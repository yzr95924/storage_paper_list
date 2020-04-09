---
typora-copy-images-to: paper_figure
---
PARIX: Speculative Partial Writes in Erasure-Coded Systems
--------------------------------

@ATC'17 @Erasure Coded writes
[TOC]

## 1. Summary
### Motivation of this paper: 
- This paper aims to solve the problem of partial write in EC. The main problem of partial write of EC is the high I/O amplification. Although this can can be mitigate by leveaging parity logging to append incremental change log (update the delta of parity), it agrues the cost of **in-place read-and-write** is still expensive (its latency is equivalent to that of random seek). 

- This paper wants to further optimize parity logging by eliminating the read. (i.e., the key to improve the performance of partial writes is to reduce the number of reads)

### PariX
- The key idea of PariX
> 1. Each parity records $m$ series of changes logs of $m$ data blocks.
> > The rationale: $d^{(0)}_i \leftarrow d^{(1)}_i, d^{(2)}_i, d^{(3)}_i, ... ,d^{(r)}_i​$,
> > $p_j^{(r)} = p_j^{(0)} + a_{ij} \times (d_i^{(r)} - d_i^{(0)})​$
> 2. The Speculation:
> > In this paper, its speculation means whether the parities need $d_i^{(0)}$ or not.
> > it speculates that 
> > 1) Assume $d_i^{(0)}$ is NOT needed (mostly right)
> > 2) Send $d_i^{(0)}$ only when it actually needed (sometimes only)

- PariX's overhead is a disk write, while previous parity log schemes's overhead is **a disk write after a disk read**, assuming the read cache is missed.

- The whole workflow of PariX:

  ![1547042229954](paper_figure/1547042229954.png)

**Note that**: For the case in replication, it is also (1 loop + 1 RTT) 

### Implementation and Evaluation
- Based on a previous block store of this group,  master server is implemented by **MySQL** and **Redis**. (refer to Usra (Open Source partly))
- Random I/O lantency
- Recovery time

## 2. Strength (Contributions of the paper)
- the idea of this paper is not very complex, but can really work. Reducing the time of read is an intitutive method to impove the performance of writing. 
- the method can truly work in the scenario of  
## 3. Weakness (Limitations of the paper)
- When there are too many one-shot write in a workload, PariX cannot work well. Because it cannot fit its speculation. 
- When PariX speculation fails, it needs to send $d_i^{(0)}$ to all parities, this penatly would introduce high network traffic overhead. (Although it argues that parities writes are small)
- In its evaluation, it just test this prototype in a small testbed (with 10 machines), with EC (4, 2). This setting cannot prove the scalability of it.

## 4. Future Works
- In this method, its speculation is very simple, which cannot fit the one-shot write well. How to handle the large sequential one-shot write workload? Maybe it is a point can be extended.
