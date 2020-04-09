---
typora-copy-images-to: paper_figure
---

# A "Hitchhiker" Guide to Fast and Efficient Data Reconstruction in Erasure-coded Data Centers
@Rashmi @SIGCOMM'14 @new erasure-coded storage system
[TOC]

## Summary
***Motivation of this paper***: There is a  problem of **decreasing the amount of data required to be downloaded for reconstruction** in erasure-coded system.<u>All existing practical solutions either demand additional storage space, or are applicable in very limited settings</u>. Thus, this paper wants to reduce both **network and data traffic**, without requiring any additional storage and maintaining the same level of fault-tolerance as RS-based system.

***Hitchhiker's Erasure Code***: It builds on top of RS codes and uses the theoretical framework of **"Piggybacking"**

![1533713900014](paper_figure/1533713900014.png)

![1533716802051](paper_figure/1533716802051.png)

There are three versions of Hitchhiker's erasure code:
1. **Hitchhiker-XOR**: require only XOR operations in addition to the underlying RS encoding,
2. **Hitchhiker-XOR+**: (further reduces the amount of data required for reconstruction): employs only additional XOR operations and <u>at least one parity function of RS code to be an XOR of all the data units.</u>
3. **Hitchhiker-nonXOR**: does not possess the all-XOR-parity property, but at the cost of additional finite-field arithmetic.

***Disk Layout (Hop-and-couple)***: 
- Way of choosing which bytes to mix
>1. couples bytes farther apart in block
>2. to minimize fragmentation of reads during reconstruction
- Translate savings in network-transfer to savings in disk-IO as well
>1. By making reads contiguous

The hop-and-couple technique couples a byte with another byte within the same unit that is a **certain distance (hop-length) ahead**. The hop-length significantly affects the contiguity of the data read during reconstruction. (==Note: discontinuous reading is a issue that should be considered== ) 

***Implementation and Evaluation***: It implementated Hitchhiker in the Hadoop Disributed File System (HDFS) 
>1. Computation time for encoding
>2. Computation time for degraded reads and recovery
>3. Read time for degraded reads and recovery



## Strength (Contributions of the paper)
1. This paper proposes a new encoding and decoding that builds on top of RS codes and **reduces the amount of download required** during reconstruction of missing or otherwise unavailable data, without increasing the storage overhead or limiting the system design.
>make use of the theoretical framework of **piggybacking** to reduce the amount of data required for reconstruction.

2. A novel **disk layout** technique ensures that the savings in network traffic offered by the new code is translated to savings in disk traffic as well.
3. Implement Hitchihker in HDFS and do the evaluation in a data center at Facebook 


##  Weakness (Limitations of the paper)
1. Compared with RS-based system, although it can reduce the amount of data required for reconstruction Hitchhiker needs to connect to more machine. Maybe it can be a issue of it because this may lead to an increase in the read latency during reconstruction.
2. The hop-and-couple technique can translate the network savings to disk savings, so there should be a optimal tradeoff point.

##  Future Work
1. It can be extended to find the optimal tradeoff point of the hop-and-couple technique
2. How to reduce the amount of machine needed to connect is another direction


