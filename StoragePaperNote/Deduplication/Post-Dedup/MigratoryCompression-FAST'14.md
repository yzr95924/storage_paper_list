---
typora-copy-images-to: ../paper_figure
---
# Migratory Compression: Coarse-grained Data Reordering to Improve Compressibility

|           Venue            |       Category       |
| :------------------------: | :------------------: |
| FAST'14 | Post Deduplication, Compression |
[TOC]

## 1. Summary
### Motivation of this paper

- motivation
  - compression can find redundancy among strings within a **limited** distance (window size)
    - can limit the finding overhead
    - gzip: 64 KiB sliding window, 7z: up to 1 GiB
  - windows sizes are small, and similarity across a large distance will not be identified
    - traditional compressors are unable to exploit redundancy across a large range of data (e.g., many GB)

![image-20220615220333487](../paper_figure/image-20220615220333487.png)

### Migratory Compression (MC)

- main idea
  - coarse-grained reorganization to **group similar blocks** to improve compressibility
    - include a generic **pre-processing** stage for standard compressors
  - reorder chunks to store similar chunks sequentially, increasing compressors' opportunity to detect redundant strings and leading to better compression
- two use cases
  - mzip: using MC to compress a single file, integrating MC with traditional compressor (e.g., gzip)
  - archival: data migration from backup storage systems to archive tiers
    - ![image-20220615221046364](../paper_figure/image-20220615221046364.png)
- design considerations
  - partition into blocks, calculate similarity features
  - group by content and **identify duplicate and similar blocks**
    - output *migrate* and  *restore* recipe
      - migrate recipe: the order after the rearrangement
      - restore recipe: the order of the original file based on the rearrangement

  - rearrange the input file
    - a large number of I/Os necessary to reorganize the original data
    - block-level
      - random I/Os
      - fine for memory and SSD

    - multi-pass (HDD)
      - convert random I/Os into sequential scans


### Implementation and Evaluation

- implementation
  - use xdelta for delta encoding, the chunk earliest in the file is selected as the base for each group of similar chunks
  - based on DDFS
    - an active tier for backups
    - a long-term retention tier for archival
    - in-memory, SSD, HDD
- evaluation
  - datasets
    - private backup workloads (6 GiB - 28 GiB)
  - compression effectiveness and performance trade-off
    - combine with different compression algoes
  - data reorganization throughput
    - test with in-memory, HDD, SSD
  - delta compression
    - compare with DC, very little improvement (0.81%)
  - sensitivity to different parameters
    - chunk size
    - chunking algo
    - compression window

## 2. Strength (Contributions of the paper)

- the idea is very simple and easy to follow
  - improve both CF and compression throughput via **deduplication** and **re-organization**
- very extensive experiments
  - try to tune every possible parameter and explain the underlying reasons behind the results

## 3. Weakness (Limitations of the paper)

- the novelty: its idea, in a sense, is a coarse-grained BTW over **a large range** (tens of GBs or more). Not very novel.
- compared with delta compression, the improvement is very limited

## 4. Some Insights (Future work)

- the ways to improve compressibility
  - increasing the look-back window
  - reordering data
