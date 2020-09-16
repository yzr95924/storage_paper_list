---
typora-copy-images-to: paper_figure
---
# Zebra: Demand-aware Erasure Coding for Distributed Storage Systems
@IWQoS'16 @Heterogeneous Erasure Codes @How to use the skewness of demand in distributed 

[TOC]

## Summary
***Motivation of this paper***: For the erasure code $(k, r)​$, the value of $k​$ can affect both CPU overhead and Network overhead. In other word, a smaller value of $k​$ means less reconstruction overhead, leading to less network transfer for data reconstruction as well as lower latency for degraded reads. <u>$k​$ can do the tradeoff between storage overhead and reconstruction overhead</u>. (Normally, a very small portion of data are highly demanded while the rest are barely touched). This paper wants to achieve that **hot data can be reconstructed with low overhead and cold data can also enjoy low storage overhad at same time**.

***Zebra Framework***:
- Goal: dynamically assign data into multiple tiers by their demand so as to achieve a **flexible tradeoff** between storage and reconstruction overhead.
- In its model, the definition of the overall reconstruction overhead is $\sum_{i=1}^{N}d_ik_i=D \times K$ (k is the parameter in $RS(k,r)$, $d_i$ is the demand of visiting per unit of time). Thus, it intends to solve $K$ to minimize the overall reconstruction overhead with respect to the storage overhead. (**integer geometric programming problem**)
- To reduce the complexity of this optimization problem, it assumes all blocks of the same file should have the **same or similar** demand in a distributed storage system, and files with similar demand will be merged into same one. This can further refine the model.
- It finally encodes  blocks in each tier with a $(k_i, r)$ RS code, where $k_i$ is the rank of the file and $r$ is the number of failures to tolerate.

**Deploying Zebra with Online Demand**:
- To work well with the demand in the future, the demand in the mode will not be purely determined by the most recent demand, but a **linear combination** of demand over a longer period of time: $D_0$ is the most recent demand, $D$ is the demand in last interval.
$$
(1-\alpha)D+\alpha D_0
$$
- To reduce the overhead of data migration, it leverages an advantage of Cauchy RS code that **any submatrix of a Cauchy matrix is still a Cauchy matrix**. Because of this property, it can easily downgrade or upgrade data between an $(mk, r)$ and a $(k, r)$, $m \in Z$
![1535025690952](paper_figure/1535025690952.png)

***Implementation and Evaluation***:
This paper does not mention the detail of implementation. It just states that it does the simulation in the workload of Facebook.
**Evaluation:**
>1. the average of the reconstruction overhead per visit
>2. the average reconstruction overhead with various values of $\alpha$ (to describe the tradeoff the consistency and the transiency of the demand)
>3. the ratio of migration traffic to demand traffic

## Strength (Contributions of the paper)
1. All current systems require users to configure the erasure code used in each tier as well as their parameters **statically**. Zebra can configure itself **flexibly**, where only the overall stroage overhead and failure tolerance need to be manually specified.
2. This paper also consider how to make this framework more practical by using **online demand**, and solve the issue of how to reduce the data migration when the configuration of Erasure Code changes by using the feature of **Cauchy Matrix**.
## Weakness (Limitations of the paper)
1. To reduce the network traffic overhead of data migration, it leverages the feature of Cauchy Matrix, and designs some constraints for the parameters. This issue makes it not very general.
2. The model of the online demand is very simple, it is just the linear combination of consistency and trnasiency of the demand. 
3. This paper lacks the details of how to implement Zebra framework in a practical system. 
## Future Works
1. For the first weakness, how to make the parameter selection more flexible is a potential direction.

