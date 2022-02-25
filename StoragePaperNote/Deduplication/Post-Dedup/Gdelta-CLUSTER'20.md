---
typora-copy-images-to: ../paper_figure
---
Exploring the Potential of Fast Delta Encoding: Marching to a Higher Compression Ratio
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| Cluster'20 | Delta Compression |
[TOC]

## 1. Summary
### Motivation of this paper

- Motivation
  - delta compression is costly because of its time-consuming word-matching operations for **delta calculation**
  - existing delta encoding approaches
    - at a slow encoding speed, such as Xdelta and Zdelta
    - at a low compression ratio, such as Ddelta and Edelta
- Observation
  - the rolling hash bottleneck: **delta encoding** is time-consuming (a bottleneck of the delta compression-based systems)
  - the further compression opportunity

### Gdelta

- Design goals
  - accelerating the delta calculation process of the already-detected delta-compression candidates
  - change the hashing, indexing, and entropy encoding schemes in Gdelta
- Main idea
  - **using Gear hash instead of Adler32** to speed up the word-matching process in delta encoding
  - **using recently proposed fast compression tools** (zstd and FSE encoding) to improve the delta compression ratio without sacrificing the fast compressing speed
- Gear-based rolling hash
  - use Gear hash as a rolling hash for word-matching in delta encoding 
    - replace the time-consuming Adler32 in Xdelta
- Array-based indexing scheme
  - use the most-significant bits of fingerprint as the index of a hash array by the right-shift operation
    - reduce the hash collision
  - traditional hash table's memory cost is about 2-3 times more than the array-based index
    - the hash table needs to store additional data pointers and the offset information for each indexing unit
- Encoding and Decoding
  - the delta chunk is recorded by the "Copy" and "Insert" instructions in turn
    - the sliding window size: 16 bytes, 64-bits fp
- Container-based further compression
  - put several delta chunks altogether in a container with a size of 2 MiB of 4 MiB
    - reduces the metadata overheads for data compression
  - compress the section of "Copy" and "Insert" instructions **at the bit-level using FSE** and compress the data part in the "Insert" instructions at **both the world- and bit-level using zstd**

### Implementation and Evaluation

- Baseline: Xdelta and Zdelta
  - Edelta and Ddelta have a much lower compression ratio
- Datasets
  - ![image-20220116030809631](../paper_figure/image-20220116030809631.png)
  - first apply FastCDC to generate the data chunks and detects similar chunks with a super-feature based resemble detection method (FAST'12)
    - compute 12 features from each chunk, and group into three Super-features
    - two chunks are considered to be similar if they share one Super-feature or more
- Main results
  - Gear hash can achieve nearly the same hashing efficiency as Adler32
    - the hash speeds of Gear hash: 1740 MiB/s
    - the hash speeds of Adler32: 478 MiB/s
  - Array-based indexing achieves a much higher encoding speed as its simple design
  - Container-based further compression improves the compression ratio by 10%-90%

## 2. Strength (Contributions of the paper)

- Very simple design
  - speeds up the delta encoding/decoding by 2x-4x over the classic Xdelta and Zdelta
  - increases the compression ratio by about 10%-120%

## 3. Weakness (Limitations of the paper)

- Do not mention how container-based compression affects the restore performance

## 4. Some Insights (Future work)

- Background of lossless data reduction
  - there are mainly three technology roadmaps for lossless data reduction
    - general compression, delta compression, and data deduplication
  - delta compression bridges the gap in compression ratio (between data deduplication and general compression)
    - focuses on removing redundancy among similar files and chunks (increase the compression ratio) 
    - still a lot of redundant data **between the very similar but non-duplicate chunks/files**
    - ![image-20220116023316997](../paper_figure/image-20220116023316997.png)

- Delta compression (two steps)

  - Resemblance detection: **generate features** and then **search similar chunks** according to their features
  - Delta encoding: refers to the process of calculating the delta among the very similar chunks/files
    - determine the final compression ratio of delta compression

  
