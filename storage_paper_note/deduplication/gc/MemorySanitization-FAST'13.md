---
typora-copy-images-to: ../paper_figure
---
Memory Efficient Sanitization of a Deduplicated Storage System
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| FAST'13 | Deduplication Sanitization |
[TOC]

## 1. Summary

### Motivation of this paper
- Motivation
Securely erasing sensitive data from a storage system (sanitization could require erasing all unreferenced blocks)
> each piece of data on the physical media could be referred to by multiple namespace objects.
> standard techniques for tracking data references will not fit in memory

- Key requirement for sanitization
> 1. all deleted data are erased
> 2. all live data are available
> 3. the whole sanitization process is efficient
> 4. the storage system is usable while sanitization runs

### Sanitization
- Threat model
1. casual attack
> access through regular file system interfaces.

2. Robust keyboard attack 
> reading blocks directly from disk, swap area, or unallocated blocks
> using non-regular interfaces.

3. Laboratory attack
> access through exotic laboratory techniques
> require specific disk format 

- Managing chunk references
1. Bloom filter
2. Bit vector
3. Perfect hash vector
**key requirement:** need a compact representation of a set of fingerprints that provides an exact answer for whether a given fingerprint exists in the set or not.
> suppose it is a static version of the membership problem where the key space is known beforehand.
> no dynamic insertion or deletion of keys.

those two points support to leverage perfect hash vector.

- Sanitization process
For read-only file system:
> 1. Merge phase: set the consistency point, flush the in-memory fingerprint index buffer and merge it with the on-disk index.
> 2. Traverse the on-disk index for all fingerprints and build the perfect hash function for all fingerprints found
> 3. Traverse all files and mark all fingerprints found as live in perfect hash vector
> 4. select containers with at least one dead chunk, and copy all live chunks from the selected containers into new containers (copy forward), and delete the selected containers.


### Implementation and Evaluation
- Evaluation:
  - without deduplication: exclude the deduplication impact on the sanitization process (as the baseline)
  - with deduplication: deleted space vs sanitization time
  - impact on ingests: the performance when both sanitization and data ingestion run concurrently.

## 2. Strength (Contributions of the paper)
1. discuss sanitization requirements in the context of deduplicated storage.
2. it proposes some memory efficient schemes for managing data references via using perfect hashes.


## 3. Weakness (Limitations of the paper)

## 4. Some Insights (Future work)
1. In this paper, it mentions the **crypto sanitization**, which encrypts each file with a different key and throws away the key of the affected files. Is it feasible to adjust this scheme to deduplication system.
2. Here, it also uses perfect hash to represent the membership, and show it is memory efficient. How to adjust this technique to our problem?


