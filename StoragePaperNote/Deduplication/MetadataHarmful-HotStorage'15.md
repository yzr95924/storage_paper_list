---
typora-copy-images-to: ../paper_figure
---
Metadata Considered Harmful ... to Deduplication
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| HotStorage'15 | Deduplication Metadata |
[TOC]

## 1. Summary
### Motivation of this paper
The effectiveness of deduplication can vary dramatically depending on the data stored.

This paper shows that many file formats suffer from a fundamental design property that is **incompatible** with deduplication.
> intersperse metadata with data in ways that result in otherwise identical data being different.

Little work has been done to examine the impact of input data in deduplication.

### Metadata meets deduplication
Main idea: separate metadata from data
>1. design deduplication-friendly formats 
>2. application-level post-processing
>3. format-aware deduplication

- Metadata impacts deduplication
>1. Metadata changes: the input is an aggregate of many small user files, which is interleaved with file metadata. (including, file path, timestamps, etc.)
>![1560321404408](../paper_figure/1560321404408.png)
>
>By mixing more frequently changing metadata with data blocks, the *tar* format unnecessarily introduces many more unique chunks.
>
>![1560328134020](../paper_figure/1560328134020.png)
>
>2. Metadata location changes: the input is encoded in blocks and metadata is inserted for each block, data insertion/deletion lead to metadata shifts.


- Deduplication-friendly formats
1. Common data format
separate data from metadata (EMC backup format)
> the metadata of all files is grouped together and stored in one section.

2. Application-level post-processing (Migratory tar)
*tar* is a well-defined data format
> unfriendly to deduplication
> in wide use for decades, thus hard to change for compatibility reasons.
> tar file is a sequence of entries 
> a entry: one header block + many data blocks

**Migratory tar** (mtar)
separate metadata from data blocks by colocating metadata blocks at the end of the mtar file.
![1560329084025](../paper_figure/1560329084025.png)

Store the offset of the metadata block in the first block of a mtar file for efficient access
> a restore operation reads the first block, find the first header block
> reads all data blocks for that file
> repeat this process for every file



### Implementation and Evaluation
- Implementation:
1. Modify the tar program to support convert $tar \Rightarrow mtar$
2. use fs-hasher to support chunking for comparison


- Evaluation
dataset: the linux kernel distribution
1. compare the deduplication ratio with different methods

## 2. Strength (Contributions of the paper)
1. The main contribution of this paper is it identifies and categorizes barriers to deduplication, and provides two cases study:
> Industrial experience: EMC Data Domain
> Academic research: GNU tar

## 3. Weakness (Limitations of the paper)
1. The idea of this paper is very simple, I only concern the restore performance of mtar
## 4. Future Works
1. This key insight in this paper is separating metadata from data can improve the deduplication ratio significantly.
2. This paper also mentions it is very necessary to design a data format to be deduplication-friendly.
> the application can improve deduplication in a **platform-independent** manner while isolating the storage system from the data format.

3. The tar format is unfriendly for deduplication, specifically, they found that metadata blocks have no deduplication, while data blocks show high deduplication ratios.