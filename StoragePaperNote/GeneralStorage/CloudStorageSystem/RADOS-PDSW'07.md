---
typora-copy-images-to: paper_figure
---
# RADOS: A Scalable, Reliable Storage Service for Petabyte-scale Storage Clusters
@PDSW'07 @Ceph
[TOC]

## Summary
***Motivation of this paper***: In OSD, the storage systems largely fail to exploit device intelligence. Consistent management of data placement, failure detection, and failure recovery places an increasingly large burden on client, controller, or metadata directory nodes, **limiting scalability**. <u>This paper wants to leverage the device intelligence to solve this issue by distributing the complexity.</u> 

***Scalable Cluster Management***
- Cluster Map: In RADOS system, the monitor cluster manages the storage cluster by using the **cluster map**. It includes two key information:
>1. which OSDs are included in the cluster
>2. distribution of all data in the system

The cluster map is replicated by every storage node. Because cluster map changes may be frequent. Thus, its updates are distributed as **incremental maps**.

- Data Placement: RADOS maps each object to a **placement group (PG)** by using the **CRUSH**, which can maintain a balanced distribution. 

***Intelligent Storage Devices and Monitors***
Because the knowledge of the data distribution encapsulated in the cluster map, this feature allows OSDs to distribute management:
>1. Data redundancy: three replication scheme
>2. Failure detection
>3. Failure recovery: parallelize failure recovery

- Monitors: a cluster of monitors are responsible for managing the storage system by storing the master copy of the cluster map and making periodic updates in response to configuration changes or changes in OSD state.  Using **Paxos part-time parliament algortihm** and **lease mechanism** to favor consistency.


## Strength (Contributions of the paper)	
This paper presents more details of scalable cluster management (cluster map), intelligenr storage devices (failure detection, failure recovery, replication), monitors (paxos service) in Ceph.

## Weakness (Limitations of the paper)
1. For the aspect of load balance, this paper does not mention how to solve the issue of many clients accessing a single popular object.
2. In addition to $n-way$ replication, this paper does not mention how to support parity-based redundancy to improve the storage efficiently.

## Future Work
I think those two weaknesses can be regared as the research direction in the future. 

