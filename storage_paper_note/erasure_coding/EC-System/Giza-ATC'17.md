---
typora-copy-images-to: paper_figure
---
# Giza: Erasure Coding Objects across Global Data Centers
@ATC'17 @ Erasure Coding across data centers @Separate the data and metadata path
[TOC]

## Summary
***Motivation of this paper***: This paper intends to solve the problem of reducing the cross-DC latency while maintaining the strong consistency of the metadata.

***Microsoft OneDrive Characteristics***:
- Large Objects Dominate: less than 0.9% of the total storage capacity is occupied by objects smaller than 4MB.
- Object Teperature Drops Fast: Since the temperature of the objects drops quickly, caching objects can be very effective.
- Writes Dominate with Caching: the cross-DC traffic is completely dominated by writes.
- Cocurrency is rare, but versioning is required: concurrent updates of same objects are rare in Giza.
- Deletion is Not Uncommon: removing the deleted objects from underlining cloud storage and reclaiming capacity is crucial in achieving storage efficiency.

***Giza***: 
Giza is a strongly consistent versioned object store, built on top of Azure storage, and optimizes for latency and is fast in the common case. The goal of Giza: 
> 1) Giza should guarantee strong *consistency* while also minimizing operation latency.
> 2) Giza should make full use of existing cloud infrastructure to simlipy its implementation and deployment.

![1535424966773](paper_figure/1535424966773.png)

**Paxos using Cloud APIs**:
Giza implements both **Paxos** and **Fast Paxos** to optimize the performance over cross-DC acceptors, and reduces the metadata path latency.

| Paxos                                                   | Fast Paxos                                                   |
| :------------------------------------------------------ | ------------------------------------------------------------ |
| Can commit with 2 round trips                           | Can commit with 1 round trip                                 |
| Requires majority of replicas to commit $\frac{N}{2}+1$ | Requires more than the majority of replicas to commit $\frac{2N}{3}+1$ |

The naive version of Giza first writes out fragments (data and parity), and then writes out metadata, resulting in two or more cross-DC round trips. (*need to reduce the latency*)
**Giza Put Operation**:
>1. Execute metadata and data path in parallel.
>2. Upon success, return acknowledgement to the client
>3. In the background, update highest committed version number in the metadata row.

**Giza Get Operation**:
>1. Optimistically read the latest commited version locally
>2. Execute both data path and metadata path in parallel:
>> a. Data path retrieves the data fragments indicated by the local latest committed version.
>> b. Metadata path will retrieve the metadata row from all data centers and validate the latest committed version.

***Implementation and Evaluation***:
Giza is implemented in C++ and uses Azure Blob and Table storage to store fragments and metadata.
**Evaluation**:
1. Latency of Put and Get: Fast vs Classic Paxos
2. Latency of performance: In different setups
3. Latency: Contention vs No Contention

## Strength (Contributions of the paper)
1. It has designed and implemented Giza, which is a strongly consisent, versioned object store that erasure codes objects across globally distributed data centers.
2. Giza achieves minimum latency in the common case, when there are no concurrent conflicting access. 
3. Giza applie classic Paxos and Fast Paxos in a novel way on top of restricted cloud storage APIs.
4. Giza is deployed in 11 DCs across 3 continents and experimental results demonstrate that it achieves its design goal. 
## Weakness (Limitations of the paper)
1. This paper just consider the case of the standrad Reed-Solomon coding, can other erasure coding scheme further optimize the latency?

## Future Works
1. In stead of  the standrad Reed-Solomon coding, Giza can further investigate other erasure codes to optimize its performance. 
