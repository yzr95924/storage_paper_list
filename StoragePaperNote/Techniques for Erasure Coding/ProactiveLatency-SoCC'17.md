---
typora-copy-images-to: paper_figure
---
# Latency Reduction and Load Balancing in Coded Storage Systems
@SoCC'17 @Proactive Latency Reduction 
[TOC]

## Summary
***Motivation of this paper***: the rigid load balancing schemes, i.e., passive recovery after timeout, is the major cause for long latency tails in erasure coded storage, especially in the presence of skewed demands.

***Load Balance in Coded Storage***: 
This paper intends to let system intentionally and intelligently perform degraded reads based on **demand informatoin** and **load statistics** in the system to direct requests away from hot servers. It would **proactively** launch a degraded read ($S_2$, $S_3$ and $L_1$) to reconstruct the requested object in the first place, and both the request latency and the load of server $S_1$ can be reduced.
![1535509143693](paper_figure/1535509143693.png)

- A key question: whether to serve a request with a normal read or a degraded read and which servers to serve the degraded read.
- 1. **Statisitical Optimization**: In the model of this paper, the **load balancing metric $F()$** is defined as the function of the **expected loads $L$** on all storage nodes plus the **exisiting queues pending $Q$** on them.
$$
minimize: \quad F()=\vec {L}+ \vec {Q}
$$

- 2. **Per-Request Optimal Decisions**: A more direct approach is to instantaneously probe the queue status of related data nodes and to make an optimal decision for each request. Then, this paper needs a criterion to measure how good a load direction choice is.
> a. **Least Latency First**: it only optimizes the latency of the current request in question regardless of future requests. It first probes queue status and optimizes for each request instantaneously.

> b. **Least Marginak Load First**: it strikes a balance between reducing the current request latency and minimizing the overall system load. It first not only optimizes for each request with instantaneous probing, but also saves system resources for future requests by penalizing degraded reads.

***Implementation and Evaluation***:
![1535530049894](paper_figure/1535530049894.png)

Each frontend server has a **controller** to execute load direction policies
Computation of *load direction table*: MOSEK in python.
**Evaluation**:
>1. (6, 2, 2) LRC: request latencies, task latencies, task waiting times, controller processing times
>2. (6, 3) RS: request latencies, task latencies, task waiting times, controller processing times

## Strength (Contributions of the paper)
1. This paper propses to proactively and intelligently launch degraded reads in order to <u>shift loads away from hotspots amd prevent potential congestion early.</u>
## Weakness (Limitations of the paper)
1. The result of statistical optimization is still **sub-optimal**, because the load direction table is only updated periodically, and failing to utilize the instantaneous load information.
2. The probing overhead could be a bottleneck of its performance.
## Future Works
1. I thinks a serious issue in this paper is the overhead of probing in each storage node. Although this paper tries to leverage the sample to reduce the probing overhead, it can be further investigated other method to minish this kind of overhead.
