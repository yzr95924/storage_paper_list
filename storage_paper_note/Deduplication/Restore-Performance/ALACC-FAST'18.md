---
typora-copy-images-to: paper_figure
---
ALACC: Accelerating Restore Performance of Data Deduplication Systems Using Adaptive Look-Ahead Window Assisted Chunk Caching
------------------------------------------
|  Venue  |       Category       |
| :-----: | :------------------: |
| FAST'18 | containers deduplication |
[TOC]

## 1. Summary
### Motivation of this paper: 
- This paper focuses on how to accelerate the restore operation in a data deduplication system. 
> Requesting a unique or duplicate data chunk may trigger a container read if the data chunk is not currently available in memory. 
> Due to the **serious data fragmentation** and **size mismatching of requested data and I/O unite**, the restore performance is much lower that that of directly <u>reading out the data which is not deduplicated.</u>  

- Existing method: chunk-based caching, container-based caching, and forward assembly. 
Remaining issues: 
> 1. how to handle the change of **workload locality**
> 2. how to use the look-ahead window to improve the cache hit ratio
> 3. how to make better trade-off between computing overhead and restore performance? 

|              | Container-based Caching                                      | Chunk-based Caching                                          | Forward Assembly                                        |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------- |
| Advantage    | Less operating and management overhead                       | High cache hit ratio (can be improved by using look-ahead window) | Highly efficient, Low operating and management overhead |
| Disadvantage | Relatively higher **cache miss ration**, especially when the caching space is limited. | Higher operating and management overhead                     | Workload sensitive, requires **good workload locality** |

- Two main research directions in this issue:
> 1. selecting and storing some duplicated data chunks during the deduplication processes
> 2. design efficient caching policies during the restore process

### Adaptive Look-Ahead Chunk Caching (ALACC)

- Major goal: reduce the number of container-reads. 
> Forward assembly + chunk-based caching + LAW (limited memory space, look-ahead window)


- Main idea: 
1. design a hybrid scheme which combines chunkl-based caching and forward assembly 
2. a new way to make better decision on which data chunks are to be cached or evicated. 


- Look-ahead window assisted chunk cache
![1553325419379](paper_figure/1553325419379.png)
FAA: consists several container size memory buffers
LAW: covers a range bigger than that of FAA in the recipe.
> divide the FAW into two portions: FAA Covered Range (size is same as FAA) + Information for Chunk Cache (used for caching policy)

Restore process:
> 1. check the chunk in container read buffer
> 2. check the chunk in chunk cache
> 3. if that chunk is not in the chunk cache, read the container that holds the data chunk from the storage
> 4. each chunk in this container is checked with LAW to identify all the locations it appears in the FAA.
> 5. decide which chunks in this container are to be inserted to the chunk cache according to the **a caching policy**

Caching policy:
1. divide chunks in read-in container into three types:
> 1. U-chunk (Unused chunk): not appear in the current entire LAW
> 2. P-chunk (Probably used chunk): appears in the current FAA but does not appear in the second portion of the LAW
> 3. F-chunk (Future used chunk): chunks will be used in the second portion of the LAW.

2. Caching principle
F-chunks should be cached, if more cache space is still available, it may cache some P-chunks. (also define the priority)

- The adaptive algorithm 
It proposes an adaptive algorithm that can dynamically adjust the size of FAA, chunk cache and LAW according to the workload variation during the restore process.
> For example, for backup trace: most data chunks are unique at the beginning. Later, in different sections of the work load, they may have various degrees of deduplication and re-usage.
> Mainly consider three sizes: LAW size $\rightarrow$ $S_{LAW}$, FAA size $\rightarrow$ $S_{FAA}$, chunk cache size $\rightarrow$ $S_{cache}$. (it should require $S_{FAA} + S_{cache} \leq S_{LAW}$)
> The size of LAW:
>
> > Too large: the computing overhead large but the extra information in the LAW is wasting 
> > Too small: it becomes forward assembly + LRU cache.

![1553335487753](paper_figure/1553335487753.png)
The key goal: to achieve best trade-off between cache efficiency and (CPU and memory) overhead.

> dynamically adjusts the memory space ratio of FAA and chunk cache, and the size of LAW.
> Instead of using a fixed value as the threshold.

### Implementation and Evaluation
- Prototype:
> a deduplication system: 11k LoC C program 
> Restore recovery log (RRL) is maintained to ensure reliability.
> Not open-source

- Evaluation
> a Dell PowerEdge server with 2.40GHz Intel Xeon 24 cores and 32GB
> four deduplication traces: FSL_1, FSL_2, EMC_1, EMC_2
> Measurement: speed factor (MB/container-read), computing cost factor (second/GB), restore throughput (MB/second). 

## 2. Strength (Contributions of the paper)
1. this paper comprehensively analyzes the trade-off of different restore operation schemes in terms of the performance in various workloads and memory configurations.
2. propose ALACC which can dynamically adjust the sizes of FAA and chunk cache to adapt to the changing of chunk locality. 
3. implement an effective look-ahead window with its size dynamically adjusted to provide essential information for FAA.
## 3. Weakness (Limitations of the paper)
1. I think ALACC can be still improved by multi-threading implementation. Also, this paper extends ALACC to handle the duplicated data chunk rewriting in FAST'19.

## 4. Future Works
When this issue meets secure deduplication algorithm, can it arise other problem?  