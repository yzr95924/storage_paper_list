---
typora-copy-images-to: ../paper_figure
---
Characteristics of Backup Workloads in Production Systems
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| FAST'12 | Workload Analysis |
[TOC]

## 1. Summary
### Motivation of this paper
- Motivation
There have been numerous studies over the past 30 years of file system characteristics.
> there has been little in the way of corresponding studies for **backup** systems.
> data backups are used to protect primary data.

- Goal
highlight the different requirements between **backup** and **primary** storage.
> backup filesystems have had to scale their throughput to meet storage growth.

### Backup analysis
- Data collection
The most practical way of conducting a large-scale study is to instead collect filesystem-level statistics and content metadata. (i.e., data about the data)
> 1. autosupport reports (storage usage, compression, file counts and ages, caching statistics and other metrics): limited  in detail but wide in deployment.
> 2. chunk-level metadata (chunk hash identifiers, sizes and location): contain great detail but are limited in deployment.


- Collecting content metadata
collect the file recipes (listing of chunk fingerprints) for each file and then collect the deduplicated chunk metadata from the storage containers, as well as sub-chunk fingerprints.
> get the information of sub-chunk: can be used to investigate deduplication rates at various chunk sizes smaller than the default 8KB.

- Trends across backup storage systems
Graph their primary workload results alongside its backup storage results.
> both a histogram (probability distribution) and a cumulative distribution function (CDF)

1. File size
For backup, this size distribution is about 3 orders of magnitude larger than for primary files.
> since it combines individual files together from the primary storage system into "tar-like" collections.
> larger files reduce the likelihood of whole-file deduplication but increase the *stream locality* within the system.

2. File and directory count
File and directory counts are typically much lower in backup workloads. 


3. File age
For backup workload, the median age is about 3 weeks.
> short retention periods lead to higher data churn.


- Effect of varying chunk size

1. Metadata overhead
Every chunk requires certain metadata to track its location
> the aggregate overhead scales inversely with chunk size.

It assumes a small fixed cost (30 bytes, a fingerprint, chunk length, and a small overhead for other metadata) 
> per physical chunk stored in the system
> per logical chunk in a file recipe

use a factor $f$ to report the reduction in deduplication effectiveness 
> $f$: the metadata size divided by the average chunk size.

the real deduplication $D^{'}$ includes metadata costs:
$$
D^{'} = \frac{L}{P+fP+fL}
$$
without metadata costs:
$$
D = \frac{L}{P}
$$
> Sometime the improvement in deduplication of small chunk size sufficiently compensates for the added per-chunk metadata.


### Implementation and Evaluation
- Evaluation
1. Cache efficiency

Writes: Using stream locality hints achieves good deduplication hit rates with caches.
> stream locality: container level


Reads: use cache to provide fast restores of data during disaster recovery.


## 2. Strength (Contributions of the paper)
1. analyze statistics from a broad set of 10,000+ production EMC Data Domain systems
> show the backup workload tends to have shorter-lived and larger files than primary storage.

2. uses a novel technique for extrapolating deduplication rates across a range of possible sizes.
> using a single chunk size to extrapolate deduplication at larger chunk sizes.

## 3. Weakness (Limitations of the paper)

## 4. Some Insights (Future work)
1. It mentions that the backup storage workloads are tied to the **applications**

> depends on the applications which generates them

Backup: individual files are typically combined into large units ("tar" file)

2. weekly "full" backups and daily "incremental" backups

Incremental backups: files are modified (a large portions in common with earlier versions)
Full backups: are likely to have many of their comprising files completely unmodified


3. Backup system

Windows 2000: entire files deduplication
Venti: fixed-block deduplication
LBFS: variable-sized chunks deduplication

4. Compression region

Combining unique chunks into "compression regions"
> aggregate new unique chunks into compression regions, which are compressed as a single unit (approximately 128KB before compression)
