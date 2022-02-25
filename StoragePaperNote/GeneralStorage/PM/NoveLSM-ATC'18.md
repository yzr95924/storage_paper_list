---
typora-copy-images-to: ../paper_figure
---
Redesigning LSMs for Nonvolatile Memory  with NoveLSM
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| ATC'18 | LSM+PM |
[TOC]

## 1. Summary
### Motivation of this paper

- LSM-Tree
  - Write optimized data structure used in key-value stores
  - Originally designed for slow hard drives
    - In memory buffering, batched, and sequential writes to disk
    - High write amplification
- Moving Towards NVM + LSM
  - Fast byte-addressable
  - Current LSMs are **not designed to exploit storage byte-addressability**
- Design goals
  - Reduce serialization: Persistent Skip List
  - Reduce compaction: Direct NVM mutability
  - Reduce logging cost: In-place commits
  - Improve parallelism: Read parallelism across levels

### NoveLSM

- How do LSMs perform on NVM
  - LevelDB: Use NVM instead of SSD for storing on-disk SSTable
  -  High (De)Serialization Cost
    - Serialization of in-memory data to SSTable storage blocks
    - Deserialization of block data to in-memory data during read
  - High Write Compaction Cost
    - Compaction time consuming and high overhead: In-memory structures must be serialized to block format
  - High Write Logging Cost
    - LSM updates are written to log, memtable, and SSTable
      - LevelDB does not sync log updates for performance
  - Lack of Parallelism
    - Sequential Reads: Search memtable --> Search Immutable memtable --> Index lookup in some levels
  - ![image-20211213022458420](../paper_figure/image-20211213022458420.png)
- Reduce Serialization: Immutable NVM
  - Idea: Introduce byte-addressable persistent NVM skip list
    - make skip lists persistent for exploiting NVM byte-addressability 
  - Persistent skip list created by mapping memory from NVM
    - Uses offset in the mapped memory instead of virtual address
- Reducing Compaction: NVM Mutability
  - Idea: Exploit byte addressability and directly update NVM memtable
  - Direct NVM mutability provides sufficient time for DRAM compaction
- Reducing Logging Cost: In-place Commits
  - Idea: Avoid logging for NVM memtable with in-place commits
    - NVM memtable recovery – remap map file and find root pointer
- Increase Parallelism: Read Threading
  - Increase Parallelism: Read Threading
  - Thread management overhead can be expensive
    - Bloom filters to launch threading only if DRAM memtable is a miss
  - ![image-20211213022829073](../paper_figure/image-20211213022829073.png)

### Implementation and Evaluation

- Evaluation
  - emulated NVM using benchmarks and application traces
    - 16 GB database size and vary values sizes
    - SSTables always placed in NVM for all approaches
  - DB_Bench and YCSB
- Serialization Impact: Immutable Memtable
- Reducing Compaction: Mutable Memtable
- Read Parallelism Impact

## 2. Strength (Contributions of the paper)

- Strong motivation
  - Simply adding NVMs to existing LSMs for storage not sufficient
  - Eliminating S/W overhead (e.g., serialization, compaction) is critical

## 3. Weakness (Limitations of the paper)

## 4. Some Insights (Future work)

