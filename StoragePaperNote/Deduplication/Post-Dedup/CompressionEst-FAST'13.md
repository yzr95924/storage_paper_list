---
typora-copy-images-to: ../paper_figure
---
To Zip or Not to Zip: Effective Resource Usage for Real-Time Compression
---------------------------------------

|           Venue            |       Category       |
| :------------------------: | :------------------: |
| FAST'13 | Compression |
[TOC]

## 1. Summary
### Motivation of this paper

- motivation
  - adding compression on the data path consumes **scarce CPU** and **memory** resources on the storage system
    - real-time compression for block and file primary storage systems
    - it is advisable to avoid compressing what we refer to as "incompressible" data
      - standard LZ type compression algorithms incur higher performance overheads **when the data does not compression well**
      - ![image-20220526171832531](../paper_figure/image-20220526171832531.png)
- main problem
  - identifying **incompressible data** in an efficient manner, allowing systems to effectively utilize their limited resources
    - a macro-scale compression estimation for the whole data set (**offline**)
    - a micro-scale compressibility test for individual write operations (**online**)

### Compression Estimation/Test

- the macro-scale solution
  - for an entire volume or file system of a storage system
    - estimate the overall compression ratio with **accuracy guarantee**
  - the general framework
    - choose `m` random locations
    - compute an average of the compression ratio of these locations
  - location, contribution
    - real life implementations of compression algorithms are subject to **locality limits **(can use a chunk to define the locality)
      - don’t want to hold long back pointers
      - memory management, need to flush their buffers
    - define the contribution of a byte as **the compression ratio of its locality**
- the micro-scale solution
  - for a single write: 8KB, 16KB, 32KB, 128KB
    - recommend to zip or not to zip (has to be much faster than actual compression)
      - do not want to read the entire chunk, impossible to get guarantees
  - the heuristics method
    - collect **a set of basic indicators** about the chunk
      - from random samples from the chunk rather than the whole chunk
      - core-set size: the character set that makes up most of the data
      - byte-entropy
      - symbol-pairs distribution indicator (from random distribution)
    - sample: at most 2KB of data per write buffer
      - 16 consecutive bytes from up to 128 randomly chosen locations
    - define several thresholds to test the indicators

### Implementation and Evaluation

- implementation
  - the macro-scale solution: written in C, multi-threaded
- evaluation
  - compression ratios v.s. the number of samples
  - running time v.s. compression trade-off
    - compared with the prefix method and the full compression

## 2. Strength (Contributions of the paper)

- the macro-scale test provides a quick and accurate estimate for which data sets to compress
- the micro-scale test heuristics have proved critical in reducing resource consumption while maximizing compression for volumes containing a mix of compressible and incompressible data

## 3. Weakness (Limitations of the paper)

- is not general to other compression algos (e.g., LZ4, ZSTD)
- define the thresholds to find a good point for disabling compression is not clear
- evaluation is limited, no end-to-end system performance evaluation

## 4. Some Insights (Future work)

- a bit about compression techniques
  - this paper focuses on **Zlib** - a popular compression engine for (zip), combines:
    - **LZ compression**: pointers instead of repetitions
    - **Huffman encoding**: use shorter encoding to popular characters
- existing solutions for estimating compression ratios
  - by file extension
    - not always accurate, not always available
  - look at the actual data
    - scan and compress everything
    - look at a prefix of (a file or a chunk) and deduce about the rest
      - not guarantees on the outcome
      - good for compressible data - zero overhead

- put all together
  - when most is compressible
    - use prefix estimation
  - when significant percent is incompressible
    - use heuristics method
  - when most is incompressible
    - turn compression off
