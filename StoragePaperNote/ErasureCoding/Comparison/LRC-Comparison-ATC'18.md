---
typora-copy-images-to: paper_figure
---
# On Fault Tolerance, Locality, and Optimality in Locally Repairable Codes
@ATC'18 @LRC
[TOC]

## Summary
***Motivation of this paper***: Existing theoretical models cannot be used to directly compare different LRCs to determine which code will offer the best recovery performance, and at what cost. Thus, this paper performs the first systematic comparison of existing LRC approaches.
The goal of this paper: Lay mathematical basis for comparison
>1. Define parameters for comparison
>2. Compare codes in all sets of configurations, extend LRCs
>3. Compare for $9 \leq n \leq 19$ and overhead $\leq 2$
>4. Validate in a real system

***Methodology***: 
- For full-LRCs and data-LRCs $(n, k, r)$
>1. Full-LRCs: all the blocks, including the global parities, can be repaired locally from $r$ surviving blocks.
>2. Data-LRCs: Only the data blocks can be repaired in local fashion by $r$ surviving blocks, while the global parities require $k$ blocks for recovery.

This paper provides a framework for directly comparing all codes in all parameter combinations. 
- the minimal distance $d$ is more appropriate for large-scale analysis, instead of MTTDL
- Average Repair Cost (ARC)
$$
ARC=\frac{\sum^n_{i=1}cost(b_i)}{n}
$$

- Normalized Repair Cost (NRC), it can be viewed as the average cost of repairing a failed data block, where the cost of repairing the parity blocks is amortized over the $k$ data blocks.
$$
NRC=ARC \times {\frac{n}{k}}=\frac{\sum^n_{i=1}cost(b_i)}{k}
$$

- Degraded Cost: average cost of repairing data blocks only:
$$
Degraded Cost=\frac{\sum^k_{i=1}cost(b_i)}{k}
$$
This paper compares Azure-LRC, Xorbas, Reed-Solomon in those aspects 
***Implementation and Evaluation***:
- This paper follows the theoretical analysis with an evaluation of those codes in a **Ceph** cluster deployed in AWS EC2. It uses the **Jeasure Erasure Code plugin** and **Locally Repairable Erasure Code plugin** to implement Azure-LRC and Azure-LRC+1
- For the generator matrix of Optimal-LRC: it uses the **Matlab** to do the construction.

**Evaluation**:
1. Amount of data read and transferred 
2. Repair time 
3. Foreground Workloads: RADOS Bench, writes objects for a given amount of time, reads all the objects, and terminates.
The evaluation result of Ceph shows the benefit of full-LRCs and data-LRCs depends on the underlying storage devices, network topology, and foreground application load.

## Strength (Contributions of the paper)
1. This paper conducts a theoretical comparison between the different LRC approaches and demonstrates the limitations of existing measures, such as locality and average repair cost.
2. **Optimal-LRC implementation**: It defines new metrics that model each code's overhead, full-node repair cost, degraded read cost, and fault tolerance.
3. **Amazon EC2 deployment**: This paper deploys its Ceph cluster on 20 instances in the Amazon EC2.
## Weakness (Limitations of the paper)
1. This paper just does the comparison between various LRCs, which is not very novel.
2. Its comparative framework of using NRC is not very novel.
## Future Works
1. I think one point can be extended is to refine its comparative framework. Currently, just give a new definiation in term of *Noramlized Repair Cost* is not enough.

