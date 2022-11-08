# Paper Reading List of Storage Systems

A reading list related to storage system, including data deduplication, erasure coding, general storage and other related topics (i.e., Security...), updating from time to time~

Table of Contents
=================

* [Paper Reading List of Storage Systems](#paper-reading-list-of-storage-systems)
   * [Data Deduplication](#data-deduplication)
      * [Summary](#summary)
      * [Workload Analysis](#workload-analysis)
      * [Deduplicated System Design](#deduplicated-system-design)
      * [Restore Performances](#restore-performances)
      * [Secure Deduplication](#secure-deduplication)
      * [Metadata Management](#metadata-management)
      * [Indexing &amp; Caching](#indexing--caching)
      * [Deduplication Estimation](#deduplication-estimation)
      * [Post-Deduplication: Data Compression and Delta Compression](#post-deduplication-data-compression-and-delta-compression)
      * [Memory &amp;&amp; Block-Layer Deduplication](#memory--block-layer-deduplication)
      * [Data Chunking](#data-chunking)
      * [Cache Deduplication](#cache-deduplication)
      * [Garbage Collection](#garbage-collection)
      * [Network Deduplication](#network-deduplication)
      * [Distributed Deduplication](#distributed-deduplication)
   * [Erasure Coding &amp;&amp; RAID](#erasure-coding--raid)
      * [Erasure Coding Basics](#erasure-coding-basics)
      * [Improve Data Recovery](#improve-data-recovery)
      * [EC Update Issue](#ec-update-issue)
      * [EC Framework](#ec-framework)
      * [New EC code](#new-ec-code)
      * [EC System](#ec-system)
      * [RAID](#raid)
   * [Security](#security)
      * [Survey](#survey)
      * [Secret Sharing](#secret-sharing)
      * [Data Encryption](#data-encryption)
      * [Secure Deletion](#secure-deletion)
      * [Differential Privacy](#differential-privacy)
      * [SGX Technique](#sgx-technique)
      * [SGX Storage](#sgx-storage)
      * [Network Security](#network-security)
   * [General Storage](#general-storage)
      * [Distributed Storage System](#distributed-storage-system)
      * [Consensus](#consensus)
      * [Cache](#cache)
      * [Hash](#hash)
      * [Lock-free storage](#lock-free-storage)
      * [SSD, NVMe](#ssd-nvme)
      * [File system](#file-system)
      * [Persistent Memory](#persistent-memory)
      * [Data Structure](#data-structure)
      * [Benchmark](#benchmark)
      * [I/O Optimizations](#io-optimizations)
      * [Deployed Systems](#deployed-systems)
      * [CXL](#cxl)
      * [Failures](#failures)
      * [Ceph Related Research](#ceph-related-research)
## Data Deduplication

### Summary
1. *Understanding Data Deduplication Ratios*----SNIA'08 ([link](https://www.snia.org/sites/default/files/Understanding_Data_Deduplication_Ratios-20080718.pdf))
2. *A Survey of Classification of Storage Deduplication Systems*----ACM Computing Surveys'14 ([link](https://dl.acm.org/citation.cfm?id=2611778))
3. *A Comprehensive Study of the Past, Present, and Future on Data Deduplication*----Proceedings of the IEEE'16 ([link](https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=7529062))
4. *99 Deduplication Problems*----HotStorage'16 ([link](https://pdfs.semanticscholar.org/bd54/6dda50541489ff23fbc1e154dea50d911a43.pdf)) ([summary](https://yzr95924.github.io/paper_summary/99DeduplicationProblem-HotStorage'16.html))
5. *A Survey of Secure Data Deduplication Schemes for Cloud Storage Systems*----ACM Computing Surveys'17 ([link](https://dl.acm.org/citation.cfm?id=3017428))
6. *Backup to the Future: How Workload and Hardware Changes Continually Redefine Data Domain File Systems*----IEEE Computer'17 ([link](https://ieeexplore.ieee.org/abstract/document/7971884))

### Workload Analysis
1. *Characterizing Datasets for Data Deduplication in Backup Applications*----IISWC'10 ([link](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=5650369))
2. *A Study of Practical Deduplication*----FAST'11 ([link](https://www.usenix.org/legacy/event/fast11/tech/full_papers/Meyer.pdf)) [summary](https://yzr95924.github.io/paper_summary/PracticalDedup-FAST'11.html)
3. *Capacity Forecasting in a Backup Storage Environment*----LISA'11 ([link](https://www.usenix.org/legacy/events/lisa11/tech/full_papers/Chamness.pdf)) [summary](https://yzr95924.github.io/paper_summary/CapacityForecasting-LISA'11.html)
4. *Characteristics of Backup Workloads in Production Systems*----FAST'12 ([link](http://www.usenix.net/legacy/events/fast12/tech/full_papers/Wallace2-9-12.pdf)) [summary](https://yzr95924.github.io/paper_summary/BackupWorkloads-FAST'12.html)
5. *A Study on Data Deduplication in HPC Storage Systems*----SC'12 ([link](https://dl.acm.org/doi/pdf/10.5555/2388996.2389006))
6. *Inside Dropbox: Understanding Personal Cloud Storage Services*----IMC'12 ([link](https://dl.acm.org/doi/pdf/10.1145/2398776.2398827))
7. *Insights for Data Reduction in Primary Storage: a Practical Analysis*----SYSTOR'12 ([link](https://dl.acm.org/doi/pdf/10.1145/2367589.2367606))
8. *Modeling the Dropbox Client Behavior*----ICC'14 ([link]( https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=6883506 ))
9. *Identifying Trends in Enterprise Data Protection Systems*----USENIX ATC'15 ([link](https://www.usenix.org/system/files/conference/atc15/atc15-paper-amvrosladis.pdf))
10. *A Long-Term User-Centric Analysis of Deduplication Patterns*----MSST'16 ([link](https://www.fsl.cs.sunysb.edu/docs/msst16dedup-study/data-set-analysis.pdf))
11. *A Simulation Analysis of Reliability in Primary Storage Deduplication*----IISWC'16
12. *A Simulation Analysis of Redundancy and Reliability in Primary Storage Deduplication*----TC'18 ([link]()) [summary](https://yzr95924.github.io/paper_summary/SimRedundancy-TC'18.html)
13. *Deduplication Analyses of Multimedia System Images*----HotStorage'18 ([link](https://www.usenix.org/system/files/conference/hotedge18/hotedge18-papers-suess.pdf))
14. *Improving Docker Registry Design based on Production Workload Analysis*----FAST'18 ([link](https://www.usenix.org/system/files/conference/fast18/fast18-anwar.pdf))

### Deduplicated System Design

1. *Venti: A New Approach to Archival Storage*----FAST'02 ([link](https://www.usenix.org/legacy/publications/library/proceedings/fast02/quinlan/quinlan.pdf))
2. *Avoiding the Disk Bottleneck in the Data Domain Deduplication File System*----FAST'08 ([link](https://www.usenix.org/legacy/event/fast08/tech/full_papers/zhu/zhu.pdf)) [summary](https://yzr95924.github.io/paper_summary/DiskBottleneck-FAST'08.html)
3. *Sparse Indexing: Large Scale, Inline Deduplication Using Sampling and Locality*----FAST'09 ([link](https://pdfs.semanticscholar.org/6585/e111960d2b170bb6677865b73b6d1f27d71a.pdf)) [summary](yzr95924.github.io/paper_summary/SparseIndex-FAST'09.html)
4. *Extreme Binning: Scalable, Parallel Deduplication for Chunk-based File Backup*----MASCOTS'09 ([link](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.467.1985&rep=rep1&type=pdf)) [summary](https://yzr95924.github.io/paper_summary/ExtremeBining-MASCOTS'09.html)
5. *I/O Deduplication: Utilizing Content Similarity to Improve I/O Performance*----FAST'10 ([link](https://www.usenix.org/legacy/event/fast10/tech/full_papers/koller.pdf))
6. *dedupv1: Improving Deduplication Throughput using Solid State Drives (SSD)*----MSST'10 ([link](https://ieeexplore.ieee.org/document/5496992)) [summary](https://yzr95924.github.io/paper_summary/dedupv1-MSST'10.html)
7. *ChunkStash: Speeding up Inline Storage Deduplication using Flash Memory*----USENIX ATC'10 ([link](https://www.usenix.org/legacy/events/atc10/tech/full_papers/Debnath.pdf)) 
8. *SiLo: A Similarity-Locality based Near-Exact Deduplication Scheme with Low RAM Overhead and High Throughput*----USENIX ATC'11 ([link](https://www.usenix.org/legacy/event/atc11/tech/final_files/Xia.pdf))
9. *Building a High-performance Deduplication System*----USENIX ATC'11 ([link]( https://www.usenix.org/legacy/event/atc11/tech/final_files/GuoEfstathopoulos.pdf )) [summary]( https://yzr95924.github.io/paper_summary/Dedup-ATC'11.html )
10. *Primary Data Deduplication - Large Scale Study and System Design*----USENIX ATC'12 ([link]( https://www.usenix.org/system/files/conference/atc12/atc12-final293.pdf ))
11. *iDedup: Latency-aware, Inline Data Deduplication for Primary Storage*----FAST'12 ([link]( https://www.usenix.org/legacy/event/fast12/tech/full_papers/Srinivasan.pdf )) [summary](https://yzr95924.github.io/paper_summary/iDedup-FAST'12.html)
12. *Deduplication in SSDs: Model and quantitative analysis*----MSST'12 ([link](https://ieeexplore.ieee.org/document/6232379))
13. *Efficiently Storing Virtual Machine Backups*----HotStorage'13 ([link](https://www.usenix.org/system/files/conference/hotstorage13/hotstorage13-smaldone.pdf))
14. *Storage Efficiency Opportunities and Analysis for Video Repositories*----HotStorage'15 ([link](https://www.usenix.org/system/files/conference/hotstorage15/hotstorage15-dewakar.pdf))
15. *Deriving and Comparing Deduplication Techniques Using a Model-Based Classification*----EuroSys'15 ([link](https://dl.acm.org/doi/pdf/10.1145/2741948.2741952))
16. *Design Tradeoffs for Data Deduplication Performance in Backup Workloads*----FAST'15 ([link](https://www.usenix.org/system/files/conference/fast15/fast15-paper-fu.pdf)) [summary](https://yzr95924.github.io/paper_summary/DedupDesignTradeoff-FAST'15.html)
17. *Sorted Deduplication: How to Process Thousands of Backup Streams*----MSST'16 ([link](https://storageconference.us/2016/Papers/SortedDeduplication.pdf))
18. Can't We All Get Along? Redesigning Protection Storage for Modern Workloads----USENIX ATC'18 ([link](https://www.usenix.org/system/files/conference/atc18/atc18-allu.pdf)) [summary](https://yzr95924.github.io/paper_summary/Redesigning-ATC'18.html)
19. *Data Domain Cloud Tier: Backup here, Backup there, Deduplicated Everywhere!*----USENIX ATC'19 ([link](https://www.usenix.org/system/files/atc19-duggal.pdf)) [summary]( https://yzr95924.github.io/paper_summary/CloudTier-ATC'19.html )
20. *SmartDedup: Optimizing Deduplication for Resource-constrained Devices*----USENIX ATC'19 ([link](https://www.usenix.org/system/files/atc19-yang-qirui.pdf))
21. *DupHunter: Flexible High-Performance Deduplication for Docker Registries*----USENIX ATC'20 ([link](https://www.usenix.org/system/files/atc20-zhao.pdf))
22. *The Dilemma between Deduplication and Locality: Can Both be Achieved?*---FAST'21 ([link](https://www.usenix.org/system/files/fast21-zou.pdf)) [summary](https://yzr95924.github.io/paper_summary/MFDedup-FAST'21.html)
23. *SLIMSTORE: A Cloud-based Deduplication System for Multi-version Backups*----ICDE'21 ([link](http://www.cs.utah.edu/~lifeifei/papers/slimstore-icde21.pdf))
24. *Improving the Performance of Deduplication-Based Backup Systems via Container Utilization Based Hot Fingerprint Entry Distilling*----ACM TOS'21 ([link](https://dl.acm.org/doi/full/10.1145/3459626))
25. *DedupSearch: Two-Phase Deduplication Aware Keyword Search*----FAST'22 ([link](https://www.usenix.org/system/files/fast22-elias.pdf)) [summary](https://yzr95924.github.io/paper_summary/DedupSearch-FAST'22.html)

### Restore Performances

1. *RevDedup: A Reverse Deduplication Storage System Optimized for Reads to Latest Backups*----APSys'13 ([link](http://adslab.cse.cuhk.edu.hk/pubs/apsys13.pdf)) [summary](https://yzr95924.github.io/paper_summary/RevDedup-APSys'13.html)
2. *ALACC: Accelerating Restore Performance of Data Deduplication Systems Using Adaptive Look-Ahead Window Assisted Chunk Caching*----FAST'18 ([link](https://www.usenix.org/system/files/conference/fast18/fast18-cao.pdf)) [summary](https://yzr95924.github.io/paper_summary/ALACC-FAST'18.html)
3. *Reducing Impact of Data Fragmentation Caused by In-line Deduplication*----SYSTOR'12 ([link](http://9livesdata.com/wp-content/uploads/2017/04/AsPresentedOnSYSTOR-1.pdf))
4. *Reducing Fragmentation Impact with Forward Knowledge in Backup Systems with Deduplication*----SYSTOR'15 ([link](https://dl.acm.org/doi/10.1145/2757667.2757678))
5. *Assuring Demanded Read Performance of Data Deduplication Storage with Backup Datasets*----MASCOTS'12 ([link](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=6298180))
6. *Sliding Look-Back Window Assisted Data Chunk Rewriting for Improving Deduplication Restore Performance*----FAST'19 ([link](https://www.usenix.org/system/files/fast19-cao.pdf)) [summary](https://yzr95924.github.io/paper_summary/LookBackWindow-FAST'19.html)
7. *Improving Restore Speed for Backup Systems that Use Inline Chunk-Based Deduplication*---FAST'13 ([link](https://www.usenix.org/system/files/conference/fast13/fast13-final124.pdf)) [summary](https://yzr95924.github.io/paper_summary/ImproveRestore-FAST'13.html)
8. *Chunk Fragmentation Level: An Effective Indicator for Read Performance Degradation in Deduplication Storage*----HPCC'11 
9. *Improving the Restore Performance via Physical Locality Middleware for Backup Systems*----Middleware'20 ([link](https://dl.acm.org/doi/pdf/10.1145/3423211.3425691)) [summary](https://yzr95924.github.io/paper_summary/HiDeStore-Middleware'20.html)
10. Efficient Hybrid Inline and Out-of-Line Deduplication for Backup Storage----ACM TOS'14 ([link](https://www.cse.cuhk.edu.hk/~pclee/www/pubs/tos14revdedup.pdf))

### Secure Deduplication
1. *Convergent Dispersal: Toward Storage-Efficient Security in a Cloud-of-Clouds*----HotStorage'14 ([link](https://www.cse.cuhk.edu.hk/~pclee/www/pubs/hotstorage14.pdf)) [summary](https://yzr95924.github.io/paper_summary/CAONT-RS-HotStorage'14.html)
2. *CDStore: Toward Reliable, Secure, and Cost-Efficient Cloud Storage via Convergent Dispersal*----USENIX ATC'15 ([link](https://www.usenix.org/system/files/conference/atc15/atc15-paper-li-mingqiang.pdf)) [summary](https://yzr95924.github.io/paper_summary/CDStore-ATC'15.html)
3. *Information Leakage in Encrypted Deduplication via Frequency Analysis*----DSN'17 ([link](https://www.cse.cuhk.edu.hk/~pclee/www/pubs/dsn17.pdf))
4. *DupLESS: Server-Aided Encryption for Deduplicated Storage*----USENIX Security'13 ([link](https://eprint.iacr.org/2013/429.pdf)) [summary](https://yzr95924.github.io/paper_summary/DupLESS-Security'13.html)
5. *Side Channels in Cloud Services, the Case of Deduplication in Cloud Storage*----S&P'10 ([link](http://www.pinkas.net/PAPERS/hps.pdf)) [summary](https://yzr95924.github.io/paper_summary/SideChannel-S&P'10.html)
6. *Side Channels in Deduplication: Trade-offs between Leakage and Efficiency*----AsiaCCS'17 ([link](https://dl.acm.org/doi/abs/10.1145/3052973.3053019)) [summary](https://yzr95924.github.io/paper_summary/SideChannelTradeOffs-AsiaCCS'17.html)
7. *On Information Leakage in Deduplication Storage Systems*----CCS Workshop'16 [summary](https://yzr95924.github.io/paper_summary/InformationLeakage-CCSW'16.html)
8. *SecDep: A User-Aware Efficient Fine-Grained Secure Deduplication Scheme with Multi-Level Key Management*----MSST'15 ([link](https://cswxia.github.io/SecDep-final-2015.pdf))
9. *Message-Locked Encryption and Secure Deduplication*----EuroCrypt'13 [summary](https://yzr95924.github.io/paper_summary/MLE-EuroCrypto'13.html)
10. *Proofs of Ownership in Remote Storage System*----CCS'11 ([link](https://dl.acm.org/doi/pdf/10.1145/2046707.2046765))
11. *Tapping the Potential: Secure Chunk-based Deduplication of Encrypted Data for Cloud Backup*----CNS'18 [summary](https://yzr95924.github.io/paper_summary/TappingPotential-CNS'18.html)
12. *A Bandwidth-Efficient Middleware for Encrypted Deduplication*----DSC'18 [summary](https://yzr95924.github.io/paper_summary/UWare-DSC'18.html)
13. *Bloom Filter Based Privacy Preserving Deduplication System*----Springer International Conference on Security & Privacy'19 ([link](https://link.springer.com/chapter/10.1007/978-981-13-7561-3_2)) [summary](https://yzr95924.github.io/paper_summary/BloomFilterDedup-ICSP'19.html)
14. *Enhanced Secure Thresholded Data Deduplication Scheme for Cloud Storage*----TDSC'16 ([link](https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=7553458)) [summary](https://yzr95924.github.io/paper_summary/EnhancedThreshold-TDSC'16.html)
15. *Transparent Data Deduplication in the Cloud*----CCS'15 ([link](http://www.ghassankarame.com/dedup.pdf)) [summary](https://yzr95924.github.io/paper_summary/ClearBox-CCS'15.html)
16. *Secure Deduplication of Encrypted Data without Additional Independent Servers*----CCS'15 ([link](https://eprint.iacr.org/2015/455.pdf)) [summary](https://yzr95924.github.io/paper_summary/IndependentServer-CCS'15.html)
17. *Fast and Secure Laptop Backups with Encrypted Deduplication*----LISA'10 ([link](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.368.3092&rep=rep1&type=pdf))
18. *Weak Leakage-Resilient Client-side Deduplication of Encrypted Data in Cloud Storage*----ASIA CCS'13 ([link](https://eprint.iacr.org/2011/538.pdf))
19. *Lamassu: Storage-Efficient Host-Side Encryption*----USENIX ATC'15 ([link](https://www.usenix.org/system/files/conference/atc15/atc15-paper-shah.pdf))
20. *Mitigating Traffic-based Side Channel Attacks in Bandwidth-efficient Cloud Storage*----IPDPS'18 ([link](https://csyhua.github.io/csyhua/hua-ipdps2018.pdf)) [summary](https://yzr95924.github.io/paper_summary/MitigatingSideChannel-IPDPS'18.html)
21. *RARE: Defeating Side Channels based on Data-Deduplication in Cloud Storage*----INFOCOM'18 ([link](https://ieeexplore.ieee.org/document/8406888)) [summary](https://yzr95924.github.io/paper_summary/RARE-INFOCOM'18.html)
22. *PerfectDedup: Secure Data Deduplication*----Data Privacy Management, and Security Assurance'15 ([link](http://www.eurecom.fr/fr/publication/4683/download/rs-publi-4683.pdf))
23. *Privacy Aware Data Deduplication for Side Channel in Cloud Storage*----ToCC'18 ([link](https://ieeexplore.ieee.org/abstract/document/8260900))
24. *PraDa: Privacy-preserving Data Deduplication as a Service*----CIKM'14 ([link](https://msuweb.montclair.edu/~dongb/publications/cikm2014.pdf))
25. *Privacy-Preserving Data Deduplication on Trusted Processors*----CLOUD'17 ([link](https://ieeexplore.ieee.org/document/8030573)) [summary]( https://yzr95924.github.io/paper_summary/PrivacyPreservingDedup-CLOUD'17.html )
26. *Distributed Key Generation for Encrypted Deduplication: Achieving the Strongest Privacy*----CCSW'14 ([link]( https://dl.acm.org/doi/abs/10.1145/2664168.2664169 )) [summary](https://yzr95924.github.io/paper_summary/DistributedKeyGen-CCSW'14.html)
27. *Proofs of Ownership on Encrypted Cloud Data via Intel SGX*----ACNS'20 ([link](https://link.springer.com/chapter/10.1007/978-3-030-61638-0_22)) [summary](https://yzr95924.github.io/paper_summary/PoWSGX-ACNS'20.html)
28. *Accelerating Encrypted Deduplication via SGX*----USENIX ATC'21([link](http://www.cse.cuhk.edu.hk/~pclee/www/pubs/atc21sgxdedup.pdf))
29. *S2Dedup: SGX-enabled Secure Deduplication*----SYSTOR'21 ([link](https://dl.acm.org/doi/pdf/10.1145/3456727.3463773)) [summary](https://yzr95924.github.io/paper_summary/S2Dedup-SYSTOR'21.html)
30. *Secure Deduplication of General Computations*----USENIX ATC'15 ([link](https://www.usenix.org/system/files/conference/atc15/atc15-paper-tang.pdf))
31. *When Delta Sync Meets Message-Locked Encryption: a Feature-based Delta Sync Scheme for Encrypted Cloud Storage*----ICDCS'21 ([link](https://shenzr.github.io/publications/featuresync-icdcs21.pdf)) [summary](https://yzr95924.github.io/paper_summary/FeatureSync-ICDCS'21.html)
31. *DUPEFS: Leaking Data Over the Network With Filesystem Deduplication Side Channels*----FAST'22 ([link](https://www.usenix.org/system/files/fast22-bacs.pdf)) [summary](https://yzr95924.github.io/paper_summary/DUPEFS-FAST'22.html)


### Metadata Management
1. *Metadedup: Deduplicating Metadata in Encrypted Deduplication via Indirection*----MSST'19 ([link](https://www.cse.cuhk.edu.hk/~pclee/www/pubs/msst19dedup.pdf))
2. *Rekeying for Encrypted Deduplication Storage*----DSN'16 ([link](http://adslab.cse.cuhk.edu.hk/pubs/dsn16reed.pdf)) [summary](https://yzr95924.github.io/paper_summary/REED-DSN'16.html)
3. *File Recipe Compression in Data Deduplication Systems*----FAST'13 ([link](https://www.usenix.org/system/files/conference/fast13/fast13-final54.pdf)) [summary](https://yzr95924.github.io/paper_summary/FileRecipeCompression-FAST'13.html)
4. *Metadata Considered Harmful ... to Deduplication*----HotStorage'15 ([link](https://www.usenix.org/system/files/conference/hotstorage15/hotstorage15-lin.pdf)) [summary](https://yzr95924.github.io/paper_summary/MetadataHarmful-HotStorage'15.html)

### Indexing & Caching

1. *LIPA: A Learning-based Indexing and Prefetching Approach for Data Deduplication*----MSST'19 ([link](http://storageconference.us/2019/Research/LIPA.pdf)) [summary](https://yzr95924.github.io/paper_summary/LIPA-MSST'19.html)
2. *Lazy Exact Deduplication*----MSST'16 ([link](https://storageconference.us/2016/Papers/LazyExactDeduplication.pdf))
3. *MAD2: A Scalable High-throughput Exact Deduplication Approach for Network Backup Services*----MSST'10 ([link](https://ranger.uta.edu/~jiang/publication/Conferences/2010/2010-MSST1-MAD2.pdf))
4. *Block Locality Caching for Data Deduplication*----SYSTOR'13 ([link](https://dl.acm.org/citation.cfm?id=2485748))
5. *HANDS: A Heuristically Arranged Non-Backup In-line Deduplication System*----ICDE'13 ([link](https://www.ssrc.ucsc.edu/Papers/ssrctr-12-03.pdf))

### Deduplication Estimation

1. *Estimating Unseen Deduplication - from Theory to Practice*----FAST'16 ([link](https://www.usenix.org/system/files/conference/fast16/fast16-papers-harnik.pdf)) [summary](https://yzr95924.github.io/paper_summary/HintsDeduplication-FAST'16.html)
2. *Estimation of Deduplication Ratios in Large Data Sets*----MSST'12 ([link](http://www.storageconference.us/2012/Papers/17.Deduplication.Estimation.pdf)) [summary](https://yzr95924.github.io/paper_summary/EstimateDedupRatio-MSST'12.html)
3. *Sketching Volume Capacities in Deduplicated Storage*----FAST'19 ([link](https://www.usenix.org/system/files/fast19-harnik.pdf)) [summary](https://yzr95924.github.io/paper_summary/SketchDeduplication-FAST'19.html)
4. *Estimating Duplication by Content-based Sampling*----USENIX ATC'13 [summary](https://yzr95924.github.io/paper_summary/ContentBasedSampling-ATC'13.html)
5. *Content-aware Load Balancing for Distributed Backup*----LISA'11 ([link](https://www.usenix.org/legacy/event/lisa11/tech/full_papers/Chamness.pdf))
6. *Rangoli: Space Management in Deduplication Environments*----SYSTOR'13 ([link](https://atg.netapp.com/wp-content/uploads/2013/07/Systor13-Rangoli.pdf)) [summary](https://yzr95924.github.io/paper_summary/Rangoli-SYSTOR'13.html)

### Post-Deduplication: Data Compression and Delta Compression
1. *Redundancy Elimination Within Large Collections of Files*----USENIX ATC'04 ([link](https://www.usenix.org/legacy/publications/library/proceedings/usenix04/tech/general/full_papers/kulkarni/kulkarni.pdf))
2. *The Design of a Similarity Based Deduplication System*----SYSTOR'09 ([link](https://dl.acm.org/doi/pdf/10.1145/1534530.1534539))
3. *Delta Compressed and Deduplicated Storage Using Stream-Informed Locality*----HotStorage'12 ([link](https://www.usenix.org/system/files/conference/hotstorage12/hotstorage12-final38_0.pdf)) [summary](https://yzr95924.github.io/paper_summary/deltaStore-HotStorage'12.html)
4. *WAN Optimized Replication of Backup Datasets Using Stream-Informed Delta Compression*----FAST'12 ([link](https://dl.acm.org/doi/pdf/10.1145/2385603.2385606)) [summary](https://yzr95924.github.io/paper_summary/deltaWAN-FAST'12.html)
5. *To Zip or not to Zip: Effective Resource Usage for Real-Time Compression*----FAST'13 ([link](https://www.usenix.org/system/files/conference/fast13/fast13-final38.pdf)) [summary](https://yzr95924.github.io/paper_summary/CompressionEst-FAST'13.html)
6. *Combining Deduplication and Delta Compression to Achieve Low-Overhead Data Reduction on Backup Datasets*----DCC'14 ([link](https://cswxia.github.io/pub/dcc-wen-delta-2014.pdf)) [summary](https://yzr95924.github.io/paper_summary/DeltaCompression-DCC'14.html)
7. *Ddelta: A Deduplication-inspired Fast Delta Compression Approach*----Performance'14 ([link](https://www.sciencedirect.com/science/article/pii/S0166531614000790))
8. *Migratory Compression: Coarse-grained Data Reordering to Improve Compressibility*----FAST'14 ([link](https://www.usenix.org/system/files/conference/fast14/fast14-paper_lin.pdf)) [summary](https://yzr95924.github.io/paper_summary/MigratoryCompression-FAST'14.html)
9. *Odess: Speeding up Resemblance Detection for Redundancy Elimination by Fast Content-Defined Sampling*----ICDE'14 ([link](https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=9458911))
10. *Reducing Replication Bandwidth for Distributed Document Databases*----SoCC'15 ([link](https://www.cs.cmu.edu/~pavlo/papers/socc15-sdedup.pdf))
11. *Edelta: A Word-Enlarging Based Fast Delta Compression Approach*----HotStorage'15 ([link](https://www.usenix.org/system/files/conference/hotstorage15/hotstorage15-xia.pdf)) 
12. *Online Deduplication for Database*----SIGMOD'17 ([link](https://www.pdl.cmu.edu/PDL-FTP/Database/xu-sigmod17.pdf))
13. *Finesse: Fine-Grained Feature Locality based Fast Resemblance Detection for Post-Deduplication Delta Compression*----FAST'19 ([link](https://www.usenix.org/system/files/fast19-zhang.pdf)) [summary](https://yzr95924.github.io/paper_summary/Finesse-FAST'19.html)
14. *Improving Restore Performance for In-Line Backup System Combining Deduplication and Delta Compression*----TPDS'20 ([link](https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=9080096))
15. *Exploring the Potential of Fast Delta Encoding: Marching to a Higher Compression Ratio*----CLUSTER'20 ([link](https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=9229609)) [summary](https://yzr95924.github.io/paper_summary/Gdelta-CLUSTER'20.html)
16. *Adaptively Compressing IoT Data on the Resource-constrained Edge*----HotEdge'20 ([link](https://www.usenix.org/system/files/hotedge20_paper_lu.pdf))
17. *Length Preserving Compression – Marrying Encryption with Compression*----SYSTOR'21 ([link](https://dl.acm.org/doi/pdf/10.1145/3456727.3463780)) [summary](https://yzr95924.github.io/paper_summary/LPC-SYSTOR'21.html)
18. *DeepSketch: A New Machine Learning-Based Reference Search Technique for Post-Deduplication Delta Compression*----FAST'22 ([link](https://www.usenix.org/system/files/fast22-park.pdf)) [summary](https://yzr95924.github.io/paper_summary/DeepSketch-FAST'22.html)
19. *Building a High Performance Fine-grained Deduplication Framework for Backup Storage with High Deduplication Ratio*----USENIX ATC'22 ([link](https://www.usenix.org/system/files/atc22-zou.pdf)) [summary](https://yzr95924.github.io/paper_summary/MeGA-ATC'22.html)
19. *Donag: Generating Eficient Patches and Difs for Compressed Archives*----ACM TOS'22 ([link](https://dl.acm.org/doi/pdf/10.1145/3507919))

