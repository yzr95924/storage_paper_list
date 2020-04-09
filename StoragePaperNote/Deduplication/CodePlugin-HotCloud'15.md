---
typora-copy-images-to: paper_figure
---
# CodePlugin: Plugging Deduplication into Erasure Coding for Cloud Storage
@HotCloud'15 @Deduplication && Erasure Code 
[TOC]
## Summary
***Motivation of this paper***: To simplify the implementations of erasure coding schemes. Many system only support file appending operations. This features leads to a non-trivial and increasing portion of redundant data on cloud storage systems.
Such redundancy leads to several consequences:
>1. extra storage has to to be used to acommodate such redundant data.
>2. extra coding (I/O) cost has to be paid since the redindant data have to be encoded as well.

***CodePlugin***
**Main Idea**: CodePlugin introduces some *pre-processing* steps before the normal encoding.
> In these pre-processing steps, the data duplications are identified and properly shuffled so that the redundant blocks do not have to be encoded. 

- CodePlugin Design
>1. the de-duplicating step that tries to identify the redundant blocks
>2. the pseudo-shuffling step. which is to virtually re-arrange the positions of data blocks so that encoding is only needed on a subset of blocks.
>3. the optimal sub-files exchanging step, which can further reduce the number of blocks to be encoded.

- CodePlugin uses a cache to keep the fingerprints. In this cache, a hash table is responsible for mapping fingerprints of unique blocks to their address.
> When a file is chunked and all fingerprints are generated, after comparing these fingerprints to hte ones in the cache, CodePlugin can tell which blocks are redundant.


After the file is chunked, the blocks are identified via **3-tuple** $(fid, sid, cid)$ address, which is composed of the corresponding *file id (fid)*, *sub-file id (sid)* and *column id (cid)*. 
![1538214740187](paper_figure/1538214740187.png)

- Pseudo-shuffling
To encode the unique blocks together, and leave the redundant blocks untouched 
Since it wants to keep the file untouched, it just needs record the original address of the block instead of moving the actual block around.

![1538237284824](paper_figure/1538237284824.png)

***Implementation and Evaluation***:
**Impementation**
none
**Evaluation**

- Preliminary experiments based on some real-world VM workloads
1. It foucses on the improvment of CodePlugin (with different coding parameters)
>1. encoding throughput
>2. storage space

2. For the CodePlugin overhead, it evaluates the throughput of pre-processing with different coding schemes. 

3. It also tests the throughput by varying cache and coding parameters.

## Strength (Contributions of the paper)
1. This paper proposes the CodePlugin, a mechanism that is applicable to any existing erasure coding scheme.
2. It also conducts the experiments based on some real-world cloud VM images
## Weakness (Limitations of the paper)
1. In order to deduplicate the redundant data, CodePlugin mainly introduces overhead from the two aspects: the CPU cost (MD5 fingerprint) and the storage cost (Map-Address file)
## Future Works
1. this work just considers the case of fixed sized chunking, how about the case of the variable sized chunking. Because the variable sized chunking has the high efficiency in detecting the redundant data.