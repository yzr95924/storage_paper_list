---
typora-copy-images-to: ../paper_figure
---
RapidCDC: Leveraging Duplicate Locality to Accelerate Chunking in CDC-based Deduplication Systems
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| SoCC'19 | Chunking |
[TOC]

## 1. Summary

### Motivation of this paper
- CDC is compute-intensive and time-consuming
  - a major performance bottleneck of the CDC based deduplication system.
  - Should make the I/O devices, such as hard disks and SSDs be the performance bottleneck

- Duplicate locality implication 
  - the improvement of chunking speed has not been considered at all.

- Main idea
  - Leverage **duplicate locality** to remove the need of byte-by-byte window rolling in the determination of chunk boundaries.
    - Duplicate locality: multiple duplicate chunks are likely to occur together.
    - the longer the sequence, the stronger the locality is.

### RapidCDC

- Quantitative analysis of duplicate locality
  - the layout of duplicate data (duplicate chunks are likely to stay together)
  - LQ sequence: deduplicatable chunks constitute a chunk sequence

use the number of contiguous deduplicatable chunks immediately following the first deduplicatable chunk to quantify the locality.
> the majority of duplicate chunks are in the LQ sequence.


- Design idea 
  - exploit the duplicate locality in the datasets to enable a chunking method which detects chunk boundaries *without a byte-by-byte window rolling*.
  - Allow a list of *next-chunk* sizes (**size list**) to be attached to a fingerprint
    - 2 bytes to record the chunk size 
    - simpler relationship chain 
      - chunk --> fingerprint --> the size of the next chunk (a list)
  - If the position is accepted, it can avoids rolling the window one byte at a time for thousands of times to reach the next chunk boundary.
    - If not accepted, it will try another next-chunk size in the size list of the duplicate chunk's fingerprint.
      - If still not accepted, it will reduce to original rolling window by byte-by-byte.

- Accepting suggested chunk boundaries
  - Trade-off between performance gain and risk of performance penalty.
  - FF (Fast-forwarding only): without further checking
  - FF + RWT (Rolling window test): compute the rolling window of the position
  - FF + MT (Marker Test): compared the last byte, need to record the last byte of a chunk
  - FF + RWT + FPT (Fingerprint test): also compute the fingerprint of the chunk, test whether the fingerprint currently exists.

- Maintaining list of next-chunk size 
  - LRU policy to update the list (an ordered list)
![image-20200914021803963](../paper_figure/image-20200914021803963.png)


### Implementation and Evaluation
- Datasets
  - Synthetic dataset
  - real-word dataset: Docker, Linux source code, Google-news
  - chunking hash functions: Robin and Gear (in FastCDC)

- Evaluation
  - Impact of modification count and distribution
  - Impact of minimum chunk sizes and hash functions 
  - Throughput of multi-threaded 
    - Consider a directory, each thread is responsible for the chunking of one file.

## 2. Strength (Contributions of the paper)

1. propose a new chunking algorithm which leverages the duplicate locality to accelerate the chunking performance.

2. an quantitative scheme to measure the duplicate locality.

## 3. Weakness (Limitations of the paper)

1. the idea is not novel

## 4. Some Insights (Future work)

1. For deduplication production system
  NetApp ONTAP system 
  Dell EMC Data Domain: 4KB, 8KB, 12KB

  LBFS: 2KB, 16KB, 64KB

2. The boundary-shift issue
  for insertion or deletion at the beginning of a store file.

CDC chunking:  a chunk boundary is determined at a byte offset which can satisfy a predefined chunking condition.

3. the difference in the hash function in chunking
the hash function used for chunk boundary detection is different from the one used for fingerprinting
> does not need to be collision-resistant
