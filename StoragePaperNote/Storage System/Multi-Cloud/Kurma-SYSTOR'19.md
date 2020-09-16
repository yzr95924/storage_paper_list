---
typora-copy-images-to: ../paper_figure
---
Kurma: Secure Geo-Distributed Multi-Cloud Storage Gateways
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| SYSTOR'19 | Secure Multi-Cloud Storage|
[TOC]

## 1. Summary
### Motivation of this paper
Many cannot store data in cloud due the security conerns and legacy infrastructure such as **network-attached** storage (NAS). This paper wants to design a multi-cloud storage gateway system to allow NAS-based programs to seamlessly and securely access cloud storage. 
### Kurma
- Threat Model
Kurma considers the on-premises gateways trusted, and the public cloud untrusted.
> Transferring data to clouds is vulnerable to man-in-the-middle attacks.
> Eventually-consistent clouds may return stale data.

- Design Goal:
1. Strong security 
2. High availability: no single point failure
3. High performance: overcome high latency of remote cloud storage.
4. High flexibility: support trade-off among security, availability, performance, and cost.
> replication, erasure coding and secret sharing
> trade-oofs among availability, performance, and costs.


![1559549453140](../paper_figure/1559549453140.png)

- Architecture
1. File system metadata: each Kurma gateway runs a separate ZooKeeper instance that stores a **full replica** of the whole file-system metadata.
2. use a persistent write-back cache to avoid the long latency of a cloud accesses.

![1559551516676](../paper_figure/1559551516676.png)


- Metadata Management
Kurma gateway maintains a replica of the entire file-system metadata in a local ZooKeeper instance.
> metadata operations can be processed without synchronizing with other gateways.
> store the metadata in-memory to achieve fast operations.

Minimizing the memory footprint using three stategies:
1. use a large block size, reduce the amount of blocks, and improve the throughput.
2. compresses its metadata, via data locality.
3. only store version number and GatewayID in gateway, and other metadata in cloud.


- Security 
Kurma stores each encrypted block as a key-value object:
> key is derived from the block's metadata.
> ensure that each version of a block hash a unique cloud object key
> attacker cannot tell whether two objects are two versions of the same block.


![1559566271088](../paper_figure/1559566271088.png)


- Multi-Cloud
1. Replication 
2. Erasure Coding
3. Secret Sharing

- File Sharing Across Gateways
close-to-open consistency: when a client opens a file, the client sees all changes made by other clients in the same region who closed the file before the open.

- Persistent Caching
Kurma NFS server has a persistent cache so that hot data can be read in the low-latency on-premises network instead of from remote clouds.


### Implementation and Evaluation
- Implementation:
Kurma NFS Server: 15800 lines C++
Gateway Server: 22700 lines java
**Optimization**:
> 1. using a separate thread to pre-compute a pool of *keymap*s (RSA keymap), so that Kurma can quickly take one *keymap* out of the pool when creating a file.
> 2. batch multiple ZooKeeper changes into a single ZooKeeper transcation.
> 3. Latencies of clouds vary significantly over time: Kurma sorts cloud providers in every $N$ seconds, and uses the $K$ fastest clouds as backends. 

- Evaluation
Compare with a baseline using a single-node NFS server.
> Metadata operation, data operations.

## 2. Strength (Contributions of the paper)
1. This paper proposes a secure geo-distributed multi-cloud storage gateway system, which keeps file-system metadata in trusted gateways, and stores the encryted data block in back-end clouds.
> a geo-distributed file system.

2. To solve the issue of metadata freshness, Kurma embeds a version number and a timestamp into each file block to ensure data freshness.


## 3. Weakness (Limitations of the paper)
1. Network paritions may disrupt the replication of version numbers among gateways. This can make Kurma cannot guarantee that a client always see all updates made by clients in other regios.


## 4. Future Works
1. this paper shows a new architecture of network storage system, the key is let a trusted gateway to storage the metadata.
2. Since this paper is related to multi-clouds scenario, there arises a natual issue, that is cost efficiency, this paper does not investigate this problem.