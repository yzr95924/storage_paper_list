---
typora-copy-images-to: ../paper_figure
---
# Can Modern LLMs Tune and Configure LSM-based Key-Value Stores?

|           Venue            |       Category       |
| :------------------------: | :------------------: |
| HotStorage'24 | Storage + AI, LLM in Storage |
[TOC]

## 1. Summary
### Motivation of this paper

- LSM-KVS are important data storage building blocks
  - tuning performance involves configuring over **100 parameters**
  - current approaches
    - done manually
    - with **limited parameters** in auto-tuning mechanisms
  - increasing number of parameters
    - challenging for even the code developers to understand the effect of every option
- key question
  - can leverage LLM's understanding of the system and LSM-KVS components for unrestricted parameter-pool tuning of LSM-KVS?
  - LLMs are trained
    - available LSM-KVS source code, research papers, and open materials
    - enabling the machines to have human-like understanding

### Method Name

### Implementation and Evaluation

## 2. Strength (Contributions of the paper)

## 3. Weakness (Limitations of the paper)

## 4. Some Insights (Future work)

- LSM-KVS adoption

  - RocksDB, Big Table, HBase, and Cassandra
  - append-only log files, in-memory tables, compaction, flush mechanisms, and Bloom filters

- understand the effect of every option
  - Navigating the Minefield of RocksDB Configuration Options
    - https://betterprogramming.pub/navigating-the-minefield-of-rocksdb-configuration-options-246af1e1d3f9
  - RocksDB Tuning Guide

    - https://github.com/facebook/rocksdb/wiki/RocksDB-Tuning-Guide
  - Enhancing RocksDB for Speed & Scale
    - https://www.yugabyte.com/blog/enhancing-rocksdb-for-speed-scale/
- LSM-KVS: tuning && optimization
  - **tuning**
    - exposed configuration parameters are tuned to improve performance
  - **optimization**
    - underlying codebase and configuration parameters are both modified to improve performance
