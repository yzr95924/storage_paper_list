---
typora-copy-images-to: paper_figure
---
# Reconsidering Single Failure Recovery in Clustered File Systems
@DSN'16 @Single Failure
[TOC]

## Summary
***Motivation of this paper***: 
1. existing studies on single failure recovery neglect the **bandwidth diversity propert** in CFS archiecture (intra-rack and cross-rack). 
2. Many single failure recovery solutions focus on XOR-based erasure-codes, which are not commonly used for maintaining fault tolerance in a CFS.
3. Existing single failure recovery solutions focus on minimizing the amount of repair traffic, but most of them do not consider the load balancing issue during the recovery operation.
*To this end, this paper aims to reduce and balance the amount of cross-rack repair traffic for a single failure tolerance.*

***Cross-rack-aware Recovery (CAR)***
Three key techniques:
- 1. CAR examines the data layout and finds a recovery solution in which the resulting reapir traffic comes from the minimum number of racks. 
![1536411514566](paper_figure/1536411514566.png)
This method is not very complex, just needs to the layout.

- 2. CAR performs intra-rack aggregation for the retrieved chunks in each rack before transmitting them across racks in order to minimize the amount of cross-rack repair traffic. 
After finding the minimum number of intact racks to be accessed for recovery, it also performs intra-rack chunk aggregation for the retrieved chunks in the same rack. (**partial decoding**)
> Due to the linearity, suppose the first $j$ requested chunks $\{H_1^{'},..., H_j^{'}\}$ are stored in the same rack, so it can specify a node in that rack to perform the linear operations based on the $\sum_{i=1}^{'}y_{i}H_{i}^{'}$

- 3. CAR examines the per-stripe recovery solutions across multiple stripes, and constructs a multi-stripe recovery solution that balances the amount of cross-rack repair traffic across multiple racks.

To describe the load balance rate $\lambda$ of the cross-rack repair traffic, it defines it as follows:
$$
\lambda = \frac{max\{t_{i,j}\}}{\sum{\frac{t_{i, j}}{r-1}}}
$$
The ratio of the maximum amount of cross-rack repair traffic across each rack to the average amount of cross-rack repair traffic over the **intact** racks.So it formulates this question into an optimization problem.
> Goal: minimize the load balancing rate, subject to the condition that the total amount of cross-rack repair.  (Minimize $\lambda$, subject to $\sum{t_{i,f}}$ is minimized)

The main idea of it is to replace the currently selected multi-stripe recovery solution with another one that introduces a smaller load balancing rate $\lambda$

![1536422986790](paper_figure/1536422986790.png)

***Implementation and Evaluation***:
**Evaluation**:
1. Cross-Rack Repair Traffic: evaluate the amount of cross-rack repair traffic when recovering a single lost chunk.
2. Load Balancing: measure the laod balancing rate (i.e, $\lambda$)
3. Computation Time and Transmission Time
## Strength (Contributions of the paper)
1. this paper identifies the open issues that are not addressed by existing studies on single failure recovery.
2. It proposes CAR, a new cross-rack-aware single failure recovery algorithm for a CFS setting.
3. It also implements CAR and conduct extensive testbed experiments based on different CFS settings with up to 20 nodes.
## Weakness (Limitations of the paper)
1. Firstly, this paper does not provider the details of how to implement this recovery scheme in to a proactical system, e.g., how to achieve the partial decoding? 
2. The idea of this paper is not vey novel and easy to understand. I think the performance of this scheme highly depends on the layout.
## Future Works
1. A very serious issue is how to decrease the overhead of the partial decoding in the internal of the a rack.
2. For a specific layout, this scheme may lead to the skewed workload for a rack. 