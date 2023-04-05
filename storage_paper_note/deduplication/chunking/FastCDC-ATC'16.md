---
typora-copy-images-to: ../paper_figure
---
FastCDC: a Fast and Efficient Content-Defined Chunking Approach for Data Deduplication
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| ATC'16 | chunking |
[TOC]

## 1. Summary

### Motivation of this paper
- Motivation
  - Existing CDC-based chunking introduces heavy CPU overhead 
    - declare the chunk cut-points by computing and judging the rolling hashes of the data stream **byte-by-byte**.
    - Two parts: hashing and judging the cutpoints
  - By using Gear function, the bottleneck has shifted to the hash judgment stage.

### FastCDC

- Three key designs
  - Simplified but enhanced hash judgment
    - padding several zero bits into the mask 
  - Sub-minimum chunk cut-point skipping
    - enlarge the minimum chunk size to maximize the chunking speed 
  - Normalized chunking
    - normalizes the chunk size distribution to a small specified region
      - increase the deduplication ratio 
      - reduce the number of small-sized chunks (can combine with the cut-point skipping technique above to maximize the CDC speed while without sacrificing the deduplication ratio.)

- Gear hash function 
  - an array of 256 random 64-bit integers to map the values of the byte contents in the sliding window.
  - using only three operations (i.e., +, <<, and an array lookup)
    - enabling it to move quickly through the data content for the purpose of CDC.

![image-20200918215222896](../paper_figure/image-20200918215222896.png)
![image-20200918215434370](../paper_figure/image-20200918215434370.png)

- Optimizing hash judgement
  - Gear-based CDC employs the same conventional hash judgment used in the Rabin-based CDC
    - A certain number of the lowest bits of the fingerprint are used to declare the chunk cut-point.
  - FastCDC enlarges the sliding window size by padding a number of zero bits into the mask value
    - change the hash judgment statement
    - involve more bytes in the final hash judgment 
      - minimizing the probability of chunking position collision
  - simplifying the hash judgment to accelerate CDC
    - in Rabin: fp mod D == r
    - in FastCDC: fp & Mask == 0 --> !fp & Mask 
    - avoid the unnecessary comparison operation 

- Cut-point skipping
  - avoid the operations for hash calculation and judgment in the skipped region.
    - may reduce the deduplication ratio.
  - the cumulative distribution of chunk size in Rabin-based CDC (without the maximum and minimum chunk size requirements) follows **an exponential distribution**.

- Normalized chunking
  - solve the problem of decreased deduplication ratio facing the cut-point skipping approach.
  - After normalized chunking, there are almost no chunks of size smaller than the minimum chunk size 
  - By changing the number of '1' bits in FastCDC, the chunk-size distribution will be approximately normalized to a specific region (always larger than the minimum chunk size, instead of following the exponential distribution)
    - define two masks
    - more effective mask bits: increase chunk size
    - fewer effective mask bits: reduce chunk size
![image-20200918224123859](../paper_figure/image-20200918224123859.png)

- The whole algorithm

![image-20200919014910600](../paper_figure/image-20200919014910600.png)

### Implementation and Evaluation

- Evaluation standard
  - deduplication ratio
  - chunking speed
  - the average generated chunk size

- Compared with 
  - FastCDC
  - Gear-based
  - AE-based
  - Rabin-based

- Evaluation of optimizing hash judgement
- Evaluation of cut-point skipping
- Evaluation of normalized chunking
- Comprehensive evaluation of FastCDC

## 2. Strength (Contributions of the paper)
1. propose a new chunking algorithm with three new designs 
> enhanced hash judgment
> sub-minimum chunk cut-point skipping
> normalized chunking 


## 3. Weakness (Limitations of the paper)

1. the part of cut-point skipping is not clear

## 4. Some Insights (Future work)

1. The research direction in chunking algorithm
> algorithmic-oriented CDC optimizations 
> hardware-oriented CDC optimizations
