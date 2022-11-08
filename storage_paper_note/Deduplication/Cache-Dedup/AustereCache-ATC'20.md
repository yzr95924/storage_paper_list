---
typora-copy-images-to: ../paper_figure
---
Austere Flash Caching with Deduplication and Compression
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| USENIX ATC'20 | I/O deduplication |
[TOC]

## 1. Summary
### Motivation of this paper

- motivation
  - deduplication and compression are promising data reduction for I/O savings via the removal of duplicate content
    - yet incur **substantial memory overhead for index management**
    - deduplication: removes chunk-level duplicates
    - compression: removes byte-level duplicates within chunks
- flash caching
  - building an SSD-based flash cache to boost the I/O performance of HDD-based primary storage
    - storing the frequently accessed data in the flash cache
  - conventional flash caching needs an SSD-HDD translation layer that maps **each LBA in an HDD** to **a chunk address (CA) in the flash cache**
  - LBA-index
    - track how each LBA is mapped to the FP of a chunk (**many-to-one**: as multiple LBAs may refer to the same FP)
  - FP-index
    - tracks how each FP is mapped to the CA and the length of a compressed chunk (**one-to-one**)
- memory amplification
  - **conventional flash caching**: 
    - only needs to index (LBA, CA) pair
  - with deduplication and compression
    - LBA-index: (LBA, FP) pairs
    - FP-index: (FP, CA) pairs

### Austere Cache

- Bucketization
  - partitions both the LBA-index and the FP-index into **equal-size buckets** composed of a fixed number of **equal-size slots**
    - each slot corresponds to an LBA and an FP
  - divide the flash cache space into **a metadata region** and **a data region** 
    - also partitioned into buckets with multiple slots (**same numbers** of buckets and slots as in the FP-index), FP-index is **one-to-one** mapping to **the same slots** in the metadata and data regions
  - ![image-20211129001332809](../paper_figure/image-20211129001332809.png)
  - Reduce the memory usage
    - each slot only **the prefix of a key**, rather than a full key
      - computes the hashes of both the LBA and the FP
    - resolve the hash collisions
      - maintain the full LBA and FP information in the metadata region in flash 
        - any hash collision only leads to a cache miss without data loss
  - write path: **input (LBA, FP)**
    - update the LBA-index and the FP-index (use **the suffix bits** to identify the bucket)
      - **scan all slots in the corresponding bucket** to see if the LBA-hash prefix has already been stored
      - If the slot is full and cannot store more LBAs, it evicts **the oldest LBA using FIFO**
  - deduplication path: **input (LBA, FP)**
    - use the FP-hash to find the corresponding buckets, and then checks **FP-hash prefix**
      - further check the corresponding slot in the metadata region in the flash, and verifies the input FP matches the one in the slot
  - read path: **input (LBA)**
    - check the LBA-index for **the FP-hash** using the LBA-hash prefix
      - further querying the FP-index for the slot that contains the FP-hash prefix, then check the corresponding slot in the metadata region
- Fixed-size compressed data management
  - **Avoid tracking the length of the compressed chunk** in the index structures
    - slice a compressed chunk into fixed-size subchunks
    - **the last subchunk is padded** to fill a subchunk size
      - subchunk size is 8 KiB
  - LBA-index remains unchanged
    - **allocate a slot for each subchunk in FP-index (data region, metadata region)**
  - find **consecutive slots in the FP-index** for the multiple subchunks of a compressed chunk
- Bucket-based cache replacement
  - the cache replacement decisions are based on **only the entries within each bucket**
    - incurs limited performance overhead
  - a bucket-based LRU policy
    - each bucket sorts all slots by the recency of their LBAs
      - the slots **at the lower offsets correspond to the more recently accessed LBAs**
    - does not incur any extra memory overhead for maintaining the recency information of all slots
  - use CM-Sketch to track the reference count of all FP-hashes

### Implementation and Evaluation

- Implementation
  - issues reads and writes to the underlying storage devices via **pread** and **pwrite** system calls
  - XXHash for fast hash computations in the index structures
  - SHA-1 from Intel ISA-L
  - bucket-level concurrency
- Evaluation
  - Trace: FIU, Synthetic
  - Baseline: CacheDedup
- Comparative analysis
  - Overall memory usage, impact of design techniques on memory saving, read hit ratio, write reduction ratio
- Sensitivity to parameters
  - Impact of chunk sizes and subchunk sizes, impact of LBA-index sizes
- Throughput and CPU overhead
  - Throughput, CPU overhead, Throughput of multi-threading 

## 2. Strength (Contributions of the paper)

- use the slot mapping to avoid storing CA
- good evaluation
  - very comprehensive 

## 3. Weakness (Limitations of the paper)

## 4. Some Insights (Future work)

- background of flash deduplication and compression
  - extra metadata:
    - the mappings of each logical address to the physical address of the non-duplicate chunk **in the flash cache**
    - **the cryptographic hashes** of all stored chunks in the flash cache
    - **the lengths** of all compressed chunks that are of variable size
  - **fixed-size** chunks fit better into flash units
