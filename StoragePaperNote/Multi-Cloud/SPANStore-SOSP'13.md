---
typora-copy-images-to: paper_figure
---
SPANStore: Cost-Effective Geo-Replicated Storage Spanning Multiple Cloud Services
------------------------------------------
@SOSP'13 @Multi-cloud 
[TOC]

## 1. Summary
### Motivation of this paper: 
Existing problems:
- Although alomst every storage service offers an isolated pool of storage in each of its data centers. leaving replication across data centers to applications.
> e.g. the application needs to replicate data in each data center to ensure low latency access to it for users in different locations.

- Cloud providers do not provide a centralized view of storage with **rich semantics**, every application needs to reason on its own about where and how to replicate data
> to satisfy its latency goals and consistency requirments at low cost. 

This paper designs and implements a key-value store that presents a **unified** viewe of storage services in several geographically distributed data centers. Its goal is to minimize the cost incurred by latency-sensitive application providers.

### SPANStore
SPANStore: span the data centers of multiple cloud service providers.
- The key ideas of SPANStore:
1. SPANStore exploits the **pricing discrepancies** to drive down the cost incurred in satisfying application providers' latency, consistency, and fault tolerance goals.
2. SPANStore determines where to replicate every data object and how to perform this replication.
> how to do the trade-off between (latency vs. storage costs and expenses)

3. SPANStore ensures all data is largely exchanged directly between application virtual machines and the storage services that SPANStore builds upon.

- The goal of SPANStore:
  1) Minimize cost 
  2) Respect latency SLOs (service level objectives)
  3) Flexible consistency (strong consistency, eventual consistency)
  4) Tolerate failures 
- Technique 1: Harness multiple clouds
> **SPANStore Architecture**: 
> ![1550132390502](paper_figure/1550132390502.png)
the application issues PUT and GET requests for objects to a SPANStore library that the application links to. 
> ![1550133510605](paper_figure/1550133510605.png)
Placement Manager collects a summary of the application's workload and latencies from remote data centers in each epoch. And then computes the replication policies. 
> **Cost Model**: Storage cost + Request Cost + Data transfer cost = Storage service cost

- Technique 2: Aggregate workload prediction per access set
> Determine replication policy for all objects with same **access set**, consider two factors:
>> application requirements
>> workload properties
> Leverage application knowledge of sharing pattern (Dropbox/Google Doc know users that share a file)

- Technique 3: Relay propagation
> capitalize on the discrepancies in pricing across different cloud services and relay updates to the replicas via another data center that has cheaper pricing for upload bandwidth.

### Implementation and Evaluation
- Implementation:
> 1) PMan 
> 2) a client library that applications can link to
> 3) an XMLRPC server that is run in every VM run by SPANStore
> 4) a memcached cluster to store in-memory metadata  

- Evaluation 
Setting: SPANStore is deployed across S3, Azure and GCS
Simluation: 
> evaluate the cost savings
> verify application requirements

## 2. Strength (Contributions of the paper)
- This paper launches a series of experiments to prove using multiple cloud providers can enable SPANStore offers lower GET/PUT latencies and costs.
- It also the dynamic workload, and solves it by dividing time into different epoch, and determine the strategy depending on the workload in previous epoch.
## 3. Weakness (Limitations of the paper)

## 4. Future Works
