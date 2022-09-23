---
typora-copy-images-to: ../paper_figure
---
# Building a High-performance Fine-grained Deduplication Framework for Backup Storage with High Deduplication Ratio

|           Venue            |       Category       |
| :------------------------: | :------------------: |
| ATC'22 | Deduplication |
[TOC]

## 1. Summary
### Motivation of this paper

- motivation: fine-grained deduplication suffers from poor backup/restore performance
  - introduces delta compression to exploit more compressibility among workloads, so workloads share more data, decreasing locality -> increasing I/O overhead
  - this paper address issues for different forms of **poor locality** in fine-grained deduplication
- problem
  - **reading base issue**: reading base chunks from delta encoding (in backup process)
    - inefficient I/O when reading base chunks
  - **fragmentation issue**: caused by a new kind of reference relationship between delta and base chunks (break **spatial locality**)
    - delta-base relationships lead to more complex fragmentation problems than deduplication alone
  - **repeatedly accessing issue**: repeatedly access containers to gather delta-base pairs (break **temporal locality**)
    - delta-base dependencies cause poor temporal locality

### MeGA

- selective delta compression
  - insights: base chunks are not distributed evenly -> base-sparse containers
  - skips delta compression whose base chunks are located in "base-sparse containers"
    - avoid reading "inefficient" containers
- delta-friendly data layout
  - change order-based data layout -> lifecycle-based data layout
    - classifies chunks into categories according to whether they are always referenced by the same set of consecutive backup workloads
  - two-level reference: **directly** referenced chunks and its **indirectly** referenced chunks
  - to simplify the implementation, only deduplicate redundancies between **adjacent backups** to ensure chunks' lifecycles are always consecutive (similar to MFDedup)
- forward reference and delta prewriting
  - when performing a restore, delta-encoded chunks are always accessed **before** their base chunks
    - ensure all restore-involved containers only need to be read only once
  - user space and backup space are **asymmetric**
    - user space: SSDs or NVMs
    - backup space: HDDs
  - prewrite delta chunks in the to-be-restored backup workload (in User space)
- ![image-20220912232446270](..\paper_figure\image-20220912232446270.png)

### Implementation and Evaluation

- baselines
  - Greedy, FGD (fine-grained deduplication with Capping), CLD (chunk-level deduplication with Capping), and MFD (FAST'21)
- traces: WEB, CHM, SYN, and VMS
- backup speed, restore speed, and deduplication ratio
- I/O overhead in maintaining data layout
  - maintenance costs v.s. GC costs

## 2. Strength (Contributions of the paper)

- analyze several forms of poor locality caused by fine-grained deduplication
  - additional I/O overhead -> poor backup/restore performance
- several designs: delta selector, delta friendly data layout, always-forward-reference traversing, and delta prewriting 

## 3. Weakness (Limitations of the paper)

- hard to follow, especially for the third design
- need a maintenance process to adjust the layout
  - overhead is high 0.32-1.92x the GC I/O overhead

## 4. Some Insights (Future work)

- term: call "delta compression" as "fine-grained deduplication"
- all deduplicated chunks are stored in containers in order, and then each container will be compressed
  - compression unit: a container
