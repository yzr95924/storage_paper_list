---
typora-copy-images-to: ../paper_figure
---
DedupSearch: Two-Phase Deduplication Aware Keyword Search
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| FAST'22 | Post-Deduplication functionality |
[TOC]

## 1. Summary
### Motivation of this paper

- motivation
  - in deduplicated storage, it creates multiple logical pointers from different files and even users, to each physical chunk
    - this many-to-one relationship complicates many functionalities (e.g., caching, capacity planning, and support for QoS)
    - present an opportunity to rethink those functionalities to be **deduplication-aware** and **more efficient**
  - this paper aims to address the keyword search issue in deduplicated storage
- the main goal
  - focus on **offline search** of large, deduplicated storage systems for legal or analytics purposes
- why other approaches cannot work
  - their index size is proportional to **the logical size of the data** and consume a large fraction of storage capacity
  - not useful for binary strings or more complex keyword patterns (assume a delimiter set such as whitespace)
  - their data structures must be continually updated as new data is received

### DedupSearch

- naive approaches
  - opening each file and scanning its content for the specified keywords (**inefficient due to fragmentation and resulting random accesses**)
  - a given chunk may be read repeatedly from storage due to deduplication
- main idea
  - begin with a **physical phase** that performs a **physical scan** of the storage system and scans each chunk of data for the keywords
    - reading the data sequentially with large I/Os as well as reading each chunk of data only once
    - record the **exact match** of the keyword, if it is found, as well as the prefixes of suffixes of the keyword (**partial matches**) found at chunk boundaries
  - then, with a **logical phase** that performs a logical scan of the file system by traversing the chunk pointers that make up the files
    - instead of reading the actual data chunks
- challenges
  - most deduplication systems do not maintain "back pointers" from chunks to the file that contain them (<u>addressed by the logical phase</u>)
    - cannot associate keyword matches in a chunk with the corresponding file
  - keywords might be split **between adjacent chunks** in a file (<u>addressed by recording the partial matches</u>)
    - record the prefixes of the keyword that appear at the end of a chunk and suffixes that appear at the beginning of a chunk

- string-matching algorithm
  - use the Aho-Corasick string-match algorithm
    - a trie-based algorithm for matching multiple strings in a single scan of the input
  - construct a trie for the **reverse** dictionary to identify suffixes at the beginning of a chunk
- match result database
  - exact matches
    - chunk-result record
    - location-list record: only if the chunk contains more than one exact match
    - long location-list record
  - tiny substrings
    - keywords that begin or end with frequent letters in the alphabet might result in the allocation of numerous chunk-result record
    - tiny-result record 
      - only if the chunk does not contain any exact match nor a partial match
  - database organization
    - in-memory database: chunk-result index, location-list index
    - disk-based hash table: the tiny-result index
- generation of full research results
  - for each file in the system, the **file recipe** is read, and the fingerprints of its chunks are used to lookup result records in the database
    - collecting exact match and combining partial matches for each fingerprint
  - the logical phase can be parallelized to some extent
    - separate backups or files can be processed in parallel

### Implementation and Evaluation

- implementation
  - based on Destor: three restore thread
  - use Destor to ingest all the data
- evaluation
  - traces
    - Wikipedia backups, linux kernel versions, and Web server VM backups
    - linux versions ordered by version, major version, minor version, and patch
    - Wikipedia backups: archived twice a month since 2017, each snapshot is 1GiB and consists of a single archive file
  - experiments
    - DedupSearch performance
      - effect of deduplication ratio, chunk size, dictionary size, and keywords in the dictionary
    - DedupSearch data structures
      - index sizes, database accesses
    - DedupSearch overheads
      - physical phase, logical phase

## 2. Strength (Contributions of the paper)

- very strong experiments
- address the string search issue from the deduplication aspect (a new direction)
  - no previous work targets this issue

## 3. Weakness (Limitations of the paper)

- the scenario is limited 
  - is more appropriate when queries are **infrequent** and moderate latency is acceptable such as in legal discovery
- the main idea is very similar to DeduplicationGC-FAST'17, GoSeed-FAST'20
  - process the **post-deduplication data** **sequentially** along with an analysis phase **on the file recipes**

- lack the support of wildcards
  - since its prefix/suffix approach incur high overhead, it would be more challenging to support wildcards
    - attempting to match the chunk content starting at all possible offsets within the keyword

## 4. Some Insights (Future work)

- the concept from **near-storage processing**
  - the <u>storage system</u> supports certain computations to **reduce I/O traffic and memory usage**

- the restore process considered by it
  - parse the file recipe
  - looking up the chunk locations in the fingerprint index
  - reading their containers 