### Memory && Block-Layer Deduplication

1. *CAFTL: A Content-Aware Flash Translation Layer Enhancing the Lifespan of Flash Memory based Solid State Drives*----FAST'11 ([link](https://www.usenix.org/legacy/event/fast11/tech/full_papers/Chen.pdf)) [summary](https://yzr95924.github.io/paper_summary/CAFTL-FAST'11.html)
2. *XLM: More Effective Memory Deduplication Scanners through Cross-Layer Hints*----USENIX ATC'13 ([link](https://www.usenix.org/system/files/conference/atc13/atc13-miller.pdf))  
3. *Using Hints to Improve Inline Block-Layer Deduplication*----FAST'16 ([link]()) [summary](https://yzr95924.github.io/paper_summary/HintsDeduplication-FAST'16.html)
4. *OrderMergeDedup: Efficient, Failure-Consistent Deduplication on Flash*----FAST'16 ([link](https://www.usenix.org/system/files/conference/fast16/fast16-papers-chen-zhuan.pdf))
5. *UKSM: Swift Memory Deduplication via Hierarchical and Adaptive Memory Region Distilling*----FAST'18 ([link](https://www.usenix.org/system/files/conference/fast18/fast18-xia.pdf)) [summary](https://yzr95924.github.io/paper_summary/UKSM-FAST'18.html)
6. *Remap-SSD: Safely and Efficiently Exploiting SSD Address Remapping to Eliminate Duplicate Writes*----FAST'21 ([link](https://www.usenix.org/system/files/fast21-zhou.pdf))
7. *Memory Deduplication for Serverless Computing with Medes*----EuroSys'22 ([link](https://dl.acm.org/doi/pdf/10.1145/3492321.3524272))
8. *On the Effectiveness of Same-Domain Memory Deduplication*----EuroSec'22 ([link](https://download.vusec.net/papers/dedupestreturns_eurosec22.pdf))
9. *Dedup-for-Speed: Storing Duplications in Fast Programming Mode for Enhanced Read Performance*----SYSTOR'22 ([link](https://dl.acm.org/doi/pdf/10.1145/3534056.3534937))

