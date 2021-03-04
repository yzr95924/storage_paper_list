---
typora-copy-images-to: ../paper_figure
---
Rangoli: Space management in deduplication environments 
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| SYSTOR'13 | Space Management |
[TOC]

## 1. Summary
### Motivation of this paper
- Motivation
In deduped volumes there is no direct relation between the *the logical size* of the file and *the physical space* occupied by it.
> hard to find an optimal space reclamation
> Space reclamation in non-deduped environments is simpler. (guarantee changes in the used space of the volume by an amount equal to the logical size of the file)

In this work, it proposes a fast and efficient tool which can identify the optimal set of files for space reclamation in a deduped environment.

- Two dimensions 
1. Source centric:
select groups of files at source that have a high degree of disk sharing
> Migrate them together to the new destination (storage efficiency preservation)

2. Destination aware:
Pick files at source that potentially have maximum duplicate data at some destination volume

In this paper, it only considers the *source centric* dimension, and the destination is agnostic.


### Rangoli
- Key idea:
migrating similar files is better for preserving storage efficiency.
> seek to partition the dataset such that most the data sharing between file within the same partition.
> files across partitions have little or no data sharing.


- Metric
1. Space Reclamation (SR): for the source volume, the difference in the total used physical space
2. Cost of Migration (CM): the number of blocks transmitted over the network. 
3. Migration Utility (MU): $\frac{SR}{CM}$ (*higher is better*)
4. Physical space bloat (PSB): the ratio of increase in the physical space consumption of the dataset to its original space consumption. (*lower is better*)

- Main algorithm
1. Step 1: FPDB processing
process the fingerprint database and compute the extent of data sharing across files 
> represent it as a bipartite graph.

In its FPDB, it stores <fp, block len, inode> such that there are multiple records with the same fp. Thus, it can achieve its goal via traversing the FPDB.
> it contains one fingerprint record for every **logical block** of the file.

2. Step 2: Migration binning: 
partition the graph to obtain $K$ migration bins 
> space reclamation is $\frac{1}{K}$ of the volume space. (each migration bin is approximately equal in size)

3. Step 3: Qualification of migration bins
compute the metrics for each migration bin and chose the best among them.
> 1. Logical size of a bin $p$:
> 2. Internal sharing of a bin $b$: denote the extent of data sharing of within the bin
> 3. Sharing Across of a $bin$: denote the extent of data sharing of the bin $p$ with the remainder of the dataset.

### Implementation and Evaluation
- Evaluation
Datasets: 
> four datasets: Debian, HomeDir, VMDK, EngWeb

- Evaluation objectives:
1. flexibility
2. impact on space consumption
3. network costs
4. scalability


## 2. Strength (Contributions of the paper)
1. a novel solution for space reclamation in deduped environments 
> fast and scalable and tested on real world dataset.

2. a deterministic solution to report the exact metrics **before the actual migration**
> find the exact space reclamation and associated penalties (e.g., network cost, physical space consumption)

3. investigate how to find optimal datasets for space reclamation
> better than alternatives based on MinHash

## 3. Weakness (Limitations of the paper)
1. From my perspective, this algorithm can only fit the NetApp deduplication system since its special design of FPDB.


## 4. Some Insights (Future work)
1. we can consider the similarity indicative hashes to repesent the similarity of the data
> min-hash, minimum hash


