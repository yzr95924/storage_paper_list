---
typora-copy-images-to: ../paper_figure
---
Memory Efficient Sanitization of a Deduplicated Storage System
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| FAST'13 | Deduplication Sanitization (EMC) |
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

- Key idea:
multiple sanitization techniques that trade-off I/O and memory requirements
> the tracking of references is the main problem to solve to efficiently sanitize a deduplicated storage system.

Instead of needing a **dynamic** data structure that can handle insertions, it can optimize with a **static** data structure for our reference set.
> perfect hashes

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
1. copy to a clean system
copy the live data to a new storage system, and then destroy the original storage system.
2. reference index
maintaining correct reference counts is challenging (**live reference counts are not preferred**)
> 1. Partitioned reference index
> 2. Bloom filter: approximate a full index using a Bloom filter. (check whether a key is existed in a Bloom filter)
> > enumerating the live files and inserting the fingerprints into the Bloom filter, (may consider dead chunks as alive chunks)

3. Bit vector
allocate a bit vector that is indexed by container number and offset within a container.
> the bottleneck has moved to the construction of the bit-vector. Check the meta region of the container.

4. Perfect hash vector
**key requirement:** need a **compact** representation of a set of fingerprints that provides an **exact** answer for whether a given fingerprint exists in the set or not.
> suppose it is a static version of the membership problem where the key space is known **beforehand**.
> no dynamic insertion or deletion of keys.

Lookup cost and generation cost:
> lookup cost: the number of random memory accesses needed for each lookup.
> generation cost: a linear function on the number of fingerprints.

The amount of memory will depend on the capacity of the system since the total number of fingerprints also depends on that.

Using two levels hash functions to construct a perfect hash function


- Sanitization process
For read-only file system: (read-only restriction: the key space is static)
> 1. Merge phase: set the consistency point, flush the in-memory fingerprint index buffer and merge it with the on-disk index.
> 2. Traverse the on-disk index for all fingerprints and build the perfect hash function for all fingerprints found
> 3. Traverse all files and mark all fingerprints found as live in perfect hash vector
> 4. select containers with at least one dead chunk, and copy all live chunks from the selected containers into new containers (copy forward), and delete the selected containers.

![image-20200306224943511](../paper_figure/image-20200306224943511.png)

### Implementation and Evaluation

- Evaluation: (using synthetic backup data set)
  - without deduplication: exclude the deduplication impact on the sanitization process (as the baseline)
  - with deduplication: deleted space vs sanitization time
  - impact on ingests: the performance when both sanitization and data ingestion run concurrently.

## 2. Strength (Contributions of the paper)
1. discuss sanitization requirements in the context of deduplicated storage.
2. it proposes some memory efficient schemes for managing data references via using perfect hashes.


## 3. Weakness (Limitations of the paper)
1. In the enumeration phase it needs to traverses all the files and marks their fingerprints as alive in the $PH_{vec}$ structure. This time depends on the **logical size** of the system.

2. The copy and zero phase are the most time-consuming ones but scale linearly with the amount of data that has been deleted.

## 4. Some Insights (Future work)
1. In this paper, it mentions the **crypto sanitization**, which encrypts each file with a different key and throws away the key of the affected files. Is it feasible to adjust this scheme to deduplication system.
> key management becomes a new complexity

2. Here, it also uses perfect hash to represent the membership, and show it is memory efficient. How to adjust this technique to our problem?





