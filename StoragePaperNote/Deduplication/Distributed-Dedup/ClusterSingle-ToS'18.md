---
typora-copy-images-to: ../paper_figure
---
Cluster and Single-Node Analysis of Long-Term Deduplication Patterns
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| ToS'18 | Distributed Deduplication |
[TOC]

## 1. Summary
### Motivation of this paper
- Motivation
  - Most past studies either were of a small static snapshot or covered only a short period that was **not representative** of how a backup system evolves over time.
  - By understanding such datasets' characteristics, it can design more efficient storage systems.
  - Single-node deduplication vs. Cluster deduplication
    - can distribute the load and aggregating throughput from multiple nodes.

### Cluster and Single-node analysis
- Stateless vs. stateful
  - stateless strategy is designed for *implementation simplicity*
  - stateful strategy checks the similarity of the super-chunk in all nodes 

- How to simulate the incremental backup?

need to detect newly added and modified files. 
> comparing two consecutive snapshot, it can identify whether a file was newly added
> checking the *mtime* (modified time)

- How to estimate the metadata overhead?

Follow the work of FAST'12
$f$ is the size of each chunk's metadata divided by the chunk size 
$L$ is the size before deduplication
$P$ is the raw data size afterwards

$f \times (L+P)$, $f \times L$ is the size of a **file's recipe**, $f \times P$ is the size of the **hash index**

- The benefit of larger chunk sizes
  - With higher deduplication ratios, metadata can become a **large fraction** of post-deduplication space.
> larger chunk sizes reduce the number of metadata entries.
> reduce the amount of metadata also reduces the number of I/Os to the chunk index.


- Chunk popularity
  - the number of duplicate occurrences of a chunk
> the skew in chunk popularity has also been found in primary storage and HPC systems
> Identifying such popular chunks would be useful in optimizing performance.
>
> > accelerate chunk indexing and improve cache hit ratios

- Analysis of groups of users
  - In a cluster deduplication system, grouping similar users into one storage node would improve the overall deduplication ratio
  - A given chunk is shared between two or more users, it is also more likely to appear several times in any single user's data.

- Analysis of cluster deduplication
  - Two challenges:
    - Maintaining high deduplication ratio (each node performs individually and no information about further duplicates is exchanged among them)
    - Balancing the load across cluster nodes: trade-off between load balance and deduplication ratios
  - different design principles: deduplication ratio, load distribution, and throughput

- Chunk-level routing

Chunk-level routing is required to achieve exact deduplication
> resulting in more CPU and memory consumption.

- Key Metrics for cluster-deduplication 
  - Cluster deduplication ratio
  - Load-Distribution
  - Communication overhead

- Load distribution
  - Physical load distributions: the capacity usage at the nodes 
  - Logical load distributions: I/O performance
  - the performance of load balance is the **Coefficient of Variation**
    - the ratio of the standard deviation to the mean 
  - Stateless algorithm leads to high data skew in terms of the logical load distribution, which is opposite to their performance in terms of the logical load distribution. 
  - Stateful approach incurs the most communication overhead, since it needs to send information to all storage nodes to request its similarity index. Using a master node may decrease the communication overhead, but it needs to store all the Bloom filters in the master node and the master node might become a bottleneck as the system scales up.
  - Stateless causes lower communication overhead since the client can choose the destination node without sending any messages.
### Implementation and Evaluation

## 2. Strength (Contributions of the paper)

1. classify data-routing algorithms and implement seven published algorithms that adopt different strategies
> provide a detailed comparative analysis.

2. study a locally collected dataset that spans a period of 2.5 years (4000 daily user snapshot)

## 3. Weakness (Limitations of the paper)
1. Does not consider aging and fragmentation effects in this trace
2. Does not consider the restore performance, especially for deduplication clusters.


## 4. Some Insights (Future work)
1. Because of the size of the chunk index itself, smaller chunk sizes are not always better at saving space
> the impact of chunk sizes

2. Even similar users behave quite differently, and this should be taken into account in future deduplication systems.

3. In distributed deduplication, the routing algorithm that can achieve a good physical load balance may lead to a huge skew in their logical distribution.

4. Deduplication trace
SYSTOR'09: Virtual machine disk images
ATC'11: Microsoft primary storage: MS 857 snapshots over 4 weeks. (**primary storage**)
FAST'12: EMC's Data Domain backup system (**backup**)
SYSTOR'12: file analysis, file type and high redundancy 
SC'12: HPC trace

5. Some insights
Routing by file types can generally achieve a better deduplication ratio than other schemes

