# Storage System Paper List
In this repo, it records some paper related to storage system, including **Data Deduplication** (aka, dedup), **Erasure Coding** (aka, EC), general **Distributed Storage System** (aka, DSS) and other related topics (i.e., Network Security.....), updating~
[TOC]

| Type                  | Paper Amount |
| --------------------- | ------------ |
| A. Data Deduplication | 33           |
| B. Erasure Coding     | 8            |
| C. Security           | 11           |
| D. Other              | 3            |



## A. Data Deduplication

### Summary
1. *99 Deduplication Problems*----HotStorage'16
2. *A Comprehensive Study of the Past, Present, and Future od Data Deduplication*----Proceedings of the IEEE'16 

### Workload Analysis
1. *Characteristics of Backup Workloads in Production Systems*----FAST'12
2. *A Study of Practical Deduplication*----FAST'11
3. *A Long-Term User-Centric Analysis of Deduplication Patterns*----MSST'16

### Deduplication System Design
1. *Avoiding the Disk Bottleneck in the Data Domain Deduplication File System*----FAST'08
2. *dedupv1: Improving Deduplication Throughput using Splid State Drives (SSD)*----MSST'10
3. *Extreme Binning: Scalable, Parallel Deduplication for Chunk-based File Backup*----MASCOTS'09
4. *Sparse Indexing: Large Scale, Inline Deduplication Using Sampling and Locality*----FAST'09
5. *Building a High-performance Deduplication System*----USENIX ATC'11
6. *Primary Data Deduplication - Large Scale Study and System Design*----USENIX ATC'12

### Restore Performance
1. *RevDedup: A Reverse Deduplication Storage System Optimized for Reads to Latest Backups*----APSys'13
2. *ALACC: Accelerating Restore Performance of Data Deduplication Systems Using Adaptive Look-Ahead Window Assisted Chunk Caching*----FAST'18
3. *Reducing Impact of Data Fragmentation Caused by In-line Deduplication*----SYSTOR'12
4. *Assuring Demanded Read Performance of Data Deduplication Storage with Backup Datasets*----MASCOTS'12

### Secure Deduplication
1. *Convergent Dispersal: Toward Storage-Efficient Security in a Cloud-of-Clouds*----HotStorage'14
2. *CDStore: Toward Reliable, Secure, and Cost-Efficient Cloud Storage via Convergent Dispersal*----USENIX ATC'15
3. *Rekeying for Encrypted Deduplication Storage*----DSN'16
4. *Information Leakage in Encrypted Deduplication via Frequency Analysis*----DSN'17
5. *Convergent Dispersal: Toward Storage-Efficient Security in a Cloud-of Clouds*----HotStorage'14
6. *DupLESS: Server-Aided Encryption for Deduplicated Storage*----USENIX Security'13
7. *Side Channels in Cloud Services, the Case of Deduplication in Cloud Storage*----S&P'10
8. *Side Channels in Deduplication: Trade-offs between Leakage and Efficiency*----AsiaCCS'17
9. *On Information Leakage in Deduplication Storage Systems*----CCS Workshop'16
10. *SecDep: A User-Aware Efficient Fine-Grained Secure Deduplication Scheme with Multi-Level Key Management*----MSST'15
11. *Message-Locked Encryption and Secure Deduplication*----EuroCrypt'13
12. *Proofs of Ownership in Remote Storage System*----CCS'11

### Metadata Management
1. *Metadedup: Deduplicating Metadata in Encrypted Deduplication via Indirection*----MSST'19
2. *Rekeying for Encrypted Deduplication Storage*----DSN'16
3. *File Recipe Compression in Data Deduplication Systems*----FAST'12
4. *LIPA: A Learning-based Indexing and Prefetching Approach for Data Deduplication*----MSST'19


### Deduplication Estimation
1. *Estimating Unseen Deduplication - from Theory to Practice*----FAST'16
2. *Estimation of Deduplication Ratios in Large Data Sets*----MSST'12
3. *Sketching Volume Capacities in Deduplicated Storage*----FAST'19

### Post-Deduplication: Data Compression
1. *Finesse: Fine-Grained Feature Locality based Fast Resemblance Detection for Post-Deduplication Delta Compression*----FAST'19

### Memory && Block-Layer Deduplication
1. *UKSM: Swift Memory Deduplication via Hierarchical and Adaptive Memory Region Distilling*----FAST'18
1. *Using Hints to Improve Inline Block-Layer Deduplication*----FAST'16

### Data Chunking
1. *SS-CDC: A Two-stage Parallel Content-Defined Chunking for Deduplicating Backup Storage*----SYSTOR'19
2. *Frequency Based Chunking for Data De-Duplication*----MASCOTS'10
3. *Bimodal Content Defined Chunking for Backup Streams*----FAST'10


### Deduplication Reliability
1. *A Simulation Analysis of Redundancy and Reliability in Primary Storage Deduplication*----TC'18
2. *A Simulation Analysis of Reliability in Primary Storage Deduplication*----IISWC'16


## B. Erasure Coding
### Improve Data Recovery
1. *CORE: Augmenting Regenerating-Coding-Based Recovery for Single and Concurrent Failures in Distributed Storage Systems*----MSST'13
2. *Degraded-First Scheduling for MapReduce in Erasure-Coded Storage Clusters*----DSN'14
3. *Repair Pipelining for Erasure-Coded Storage*----USENIX ATC'17
### EC Update Issue
1. *Cross-Rack-Aware Updates in Erasure-Coded Data Centers*----ICPP'18
2. *PARIX: Speculative Partial Writes in Erasure-Coded Systems*----USENIX ATC'17
### EC Framework
1. *OpenEC: Toward Unified and Configurable Erasure Coding Management in Distributed Storage Systems*----FAST'19

### New EC code
1. *CodePlugin: Plugging Deduplication into Erasure Coding for Cloud Storage*----HotCloud'15

### EC System
1. *Giza: Erasure Coding Objects across Global Data Centers*----USENIX ATC'17

## C. Security
### Secret Sharing
1. *How to Best Share a Big Secret*----SYSTOR'18
2. *AONT-RS: Blending Security and Performance in Dispersed Storage Systems*----FAST'11

### Data Encryption
1. *Differentially Private Access Patterns for Searchable Symmetric Encryption*----INFOCOM'18
2. *Frequency-Hiding Order-Preserving Encryption*----CCS'15
3. *RAPPOR: Randomized Aggregatable Privacy-Preserving Ordinal Response*----CCS'14
4. *Privacy at Scale: Local Differential Privacy in Practice*----SIGMOD'18
5. *Frequency-smoothing Encryption: Preventing Snapshot Attacks on Deterministically Encrypted Data*----IACR'17
6. *Efficient Homophonic Coding*----TIT'99
7. *A Note on the Optimality of Frequency Analysis vs. lp-Optimization*----IACR'15
8. *Inference Attacks on Property-Preserving Encrypted Databases*----CCS'15
9. *How Far Can we Go Beyond Linear Cryptanalysis?*----AsiaCRYPTO'04
## D. Others
### Multi-Cloud System
1. *Kurma: Secure Geo-Distributed Multi-Cloud Storage Gateways*----SYSTOR'19
2. *SPANStore: Cost-Effective Geo-Replicated Storage Spanning Multiple Cloud Services*----SOSP'13

### New PAXOS 
1. *In Search of an Understandable Consensus Algorithm*----USENIX ATC'14
