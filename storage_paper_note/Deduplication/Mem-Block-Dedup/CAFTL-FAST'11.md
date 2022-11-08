---
typora-copy-images-to: ../paper_figure
---
GoSeed: Generating an Optimal Seeding Plan for Deduplicated Storage
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| FAST'11 | FTL Deduplication |
[TOC]

## 1. Summary
### Motivation of this paper

- Motivation
  - the limited lifespan of SSDs, which are built on flash memories with limited erase/program cycles, is   still one of the most critical concerns
    - as bit density increases, high probability of correlated device failures in SSD-based RAID, endurance and retention (of SSDs) not yet proven in the field
- The lifespan of SSDs
  - The amount of incoming write traffic
  - The size of over-provisioned flash space
  - The efficiency of garbage collection and wear-leveling mechanisms
- Data deduplication 
  - data duplication
    - slice the disk space into 4KiB blocks and use the SHA-1 hash function to calculate a 160-bit hash value for each block
    - the duplication rate ranges from **7.9% to 85.9%** across the 15 disk
  - I/O duplication
    - intercept each I/O request and calculate a hash value for each requested block
      - **5.8-28.1%** of the writes are duplicated
- Challenges
  - limited resources: with limited memory space and computing power
  - relatively lower redundancy: **much lower duplication rate** than that of backup streams
  - low overhead requirement

### CAFTL (Content-Aware Flash Translation Layer)

- Main goal
  - effectively reduce write traffic to flash memory by removing unnecessary duplicate writes and can also substantially extend available free flash memory space
- The main workflow
  - intercepts incoming write requests **at the SSD device level**
  - use a hash function to generate fingerprints summarizing the content of updated data
    - querying a fingerprint store
  - design a set of acceleration method to speed up fingerprinting
    - does not need to change the standard host/device interface (without file system)
  - small on-device buffer spaces (e.g., 2MiB) and make performance overhead nearly negligible
- Design overview
  - a combination of both in-line and out-of-line deduplication
    - does not guarantee that all duplicate writes can be examined and removed immediately
  - ![image-20211122232836344](../paper_figure/image-20211122232836344.png)
- Hashing
  - adopts a **fixed-sized** chunking approach (operation unit in flash is a page (e.g., 4KiB))
  - calculate a **SHA-1** hash value as its fingerprint and store it as the page's metadata in flash
- Fingerprint store
  - manage an **in-memory** structure 
    - only store and search in the **most likely-to-be-duplicated fingerprints** in memory
    - {fingerprint, (PBA/VBA, reference)}
    - consider fingerprints with a reference counter larger that 255  as highly referenced
  - optimization
    - range check, hotness-based reorganization, bucket-level binary search
      - move the hot fingerprints closer to the list head and potentially reduces the number of the scanned buckets
- Indirect mapping
  - a mapping table to track the physical block address (PBA) to which each LBA is mapped
    - N-to-1 mapping
  - maintain a primary mapping and a secondary mapping table in memory 
    - LBA->VBA->PBA
    - track the number of referencing logical pages
    - must be able to identify quickly all the logical pages mapped to this physical page and update their mapping entries to point to the new location (GC)
    - ![image-20211123000856695](../paper_figure/image-20211123000856695.png)
  - the mapping tables in flash
    - when updating the in-memory tables, update record is logged into a small in-memory buffer
  - the metadata pages in flash
    - reserve a dedicated number of flash pages (metadata page: LBA and fingerprint)
      - keep a metadata page array for tracking PBAs of the metadata pages
- Acceleration methods
  - Sampling for hashing
    - select the first four bytes from each page in a write request
  - Light-weight pre-hashing
    - first CRC-32 then SHA-1
  - Dynamic switches
    - high watermark, low watermark to turn the in-line deduplication off and on
- Out-of-line deduplication
  - scan the metadata page array to find physical pages not yet fingerprinted
  - perform together with the GC process or independently

### Implementation and Evaluation

- Implementation
  - SSD simlator
    - DiskSim simulation environment
    - the indirect mapping, garbage collection and wear-leveling policies, and others
    - when a write request is received at the SSD, it is first buffered in the cache, and the SSD reports completion to the host
- Evaluation
  - effectiveness of deduplication
    - removing duplicate writes, extending flash space
  - performance impact
    - cache size, hashing speed, fingerprint searching
  - acceleration methods
    - sampling, light-weight pre-hashing, dynamic switch

## 2. Strength (Contributions of the paper)

- good evaluation

## 3. Weakness (Limitations of the paper)

- the implementation is based on a simulator

## 4. Some Insights (Future work)

- SSD background
  - An erase block usually consists of 64-128 pages, each page has a data area (e.g., 4KiB)
  - read and write are performed in units of **pages**, and erase clears all the pages in an **erase block**
  - Rule:
    - No in-place overwrite: the whole block must be erased before writing any page in this block
    - No random writes: the pages in an erase block must be written sequentially
    - Limited erase/program cycles
- Flash Translation Layer (FTL)
  - emulate a hard disk drive by exposing an array of logical block addresses (LBAs) to the host
  - **Indirect mapping**: track the dynamic mapping between logical block addresses (LBAs) and physical block addresses (PBAs)
  - **Log-like write mechanism**: the new content data is **appended sequentially in a clean erase block**, like a log
  - **Garbage collection**: periodically to consolidate the valid pages into a new erase block, and clean the old erase block
  -  **Wear-leveling**: tracks and shuffles hot/cold data to even out writes in flash memory
  - **Over-provisioning**: In order to assist garbage collection and wear-leveling
    - include a certain amount of over-provisioned spare flash memory space