### Data Chunking
1. *A Framework for Analyzing the Improving Content-Based Chunking Algorithms*----HP Technique Report'05 ([link](https://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.394.569&rep=rep1&type=pdf))
2. *Multi-Level Comparison of Data Deduplication in a Backup Scenario*----SYSTOR'09 ([link](https://dl.acm.org/doi/abs/10.1145/1534530.1534541))
3. *Frequency Based Chunking for Data De-Duplication*----MASCOTS'10 ([link](https://d1wqtxts1xzle7.cloudfront.net/32669314/PSFBC-libre.pdf?1391107979=&response-content-disposition=inline%3B+filename%3DPSFBC.pdf&Expires=1663993649&Signature=FTBTHVpEJbisnhRMvnK9OAIAU0rwVDIPrmVgSjOR5sNurF-EZSBabJ9UAqt9STj1ZlTS6pUMXTwvGCeMRbU2XvQP20VaGwlVVfEEgvFbGV~OFPlK7zVLEFkWZTUvVvEV~mNMYaHvdDNMferbqBtKDhv6cM~tXZJwndFN0YVAvX-~AhOyZhqdkBCQl7RKC6A3bp9sVruT8iI4FAyHsXYAVlx8NASeKkgk2-CtrnPncy7s4hUFZbf99APPIQpbvSMQiIp7vq1MasXjZS-0l51veeFYyMuzQJMAuT4nkbI5wPDPQwFLs2ZeM4ywa4DemLMO82fCyUO7AmYAgbbuQBXj~w__&Key-Pair-Id=APKAJLOHF5GGSLRBV4ZA)) [summary](https://yzr95924.github.io/paper_summary/FrequencyBasedChunking-MASCOTS'10.html)
4. *Bimodal Content Defined Chunking for Backup Streams*----FAST'10 ([link](https://www.usenix.org/legacy/event/fast10/tech/full_papers/kruus.pdf))
5. *MUCH: Multi-threaded Content-Based File Chunking*----TC'15 ([link](https://oslab.kaist.ac.kr/wp-content/uploads/esos_files/publication/conferences/international/MUCH_Multithreaded_Content_Based_File_Chunking.pdf))
6. *FastCDC: a Fast and Efficient Content-Defined Chunking Approach for Data Deduplication*----USENIX ATC'16 ([link](https://www.usenix.org/system/files/conference/atc16/atc16-paper-xia.pdf)) [summary](https://yzr95924.github.io/paper_summary/FastCDC-ATC'16.html)
7. *SS-CDC: A Two-stage Parallel Content-Defined Chunking for Deduplicating Backup Storage*----SYSTOR'19 ([link]( http://ranger.uta.edu/~sjiang/pubs/papers/ni19-ss-cdc.pdf )) [summary](https://yzr95924.github.io/paper_summary/SSCDC-SYSTOR'19.html)
8. *RapidCDC: Leveraging Duplicate Locality to Accelerate Chunking in CDC-based Deduplication Systems*----SoCC'19 ([link](http://ranger.uta.edu/~sjiang/pubs/papers/ni19-rapidcdc.pdf)) [summary](https://yzr95924.github.io/paper_summary/RapidCDC-SoCC'19.html)

