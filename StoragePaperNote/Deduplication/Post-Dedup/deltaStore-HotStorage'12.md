---
typora-copy-images-to: ../paper_figure
---
Delta Compressed and Deduplicated Storage Using Stream-Informed Locality
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| HotStorage'12 | Delta Compression |
[TOC]

## 1. Summary
### Motivation of this paper

- Motivation
  - increasing compression allows users to protect more data without increasing their costs or storage footprint
  - add delta compression to deduplicated storage to **compress similar (non-duplicate) regions**
- Background
  - it builds the first storage system prototype to combine delta compression and deduplication
    - several challenges
  - A key challenge
    - a large number of data regions to be indexed
      - will not fit in memory for production systems
    - previous work needs
      - version information
      - similarity index
  - Difference from DeltaWAN-FAST'12
    - previous work investigated stream-informed locality for replication across the WAN, that work was limited to low-throughput environments
      - did not attempt to store delta encoded chunks

### Delta file system

- stream locality
  - grouping neighboring chunks together as a cache unit and loading the group's fingerprints whenever one of them is queried in the index
    - non-duplicate chunks in a stream are often similar to chunks loaded by a neighboring chunk
- File system
  - fingerprint is compared against the cache and potentially an on-disk index for a match
    - if no fingerprint match is found
      - it further check for a similarity match by comparing the chunk's sketch against the cached sketches
    - If a sketch match is found
      - it reads in the base chunk, delta encodes the current chunk, compress the delta, and store the results
      - achieve higher total compression than **deduplication and local compression** can achieve
- Practical considerations
  - Throughput
    - **reading back a base chunk** from disk is clearly the bottleneck in processing 
      - One solution is to move from hard drives to SSD type storage with faster I/O characteristics
  - GC
    - For a complete backup storage system
      - removing deleted files and the underlying chunks is a necessary feature
    - decrease the locality --> miss out on potential delta compression
  - Compression vs. Chunk size
    - Delta compression starts off slightly above 1 and **grows steadily as the chunk size increases because it finds compression that deduplication is now missing**
      - the delta compression adds **significantly increased compression across chunk sizes**
  - Data integrity
    - the integrity of user data is the highest priority for backup storage
      - end-to-end checks: confirming that all chunks referenced by file recipes exist and that every chunk is valid

### Implementation and Evaluation

## 2. Strength (Contributions of the paper)

- the first work to present a delta compressed and deduplicated storage prototype based on stream-informed locality

## 3. Weakness (Limitations of the paper)

## 4. Some Insights (Future work)

- Delta compression is costly in the sense of requiring extra computation and I/O
  - but by applying deduplication first, delta compression on the remaining bytes becomes feasible
