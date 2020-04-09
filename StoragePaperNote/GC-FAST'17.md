---
typora-copy-images-to: ../paper_figure
---
The Logic of Physical Garbage Collection in Deduplicating Storage
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| FAST'17 | Deduplication GC |
[TOC]

## 1. Summary
### Motivation of this paper
- In a deduplicating storage system, GC is complicated by possibility of numerous references to the same underlying data.

- *Logic level GC*: the system analyzes each live file to determine the set of live chunks in the storage system.
> this cannot handle the shift to using individual file-level backups, rather than tar-like aggregates (the number of files increased dramatically) **high overhead in mark phase**

This motivates this paper to propose *physical level GC*, which performs a series of sequential passes through the **storage containers**.
> I/O pattern is sequential
> it is scales with the physical capacity rather than the deduplication

### Scalable GC 
- Assumption:
While some deduplication research assumed a FIFO deletion pattern, file deletion can generally be in **any order**.

- Deuplicated file representation
using Merkle trees for deduplicated storage to represent a file.

- Performance issues with enumeration
1. Deduplication and compression:
some datasets have extremely high $TC$, making the logical space vary large and unreasonably slow to enumerate. 

2. Number of files:
Every file has metadata overhead to be processed when enumerating a file, and for small files, the overhead may be as large as the enumeration time itself.

3. Spatial locality:
While sequentially written files tend to have high locality of $L_p$ chunks, creating a new file from incremental changes harms locality as enumeration has to jump among branches of the $L_p$ tree.

- Original Logical GC
1. **Enumeration**: To identify the live chunks, it enumerates all of the files referenced from the root ($L_1$ to $L_6$ chunks).
> record the fingerprints for live files in the **Live Bloom Filter**.
> track the most recent instance of each chunk in the presence of duplicates in the **Live Instance Bloom Filter**.

2. **Filter**: Check each container via iterating through the container metadata region, clean on those where the most space can be reclaimed with the least effort.
> liveness of the container: the number of live fingerprints divided by the total number of fingerprints in the containers.

3. **Copy**: new containers are formed from live chunks copied out of previous containers. 

**Memory optimization**: 
Only consider fingerprints that match the **sampling criteria** before inserting into the Bloom filters. (pre-enumeration)
> create a Candidate Bloom Filter 
> only adds live fingerprints to the Live Bloom Filter if the fingerprint is in the Candidate Bloom Filter.

- Physical GC
  A new method of enumeration identifies live chunks from **containers rather than by iterating through each file**.
> via using perfect hashing

1. **Analysis**: create the PH function by analyzing the fingerprints in the on-disk index. 
> unique mapping from fingerprint to offset in a PHV. (4 bits per fingerprint)

2. **Enumeration**: Unlike LGC, instead of walking the file tree structure, it performs a series of sequential container scans.

3. **Filter, select, copy**: 


### Implementation and Evaluation
- Evaluation
1. Standard workloads
2. Problematic workloads

## 2. Strength (Contributions of the paper)
1. It mentions two approaches to garbage collection in a deduplicating storage system.
> handle different workload.
> make enumeration time 

## 3. Weakness (Limitations of the paper)

## 4. Some Insights (Future work)
1. This paper mentions two classical deletion algorithms, reference counts and mark-and-sweep.
> 1. reference count: maintain a count for each chunk. (inline approach) 
> 2. mark-and-sweep: walk all live files and marks them in a data structure. Then, scans the containers and sweeps unreferenced chunks. (asynchronous algorithm). live chunks are read from disk and written to new containers.

2. This paper mentions two trends in deduplication system.
> 1. the increase in the file count. (treating it more like primary storage than backup)
> 2. deduplication rate goes up (more frequent point-in-time backups)
