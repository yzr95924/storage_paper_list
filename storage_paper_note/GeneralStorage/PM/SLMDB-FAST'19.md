---
typora-copy-images-to: ../paper_figure
---
SLM-DB: Single Level Merge Key-Value Store with Persistent Memory
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| FAST'19 | PM |
[TOC]

## 1. Summary
### Motivation of this paper

- LSM-Tree
  - Optimized for heavy write application usage
  - Designed for slow hard drives
- LSM-Tree limitations
  - Large overhead to locate needed data
    - scan multiple levels
  - High disk write amplification

### SLD-DB

- Persistent Memory + Disk
  - B+tree in PM to manage data stored in the disk
  - single disk component C1 that does self-merging
- Architecture
  - ![image-20211213011550369](../paper_figure/image-20211213011550369.png)
- Persistent MemTable
  - No write-ahead-logging (WAL)
  - Stronger consistency compared to LevelDB
  - Key-index insertion into B+-tree happens during immutable Memtable flush to disk
- Persistent B+-tree Index (FAST-FAIR B+-tree)
  - Per-key index for fast search
  - No multi-leveled merge structure
- Selective compaction
  - why compaction?
    - need GC for obsolete KV-pairs
    - need to improve **sequentiality** for **RangQuery**
  - Selectively pick SSTable files
    - Make those files as compaction candidates
    - Merge together most overlapping compaction candidates
    - Selection schemes for compaction candidates:
      - Live-key ratio selection of an SSTable
      - Leaf node scans in the B+-tree
      - Degree of sequentiality per range query
  - Compaction triggered when there are too many compaction candidate files

### Implementation and Evaluation

- Evaluation
  - PM simulator: PM write latency 500ns (5x of DRAM write latency)
    - PM read latency & bandwidth same same as DRAM’s
  - Intel’s PMDK used to control PM pool
  - db_bench and YCSB
- microbenchmark
- PM sensitivity
  - PM write latency sensitivity
  - PM bandwidth sensitivity

## 2. Strength (Contributions of the paper)

- A new design of KV-stores with persistent memory
  - High write/read performance compared to LevelDB
- Low write amplification and near-optimal read amplification

## 3. Weakness (Limitations of the paper)

## 4. Some Insights (Future work)

- Level-DB
  - Memory:
    - MemTable, Immutable MemTable
      - In-memory skiplist to buffer updates, mark immutable when becoming full 
        - flush to disk
        - update the **write-ahead-log** (**no fsync**)
      - sequential write to the disk
    - Disk:
      - MANIFEST: store file organization and metadata
      - Sorted string tables (SST): merge from Level N to N+1
- LSM-tree optimizations
  - Improve parallelism: RocksDB, HyperLevelDB
  - Reduce write amplification: PebblesDB
  - Optimize for hardware (SSD): VT-tree, WiscKey