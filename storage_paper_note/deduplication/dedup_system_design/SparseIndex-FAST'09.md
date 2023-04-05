---
typora-copy-images-to: ../paper_figure
---
Sparse Indexing: Large Scale, Inline Deduplication Using Sampling and Locality
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| FAST'09 | Deduplication Index |
[TOC]

## 1. Summary
### Motivation of this paper
1. This paper wants to solve the chunk-lookup disk bottleneck problem of large-scale backup that inline, chunk-based deduplication schemes face.
> originally, it needs to index every chunk.
> **chunk-lookup disk bottleneck problem**

2. It concerns the scenario of inline-deduplication
> data is deduplicated as it arrives rather than later in batch mode.
> the backup data is a form of data stream (e.g., 400GB image)

3. This paper solves this problem via **chunk locality**.
> 1. the tendency for chunks in backup data streams to reoccur together
> 2. For example: if the last time it encountered chunk $A$, it was surrounded by chunks $B$, $C$, and $D$, then next time it encounters $A$ (even for different backup), it is likely that it will also encounter $B$, $C$ or $D$ nearby.

### Spare Index
- Key observation
If two pieces of backup streams share any chunks, they are likely to share many chunks.


- Inline deduplication vs. out-of-line deuplication
1. Inline deduplication: the data is deuplicated as it arrives and before it hits disk.
2. Out-of-line deduplication: the data is first accumulated in an on-disk holding area and then deduplicated later in batch mode.

The disadvantages of out-of-line deduplication:
>1. the need for an on-disk holding area large enough to hold an entire backup window's worth of raw data.
>2. need to implement a separate holding area. 
>3. it is not possible to conserve network or disk bandwidth because every chunk must be written to the hold  

- Segmentation
In its approach, a segment is the unit of storage and retrieval. 
> segment: a sequence of chunks, a few megabytes
> two segments are similar: if they share a number of chunks.

**Segment manifests**: segment recipe
> allows reconstructing a segment from its chunks.
> every stored segment has a manifest that is stored on disk.

Segmentation method:
> 1. Fixed-sized segments
> 2. Variable-size segmentation: use the same trick at the chunking level to avoid the boundary-shifting problem, base the landmarks in the content.

- Deduplication step
Step 1: identify among all the segments in the store some that are most similar to the incoming segment
> this paper calls them "champions"

Step 2: deduplicate against those "champions" segments by finding the chunks they share with the incoming segment, which do not need to be stored again.

![1561021159924](../paper_figure/1561021159924.png)


- How to identify the similar segments
sample the chunk hashes of the incoming segment, and use an in-RAM index to determine which already-stored segments contain how many of those hashes.
> **sparse index**: maps the samples to the manifests in which they occur. (Need to set the limitation for the number of manifests that can be pointed by any one hook)
> once it has chosen champions, it can load their manifests into RAM and use them to deduplicate the incoming segment.
> Assuptions: chunk locality they are likely to share many other chunks with the incoming segment as well.
> a **score  scheme** to determine the champions (choose the manifest with highest non-zero score)

- Deduplicating against the champions
1. load the champion manifests from disk to the RAM
> can use a small cache to speed this process since the adjacent segments sometimes have champions in common.

### Implementation and Evaluation
- Evaluation
1. simulator based evaluation
> input: a series of (chunk hash, length) pairs
> divide into some segments, determines the champions for each segment, and then calculates the amount of deduplication obtained.

2. setting
chunk size: 4KB mean, which they find a good trade-off between maximizing deduplication and minimizing per-chunk overhead.
3. dataset
> Workgroup: a semi-regular series of backups of the desktop PCs of a group of 20 engineers. (3TB)
> SMB: medium business server backedup to virtual tape, 0.6TB.

4. Data Locality
> Deduplicating each input segment against only 2 prior segments can sufficeto remove all but 1% of the duplicate chunks.
> Larger segment size yield slightly less locality

5. RAM Space usage
Suppose the scenario of 100TB backup data, and compare the RAM usage with bloom filter.


## 2. Strength (Contributions of the paper)
1. the key strength of this paper is only maintaining the sparse index in RAM, which is much smaller than a full chunk index.
> can be 128 times smaller than a full chunk index.
> amortize the disk seek overhead of  the thousands of chunks in each segment.

## 3. Weakness (Limitations of the paper)

## 4. Future Works
1. this paper mentions the concept of chunk locality, and takes advantages of this property to adopt the segment level deduplication. But it does not mention how to measure chunk locality for a given workload?
> without knowing the chunk locality, it is hard to decide the segment.


2. In this paper, it uses a manifest to store the metadata of the segment, and put them in the disk.

3. In this paper, it does not consider the client-side deduplication, the reason this paper argues 
> it needs the cost of some client-side processing
> need to modify the legacy backup clients
