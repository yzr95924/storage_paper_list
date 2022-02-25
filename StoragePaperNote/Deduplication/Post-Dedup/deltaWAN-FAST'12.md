---
typora-copy-images-to: ../paper_figure
---
WAN Optimized Replication of Backup Datasets Using Stream-Informed Delta Compression
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| FAST'12 | Delta compression |
[TOC]

## 1. Summary
### Motivation of this paper

- Motivation
  - Replicating data off-site is critical for disaster recovery reasons
    - Replicating across **a wide area network (WAN)** is a promising alternative, but fast network connections are expensive or impractical in many remote locations
    - **Network bandwidth** across the WAN is often a limiting factor
  - Compressing data before transfer improves effective throughput
    - delta compression: compress relative to similar regions by calculating the differences
- Background
  - turning on delta compression when replicating between their deduplicated backup storage systems to achieve higher compression and correspondingly higher effective throughput

### Stream-Informed Delta Compression

- Goal
  - achieves identity and delta compression across petabyte backup datasets **with no prior knowledge** 
    - reducing the index overheads of supporting both compression and deduplication (**index issues**)
- Observation
  - Repeated patterns in backup dataset
    - the same data patterns exist for identifying similar data as well as duplicates, without additional index structures
- Similarity Index Options
  - Full sketch index
  - Partial sketch index
    - use a partial index holding recently transmitted sketches (LRU, FIFO)
  - Stream-Informed sketch cache
    - repeated patterns related to backup policies
    - **edits my be surrounded by duplicate regions** that can load a cache effectively
- Delta Replication Architecture
  - ![image-20211103001651759](../paper_figure/image-20211103001651759.png)
  - Fingerprints and chunks are **laid out in containers** and can be loaded into a fingerprint cache
    - all fingerprints from a container are evicted as a group
  - Delta replication
    - adding sketches to the **container meta data section**. 
    - sketches are designed so that similar chunks often have identical sketches
- Network Protocol Considerations for Delta Compression
  - both the source and destination must agree on and have **the same base chunk**
    - source for encode
    - destination for decode
  - the whole workflow
    - the backup server sends the sketches of unique chunks to the repository
    - the repository checks the cache for matching sketches
      - response **the base fingerprints** to the backup server
    - if the backup server has the base fingerprints
      - it delta compresses a chunk relative to the base before local compression and transfer
- Similarity Detection with Sketches
  - Chunks that have one or more features (maximal values) in common are likely to be very similar, but small changes to the data are unlikely to perturb the maximal values
    - **feature**: the maximal hash value of using a rolling hash function over all overlapping small regions of data (32 byte windows)
  - a **super-feature** is formed by taking a Rabin fingerprint over **k consecutive features**
  - an **index** representing <u>the corresponding super-features of previously processed chunks</u>
- Delta Compression
  - the target chunk currently being processed will be **represented as a 1-level delta** of the base chunk.
  - using Xdelta which is optimized for compressing highly similar data regions

### Implementation and Evaluation

- Trace

  - source code repository, workstations, email, system log, home directories

- Configure

  - 3 super-feature per sketch, 12 MiB sketch cache, 4.5 MiB containers holding meta data and locally
    - a sketch of each non-duplicate chunk

  - deduplication -> delta compression -> GZ compression

- Evaluation

  - metrics:
    - normalized compression (divided by the maximum total compression)

1. sketch cache size
2. delta encoding
   1. **3.55x** storage saving compared with only deduplication
3. multi-level vs 1-level delta
   1. a delta storage system may only support 1- or 2-level delta encoding to bound decode time
   2. 1-level delta is a reasonable approximation to multi-level
4. sketch index vs Stream-Informed sketch cache
   1. using a cache with two or more super-features achieves greater compression than a single index
5. interaction of delta and local compression
   1. see significant advantages to using both techniques in combination with deduplication
6. WAN replication improvement
   1. 1-2 orders of magnitude faster than network throughput
7. performance characteristics
   1. each chunk stored in a container also has a sketch added to the meta data section (less than 20 bytes)
   2. overhead
      1. sketching on the write path and reading similar base chunks to perform delta compression

## 2. Strength (Contributions of the paper)

- a complete system for delta-compression backup 
  - leverages deduplication locality to also find similarity matches used for delta compression
- good experiments
  - very comprehensive 

## 3. Weakness (Limitations of the paper)

- only consider the backup task under a WAN setting
  - aim to save network traffic, instead of the storage space
  - effective for the case where the network bandwidth is bottleneck
    - e.g., 6.3Mb/s or slower

## 4. Some Insights (Future work)

- delta compression can fit the case with limited network bandwidth (WAN backup)
  - save the backup network traffic 