### Cache Deduplication

1. *PLC-cache: Endurable SSD cache for deduplication-based primary storage*----MSST'14 ([link](https://ieeexplore.ieee.org/abstract/document/6855536))
2. *Nitro: A Capacity-Optimized SSD Cache for Primary Storage*----USENIX ATC'14 ([link](https://www.usenix.org/system/files/conference/atc14/atc14-paper-li_cheng_nitro.pdf))
3. *CDAC: Content-Driven Deduplication-Aware Storage Cache*----MSST'19 ([link](http://storageconference.us/2019/Research/CDAC.pdf))
4. *Austere Flash Caching with Deduplication and Compression*----USENIX ATC'20 ([link](https://www.usenix.org/system/files/atc20-wang-qiuping.pdf))

### Garbage Collection

1. *Memory Efficient Sanitization of a Deduplicated Storage System*----FAST'13 ([link](https://www.usenix.org/system/files/conference/fast13/fast13-final100_0.pdf)) [summary](https://yzr95924.github.io/paper_summary/MemorySanitization-FAST'13.html)
2. Concurrent Deletion in a Distributed Content-addressable Storage System with Global Deduplication----FAST'13 ([link](https://www.usenix.org/system/files/conference/fast13/fast13-final91.pdf))
3. *Accelerating Restore and Garbage Collection in Deduplication-based Backup System via Exploiting Historical Information*----USENIX ATC'14 ([link](https://pdfs.semanticscholar.org/9b8d/a007a6801c9f96784dc7bc839794cb0db3ad.pdf)) [summary]( https://yzr95924.github.io/paper_summary/AcceleratingRestore-ATC'14.html )
4. The Logic of Physical Garbage Collection in Deduplicating Storage----FAST'17 ([link](https://www.usenix.org/system/files/conference/fast17/fast17-douglis.pdf))

