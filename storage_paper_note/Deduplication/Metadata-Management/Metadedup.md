---
typora-copy-images-to: paper_figure
---
Metadedup: Deduplicating Metadata in Encrypted Deduplication via Indirection
------------------------------------------
@MSST'19 (under review) @deduplication (metadata)
[TOC]

## 1. Summary
### Motivation of this paper: 
This paper wants to reduce the storage overhead of **deduplication metadata** and **key metadata** of encrypted deduplication.
> 1. deduplication metadata: fingerprint index of all chunks and file recipe (hold the mapping from chunks in the file to the references of the physical copies.)
> 2. key metadata: chunk-to-key mappings to allow the decryption of individual files. (encrypted by a master key of file owners)

### Metadedup
- Metadedup applies deduplication to remove content redundancies of both data and metadata, so as to improve the overall storage efficiency. Three key goals
>1. reduce the storage overhead of metadata
>2. provide the security for both data and metadata
>3. introduce low overhead of the overall performance

- Design
>1. Indirection: Metadedup collects the metadata of multiple regions of **adjacent encrypted data chunks** into *metadata chunk*.
>2. Segmentation: 
> ![1547724806425](paper_figure/1547724806425.png)

- Two kinds of operations

**Write Operation**:
> 1. divide the target file into a set of data chunks, and do encryption of them
> 2. divde the set of encrypted chunks into a set of segments
> 3. For each segment, add the metadata of encrypted chunks in this segment to an individual metadata chunk. **(each segment has a metadata chunk)**
> 4. Add deduplication metadata of encrypted metadata chunk to recipes.
> 5. After server received encrypted data chunks and encrypted metadata chunks, it executes the deduplication according to the fingerprint index of data chunks and metadata chunks.

**Restore Operation**:
Two-round interactions:
> 1. Client sends the request to get metadata of the file. Server sends back the encrypted metadata chunks and encrypted key recipes
> 2. Client decrypts the key recipe and metadata chunks, and requests the file data. Server sends back the encrypted data chunks to the client. And client decrypts each data chunk based on the corresponding key in metadata chunks.


### Implementation and Evaluation
This paper implements the Metadedup based on CDStore
1. It adds a metadata chunk construction module in CDStore, which treats $s$ input share streams as encrypted data chunks.

2. It implements the fingerprint index by using the key-value store LevelDB, which maps a fingerprint to an ID of a container which stores the corresponding data share or metadata chunk.
![1547719418272](paper_figure/1547719418272.png)

Evaluation:
1. Microbenchmarks test: consumed time and throughput in both write and restore operations
2. prototype test: the loss performance with various segment sizes, data sizes
3. trace-driven simulation: storage efficiency. (storage space it can save)

## 2. Strength (Contributions of the paper)
- This paper proves the high metadata storage overhead in encrypted deduplication system by using mathematical analysis and trace-driven simulation. This can support the motivation of this paper well.

- It also considers the compatibility of Metadedup with some existing countermeasures, which can make its threat model more consolidated.
## 3. Weakness (Limitations of the paper)
- Compared with the case without metadata deduplication, it needs to retrieve from server twice in its restore operation. 
- From it experiments, it shows that handling metadata chunk consumes dominated time, it can become as the bottleneck when there are too many metadata chunks. 
## 4. Future Works
- It can further optimize restore operation because this two round may take too much time. One thing that can be considered is how to merge those two steps as one step (metadata chunk + data chunk)
- It is obvious that handling metadata chunk is the main bottleneck. However, given a target file, the amount of metadata chunks depends on the size of the segment. It can further consider how to mitigate this bottleneck by controling the size of the segment.