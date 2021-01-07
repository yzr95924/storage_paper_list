---
typora-copy-images-to: paper_figure
---
Design Tradeoffs for Data Deduplication Performance in Backup Workloads
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| FAST'15 | Data Deduplication |
[TOC]

## 1. Summary
### Motivation of this paper
- Motivation
  - In order to understand the fundamental tradeoffs in each of its design choices 
    - disassemble data deduplication into a large *N-dimensional parameter space*
  - Parameter space (design parameter)
    - backup and restore performance
    - prefetching and caching
    - rewriting
    - memory footprint
    - storage cost
  - Goal
    - make efficient design decisions according to the desire tradeoff.
  - Present a general-purpose *Deduplication Framework*
    - DeFrame: for comprehensive data deduplication evaluation

### DeFrame
- Inline data deduplication space 
  - the fingerprint index
  - the recipe store
  - the container store
  - a fingerprint cache (hold popular fingerprints)

- Fingerprint index
  - a well-recognized performance bottleneck (a large-scale deduplication system)
  - putting all fingerprints in DRAM is cost-efficient
  - Two submodules:
    - a key-value store
    - a fingerprint prefetching/caching module

- Classification
  - Exact deduplication / Near-exact deduplication
  - Prefetching policy
    - Logical locality (LL): for the *recipe*, point to segment
    - Physical locality (PL): for the *container*, point to container ID
  - ![image-20201029020046029](paper_figure/image-20201029020046029.png)

- Exact + Prefetching
  - avoid a large fraction of lookup requests to the key-value store.
  - the fragmentation problem would reduce the efficiency of the fingerprint prefetching and caching
    - making the key-value store become **lookup-intensive over time**.

- Near-exact + sample
  - to downsize the key-value store
  - important to maintain a high deduplication ratio
  - near-exact deduplication generally indicates a **cost increase**.

- rewriting 
  - improve the physical locality, the lookup overhead of EDPL no longer increases over time.


- DeFrame Architecture
  - Container store: metadata section + data section
  - Recipe store: associated container IDs without the need to consult the fingerprint index, add some indicators of segment boundaries.
  - fingerprint index:
    - in-DRAM hash table / a MySQL database paired with a Bloom filter
  - Backup pipeline:
    - Chunk, Hash, Dedup, Rewrite, Filter, Append
  - Restore pipeline:
    - Reading recipe, reading chunks, writing chunks

### Implementation and Evaluation
- Implementation
  - C++
  - Dataset: Kernal, VMDK, RDB

- Metrics
  - Deduplication ratio
  - memory footprint
  - storage cost
  - lookup requests per GB

- Findings
  - fragmentation results in an ever-increasing lookup overhead for EDPL
    - EDLL achieves sustained performance
  - Consider the self-reference 

## 2. Strength (Contributions of the paper)

1. provide an overview of the deduplication (all design parameters)

## 3. Weakness (Limitations of the paper)

## 4. Some Insights (Future work)
1. It mentions that the uniform sampling achieves a significantly higher deduplication ratio.

2. when we use logical locality, it needs extremely high update overhead
> all fingerprints are updated with their new segment IDs in the key-value store.

3. Although near-exact deduplication reduces the DRAM cost, it cannot reduce the total storage cost.

4. Design decision
> For lowest storage cost: EDLL is preferred (highest deduplication ratio, sustained high backup performance)
> For low memory footprint: ND is preferred, 
> > NDPL: for its simpleness
> > NDLL: better deduplication ratio
> For a sustained high restore performance:
> > EDPL + rewriting