### Network Deduplication

1. *EF-Dedup: Enabling Collaborative Data Deduplication at the Network Edge*----ICDCS'19 ([link](https://pdfs.semanticscholar.org/0f50/70cb39879fdcb3214e4068e3fa04f24e5ae6.pdf))

### Distributed Deduplication

1. *Even Data Placement for Load Balance in Reliable Distributed Deduplication Storage Systems*--IWQoS'15 ([link](https://www.cse.cuhk.edu.hk/~pclee/www/pubs/iwqos15edp.pdf)) [summary](https://yzr95924.github.io/paper_summary/EDP-IWQoS'15.html)
2. *Probabilistic Deduplication for Cluster-Based Storage Systems*----SoCC'12 ([link](https://dl.acm.org/citation.cfm?id=2391246)) [summary]( https://yzr95924.github.io/paper_summary/Produck-SoCC'12.html )
3. *A Scalable Inline Cluster Deduplication Framework for Big Data Protection*----Middleware'12 ([link](https://hal.inria.fr/hal-01555548/document))
4. *Tradeoffs in Scalable Data Routing for Deduplication Clusters*----FAST'11 ([link](https://www.usenix.org/legacy/events/fast11/tech/full_papers/Dong.pdf)) [summary]( https://yzr95924.github.io/paper_summary/TradeoffDataRouting-FAST'11.html )
5. *Cluster and Single-Node Analysis of Long-Term Deduplication Patterns*----ACM TOS'18 ([link](https://dl.acm.org/doi/pdf/10.1145/3183890)) [summary](https://yzr95924.github.io/paper_summary/ClusterSingle-ToS'18.html)
6. *Decentralized Deduplication in SAN Cluster File Systems*----USENIX ATC'09 ([link](https://static.usenix.org/events/usenix09/tech/full_papers/clements/clements.pdf))
7. *HYDRAstore: A Scalable Secondary Storage*----FAST'09 ([link](http://9livesdata.com/wp-content/uploads/2017/04/HYDRAstor-A-Scalable-Secondary-Storage-1.pdf))
8. *GoSeed: Generating an Optimal Seeding Plan for Deduplicated Storage*----FAST'20 ([link](https://www.usenix.org/system/files/fast20-nachman.pdf))
9. *The what, The from, and The to: The Migration Games in Deduplicated Systems*----FAST'22 ([link](https://www.usenix.org/system/files/fast22-kisous.pdf)) [summary](https://yzr95924.github.io/paper_summary/MigrationGame-FAST'22.html)

## Erasure Coding && RAID

### Erasure Coding Basics

1. *Network Coding for Distributed Storage System*----TIT'09
2. *A Performance Evaluation and Examination of Open-Source Erasure Coding Libraries for Storage*----FAST'09
3. *Erasure Coding for Cloud Storage Systems: A Survey*----By Jun Li in 2013

### Improve Data Recovery
1. *CORE: Augmenting Regenerating-Coding-Based Recovery for Single and Concurrent Failures in Distributed Storage Systems*----MSST'13
2. *Degraded-First Scheduling for MapReduce in Erasure-Coded Storage Clusters*----DSN'14
3. *Repair Pipelining for Erasure-Coded Storage*----USENIX ATC'17
4. *A Tale of Two Erasure Codes in HDFS*----FAST'15
5. *On the Speedup of Single-Disk Failure Recovery in XOR-Coded Storage Systems: Theory and Practice*----MSST'12
6. *Rethinking Erasure Codes for Cloud File Systems: Minimizing I/O for Recovery and Degraded Reads*----FAST'12
7. *Lazy Means Smart: Reducing Repair Bandwidth Costs in Erasure-coded Distributed Storage*----SYSTOR'14
8. *Enabling Efficient and Reliable Transition from Replication to Erasure Coding for Clustered File System*----DSN'15
9. *Reconsidering Single Failure Recovery in Clustered File Systems*----DSN'16 [summary](https://yzr95924.github.io/paper_summary/CAR-DSN'16.html)
10. *RAFI: Risk-Aware Failure Identification to Improve the RAS in Erasure-coded Data Center*----USENIX ATC'18
11. *Partial-Parallel-Repair (PPR): A Distributed Technique for Repairing Erasure Coded Storage*----EuroSys'16

### EC Update Issue
1. *Cross-Rack-Aware Updates in Erasure-Coded Data Centers*----ICPP'18
2. *PARIX: Speculative Partial Writes in Erasure-Coded Systems*----USENIX ATC'17

### EC Framework
1. *OpenEC: Toward Unified and Configurable Erasure Coding Management in Distributed Storage Systems*----FAST'19

### New EC code
1. *CodePlugin: Plugging Deduplication into Erasure Coding for Cloud Storage*----HotCloud'15
2. *Double Regenerating Codes for Hierarchical Data Centers*----ISIT'16
3. *Pyramid codes: Flexible schemes to trade space for access efficiency in reliable data storage systems*----ACM TOS'13
4. *Having Your Cake and Eating It Too: Jointly Optimal Erasure Codes for I/O, Storage and Network-bandwidth*----FAST'15
5. *Opening the Chrysalis: On the Real Repair Performance of MSR Codes*----FAST'16
6. *NCCloud: A Network-Coding-Based Storage System in a Cloud-of-Clouds*----FAST'12
7. *Erasure Coding in Windows Azure Storage*----USENIX ATC'12
8. *XORing Elephants: Novel Erasure Codes for Big Data*----VLDB'13
9. *Clay Codes: Moulding MDS Codes to Yield an MSR Code*----FAST'18
10. *Alpha Entanglement Codes: Practical Erasure Codes to Archive Data in Unreliable Environments*----DSN'18 [summary](https://yzr95924.github.io/paper_summary/Alpha-Entanglement-Codes-DSN'18.html)
11. *On Fault Tolerance, Locality, and Optimality in Locally Repairable Codes*----USENIX ATC'18
12. *Parallelism-Aware Locally Repairable Code for Distributed Storage Systems*----ICDCS'18
13. *Beehive: Erasure Codes for Fixing Multiple Failures in Distributed Storage Systems*----HotStorage'15
14. *Pipelined regeneration with Regenerating Codes for Distributed Storage Systems*----NetCod'11
15. *Cooperative Pipelined Regeneration in Distribution Storage Systems*----INFOCOM'14
16. *Zebra: Demand-aware Erasure Coding for Distributed Storage Systems*----IWQoS'16
17. *On Data Parallelism of Erasure Coding in Distributed Storage Systems*----ICDCS'17

### EC System
1. *Giza: Erasure Coding Objects across Global Data Centers*----USENIX ATC'17
2. *EC-Store: Bridging the Gap Between Storage and Latency in Distributed Erasure Coded Systems*----ICDCS'18
3. *Latency Reduction and Load Balancing in Coded Storage Systems*----SoCC'17

### RAID

1. *RAID+: Deterministic and Balanced Data Distribution for Large Disk Enclosures*----FAST'21 ([link](https://www.usenix.org/system/files/conference/fast18/fast18-zhang.pdf))
2. *FusionRAID: Achieving Consistent Low Latency for Commodity SSD Arrays*----FAST'22 ([link](https://www.usenix.org/system/files/fast21-jiang.pdf))

## Security

### Survey
1. *A Survey on Systems Security Metrics*----ACM Computing Surveys'16

### Secret Sharing
1. *How to Best Share a Big Secret*----SYSTOR'18 ([link](http://www.systor.org/2018/pdf/systor18-24.pdf)) [summary](https://yzr95924.github.io/paper_summary/ShareBigSecret-SYSTOR'18.html)
2. *AONT-RS: Blending Security and Performance in Dispersed Storage Systems*----FAST'11
3. *Splinter: Practical Private Queries on Public Data*----NSDI'17

