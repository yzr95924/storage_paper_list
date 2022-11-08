---
typora-copy-images-to: ../paper_figure
---
Duplicacy: A New Generation of Cloud Backup Tool Based on Lock-Free Deduplication
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| ToCC'20 | Data Encryption |
[TOC]

## 1. Summary
### Motivation of this paper

- Motivation
  - an ever-increasing demand for cross-client deduplication solution to **save network bandwidth, lower storage costs and improve backup speeds**.
  - existing solutions depend on **lock-based** approaches relying on a `centralized` chunk database
    - tends to hinder performance scalability
    - the chunk database must be shared and synchronized by all clients
  - make it hard to delete a chunk in presence of multiple clients

### Lock-free deduplication

- Main idea
  - each chunk is uploaded to storage server `individually` and stored in a file using the hash of the chunk content as the file name
- Naming chunks
  - packing together all files, as if it were creating **one big tar archive**
    - splitting the conceptual tar into chunks
    - make the number of chunks only `dependent on the total size of files`
  - backup manifest files are also split into chunks using the same variable-size chunking algorithm
    - shorten the size of the final uploaded manifest
  - key points
    - eliminating the need for a chunk database `has not been attempted before by any backup tool`
      - without a centralized chunk database or locking operations
- Lock-free situations
  - concurrent backup
    - just perform a file lookup via the file storage API
      - using the file name derived from the hash of the chunk
      - Before uploading a new chunk, the backup procedure always performs a lookup with **a filename determined only be the chunk content**
  - concurrent deletion: two-step fossil deletion
    - **fossil collection**: `aggressively identifies` unreferenced chunks based on only existing backups 
      - ignoring backups that may be still in progress
      - use all know backup manifest files to identifies unreferenced chunks
      - instead of deleting unreferenced chunks **immediately**, it performs a **renaming** operation on these chunks 
        - called "fossil chunks", can roll back later if need be
    - **fossil deletion**: lists all chunks referenced by these new backups, also check fossil chunks, 
      - if exists in the list, the fossil chunks are turned by back into the original chunk by recovering its original name
      - Finally, renaming fossil chunks will be deleted permanently
  - concurrent backup and deletion
- Duplicacy: command line version is written in **the Go language**
  - a single backup tool to support all different storage services
    - only be built on top of **only 6 basic file system APIs**: upload, download, deletion, lookup, list, and renaming
  - features: incremental backup, full snapshot, **encryption**
    - why full snapshots? a full snapshot independent of others for quick restoration and easy deletion
  - support both fixed-size chunking and variable-size chunking
    - fixed-size chunking: for the large backup files that unlikely  subject to insertions and deletions
      - e.g., databases and virtual machine disk images
  - chunk-based approaches provide native support for **incremental backup** and **full snapshot**
  - cache 
    - a file lookup is performed before uploading each chunk
    - To reduce the file lookup API calls, it maintains an in-memory cache to store hashes of chunks `referenced by the last backup from the same client`
      - directly save the backup manifest file in a local disk
  - encryption and compression
    - each chunk can be individually compressed and encrypted
    - apply HMAC-SHA256 to generate the chunk hash
      - use as the file name
      - share a HMAC secret key to all backup clients
    - chunk content is encrypted by AES-GCM with an encryption key that is **the HMAC-SHA256 of the chunk hash**
    - a master key derived from the PBKDF2 function on the storage password chosen by the user

### Implementation and Evaluation

- Evaluation
  - Trace:
    - Linux code base
    - virtual machine file of CentOS
  - Backup performance:
    - compare the execution time of backup and restore
  - Storage efficiency
    - the amounts of the storage usage
  - Ubiquity integration
    - the wide support for cloud storage systems
      - include local or networked drives
  - ![image-20210617025834304](../paper_figure/image-20210617025834304.png)

## 2. Strength (Contributions of the paper)

- It explores the way that achieves deduplication without using a dedicated chunk database
- prove all proposed schemes are lock-free

## 3. Weakness (Limitations of the paper)

- does not consider the case that has malicious clients 

## 4. Some Insights (Future work)

- cross-client deduplication
  - deduplication can be exploited by multiple backup processes running **concurrently**

- Existing backup system (open-source)
  - Duplicity: http://duplicity.nongnu.org/
    - based on librsync
  - Restic: https://github.com/restic/restic
  - Attic (Borg) - python
    - Attic: https://github.com/jborg/attic
    - Borg: https://github.com/borgbackup/borg
- Obnam: https://obnam.org/
  - Bupstash: https://bupstash.io/
    - https://github.com/andrewchambers/bupstash
  
- drawback of rsync
  - make uses of the **fixed-size chunking algorithm** where the chunk size is determined by the file size
    - add a simple rolling hash to detect insertions and deletions
      - one lookup every time the sliding window is shifted by one byte
    - different from the CDC chunking
      - a lookup to check duplicates is performed only after a breakpoint hash been identified
        - rather than one per byte as in the case of rsync
    - incremental backup becomes dependent on previous backups
      - the dependency chain would be too long led to rendering deletion impossible and restoration slow