### Data Encryption

1. *Efficient Homophonic Coding*----TIT'99 ([link](https://boris.ryabko.net/Ry-Fion.pdf))
2. *How Far Can we Go Beyond Linear Cryptanalysis?*----AsiaCRYPTO'04 ([link](https://link.springer.com/content/pdf/10.1007/978-3-540-30539-2_31.pdf))
3. *CryptDB: Protecting Confidentiality with Encrypted Query Processing*----SOSP'11 ([link](https://dspace.mit.edu/bitstream/handle/1721.1/74107/cryptdb-sosp11.pdf?sequence=1&isAllowed=y))
4. *Dark Clouds on the Horizon: Using Cloud Storage as Attack Vector and Online Slack Space*----USENIX Security'11 ([link](https://www.usenix.org/legacy/event/sec11/tech/full_papers/Mulazzani.pdf))
5. *RAPPOR: Randomized Aggregable Privacy-Preserving Ordinal Response*----CCS'14 ([link](https://uvammm.github.io/s19/docs/rappor.pdf))
6. *Frequency-Hiding Order-Preserving Encryption*----CCS'15 ([link](https://dl.acm.org/doi/10.1145/2810103.2813629))
7. *Inference Attacks on Property-Preserving Encrypted Databases*----CCS'15 ([link](https://www.microsoft.com/en-us/research/wp-content/uploads/2017/02/edb.pdf))
8. *A Note on the Optimality of Frequency Analysis vs. lp-Optimization*----IACR'15 ([link](https://eprint.iacr.org/2015/1158.pdf))
9. *Oblivious RAM as a Substrate for Cloud Storage - The Leakage Challenge Ahead*----CCSW'16 ([link](https://dl.acm.org/citation.cfm?id=2996430)) [summary](https://yzr95924.github.io/paper_summary/ORAM-CCSW'16.html)
10. *Oblivious RAM: A Dissection and Experimental Evaluation*---VLDB'16 ([link](http://www.vldb.org/pvldb/vol9/p1113-chang.pdf))
11. *MiniCrypt: Reconciling Encryption and Compression for Big Data Stores*----EuroSys'17 ([link](https://dl.acm.org/doi/pdf/10.1145/3064176.3064184))
12. *Splinter: Practical Private Queries on Public Data*----NSDI'17 ([link](https://www.usenix.org/system/files/conference/nsdi17/nsdi17-wang-frank.pdf))
13. *Frequency-smoothing Encryption: Preventing Snapshot Attacks on Deterministically Encrypted Data*----IACR'17 ([link](https://eprint.iacr.org/2017/1068.pdf)) [summary](https://yzr95924.github.io/paper_summary/FrequencySmoothing-ICAR'17.html)
14. *The Overhead of Confidentiality and Client-side Encryption in Cloud Storage Systems*----UCC'19 ([link](https://www.ida.liu.se/~nikca89/papers/cloud-eric-cse-A.pdf)) [summary](https://yzr95924.github.io/paper_summary/OverheadConfidentiality-UCC'19.html)
15. *PRO-ORAM: Practical Read-Only Oblivious RAM*----RAID'19 ([link](https://www.usenix.org/system/files/raid2019-tople.pdf))
16. *Quantifying Information Leakage of Deterministic Encryption*----CCSW'19 ([link]( http://users.cs.fiu.edu/~mjura011/documents/2019_CCSW_Quantifying_Information_Leakage_of_Deterministic_Encryption )) [summary](https://yzr95924.github.io/paper_summary/QuantifyingInformationLeakage-CCSW'19.html)
17. *Pancake: Frequency Smoothing for Encrypted Data Stores*----USENIX Security'20 ([link](https://www.usenix.org/system/files/sec20-grubbs.pdf))
18. *Hiding the Lengths of Encrypted Message via Gaussian Padding*----CCS'21 ([link](https://dl.acm.org/doi/pdf/10.1145/3460120.3484590))
19. *On Fingerprinting Attacks and Length-Hiding Encryption*----CT-RSA'22 ([link]())
20. *Rethinking Block Storage Encryption with Virtual Disks*----HotStorage'22 ([link](https://dl.acm.org/doi/pdf/10.1145/3538643.3539748)) [summary](https://yzr95924.github.io/paper_summary/BlockEnc-HotStorage'22.html)

### Secure Deletion

1. *Secure Overlay Cloud Storage with Access Control and Assured Deletion*----TDSC'12 ([link](https://www.cse.cuhk.edu.hk/~pclee/www/pubs/tdsc12_tech.pdf)) [summary]( https://yzr95924.github.io/paper_summary/FADE-TDSC'12.html )

### Differential Privacy

1. *Differential Privacy*----ICALP'06 ([link](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/dwork.pdf))
2. *Calibrating Noise to Sensitivity in Private Data Analysis*----TCC'06 ([link](https://www.microsoft.com/en-us/research/wp-content/uploads/2006/03/dmns06.pdf))
3. *Differentially Private Access Patterns for Searchable Symmetric Encryption*----INFOCOM'18 ([link](https://reitermk.github.io/papers/2018/INFOCOM.pdf)) [summary](https://yzr95924.github.io/paper_summary/DifferentialPrivacy-INFOCOM'18.html)
4. *Privacy at Scale: Local Differential Privacy in Practice*----SIGMOD'18 ([link](http://dimacs.rutgers.edu/~graham/pubs/papers/ldptutorial.pdf))

### SGX Technique
1. *Graphene-SGX: A Practical Library OS for Unmodified Applications on SGX*----USENIX ATC'17 ([link](https://www.usenix.org/system/files/conference/atc17/atc17-tsai.pdf))
2. *Intel SGX Explained*----IACR'16 ([link]( https://eprint.iacr.org/2016/086.pdf ))
3. *OpenSGX: An Open Platform for SGX Research*----NDSS'16 ([link](http://ina.kaist.ac.kr/~dongsuh/paper/opensgx.pdf))
4. *SCONE: Secure Linux Containers with Intel SGX*----OSDI'16 ([link](https://www.usenix.org/system/files/conference/osdi16/osdi16-arnautov.pdf))
5. *Varys: Protecting SGX Enclaves From Practical Side-Channel Attacks*---USENIX ATC'18 ([link](https://www.usenix.org/system/files/conference/atc18/atc18-oleksenko.pdf))
6. *sgx-perf: A Performance Analysis Tool for Intel SGX Enclaves*----Middleware'18 ([link](https://www.ibr.cs.tu-bs.de/users/weichbr/papers/middleware2018.pdf)) [summary]( https://yzr95924.github.io/paper_summary/SGXPerf-Middleware'18.html )
7. *TaLoS: Secure and Transparent TLS Termination inside SGX Enclaves*----arxiv'17 ([link](https://www.doc.ic.ac.uk/~fkelbert/papers/talos17.pdf)) [summary](https://yzr95924.github.io/paper_summary/talos-arxiv'17.html)
8. *Switchless Calls Made Practical in Intel SGX*----SysTex'18 ([link](https://dl.acm.org/doi/pdf/10.1145/3268935.3268942)) [summary](https://yzr95924.github.io/paper_summary/SwitchLess-SysTEX'18.html)
9. *Regaining Lost Seconds: Efficient Page Preloading for SGX Enclaves*----Middleware'20 ([link](https://dl.acm.org/doi/pdf/10.1145/3423211.3425673))
10. *Everything You Should Know About Intel SGX Performance on Virtualized Systems*----Sigmeterics'19 ([link](https://dl.acm.org/doi/pdf/10.1145/3322205.3311076)) [summary](https://yzr95924.github.io/paper_summary/SGXPerformance-SIGMETRICS'19.html)
11. *A Comparison Study of Intel SGX and AMD Memory Encryption Technology*---HASP'18 ([link](https://dl.acm.org/doi/abs/10.1145/3214292.3214301))
12. *SGXoMeter: Open and Modular Benchmarking for Intel SGX*----EuroSec'21 ([link](https://www.ibr.cs.tu-bs.de/users/mahhouk/papers/eurosec2021.pdf))
13. *Foreshadow: Extracting the Keys to the Intel SGX Kingdom with Transient Out-of-Order Execution*----USENIX Security'18 ([link](https://www.usenix.org/system/files/conference/usenixsecurity18/sec18-van_bulck.pdf))

### SGX Storage

1. *NEXUS: Practical and Secure Access Control on Untrusted Storage Platforms using Client-side SGX*----DSN'19 ([link](https://people.cs.pitt.edu/~adamlee/pubs/2019/djoko2019dsn-nexus.pdf))
2. *Securing the Storage Data Path with SGX Enclaves*----arxiv'18 ([link](https://arxiv.org/abs/1806.10883)) [summary](https://yzr95924.github.io/paper_summary/StorageDataPathSGX-arxiv.html)
3. *EnclaveDB: A Secure Database using SGX*----S&P'18 ([link](https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=8418608))
4. *Isolating Operating System Components with Intel SGX*----SysTEX'16 ([link](https://faui1-files.cs.fau.de/filepool/projects/sgx-kernel/sgx-kernel.pdf))
5. *SPEICHER: Securing LSM-based Key-Value Stores using Shielded Execution*----FAST'19 ([link](https://www.usenix.org/system/files/fast19-bailleu.pdf)) [summary](https://yzr95924.github.io/paper_summary/SPEICHER-FAST'19.html)
6. *ShieldStore: Shielded In-memory Key-Value Storage with SGX*----EuroSys'19 ([link]( http://calab.kaist.ac.kr:8080/~jhuh/papers/kim_eurosys19_shieldst.pdf )) [summary](https://yzr95924.github.io/paper_summary/ShieldStore-EuroSys'19.html)
7. SeGShare: Secure Group File Sharing in the Cloud using Enclaves----DSN'20 ([link](http://www.fkerschbaum.org/dsn20.pdf)) [summary](https://yzr95924.github.io/paper_summary/SeGShare-DSN'20.html)
8. *DISKSHIELD: A Data Tamper-Resistant Storage for Intel SGX*----AsiaCCS'20 ([link](https://dl.acm.org/doi/pdf/10.1145/3320269.3384717))
9. *SPEED: Accelerating Enclave Applications via Secure Deduplication*----ICDCS'19 ([link](https://conferences.computer.org/icdcs/2019/pdfs/ICDCS2019-49XpIlu3rRtYi2T0qVYnNX/5DGHpUvuZKbyIr6VRJc0zW/5PfoKBVnBKUPCcy8ruoayx.pdf)) [summary](https://yzr95924.github.io/paper_summary/SPEED-ICDCS'19.html)
12. *Secure In-memory Key-Value Storage with SGX*----SoCC'18
13. *EnclaveCache: A Secure and Scalable Key-value Cache in Multi-tenant Clouds using Intel SGX*----Middleware'19 ([link](https://dl.acm.org/doi/pdf/10.1145/3361525.3361533)) [summary](https://yzr95924.github.io/paper_summary/EnclaveCache-Middleware'19.html)
12. *Building enclave-native storage engines for practical encrypted databases*----VLDB'21 ([link]([www.vldb.org/pvldb/vol14/p1019-sun.pdf](http://www.vldb.org/pvldb/vol14/p1019-sun.pdf))) 
13. *Aria: Tolerating Skewed Workloads in Secure In-memory Key-value Stores*----ICDE'21 ([link](http://storage.cs.tsinghua.edu.cn/papers/icde21-aria.pdf))

### Network Security

1. *A Privacy-Preserving Defense Mechanism Against Request Forgery Attacks*----TrustCom'11 ([link](https://www.cse.cuhk.edu.hk/~pclee/www/pubs/trustcom11.pdf)) [summary]( https://yzr95924.github.io/paper_summary/DeRef-TrustCom'11.html )

## General Storage
### Cloud Storage System
1. *MapReduce: Simplified Data Processing on Large Clusters*----OSDI'04 ([link](https://static.googleusercontent.com/media/research.google.com/en//archive/mapreduce-osdi04.pdf))
2. *CRUSH: Controlled, Scalable, Decentralized Placement of Replicated Data*----SC'06 ([link](https://ceph.com/assets/pdfs/weil-crush-sc06.pdf))
3. *RADOS: A Scalable, Reliable Storage Service for Petabyte-scale Storage Clusters*----PDSW'07 ([link](https://ceph.com/assets/pdfs/weil-rados-pdsw07.pdf))
4. *Cumulus: Filesystem Backup to the Cloud*----FAST'09 ([link](https://www.usenix.org/legacy/event/fast09/tech/full_papers/vrable/vrable.pdf)) [summary](https://yzr95924.github.io/paper_summary/Cumulus-FAST'09.html)
5. *RACS: A Case for Cloud Storage Diversity*----SoCC'10 ([link](http://pubs.0xff.co/papers/racs-socc.pdf))
6. *The Hadoop Distributed File System*----MSST'10 ([link](http://storageconference.us/2010/Papers/MSST/Shvachko.pdf)) [summary](https://yzr95924.github.io/paper_summary/HDFS-MSST'10.html)
7. *SPANStore: Cost-Effective Geo-Replicated Storage Spanning Multiple Cloud Services*----SOSP'13 ([link](https://dl.acm.org/doi/pdf/10.1145/2517349.2522730)) [summary](https://yzr95924.github.io/paper_summary/SPANStore-SOSP'13.html)
8. *A Day Late and a Dollar Short: The Case for Research on Cloud Billing Systems*----HotCloud'14 ([link](https://rist.tech.cornell.edu/papers/billing.pdf))
9. *CosTLO: Cost-Effective Redundancy for Lower Latency Variance on Cloud Storage Service*----NSDI'15 ([link](https://www.usenix.org/system/files/conference/nsdi15/nsdi15-paper-wu.pdf))
10. *Kurma: Secure Geo-Distributed Multi-Cloud Storage Gateways*----SYSTOR'19 ([link](https://www.fsl.cs.sunysb.edu/docs/nfs4perf/kurma-systor19.pdf)) [summary](https://yzr95924.github.io/paper_summary/Kurma-SYSTOR'19.html)
11. *Duplicacy: A New Generation of Cloud Backup Tool Based on Lock-Free Deduplication*----TCC'20 ([link](https://github.com/gilbertchen/duplicacy/blob/master/duplicacy_paper.pdf)) [summary](https://yzr95924.github.io/paper_summary/Duplicacy-ToCC'20.html)

### Consensus

1. *In Search of an Understandable Consensus Algorithm*----USENIX ATC'14 ([link](https://raft.github.io/raft.pdf))

### Cache

1. *TinyLFU: A Highly Efficient Cache Admission Policy*----ACM TOS'17 ([link](https://arxiv.org/pdf/1512.00727.pdf))
2. *Hyperbolic Caching: Flexible Caching for Web Applications*----USENIX ATC'17 ([link](https://www.cs.princeton.edu/~mfreed/docs/hyperbolic-atc17.pdf))
3. *It’s Time to Revisit LRU vs. FIFO*----HotStorage'20 ([link](https://www.usenix.org/system/files/hotstorage20_paper_eytan.pdf)) [summary](https://yzr95924.github.io/paper_summary/Cache-HotStorage'20.html) [trace](http://iotta.snia.org/traces/key-value)
4. *The CacheLib Caching Engine: Design and Experiences at Scale*----OSDI'20 ([link](https://www.usenix.org/system/files/osdi20-berg.pdf))
5. *Unifying the Data Center Caching Layer — Feasible? Profitable?*----HotStorage'21 ([link](https://dl.acm.org/doi/pdf/10.1145/3465332.3470884))
6. *Learning Cache Replacement with Cacheus*----FAST'21 ([link](https://www.usenix.org/system/files/fast21-rodriguez.pdf))
7. *Kangaroo: Caching Billions of Tiny Objects on Flash*----SOSP'21 ([link](https://jasony.me/publications/sosp21-kangaroo.pdf))
8. *Segcache: a Memory-efficient and Scalable In-memory Key-value Cache for Small Objects*----NSDI'21 ([link](https://jasony.me/publications/nsdi21-segcache.pdf))


### Hash
1. *An Analysis of Compare-by-Hash*----HotOS'03 ([link](http://www.cs.utah.edu/~shanth/stuff/research/dup_elim/hash_cmp.pdf))
2. *On-the-Fly Verification of Rateless Erasure Codes for Efficient Content Distribution*----S&P'04 ([link](https://pdos.csail.mit.edu/papers/otfvec/paper.pdf))
3. *Compare-by-Hash: A Reasoned Analysis*----USENIX ATC'06 ([link](https://www.usenix.org/legacy/event/usenix06/tech/full_papers/black/black.pdf)) [summary](https://yzr95924.github.io/paper_summary/CompareByHash-ATC'06.html)
4. *Don’t Thrash: How to Cache your Hash on Flash*----HotStorage'11 ([link](https://www.usenix.org/legacy/events/hotstorage11/tech/final_files/Bender.pdf))
5. *Algorithmic Improvements for Fast Concurrent Cuckoo Hashing*----EuroSys'14 ([link](https://www.cs.princeton.edu/~mfreed/docs/cuckoo-eurosys14.pdf))

### Lock-free storage
1. *A Lock-Free, Cache-Efficient Multi-Core Synchronization Mechanism for Line-Rate Network Traffic Monitoring*----IPDPS'10 ([link](https://www.cse.cuhk.edu.hk/~pclee/www/pubs/ipdps10.pdf))
2. *Lock-Free Collaboration Support for Cloud Storage Services with Operation Inference and Transformation*----FAST'20 ([link](https://www.usenix.org/system/files/fast20-chen_jian.pdf))

### Block storage (SSD, NVMe)

1. *ZNS: Avoiding the Block Interface Tax for Flash-based SSDs*----USENIX ATC'21 ([link](https://www.usenix.org/system/files/atc21-bjorling.pdf)) [code](https://github.com/westerndigitalcorporation/zenfs)
1. *ZNS+: Advanced Zoned Namespace Interface for Supporting In-Storage Zone Compaction*----OSDI'21 ([link](https://www.usenix.org/system/files/osdi21-han.pdf))
1. *The CASE of FEMU: Cheap, Accurate, Scalable and Extensible Flash Emulator*----FAST'18 ([link](https://www.usenix.org/system/files/conference/fast18/fast18-li.pdf)) [summary](https://yzr95924.github.io/paper_summary/FEMU-FAST'18.html)
1. *From blocks to rocks: a natural extension of zoned namespaces*----HotStorage'21 ([link](https://dl.acm.org/doi/pdf/10.1145/3465332.3470870))
1. *Don’t Be a Blockhead: Zoned Namespaces Make Work on Conventional SSDs Obsolete*----HotOS'21 ([link](https://sigops.org/s/conferences/hotos/2021/papers/hotos21-s07-stavrinos.pdf)) [summary](https://yzr95924.github.io/paper_summary/BlockHead-HotOS'21.html)
1. Zone Append: A New Way of  Writing to Zoned Storage----Vault'20 ([link](https://www.usenix.org/system/files/vault20_slides_bjorling.pdf))
1. *What Systems Researchers Need to Know about NAND Flash*----HotStorage'13 ([link](https://www.usenix.org/system/files/conference/hotstorage13/hotstorage13-desnoyers.pdf))
1. *Caveat-Scriptor: Write Anywhere Shingled Disks*----HotStorage'15 ([link](https://www.usenix.org/system/files/conference/hotstorage15/hotstorage15-kadekodi.pdf))
1. *Improving the Reliability of Next Generation SSDs using WOM-v Codes*----FAST'22 ([link](https://www.usenix.org/system/files/fast22-jaffer.pdf))
1. *Fantastic SSD internals and how to learn and use them*----SYSTOR'22 ([link](https://dl.acm.org/doi/pdf/10.1145/3534056.3534940))
1. *Understanding Modern Storage APIs: A systematic study of libaio, SPDK, and io_uring*----SYSTOR'22 ([link](https://dl.acm.org/doi/pdf/10.1145/3534056.3534945))
1. *Understanding NVMe Zoned Namespace (ZNS) Flash SSD Storage Devices*----ArXiv ([link](https://arxiv.org/pdf/2206.01547.pdf))
1. *Compaction-Aware Zone Allocation for LSM based Key-Value Store on ZNS SSDs*----HotStorage'22 ([link](https://dl.acm.org/doi/pdf/10.1145/3538643.3539743))
1. *Lifetime-leveling LSM-tree Compaction for ZNS SSD*----HotStorage'22 ([link](https://dl.acm.org/doi/pdf/10.1145/3538643.3539741))
1. *What You Can't Forget: Exploiting Parallelism for Zoned Namespaces*----HotStorage'22 ([link](https://dl.acm.org/doi/pdf/10.1145/3538643.3539744))
1. *NVMe SSD Failures in the Field:  the Fail-Stop and the Fail-Slow*----USENIX ATC'22 ([link](https://www.usenix.org/system/files/atc22-lu.pdf))
1. *Offline and Online Algorithms for SSD Management*----ACM TOS'22 ([link](https://dl.acm.org/doi/pdf/10.1145/3491045))

### File system

1. *XFUSE: An Infrastructure for Running Filesystem Services in User Space*----USENIX ATC'21 ([link](https://www.usenix.org/system/files/atc21-huai.pdf))
2. *WineFS: a hugepage-aware file system for persistent memory that ages gracefully*----SOSP'21 ([link](https://www.cs.utexas.edu/~vijay/papers/winefs-sosp21.pdf))
3. *SplitFS: persistent-memory file system that reduces software overhead*----SOSP'19 ([link](https://www.cs.utexas.edu/~vijay/papers/sosp19-splitfs.pdf))
4. *LineFS: Efficient SmartNIC Offload of a Distributed File System with Pipeline Parallelism*----SOSP'21 ([link](https://dl.acm.org/doi/pdf/10.1145/3477132.3483565))
5. *EROFS: A Compression-friendly Readonly File System for Resource-scarce Devices*----USENIX ATC'19 ([link](https://www.usenix.org/system/files/atc19-gao.pdf))
5. *F2FS: A New File System for Flash Storage*----FAST'15 ([link](https://www.usenix.org/system/files/conference/fast15/fast15-paper-lee.pdf))
5. *How to Copy Files*----FAST'20 ([link](https://www.usenix.org/system/files/fast20-zhan.pdf))
5. *BetrFS: A Compleat File System for Commodity SSDs*----EuroSys'22 ([link](https://dl.acm.org/doi/pdf/10.1145/3492321.3519571))
5. *The Full Path to Full-Path Indexing*----FAST'18 ([link](https://www.usenix.org/system/files/conference/fast18/fast18-zhan.pdf))
5. *BetrFS: A Right-Optimized Write-Optimized File System*----FAST'15 ([link](https://www.usenix.org/system/files/conference/fast15/fast15-paper-jannen_william.pdf))
5. *Performance and Resource Utilization of FUSE User-Space File Systems*----ACM TOS'19 ([link](https://dl.acm.org/doi/10.1145/3310148))
11. *Filesystem Aging: It's more Usage than Fullness*----HotStorage'19 ([link](https://www.cs.unc.edu/~porter/pubs/hotstorage19-paper-conway.pdf))
12. *File Systems Fated for Senescence? Nonsense, Says Science!*----FAST'17 ([link](https://www.usenix.org/system/files/conference/fast17/fast17-conway.pdf))
12. *Survey of Distributed File System Design Choices*----ACM TOS'22 ([link](https://dl.acm.org/doi/pdf/10.1145/3465405))

### Persistent Memory

1. *SLM-DB: Single-Level Key-Value Store with Persistent Memory*----FAST'19 ([link](https://www.usenix.org/system/files/fast19-kaiyrakhmet.pdf)) [summary](https://yzr95924.github.io/paper_summary/SLMDB-FAST'19.html)
2. *Redesigning LSMs for Nonvolatile Memory with NoveLSM*----USENIX ATC'18 ([link](https://www.usenix.org/system/files/conference/atc18/atc18-kannan.pdf)) [summary](https://yzr95924.github.io/paper_summary/NoveLSM-ATC'18.html)

### Data Structure

1. *An Introduction to Be-trees and Write-Optimization*----USENIX Login'15 ([link](https://www.usenix.org/system/files/login/articles/login_oct15_05_bender.pdf)) [code](https://github.com/oscarlab/Be-Tree) 
1. *Building Workload-Independent Storage with VT-Trees*----FAST'13 ([link](https://www.usenix.org/system/files/conference/fast13/fast13-final165_0.pdf))

### Benchmark

1. *SDGen: Mimicking Datasets for Content Generation in Storage Benchmarks*----FAST'15 ([link](https://www.usenix.org/system/files/conference/fast15/fast15-paper-gracia-tinedo.pdf))

### I/O Optimizations

1. *PAIO: General, Portable I/O Optimizations With Minor Application Modifications*----FAST'22 ([link](https://www.usenix.org/system/files/fast22-macedo.pdf))
2. *zIO: Accelerating IO-Intensive Applications  with Transparent Zero-Copy IO*----OSDI'22 ([link](https://www.usenix.org/system/files/osdi22-stamler.pdf))
3. *XRP: In-Kernel Storage Functions with eBPF*----OSDI'22 ([link](https://www.usenix.org/system/files/osdi22-zhong_1.pdf))

### Deployed Systems

1. *The Google File System*----SOSP'03 ([link](https://dl.acm.org/doi/pdf/10.1145/945445.945450))
2. *Ceph: A Salable, High-Performance Distributed File System*----OSDI'06 ([link](https://www.ssrc.ucsc.edu/media/pubs/6ebbf2736ae06c66f1293b5e431082410f41f83f.pdf))
3. *Bigtable: A Distributed Storage System for Structured Data*----OSDI'06 ([link](https://dl.acm.org/doi/pdf/10.1145/1365815.1365816))
4. *Amazon DynamoDB: A Scalable, Predictably Performant, and Fully Managed NoSQL Database Service*----USENIX ATC'22 ([link](https://www.usenix.org/system/files/atc22-elhemali.pdf))
5. *CacheSack: Admission Optimization for Google  Datacenter Flash Caches*----USENIX ATC'22 ([link](https://www.usenix.org/system/files/atc22-yang-tzu-wei.pdf))
6. *From Luna to Solar: The Evolutions of the Compute-to-Storage Networks in Alibaba Cloud*----SIGCOMM'22 ([link](https://rmiao.github.io/assets/pdf/solar-sigcomm22.pdf))

### CXL

1. *Hello Bytes, Bye Blocks: PCIe Storage Meets Compute Express Link for Memory Expansion (CXL-SSD)*----HotStorage'22 ([link](https://dl.acm.org/doi/pdf/10.1145/3538643.3539745))

### Failures

1. *Metastable Failures in Distributed Systems*----HotOS'21 ([link](https://sigops.org/s/conferences/hotos/2021/papers/hotos21-s11-bronson.pdf))
2. *Metastable Failures in the Wild*----OSDI'22 ([link](https://www.usenix.org/system/files/osdi22-huang-lexiang.pdf))
